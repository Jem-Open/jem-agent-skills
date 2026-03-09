## Description

<!-- Brief description of what this PR adds or changes. -->

## Skill Validation Checklist

If this PR adds or modifies a skill, verify:

- [ ] `SKILL.md` exists in `skills/<skill-name>/`
- [ ] YAML frontmatter has `name` and `description` fields
- [ ] `name` field matches the directory name exactly
- [ ] `name` is lowercase and hyphen-separated (`[a-z0-9-]+`)
- [ ] `description` is under 500 characters
- [ ] Skill content is self-contained (no dependencies on other skills)
- [ ] Instructions are clear and actionable for an AI agent
- [ ] Prerequisites are checked in the skill's first step
- [ ] Locally verified: `npx skills add . --list` shows the skill

## Related Issues

<!-- Link any related issues: Fixes #123, Closes #456 -->
