# Contributing to jem-agent-skills

Thank you for your interest in contributing! This guide explains how to create and submit a new skill.

## What is a skill?

A skill is a single `SKILL.md` file that provides instructions for an AI coding agent. Each skill lives in its own directory under `skills/`.

## Directory structure

```
skills/
  your-skill-name/
    SKILL.md
```

## SKILL.md format

Every skill requires a `SKILL.md` file with YAML frontmatter:

```markdown
---
name: your-skill-name
description: Brief explanation of what this skill does and when to use it.
---

# your-skill-name

Detailed instructions for the AI agent go here.

## Step 1 — ...

## Step 2 — ...
```

### Required frontmatter fields

| Field | Rules |
|-------|-------|
| `name` | Lowercase, hyphen-separated (`[a-z0-9-]+`). **Must match the directory name.** |
| `description` | One-line summary, under 500 characters. Describe what the skill does and when to use it. |

### Optional frontmatter fields

| Field | Purpose |
|-------|---------|
| `metadata.internal` | Set to `true` to hide from normal discovery. |

## Naming conventions

- Lowercase only: `my-skill` not `My-Skill`
- Hyphens to separate words: `code-review` not `code_review` or `codereview`
- Descriptive: the name should hint at what the skill does
- The directory name and the `name` field in frontmatter **must match exactly**

## Writing good skill instructions

- **Be self-contained**: Include everything the agent needs. Don't reference other skills or external context.
- **Be specific**: Use exact command names, file paths, and expected outputs.
- **Structure with steps**: Number your steps so the agent follows them in order.
- **Handle errors**: Tell the agent what to do when things fail.
- **Include prerequisites**: If the skill needs external tools, check for them first and tell the user how to install them.

## Submitting your skill

1. **Fork** this repository.
2. **Create** your skill directory and `SKILL.md` file.
3. **Validate** locally:
   ```bash
   # Check that the skills CLI discovers your skill
   npx skills add . --list
   ```
4. **Submit** a pull request. The PR template includes a validation checklist.

## Validation checklist (self-review)

Before submitting, verify:

- [ ] `SKILL.md` exists in `skills/<your-skill-name>/`
- [ ] YAML frontmatter has `name` and `description` fields
- [ ] `name` field matches the directory name exactly
- [ ] `name` is lowercase and hyphen-separated
- [ ] `description` is under 500 characters
- [ ] Skill content is self-contained (no dependencies on other skills)
- [ ] Instructions are clear and actionable for an AI agent
- [ ] Prerequisites are checked in the skill's first step

## Code of conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to abide by its terms.
