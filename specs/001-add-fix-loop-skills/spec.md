# Feature Specification: Add Fix-Loop Skills and Public Repository Setup

**Feature Branch**: `001-add-fix-loop-skills`
**Created**: 2026-03-09
**Status**: Draft
**Input**: User description: "Add two fix-loop skills (coderabbit-fix-loop, deepsource-fix-loop) for public sharing, update README, and make the codebase useful for others"

## Clarifications

### Session 2026-03-09

- Q: Does MVP scope include building a CLI tool for installation? → A: No. Distribution uses the existing vercel-labs/skills CLI (`npx skills add`). This repo only provides skill content files in the format expected by that CLI.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Install a Skill into a Project (Priority: P1)

A developer discovers jem-agent-skills and wants to add the
coderabbit-fix-loop skill to their existing project so their AI
coding agent can automatically fix CodeRabbit review findings.
They use the existing `npx skills` CLI from vercel-labs/skills
to install it.

**Why this priority**: This is the core value proposition. If a
user cannot install a single skill and have it work, nothing
else matters.

**Independent Test**: Can be fully tested by running
`npx skills add jem-open/jem-agent-skills@coderabbit-fix-loop`
in a clean project directory and verifying the SKILL.md file
appears in the agent's skill directory.

**Acceptance Scenarios**:

1. **Given** a project without any jem-agent-skills installed,
   **When** the user runs
   `npx skills add jem-open/jem-agent-skills@coderabbit-fix-loop`,
   **Then** the SKILL.md is symlinked or copied into the
   target agent's skill directory and is immediately usable.

2. **Given** a project that already has coderabbit-fix-loop
   installed,
   **When** the user installs deepsource-fix-loop from the
   same repository,
   **Then** the second skill is added without modifying or
   breaking the first skill.

3. **Given** a developer wants to see what skills this repo
   offers before installing,
   **When** they run
   `npx skills add jem-open/jem-agent-skills --list`,
   **Then** they see both skills listed with names and
   descriptions.

---

### User Story 2 - Discover Available Skills (Priority: P2)

A developer visits the GitHub repository to browse what skills
are available before deciding which to install.

**Why this priority**: Discovery drives adoption. Without
knowing what is available, users cannot install skills.

**Independent Test**: Can be tested by viewing the README on
GitHub and verifying that both skills appear with descriptions,
prerequisites, and install commands.

**Acceptance Scenarios**:

1. **Given** a developer visits the repository README on GitHub,
   **When** they look for available skills,
   **Then** they see a catalog listing each skill with its name,
   description, prerequisites, and a copy-pasteable install
   command.

2. **Given** a developer wants quick install instructions,
   **When** they read the README,
   **Then** they find a one-liner install command within the
   first screenful of content.

---

### User Story 3 - Contribute a New Skill (Priority: P3)

A developer wants to create and contribute their own skill
(e.g., for a different code analysis tool) to the repository.

**Why this priority**: Community contributions grow the
ecosystem, but this is secondary to the core install and
discover flows.

**Independent Test**: Can be tested by following the
contribution guide to create a new skill directory with a
SKILL.md file, verifying it follows the required format, and
confirming it appears when running `--list`.

**Acceptance Scenarios**:

1. **Given** a developer wants to contribute a new skill,
   **When** they follow the contribution guide in the README,
   **Then** they can create a new skill directory under
   `skills/` containing a properly formatted SKILL.md file.

2. **Given** a contributor has authored a new skill,
   **When** they submit a pull request,
   **Then** the skill follows the required directory structure
   (`skills/<skill-name>/SKILL.md`) and YAML frontmatter
   format (name, description fields).

---

### Edge Cases

- What happens when a user tries to install a skill that does
  not exist? The `npx skills` CLI handles this with its own
  error messaging; our responsibility is ensuring all published
  skills are discoverable.
- What happens when a user tries to install a skill that is
  already installed? The `npx skills` CLI handles
  deduplication; no action needed on our side.
- What happens when the target project has no recognized AI
  agent configuration directory? The `npx skills` CLI creates
  the required directory structure automatically.
- What happens when a contributed skill has malformed
  frontmatter? Pull request review MUST catch this; a
  validation checklist in the contribution guide helps
  contributors self-check.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Repository MUST contain the coderabbit-fix-loop
  skill as `skills/coderabbit-fix-loop/SKILL.md` with valid
  YAML frontmatter (name, description) and the full skill
  instructions in markdown.
- **FR-002**: Repository MUST contain the deepsource-fix-loop
  skill as `skills/deepsource-fix-loop/SKILL.md` with valid
  YAML frontmatter (name, description) and the full skill
  instructions in markdown.
- **FR-003**: Each skill MUST be independently installable via
  `npx skills add jem-open/jem-agent-skills@<skill-name>`.
- **FR-004**: Repository README MUST describe the project
  purpose, list available skills with descriptions and
  prerequisites, and provide copy-pasteable install commands.
- **FR-005**: All skills MUST follow the directory convention:
  `skills/<skill-name>/SKILL.md`.
- **FR-006**: Each SKILL.md MUST include YAML frontmatter with
  at minimum `name` and `description` fields, matching the
  vercel-labs/skills format.
- **FR-007**: Repository MUST include a contribution guide
  (in README or CONTRIBUTING.md) explaining the SKILL.md
  format, directory structure, and pull request expectations.
- **FR-008**: Repository README MUST be the primary discovery
  mechanism, listing all skills with install commands.
- **FR-009**: Skill content (the markdown body of SKILL.md)
  MUST be the complete, self-contained instructions that the
  AI agent needs to execute the skill.
- **FR-010**: Repository MUST include an AGENTS.md file at the
  root to provide context for AI agents working on the
  codebase itself.

### Key Entities

- **Skill**: A single SKILL.md file within a named directory
  under `skills/`. Contains YAML frontmatter (name,
  description) and markdown instructions. Each skill is
  self-contained and independently installable.
- **Skills Directory**: The `skills/` directory at repository
  root, containing one subdirectory per skill. This is the
  standard discovery path used by the vercel-labs/skills CLI.
- **Target Project**: The user's existing project where skills
  are installed into by the `npx skills` CLI. The CLI handles
  symlinking or copying SKILL.md into the appropriate agent
  directory.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A new user can install a skill into their project
  in under 1 minute by copying the install command from the
  README.
- **SC-002**: Both skills (coderabbit-fix-loop and
  deepsource-fix-loop) are discoverable via
  `npx skills add jem-open/jem-agent-skills --list`.
- **SC-003**: A contributor can create a new skill by following
  the contribution guide in under 10 minutes.
- **SC-004**: All SKILL.md files have valid YAML frontmatter
  with required fields (name, description).
- **SC-005**: Installing either skill produces a working AI
  agent command that can be invoked immediately.

## Assumptions

- Distribution uses the vercel-labs/skills CLI
  (`npx skills add`). This project does NOT build its own CLI.
- The repository GitHub path is `jem-open/jem-agent-skills`,
  making the install command
  `npx skills add jem-open/jem-agent-skills`.
- Skills target Claude Code as the primary agent (the skill
  instructions reference Claude Code-specific features like
  the Agent tool and subagents), but the vercel-labs/skills
  CLI supports 40+ agents and will install to whichever agent
  the user selects.
- The two provided skill definitions are complete and ready to
  be packaged as SKILL.md files with their existing content.
- The repository is public under Apache 2.0 license.
