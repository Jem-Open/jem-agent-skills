---
name: gtm
description: Release workflow for when you are ready to ship. Bumps the version, creates a release branch, publishes a GitHub release with auto-generated notes, and opens release PRs. Supports both gitflow (development + main) and trunk-based (main only) strategies, and reads the version from a `version` file, `setup.py`, or `pyproject.toml`. Both PRs are auto-merged by default; pass --no-auto-merge to leave them open.
---

# gtm

Go-to-market release workflow.

The skill auto-detects two things about the repository:

- **Branching strategy:** `gitflow` (a base branch like `development` flowing into `main`) or `trunk` (`main` is the base; tagging the merged commit triggers production deploy).
- **Version source:** a `version` file at the project root, the `version=` argument in `setup.py`, or the `version` field in `pyproject.toml`.

Both can be overridden with explicit flags.

## Examples

Patch release from the default `development` branch (gitflow):

```
/gtm --patch
```

Minor release:

```
/gtm --minor
```

Major release:

```
/gtm --major
```

Specify a different base branch (gitflow):

```
/gtm --minor --base=develop
```

Trunk-based release where `main` is the base and the production deploy is tag-driven:

```
/gtm --patch --base=main
```

Force a specific version source (auto-detection is usually correct):

```
/gtm --patch --version-source=setup-py
/gtm --minor --version-source=pyproject
/gtm --patch --version-source=file
```

Leave PRs open for manual review instead of auto-merging:

```
/gtm --patch --no-auto-merge
```

---

## Step 1 — Prerequisite check

Verify required tools are available:

```bash
command -v git >/dev/null 2>&1
command -v gh >/dev/null 2>&1
```

If `git` is not found, print: `git is not installed. Install it from https://git-scm.com` and **exit**.

If `gh` is not found, print: `GitHub CLI (gh) is not installed. Install it from https://cli.github.com` and **exit**.

Verify `gh` is authenticated:

```bash
gh auth status
```

If not authenticated, print: `GitHub CLI is not authenticated. Run: gh auth login` and **exit**.

---

## Step 2 — Parse arguments and validate

Parse `$ARGUMENTS` for exactly one of: `--patch`, `--minor`, `--major`.

If none or more than one is provided, print:

```
Usage: /gtm --patch | --minor | --major [--base=<branch>] [--version-source=file|setup-py|pyproject] [--strategy=trunk|gitflow] [--no-auto-merge]
```

and **exit**.

Parse `$ARGUMENTS` for an optional `--base=<branch>` flag. If provided, use that value as `BASE_BRANCH`. If not provided, default to `development`.

Parse `$ARGUMENTS` for an optional `--strategy=<trunk|gitflow>` flag. If not provided, **auto-detect**:

- If `BASE_BRANCH` is `main` (or `master`) → `STRATEGY=trunk`.
- Otherwise → `STRATEGY=gitflow`.

Parse `$ARGUMENTS` for an optional `--version-source=<file|setup-py|pyproject>` flag. If not provided, **auto-detect** in this priority order:

1. If a `version` file exists at the project root → `VERSION_SOURCE=file`.
2. Else if a `setup.py` exists at the project root and contains a `version=` keyword argument → `VERSION_SOURCE=setup-py`.
3. Else if a `pyproject.toml` exists at the project root and declares a `version` under `[project]` or `[tool.poetry]` → `VERSION_SOURCE=pyproject`.
4. Otherwise, print: `No version source detected. Expected a 'version' file, setup.py with version=, or pyproject.toml with [project].version.` and **exit**.

Parse `$ARGUMENTS` for an optional `--no-auto-merge` flag. If present, set `AUTO_MERGE=false`; otherwise default to `AUTO_MERGE=true`.

Verify the current branch matches `BASE_BRANCH`:

```bash
git rev-parse --abbrev-ref HEAD
```

If the current branch does not match `BASE_BRANCH`, print:

```
You must be on the <BASE_BRANCH> branch to create a release. Current branch: <branch>
```

and **exit**.

Verify the working tree is clean (untracked files are tolerated; modifications and staged changes are not):

```bash
git status --porcelain | grep -v '^??' || true
```

If there is any non-untracked output, print:

```
Working tree is not clean. Please commit or stash your changes before releasing.
```

and **exit**.

Pull latest from remote to ensure we're up to date:

```bash
git pull --ff-only
```

