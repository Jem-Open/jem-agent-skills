<!--
Sync Impact Report
==================
- Version change: N/A → 1.0.0
- Modified principles: N/A (initial ratification)
- Added sections:
  - Core Principles (5 principles)
  - Skill Structure Standards
  - Contribution & Quality Workflow
  - Governance
- Removed sections: N/A
- Templates requiring updates:
  - .specify/templates/plan-template.md ✅ (Constitution Check
    section is generic; compatible with these principles)
  - .specify/templates/spec-template.md ✅ (user-story-driven
    format aligns with Principle I self-containment)
  - .specify/templates/tasks-template.md ✅ (phase-based task
    structure is compatible; no updates needed)
- Follow-up TODOs: None
-->

# jem-agent-skills Constitution

## Core Principles

### I. Self-Contained Skills

Every skill MUST be independently installable and functional.
A skill MUST NOT require other skills as prerequisites unless
explicitly declared as a peer dependency. Each skill MUST
include all files, templates, and metadata needed to operate
once installed.

- A single skill added via `npx skills add <name>` MUST work
  without additional manual setup steps.
- Skills MUST declare their own dependencies (if any) in
  structured metadata, never via prose or implied convention.
- Skills MUST NOT import from or reference other skills at
  runtime unless the dependency is declared and resolvable.

### II. Agent-Agnostic by Default

Skills MUST target the broadest possible set of AI coding
agents. Where agent-specific behavior is unavoidable, the
skill MUST isolate agent-specific logic behind a clearly
marked adapter layer.

- Skill content (prompts, templates, instructions) MUST use
  generic terminology unless an agent-specific variant is
  explicitly provided.
- Agent-specific files MUST be namespaced (e.g.,
  `adapters/claude.md`, `adapters/cursor.md`) so removal of
  one adapter does not break the skill.
- The default skill entry point MUST function without any
  agent-specific adapter present.

### III. Convention Over Configuration

Skills MUST follow a standard directory structure, naming
convention, and metadata schema so that the CLI tooling
(`npx skills add`, `npx skills list`, etc.) can discover,
install, and manage skills without bespoke configuration.

- Skill metadata MUST live in a well-known location within
  the skill directory (e.g., `skill.json` or frontmatter).
- File naming MUST be predictable: lowercase, hyphen-separated,
  no spaces or special characters.
- The registry/index MUST be machine-readable and
  auto-generated from skill metadata where possible.

### IV. Zero-Side-Effect Installation

Adding a skill to a project MUST NOT modify existing project
files, overwrite user content, or alter the behavior of
previously installed skills.

- Installation MUST be additive: new files only, no patches
  to existing files unless the user explicitly opts in.
- Uninstalling a skill MUST cleanly remove all files the
  skill introduced, leaving no orphaned configuration.
- Skills MUST NOT register global hooks, modify shell
  profiles, or write outside their designated directory
  without explicit user consent.

### V. Low Barrier to Contribution

The project MUST make it straightforward for anyone to author,
test, and publish a new skill. Clear templates, examples, and
validation tooling MUST be provided.

- A skill authoring template MUST exist so contributors can
  scaffold a new skill with one command.
- Contributed skills MUST pass automated validation (schema
  check, lint, dry-run install) before merge.
- Documentation for authoring a skill MUST be concise and
  accessible from the repository README within two clicks.

## Skill Structure Standards

All skills in this repository MUST conform to the following
structural constraints:

- **Registry**: A central index (machine-readable) MUST list
  every available skill with name, version, description, and
  supported agents.
- **Versioning**: Each skill MUST carry its own semantic
  version. Breaking changes to a skill's interface MUST
  increment the MAJOR version.
- **Testing**: Every skill MUST include at least one
  validation test that confirms correct installation into a
  clean target project (dry-run or integration test).
- **Licensing**: All skills in this repository are licensed
  under Apache 2.0 consistent with the project root LICENSE.
  Third-party skills MUST declare their license in metadata.

## Contribution & Quality Workflow

- **Branching**: Feature branches MUST follow the pattern
  `<type>/<short-description>` (e.g., `feat/add-tdd-skill`).
- **Pull Requests**: Every skill addition or modification MUST
  go through a pull request with at least one review.
- **Validation Gate**: CI MUST run the skill validator on all
  changed skills before a PR can merge. The validator checks
  metadata schema, file structure, naming conventions, and
  dry-run installation.
- **Documentation**: PRs adding a new skill MUST include a
  short description in the skill's own README and update the
  registry index.

## Governance

This constitution is the authoritative source of project
principles. All contributions, reviews, and architectural
decisions MUST be evaluated against these principles.

- **Amendments**: Any change to this constitution MUST be
  proposed via pull request, reviewed by at least one
  maintainer, and accompanied by a migration plan if existing
  skills are affected.
- **Versioning**: This constitution follows semantic
  versioning. MAJOR for principle removals or redefinitions,
  MINOR for new principles or material expansions, PATCH for
  clarifications and typo fixes.
- **Compliance Review**: Maintainers SHOULD audit existing
  skills against this constitution at least once per quarter.
  Non-compliant skills MUST be flagged with an issue and given
  a remediation deadline.

**Version**: 1.0.0 | **Ratified**: 2026-03-09 | **Last Amended**: 2026-03-09
