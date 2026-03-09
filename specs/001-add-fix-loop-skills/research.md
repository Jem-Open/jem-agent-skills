# Research: Add Fix-Loop Skills and Public Repository Setup

**Date**: 2026-03-09
**Feature**: 001-add-fix-loop-skills

## Decision 1: Skill File Format

**Decision**: Use `SKILL.md` with YAML frontmatter (name, description)
in `skills/<skill-name>/SKILL.md` directories.

**Rationale**: This is the standard format defined by the
vercel-labs/skills CLI. The CLI discovers skills by searching for
SKILL.md files in standard paths including `skills/`. Using the
canonical format ensures zero-friction discovery and installation.

**Alternatives considered**:
- Custom skill.json + separate markdown: Rejected — adds complexity
  with no benefit since the skills CLI already parses SKILL.md
  frontmatter.
- Single flat directory of .md files: Rejected — the skills CLI
  expects `skills/<name>/SKILL.md` directory structure for proper
  name resolution and `@skill-name` targeting.

## Decision 2: Distribution Mechanism

**Decision**: Rely entirely on the vercel-labs/skills CLI
(`npx skills add jem-open/jem-agent-skills`). No custom CLI.

**Rationale**: The skills CLI already handles cloning, skill
discovery, agent detection (40+ agents), symlink/copy installation,
update checking, and lock file management. Building a custom CLI
would duplicate existing functionality.

**Alternatives considered**:
- Custom npx package: Rejected — unnecessary duplication of
  vercel-labs/skills functionality.
- Shell script installer: Rejected — would not support the multi-agent
  installation that the skills CLI provides.

## Decision 3: Open Source Repository Structure

**Decision**: Follow GitHub open-source best practices with these
files at repository root: README.md, CONTRIBUTING.md,
CODE_OF_CONDUCT.md, SECURITY.md, AGENTS.md, .gitignore, and
.github/ templates.

**Rationale**: These are the standard files expected by open-source
contributors and recognized by GitHub's community health features.
The vercel-labs/skills repo itself includes AGENTS.md at root.

**Alternatives considered**:
- Minimal (README only): Rejected — fails Constitution Principle V
  (Low Barrier to Contribution) and open-source best practices.
- Full npm package with package.json: Rejected — this repo has no
  code to build or publish; it is pure content consumed directly
  from GitHub by the skills CLI.

## Decision 4: Agent Compatibility Approach

**Decision**: Both initial skills are Claude Code-specific. This is
documented but not abstracted. Future skills can be agent-agnostic
or target other agents.

**Rationale**: The coderabbit-fix-loop and deepsource-fix-loop skills
use Claude Code's Agent tool for parallel subagent dispatch. There is
no generic equivalent across agents. The vercel-labs/skills CLI will
install the SKILL.md to any selected agent, but the content only
functions in Claude Code. This is an acceptable tradeoff documented
in the constitution's Complexity Tracking.

**Alternatives considered**:
- Adapter layer per agent: Rejected — no other agent supports the
  parallel subagent pattern these skills require.
- Generic instructions only: Rejected — loses the core autonomous
  fix-loop value proposition.

## Decision 5: GitHub Issue and PR Templates

**Decision**: Include issue templates for bug reports and skill
requests, plus a PR template with a skill validation checklist.

**Rationale**: Templates lower the barrier for contribution
(Principle V) and standardize the information maintainers need to
review submissions. The PR template specifically includes a checklist
aligned with the Constitution's Skill Structure Standards.

**Alternatives considered**:
- No templates: Rejected — leads to inconsistent submissions.
- Complex multi-step templates: Rejected — over-engineered for a
  content repository.
