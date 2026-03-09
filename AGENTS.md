# AGENTS.md

This file provides guidance to AI coding agents working on this repository.

## Project Overview

`jem-agent-skills` is a collection of AI coding agent skills distributed via the [skills CLI](https://github.com/vercel-labs/skills). This is a content-only repository — no code to build or compile.

## Repository Structure

```
skills/                          # One directory per skill
  <skill-name>/
    SKILL.md                     # Skill definition (frontmatter + instructions)
.github/                         # Issue and PR templates
  ISSUE_TEMPLATE/
    bug-report.md
    skill-request.md
    config.yml
  PULL_REQUEST_TEMPLATE.md
README.md                        # Project overview and skill catalog
CONTRIBUTING.md                  # How to create and submit a skill
CODE_OF_CONDUCT.md               # Community standards
SECURITY.md                      # Vulnerability reporting
AGENTS.md                        # This file
LICENSE                          # Apache 2.0
.gitignore
```

## SKILL.md Format

Each skill is a single `SKILL.md` file with YAML frontmatter:

```yaml
---
name: skill-name-here
description: Brief explanation of what this skill does
---
```

**Rules:**
- `name` must be lowercase, hyphen-separated (`[a-z0-9-]+`)
- `name` must match the parent directory name exactly
- `description` must be non-empty and under 500 characters
- The markdown body contains complete, self-contained instructions

## Adding a New Skill

1. Create `skills/<skill-name>/SKILL.md`
2. Add valid YAML frontmatter with `name` and `description`
3. Write the skill instructions in markdown
4. Verify: `npx skills add . --list` should show the new skill

## Key Conventions

- No build step — all content is markdown
- Skills are self-contained; they must not reference other skills
- Directory names are lowercase and hyphen-separated
- One SKILL.md per skill directory, no nested subdirectories needed
