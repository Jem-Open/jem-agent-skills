# Quickstart: jem-agent-skills

## Install a skill (end user)

```bash
# Install all skills from this repo
npx skills add jem-open/jem-agent-skills

# Install a specific skill
npx skills add jem-open/jem-agent-skills@coderabbit-fix-loop

# List available skills before installing
npx skills add jem-open/jem-agent-skills --list
```

## Verify installation

After installation, the skill should appear in your agent's skill
directory. For Claude Code:

```bash
ls .claude/commands/
# Should show: coderabbit-fix-loop.md (or similar)
```

## Use a skill

Once installed, the skill is available to your AI agent. In Claude
Code, invoke it by name:

```
/coderabbit-fix-loop
```

## Contribute a new skill

1. Fork the repository.
2. Create a new directory under `skills/`:
   ```
   skills/my-new-skill/SKILL.md
   ```
3. Add YAML frontmatter:
   ```yaml
   ---
   name: my-new-skill
   description: Brief explanation of what this skill does
   ---
   ```
4. Write the skill instructions in markdown below the frontmatter.
5. Verify the `name` field matches the directory name.
6. Submit a pull request.

## Validate your skill locally

```bash
# Test that the skills CLI can discover your skill
npx skills add ./path/to/jem-agent-skills --list

# Test installation into a clean project
cd /tmp && mkdir test-project && cd test-project
npx skills add /path/to/jem-agent-skills@my-new-skill
```
