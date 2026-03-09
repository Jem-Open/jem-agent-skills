# Design: review-fix-loop

## Purpose

A single unified skill that runs any code analysis tool, dispatches parallel subagents to fix findings, verifies with the project's own lint/test commands, and loops until clean or stuck.

Replaces the existing `coderabbit-fix-loop` and `deepsource-fix-loop` skills, which were near-duplicates with hardcoded Python tooling.

## Invocation

```
/review-fix-loop <analysis-command>
/review-fix-loop                        # auto-detect tool from project config
```

Examples documented in the skill:

```
/review-fix-loop coderabbit review --plain -t all
/review-fix-loop deepsource issues --output json
```

The entire argument string after the skill name is the analysis command. If no arguments are provided, the skill looks for config files (`.coderabbit.yaml`, `.deepsource.toml`, `.eslintrc`, etc.) to auto-detect which tool to run.

## Skill Flow

### Step 1 -- Prerequisite check

Verify the analysis tool (first word of the command) is installed and available on `$PATH`. If not, tell the user how to install it. Halt until resolved.

### Step 2 -- Run analysis

Execute the user-provided command (or auto-detected command). Capture full stdout.

### Step 3 -- Parse findings

LLM extracts findings from the output regardless of format. Each finding has:

- File path (relative)
- Line number (if present)
- Issue description
- Fix instructions (verbatim from tool output where available)

### Step 4 -- Zero findings check

If no findings, print clean message, exit loop.

### Step 5 -- Group by file

Batch findings by file path. Files with multiple findings go to one subagent to avoid conflicting edits.

### Step 6 -- Fix with subagents (parallel)

Dispatch one subagent per file group. Agent-specific dispatch in conditional sub-sections:

- **Claude Code**: Use `Agent` tool with `general-purpose` subagent type
- **Gemini CLI**: Use `generalist_agent` tool
- **Other agents**: Fix files sequentially as fallback

Each subagent receives: the file content, the list of findings for that file, and instructions to edit only what is needed.

### Step 7 -- Verify

Run the project's lint and test commands. Discovery order:

1. Project files: `package.json` (scripts.lint / scripts.test), `pyproject.toml` (tool.ruff, pytest config), `Makefile`, `Justfile`
2. CLAUDE.md or project docs "Commands" section
3. Ask the user if nothing can be detected

If verification fails, fix regressions directly (no subagents), re-run until green.

### Step 8 -- Loop

Return to Step 2. Stuck detection: if the same set of findings appears unchanged for 2 consecutive iterations, exit with a manual review list.

### Step 9 -- Report

Print a summary table:

- Iterations completed
- Findings fixed
- Findings remaining

If remaining > 0, list each stuck finding with file, line, and description.

## File Changes

### Deleted

- `skills/coderabbit-fix-loop/SKILL.md` -- removed entirely
- `skills/deepsource-fix-loop/SKILL.md` -- removed entirely

### Created

- `skills/review-fix-loop/SKILL.md` -- the unified skill

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| No tool-specific parsing | LLM handles any output format; avoids maintaining parsers for each tool |
| No hardcoded verification commands | Auto-detected from project; works for any language/toolchain |
| Agent adapters in one step only | Keeps the rest of the flow generic; easy to add new agents later |
| CodeRabbit + DeepSource as examples | Documented as invocation examples, not as "supported tools" |
| Accept any analysis command | Maximally flexible; user brings their own tool |
| Stuck detection after 2 loops | Prevents infinite loops without being too aggressive |