If this fails (diverged history), print the error and **exit**.

---

## Step 3 — Read and bump version

Read the current version according to `VERSION_SOURCE`:

| Source | Read command |
|---|---|
| `file` | `cat version` |
| `setup-py` | `python3 -c "import re,sys; m=re.search(r'version\s*=\s*[\"\\']([^\"\\']+)[\"\\']', open('setup.py').read()); print(m.group(1) if m else sys.exit('version not found in setup.py'))"` |
| `pyproject` | `python3 -c "import re,sys; t=open('pyproject.toml').read(); m=re.search(r'^version\s*=\s*[\"\\']([^\"\\']+)[\"\\']', t, re.M); print(m.group(1) if m else sys.exit('version not found in pyproject.toml'))"` |

The version string must follow `MAJOR.MINOR.PATCH` (e.g. `3.152.0`).

Parse into components and apply the bump:

| Flag | Rule |
|---|---|
| `--patch` | `MAJOR.MINOR.(PATCH+1)` |
| `--minor` | `MAJOR.(MINOR+1).0` |
| `--major` | `(MAJOR+1).0.0` |

Store the new version as `NEW_VERSION` (e.g. `3.153.0`).

Display:

```
Bumping version: <OLD_VERSION> → <NEW_VERSION> (source: <VERSION_SOURCE>, strategy: <STRATEGY>)
```

Create and switch to the release branch:

```bash
git checkout -b release/v<NEW_VERSION>
```

Write the new version back to the source:

| Source | Write command |
|---|---|
| `file` | `printf '%s\n' "<NEW_VERSION>" > version` |
| `setup-py` | `python3 -c "import re; p='setup.py'; s=open(p).read(); s2=re.sub(r'(version\s*=\s*[\"\\'])([^\"\\']+)([\"\\'])', r'\\g<1><NEW_VERSION>\\g<3>', s, count=1); open(p,'w').write(s2)"` |
| `pyproject` | `python3 -c "import re; p='pyproject.toml'; s=open(p).read(); s2=re.sub(r'(?m)^(version\s*=\s*[\"\\'])([^\"\\']+)([\"\\'])', r'\\g<1><NEW_VERSION>\\g<3>', s, count=1); open(p,'w').write(s2)"` |

Verify the rewrite landed by re-reading the source and confirming it equals `NEW_VERSION`. If not, print the error and **exit**.

Commit and push:

```bash
git add <version-file>            # 'version', 'setup.py', or 'pyproject.toml'
git commit -m "Bump version to v<NEW_VERSION>"
git push -u origin release/v<NEW_VERSION>
```

Display:

```
Branch release/v<NEW_VERSION> created and pushed.
```

---

## Step 4 — Create GitHub release (gitflow only)

> **Trunk-based releases skip this step.** When `STRATEGY=trunk`, the tag is created in Step 6 *after* the PR merges into `main`, so the tag points at the merge commit on `main` (which is what production deploys from). Skip directly to Step 5.

Find the most recent existing release tag to use as the comparison base:

```bash
gh release list --limit 1 --json tagName --jq '.[0].tagName'
```

Create a new GitHub release targeting the release branch with auto-generated notes:

```bash
gh release create v<NEW_VERSION> \
  --target release/v<NEW_VERSION> \
  --title "v<NEW_VERSION>" \
  --generate-notes \
  --notes-start-tag <PREVIOUS_TAG>
```

If there is no previous release, omit the `--notes-start-tag` flag.

If the release creation fails, print the error output and **exit**. Do not continue to PR creation if the release was not created successfully.

Display:

```
GitHub release v<NEW_VERSION> created.
```

---

## Step 5 — Create release PR(s)

### 5a. PR into `BASE_BRANCH` (always)

```bash
gh pr create \
  --base <BASE_BRANCH> \
  --head release/v<NEW_VERSION> \
  --title "Release v<NEW_VERSION>" \
  --body "Release v<NEW_VERSION>."
```

> For gitflow (`BASE_BRANCH` is e.g. `development`), use the title `Bump version to v<NEW_VERSION>` and body `Merge release branch back into <BASE_BRANCH> after v<NEW_VERSION> release.` to make intent clear.

If PR creation fails (e.g. branch protection, permissions), print the error and continue — do not **exit**, as a second PR may still succeed.

Capture and display the PR URL:

```
PR created: <URL> (release → <BASE_BRANCH>)
```

