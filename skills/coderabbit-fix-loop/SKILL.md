---
name: coderabbit-fix-loop
description: Use when CodeRabbit review reports findings across one or more files and you want an automated fix-verify loop that runs until the codebase is clean, without manual back-and-forth per finding.
---

# coderabbit-fix-loop

Autonomous CodeRabbit review-and-fix loop. Runs `coderabbit review`, dispatches parallel subagents to fix all findings, verifies with lint + tests, and loops until zero findings remain.

---

## Step 1 — Prerequisites

Run both checks in parallel:
```bash
coderabbit --version
coderabbit auth status
```

If either command fails or returns unauthenticated:
- Not installed: tell the user `brew install coderabbit` (macOS) or direct them to https://docs.coderabbit.ai/cli
- Not authenticated: tell the user `coderabbit auth login`
- **Halt** — do not proceed until prerequisites pass.

---

## Step 2 — Review

Parse user arguments (from `$ARGUMENTS`) for:
- `-t <type>`: review type — `all` (default), `committed`, or `uncommitted`
- `--base <branch>`: base branch for diff (optional)

Run:
```bash
coderabbit review --plain -t <type> [--base <branch>]
```

Capture full stdout output.

---

## Step 3 — Parse findings

From the review output, extract all findings. Each finding has:
- **File path** (relative)
- **Line number** (if present)
- **Issue type / title**
- **Fix instructions** — the verbatim text under `Prompt for AI Agent:` blocks (these contain the actionable fix description)

Build a list: `findings = [{file, line, type, prompt}, ...]`

---

## Step 4 — Zero findings check

If `findings` is empty:
- Print: `No CodeRabbit findings. Codebase is clean.`
- **Exit loop** — done.

---

## Step 5 — Group by file

Group findings by `file` path. Files with multiple findings are batched into one subagent call to avoid conflicting edits.

---

## Step 6 — Fix with subagents (parallel)

For each file group, dispatch one `general-purpose` Agent **in parallel** with this prompt:

```
Fix the following CodeRabbit findings in `<file_path>`.

Current file content:
<content of the file>

Findings to fix:
<for each finding in the group>
- Line <line>: <type>
  Fix instructions: <verbatim Prompt for AI Agent text>
</for each>

Requirements:
- Edit only what is needed to fix the listed findings.
- Do not refactor unrelated code.
- Preserve all existing tests.
- After editing, confirm what was changed.
```

Wait for all subagents to complete before proceeding.

---

## Step 7 — Verify

Run both checks sequentially:

```bash
LINT_PATHS=$(for d in src app lib tests test; do [ -d "$d" ] && printf "%s " "$d"; done)
uv run ruff check $LINT_PATHS
```

```bash
ENV=test uv run pytest --tb=short -q
```

If either fails:
- Read the error output
- Fix the regressions directly (do not spawn subagents for this — keep it simple)
- Re-run the failing check until it passes before looping

---

## Step 8 — Loop

Go to Step 2.

**Stuck detection**: track the set of findings each iteration. If the same findings appear unchanged for 2 consecutive loops:
- Print: `Stuck after <n> loops. The following findings require manual review:`
- List the stuck findings with file, line, type, and fix instructions
- **Exit loop**

---

## Step 9 — Report

After the loop exits (clean or stuck), print a summary table:

```text
╔══════════════════════════════════╗
║  CodeRabbit Fix Loop — Summary   ║
╠══════════════╦═══════════════════╣
║ Iterations   ║ <n>               ║
║ Fixed        ║ <count>           ║
║ Remaining    ║ <count>           ║
╚══════════════╩═══════════════════╝
```

If remaining > 0, list each stuck finding below the table.
