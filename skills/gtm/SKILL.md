---
name: gtm
description: Release workflow for when you are ready to ship. Bumps the version file, creates a release branch, publishes a GitHub release with auto-generated notes, and opens PRs into development and main.
---

# gtm

Go-to-market release workflow. Bumps the version, creates a release branch, pushes a GitHub release with auto-generated notes, and opens PRs into `development` and `main`.

## Examples

Patch release (e.g. 1.2.3 → 1.2.4):

```
/gtm --patch
```

Minor release (e.g. 1.2.3 → 1.3.0):

```
/gtm --minor
```

Major release (e.g. 1.2.3 → 2.0.0):

```
/gtm --major
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
Usage: /gtm --patch | --minor | --major
```

and **exit**.

Verify the current branch is `development`:

```bash
git rev-parse --abbrev-ref HEAD
```

If not on `development`, print:

```
You must be on the development branch to create a release. Current branch: <branch>
```

and **exit**.

Verify the working tree is clean:

```bash
git status --porcelain
```

If there are uncommitted changes, print:

```
Working tree is not clean. Please commit or stash your changes before releasing.
```

and **exit**.

Pull latest from remote to ensure we're up to date:

```bash
git pull --ff-only
```

If this fails (diverged history), print the error and **exit**.

Verify the version file exists at the project root:

```bash
test -f version
```

If not found, print: `No version file found in the project root.` and **exit**.

---

## Step 3 — Bump version and create release branch

Read the version file at the project root:

```bash
cat version
```

The file contains a single line with a semantic version: `MAJOR.MINOR.PATCH` (e.g. `3.152.0`).

Parse into components and apply the bump:

| Flag | Rule |
|---|---|
| `--patch` | `MAJOR.MINOR.(PATCH+1)` |
| `--minor` | `MAJOR.(MINOR+1).0` |
| `--major` | `(MAJOR+1).0.0` |

Store the new version as `NEW_VERSION` (e.g. `3.153.0`).

Display:

```
Bumping version: <OLD_VERSION> → <NEW_VERSION>
```

Create and switch to the release branch:

```bash
git checkout -b release/v<NEW_VERSION>
```

Write the new version to the version file:

```bash
echo "<NEW_VERSION>" > version
```

Commit and push:

```bash
git add version
git commit -m "Bump version to v<NEW_VERSION>"
git push -u origin release/v<NEW_VERSION>
```

Display:

```
Branch release/v<NEW_VERSION> created and pushed.
```

---

## Step 4 — Create GitHub release

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

## Step 5 — Create PR into development

```bash
gh pr create \
  --base development \
  --head release/v<NEW_VERSION> \
  --title "Bump version to v<NEW_VERSION>" \
  --body "Merge release branch back into development after v<NEW_VERSION> release."
```

If PR creation fails (e.g. branch protection, permissions), print the error and continue to Step 6 — do not **exit**, as the main PR may still succeed.

Capture and display the PR URL:

```
PR created: <URL> (release → development)
```

---

## Step 6 — Create PR into main

```bash
gh pr create \
  --base main \
  --head release/v<NEW_VERSION> \
  --title "Release v<NEW_VERSION>" \
  --body "Production release of v<NEW_VERSION>."
```

If PR creation fails, print the error and continue to Step 7.

Capture and display the PR URL:

```
PR created: <URL> (release → main)
```

---

## Step 7 — Summary

Print a summary:

```
+----------------------------------------------+
|  Release v<NEW_VERSION> — Complete            |
+---------------------+------------------------+
| Version             | <OLD> → <NEW_VERSION>  |
| Branch              | release/v<NEW_VERSION> |
| GitHub Release      | v<NEW_VERSION>         |
| PR → development    | <URL>                  |
| PR → main           | <URL>                  |
+---------------------+------------------------+
```

If either PR failed, show `FAILED` instead of the URL in the summary.

Switch back to the development branch:

```bash
git checkout development
```