If `AUTO_MERGE=true` and the PR was created successfully, enable auto-merge:

```bash
gh pr merge <URL> --auto --merge
```

If this fails, print the error as a warning but do not **exit**:

```
Warning: auto-merge could not be enabled for PR → <BASE_BRANCH>: <error>
```

### 5b. PR into `main` (gitflow only)

> **Skip when `STRATEGY=trunk`** — the PR in 5a already targets `main`, and a second PR with the same head branch will fail.

```bash
gh pr create \
  --base main \
  --head release/v<NEW_VERSION> \
  --title "Release v<NEW_VERSION>" \
  --body "Production release of v<NEW_VERSION>."
```

If PR creation fails, print the error and continue to Step 6.

Capture and display the PR URL:

```
PR created: <URL> (release → main)
```

If `AUTO_MERGE=true` and the PR was created successfully, enable auto-merge:

```bash
gh pr merge <URL> --auto --merge
```

If this fails, print the error as a warning but do not **exit**:

```
Warning: auto-merge could not be enabled for PR → main: <error>
```

---

## Step 6 — Tag from `main` after merge (trunk only)

> **Gitflow releases skip this step** — the tag was created in Step 4.

When `STRATEGY=trunk`, the GitHub release / tag must be created *after* the release PR merges into `main`, so production picks up the same commit it sees on `main`. The release PR is normally auto-merging at this point; wait for it to complete.

Poll until the PR is merged:

```bash
gh pr view <PR_URL> --json state --jq '.state'
```

Repeat with a short delay until the value is `MERGED`. If it stays `OPEN` because checks are pending or auto-merge could not be enabled, print:

```
Release PR <PR_URL> is not yet merged. Once it merges, run:

  git checkout main && git pull --ff-only
  gh release create v<NEW_VERSION> --target main --title "v<NEW_VERSION>" --generate-notes --notes-start-tag <PREVIOUS_TAG>

to create the tag and trigger the production deploy.
```

and **exit** — do not block forever.

Once merged, fetch `main` and create the release pointing at it:

```bash
git checkout main
git pull --ff-only

PREVIOUS_TAG=$(gh release list --limit 1 --json tagName --jq '.[0].tagName')

gh release create v<NEW_VERSION> \
  --target main \
  --title "v<NEW_VERSION>" \
  --generate-notes \
  --notes-start-tag "$PREVIOUS_TAG"
```

If there is no previous release, omit the `--notes-start-tag` flag.

Display:

```
GitHub release v<NEW_VERSION> created from main.
```

If the release creation fails, print the error and **exit**.

Optionally clean up the merged release branch:

```bash
git push origin --delete release/v<NEW_VERSION>
git branch -D release/v<NEW_VERSION>
```

---

## Step 7 — Summary

Print a summary appropriate to the strategy.

**Gitflow:**

```
+----------------------------------------------+
|  Release v<NEW_VERSION> — Complete            |
+---------------------+------------------------+
| Strategy            | gitflow                |
| Version source      | <file|setup-py|pyproject> |
| Version             | <OLD> → <NEW_VERSION>  |
| Branch              | release/v<NEW_VERSION> |
| GitHub Release      | v<NEW_VERSION>         |
| PR → <BASE_BRANCH>  | <URL>                  |
| PR → main           | <URL>                  |
| Auto-merge          | enabled / disabled     |
+---------------------+------------------------+
```

**Trunk:**

```
+----------------------------------------------+
|  Release v<NEW_VERSION> — Complete            |
+---------------------+------------------------+
| Strategy            | trunk                  |
| Version source      | <file|setup-py|pyproject> |
| Version             | <OLD> → <NEW_VERSION>  |
| Branch              | release/v<NEW_VERSION> |
| PR → main           | <URL>                  |
| GitHub Release      | v<NEW_VERSION> (from main HEAD) |
| Auto-merge          | enabled / disabled     |
+---------------------+------------------------+
```

If a PR failed, show `FAILED` instead of the URL. If `AUTO_MERGE=true` but auto-merge could not be enabled for a PR, show the URL followed by `(auto-merge failed)`. If the trunk-mode PR did not merge in time and Step 6 printed the manual-tag instructions, show `PENDING — see Step 6 instructions` for the GitHub Release row.

Switch back to the base branch:

```bash
git checkout <BASE_BRANCH>
```
