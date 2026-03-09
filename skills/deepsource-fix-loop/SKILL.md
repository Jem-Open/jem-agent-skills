---
name: deepsource-fix-loop
description: Use when DeepSource analysis reports issues across one or more files and you want an automated fix-verify loop that runs until zero findings remain, without manual back-and-forth per issue.
---

# deepsource-fix-loop

Autonomous DeepSource analysis-and-fix loop. Fetches issues via the DeepSource CLI, dispatches parallel subagents to fix all findings, verifies with lint + tests, and loops until zero issues remain.

---

## Step 1 — Prerequisites

Run both checks:
```bash
deepsource --version 2>/dev/null
deepsource auth status 2>&1
```

If either fails:
- Not installed: tell the user `curl https://deepsource.io/cli | sh` or `brew install deepsource`
- Not authenticated: tell the user `deepsource auth login`
- **Halt** — do not proceed until prerequisites pass.

---

## Step 2 — Fetch issues

Parse user arguments (from `$ARGUMENTS`) for optional filters:
- `--severity <levels>` e.g. `critical,major`
- `--category <categories>` e.g. `security,bug-risk`
- `--pr <n>` e.g. `--pr 42`

Run:
```bash
deepsource issues --output json [<user filters>]
```

Capture JSON output.

---

## Step 3 — Parse JSON

From the JSON array, extract each issue:
- `location.path` — file path
- `title` — issue title
- `category` — issue category (e.g. `bug-risk`, `security`, `style`)
- `severity` — `critical`, `major`, `minor`
- `description` — full issue description
- `suggested_fix` — DeepSource's suggested remediation (if present)

Group issues by `location.path`.

Display counts:
```
Issues found: <total> (critical: <n>, major: <n>, minor: <n>)
```

---

## Step 4 — Zero issues check

If no issues are returned:
- Print: `DeepSource reports zero issues. Codebase is clean.`
- **Exit loop** — done.

---

## Step 5 — Filter by fixability

Separate issues into two buckets:

**Fixable** — has a clear `title` + `category` and does not require architectural decisions (e.g. style, performance, bug-risk, security with a concrete suggested fix).

**Skip** — issues that require architectural decisions (e.g. broad refactors, dependency replacements, schema changes). Log these separately as "requires manual review" but do not attempt to fix them.

Only proceed with fixable issues.

---

## Step 6 — Fix with subagents (parallel)

For each file or related group of fixable issues, dispatch one `general-purpose` Agent **in parallel** with this prompt:

```
Fix the following DeepSource issues in `<file_path>`.

Current file content:
<content of the file>

Issues to fix:
<for each issue in the group>
- [<severity>] <title> (<category>)
  Description: <description>
  Suggested fix: <suggested_fix or "See description above">
</for each>

Requirements:
- Edit only what is needed to fix the listed issues.
- Do not refactor unrelated code.
- Preserve all existing tests.
- After editing, confirm what was changed and why.
```

Wait for all subagents to complete before proceeding.

---

## Step 7 — Verify

Run both checks sequentially:

```bash
uv run ruff check src/ tests/
```

```bash
ENV=test uv run pytest --tb=short -q
```

If either fails:
- Read the error output
- Fix the regressions directly (do not spawn subagents for this)
- Re-run the failing check until it passes before looping

---

## Step 8 — Loop

Go to Step 2.

**Stuck detection**: compare the issue list each iteration by issue ID or (file + title). If the same issues persist unchanged for 2 consecutive loops:
- Print: `Stuck after <n> loops. The following issues require manual review:`
- List each stuck issue with file, severity, title, and description
- **Exit loop**

---

## Step 9 — Report

After the loop exits (clean or stuck), print a summary:

```
╔══════════════════════════════════╗
║  DeepSource Fix Loop — Summary   ║
╠══════════════╦═══════════════════╣
║ Iterations   ║ <n>               ║
║ Fixed        ║ <count>           ║
║ Skipped      ║ <count>           ║
║ Remaining    ║ <count>           ║
╚══════════════╩═══════════════════╝
```

If remaining > 0 or skipped > 0, list each issue below the table with its category, severity, and reason it was skipped or stuck.
