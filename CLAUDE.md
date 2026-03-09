# jem-agent-skills Development Guidelines

Auto-generated from all feature plans. Last updated: 2026-03-09

## Active Technologies

- Markdown with YAML frontmatter (skill content format)
- vercel-labs/skills CLI (external distribution tool, not bundled)

## Project Structure

```text
skills/                              # Skill content (one dir per skill)
  review-fix-loop/SKILL.md            # Unified analysis review-and-fix loop
.github/                             # Issue/PR templates
  ISSUE_TEMPLATE/                    # Bug report, skill request
  PULL_REQUEST_TEMPLATE.md           # PR checklist
docs/plans/                          # Design docs and implementation plans
README.md                            # Skill catalog and install instructions
CONTRIBUTING.md                      # How to author and submit skills
AGENTS.md                            # Guidance for AI agents on this repo
CODE_OF_CONDUCT.md                   # Community standards
SECURITY.md                          # Vulnerability reporting
```

## Key Conventions

- Skills follow `skills/<name>/SKILL.md` directory convention
- SKILL.md requires YAML frontmatter with `name` and `description`
- The `name` field must match the parent directory name
- Names are lowercase, hyphen-separated: `[a-z0-9-]+`
- No custom CLI — distribution via `npx skills add jem-open/jem-agent-skills`

## Commands

```bash
# List available skills in this repo
npx skills add jem-open/jem-agent-skills --list

# Test local skill discovery
npx skills add . --list

# Review with CodeRabbit
coderabbit review --plain -t all
```

## Code Style

- Markdown: ATX headings, blank line between sections
- YAML frontmatter: 2-space indent, no trailing whitespace
- File naming: lowercase, hyphen-separated, no spaces

## Recent Changes

- 001-add-fix-loop-skills: Replaced coderabbit-fix-loop and deepsource-fix-loop with unified review-fix-loop skill, plus open-source repo scaffolding

<!-- MANUAL ADDITIONS START -->
## Gotchas

- API content filtering can block Code of Conduct text — keep minimal, link to Contributor Covenant externally
- This is a content-only repo: no build step, no package.json, no src/tests directories
- review-fix-loop supports Claude Code, Gemini CLI, and a sequential fallback for other agents

## Project Principles

- Skills must be self-contained — no cross-skill dependencies
- Skills should be agent-agnostic where possible (review-fix-loop supports Claude Code, Gemini CLI, and sequential fallback)
<!-- MANUAL ADDITIONS END -->
