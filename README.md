# jem-agent-skills

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

A curated collection of AI coding agent skills. Install any skill into your project with one command using the [skills CLI](https://github.com/vercel-labs/skills).

```bash
npx skills add jem-open/jem-agent-skills
```

## Available Skills

| Skill | Description | Prerequisites |
|-------|-------------|---------------|
| [coderabbit-fix-loop](skills/coderabbit-fix-loop/SKILL.md) | Autonomous CodeRabbit review-and-fix loop. Runs `coderabbit review`, dispatches parallel subagents to fix all findings, verifies with lint + tests, and loops until zero findings remain. | [CodeRabbit CLI](https://docs.coderabbit.ai/cli) installed and authenticated |
| [deepsource-fix-loop](skills/deepsource-fix-loop/SKILL.md) | Autonomous DeepSource analysis-and-fix loop. Fetches issues via the DeepSource CLI, dispatches parallel subagents to fix all findings, verifies with lint + tests, and loops until zero issues remain. | [DeepSource CLI](https://deepsource.io/cli) installed and authenticated |

## Install

Install all skills from this repository:

```bash
npx skills add jem-open/jem-agent-skills
```

Install a specific skill:

```bash
npx skills add jem-open/jem-agent-skills@coderabbit-fix-loop
npx skills add jem-open/jem-agent-skills@deepsource-fix-loop
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
/coderabbit-fix-loop
/deepsource-fix-loop
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
