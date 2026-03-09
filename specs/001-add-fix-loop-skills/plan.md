# Implementation Plan: Add Fix-Loop Skills and Public Repository Setup

**Branch**: `001-add-fix-loop-skills` | **Date**: 2026-03-09 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-add-fix-loop-skills/spec.md`

## Summary

Add two AI agent skills (coderabbit-fix-loop and deepsource-fix-loop)
to the repository in the vercel-labs/skills SKILL.md format, and set
up the repository with open-source best practices so it is immediately
useful for others. No custom CLI is needed — distribution uses the
existing `npx skills add` from vercel-labs/skills.

## Technical Context

**Language/Version**: Markdown with YAML frontmatter (no programming language)
**Primary Dependencies**: vercel-labs/skills CLI (external, not bundled)
**Storage**: N/A (static files in git)
**Testing**: Manual validation via `npx skills add --list` and dry-run install
**Target Platform**: GitHub-hosted repository, consumed by `npx skills`
**Project Type**: Open-source skill content repository
**Performance Goals**: N/A (static content)
**Constraints**: SKILL.md files must conform to vercel-labs/skills
  frontmatter format (name, description required)
**Scale/Scope**: 2 skills initially, designed to grow via contributions

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Self-Contained Skills | PASS | Each SKILL.md is fully self-contained with all instructions inline |
| II. Agent-Agnostic | JUSTIFIED VIOLATION | Both skills reference Claude Code-specific features (Agent tool, subagents). This is inherent to their function. The vercel-labs/skills CLI handles multi-agent installation; the skill content is agent-specific by necessity. |
| III. Convention Over Configuration | PASS | Following `skills/<name>/SKILL.md` convention from vercel-labs/skills |
| IV. Zero-Side-Effect Installation | PASS | Installation handled entirely by `npx skills` CLI (symlink/copy) |
| V. Low Barrier to Contribution | PASS | CONTRIBUTING.md with skill template and validation checklist planned |

## Project Structure

### Documentation (this feature)

```text
specs/001-add-fix-loop-skills/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
└── tasks.md
```

### Source Code (repository root)

```text
jem-agent-skills/
├── skills/
│   ├── coderabbit-fix-loop/
│   │   └── SKILL.md
│   └── deepsource-fix-loop/
│       └── SKILL.md
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug-report.md
│   │   ├── skill-request.md
│   │   └── config.yml
│   └── PULL_REQUEST_TEMPLATE.md
├── README.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
├── AGENTS.md
├── LICENSE
└── .gitignore
```

**Structure Decision**: Content-only repository. No src/, tests/, or
build directories needed. The `skills/` directory is the primary
content, and root-level documentation files provide the open-source
scaffolding.

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|--------------------------------------|
| Agent-specific skill content (Principle II) | CodeRabbit and DeepSource fix-loop skills use Claude Code Agent tool for parallel subagent dispatch — no agent-agnostic equivalent exists | A generic version would lose the core value (autonomous parallel fixing). Agent-specific content is acceptable when the skill's function requires agent-specific capabilities. |
