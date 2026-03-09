# jem-agent-skills

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

A curated collection of AI coding agent skills. Install any skill into your project with one command using the [skills CLI](https://github.com/vercel-labs/skills).

```bash
npx skills add jem-open/jem-agent-skills
```

## Available Skills

| Skill | Description | Prerequisites |
|-------|-------------|---------------|
| [review-fix-loop](skills/review-fix-loop/SKILL.md) | Autonomous review-and-fix loop. Runs any code analysis tool, dispatches parallel subagents to fix findings, verifies with project lint and tests, and loops until clean. | An analysis CLI (e.g. [CodeRabbit](https://docs.coderabbit.ai/cli), [DeepSource](https://deepsource.io/cli), ESLint, Ruff) installed |
| [jem-ui-components](skills/jem-ui-components/SKILL.md) | Complete reference for using `@jem-open/jem-ui` components — props, variants, design tokens, setup, and common mistakes to avoid. | `@jem-open/jem-ui` installed in your project |
| [jem-ui-patterns](skills/jem-ui-patterns/SKILL.md) | Guide for composing `@jem-open/jem-ui` components into app-level UI patterns — forms, data views, modals, navigation, and feedback. | `@jem-open/jem-ui` installed in your project |
| [jem-ui-recipes](skills/jem-ui-recipes/SKILL.md) | Copy-paste-ready code blocks for common pages and features built with `@jem-open/jem-ui` — search tables, CRUD forms, settings pages, and more. | `@jem-open/jem-ui` installed in your project |

## Install

Install all skills from this repository:

```bash
npx skills add jem-open/jem-agent-skills
```

Install a specific skill:

```bash
npx skills add jem-open/jem-agent-skills@review-fix-loop
```

List available skills before installing:

```bash
npx skills add jem-open/jem-agent-skills --list
```

Install globally (user-level, available in all projects):

```bash
npx skills add jem-open/jem-agent-skills -g
```

The [skills CLI](https://github.com/vercel-labs/skills) supports 40+ AI coding agents including Claude Code, Cursor, GitHub Copilot, Windsurf, and more. It will detect your agent and install to the correct location.

## Usage

Once installed, skills are available as commands in your AI agent. For example, in Claude Code:

```
/review-fix-loop coderabbit review --plain -t all
/review-fix-loop deepsource issues --output json
/review-fix-loop
```

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for how to create and submit a new skill.

**Quick start for contributors:**

1. Create `skills/<your-skill-name>/SKILL.md`
2. Add YAML frontmatter with `name` and `description`
3. Write the skill instructions in markdown
4. Submit a pull request

## License

[Apache 2.0](LICENSE)
