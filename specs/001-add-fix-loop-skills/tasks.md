# Tasks: Add Fix-Loop Skills and Public Repository Setup

**Input**: Design documents from `/specs/001-add-fix-loop-skills/`
**Prerequisites**: plan.md (required), spec.md (required), research.md, data-model.md, quickstart.md

**Tests**: No automated tests requested. Validation is manual via `npx skills add --list` and quickstart walkthrough.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- Content-only repository: `skills/` for skill content, root for documentation
- No src/ or tests/ directories

---

## Phase 1: Setup

**Purpose**: Repository initialization and directory structure

- [x] T001 Create .gitignore at repository root with exclusions for .DS_Store, node_modules/, .env, .env.local, *.log, and editor configs (.vscode/, .idea/)
- [x] T002 Create skills/ directory with subdirectories skills/coderabbit-fix-loop/ and skills/deepsource-fix-loop/

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: No foundational blocking tasks for this content-only project. All user stories can begin immediately after Phase 1 setup.

**Checkpoint**: Directory structure ready — user story implementation can begin.

---

## Phase 3: User Story 1 - Install a Skill into a Project (Priority: P1) MVP

**Goal**: Both skills exist as valid SKILL.md files that the vercel-labs/skills CLI can discover and install.

**Independent Test**: Run `npx skills add . --list` from repo root and verify both skills appear with correct names and descriptions.

### Implementation for User Story 1

- [x] T003 [P] [US1] Create skills/coderabbit-fix-loop/SKILL.md with YAML frontmatter (name: coderabbit-fix-loop, description) and the full skill instructions from the user-provided content. Ensure the frontmatter name matches the directory name.
- [x] T004 [P] [US1] Create skills/deepsource-fix-loop/SKILL.md with YAML frontmatter (name: deepsource-fix-loop, description) and the full skill instructions from the user-provided content. Ensure the frontmatter name matches the directory name.

**Checkpoint**: Both skills are installable. `npx skills add . --list` shows coderabbit-fix-loop and deepsource-fix-loop.

---

## Phase 4: User Story 2 - Discover Available Skills (Priority: P2)

**Goal**: The README serves as the primary discovery mechanism with a skill catalog, install commands, and project description.

**Independent Test**: View README.md on GitHub (or locally) and verify it contains: project description, skill catalog with names/descriptions/prerequisites, copy-pasteable install commands within the first screenful.

### Implementation for User Story 2

- [x] T005 [US2] Rewrite README.md with: project title and description, quick-start install command (one-liner), skill catalog table listing both skills with name/description/prerequisites/install command, link to vercel-labs/skills for CLI documentation, and Apache 2.0 license badge

**Checkpoint**: README provides full discovery experience. A developer can find and install any skill from the README alone.

---

## Phase 5: User Story 3 - Contribute a New Skill (Priority: P3)

**Goal**: Contributors can follow a clear guide to author and submit a new skill, with PR templates that enforce quality standards.

**Independent Test**: Follow the CONTRIBUTING.md guide to create a dummy `skills/test-skill/SKILL.md`, verify it matches the required format, and confirm the PR template includes a validation checklist.

### Implementation for User Story 3

- [x] T006 [US3] Create CONTRIBUTING.md at repository root with: welcome message, skill directory structure convention (skills/<name>/SKILL.md), YAML frontmatter format (name, description fields), naming rules (lowercase, hyphen-separated, name must match directory), a complete SKILL.md template example, PR submission process, and validation checklist for self-review
- [x] T007 [P] [US3] Create .github/PULL_REQUEST_TEMPLATE.md with: description section, skill validation checklist (frontmatter valid, name matches directory, description present, content self-contained), and link to CONTRIBUTING.md
- [x] T008 [P] [US3] Create .github/ISSUE_TEMPLATE/bug-report.md with: skill name field, expected vs actual behavior, steps to reproduce, and environment details (agent, OS)
- [x] T009 [P] [US3] Create .github/ISSUE_TEMPLATE/skill-request.md with: proposed skill name, description of what it does, target code analysis tool, and use case
- [x] T010 [P] [US3] Create .github/ISSUE_TEMPLATE/config.yml with blank_issues_enabled: true and links to discussions if applicable

**Checkpoint**: A contributor can follow the guide, create a valid skill, and submit a PR with the correct template.

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Open-source scaffolding and final validation

- [x] T011 [P] Create CODE_OF_CONDUCT.md at repository root using the Contributor Covenant v2.1 adapted for the project
- [x] T012 [P] Create SECURITY.md at repository root with: supported versions, how to report vulnerabilities privately (email or GitHub Security Advisories), and expected response timeline
- [x] T013 [P] Create AGENTS.md at repository root with: project overview (skill content repository), directory structure, how to add/modify skills, SKILL.md format reference, and key conventions from CLAUDE.md
- [x] T014 Update CLAUDE.md at repository root with final project structure reflecting all created files
- [x] T015 Validate skill discovery by running `npx skills add . --list` from repository root and confirming both skills appear
- [x] T016 Run quickstart.md validation: test install command, verify skill appears in target agent directory

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — start immediately
- **User Stories (Phase 3-5)**: Depend on Phase 1 (directory structure)
  - US1 (skills) can start immediately after Phase 1
  - US2 (README) can start immediately after Phase 1, but should reference skill names from US1
  - US3 (contributing) can start immediately after Phase 1
- **Polish (Phase 6)**: Can start after Phase 1, but T014-T016 depend on all user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: No dependencies on other stories. Just needs skills/ directories from Phase 1.
- **User Story 2 (P2)**: References skill names and descriptions from US1 SKILL.md files. Can start after T003/T004.
- **User Story 3 (P3)**: References directory convention established in US1. Can start after T003/T004 (to have a real example).

### Parallel Opportunities

- T003 and T004 (both skills) can run in parallel
- T007, T008, T009, T010 (GitHub templates) can all run in parallel
- T011, T012, T013 (cross-cutting docs) can all run in parallel
- US1, US3 can run in parallel (US2 should follow US1 to reference real skill content)

---

## Parallel Example: User Story 1

```bash
# Launch both skill files together:
Task: "Create skills/coderabbit-fix-loop/SKILL.md"
Task: "Create skills/deepsource-fix-loop/SKILL.md"
```

## Parallel Example: User Story 3

```bash
# Launch all GitHub templates together (after CONTRIBUTING.md):
Task: "Create .github/PULL_REQUEST_TEMPLATE.md"
Task: "Create .github/ISSUE_TEMPLATE/bug-report.md"
Task: "Create .github/ISSUE_TEMPLATE/skill-request.md"
Task: "Create .github/ISSUE_TEMPLATE/config.yml"
```

## Parallel Example: Polish

```bash
# Launch all cross-cutting docs together:
Task: "Create CODE_OF_CONDUCT.md"
Task: "Create SECURITY.md"
Task: "Create AGENTS.md"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup (T001-T002)
2. Complete Phase 3: User Story 1 (T003-T004)
3. **STOP and VALIDATE**: Run `npx skills add . --list`
4. Skills are installable — MVP done

### Incremental Delivery

1. Phase 1 (Setup) → Directory structure ready
2. Phase 3 (US1: Skills) → Both skills installable (MVP!)
3. Phase 4 (US2: README) → Discovery experience complete
4. Phase 5 (US3: Contributing) → Contributor experience complete
5. Phase 6 (Polish) → Full open-source scaffolding

### Parallel Execution Strategy

With subagents available:

1. Complete T001-T002 (Setup) sequentially
2. Launch T003 + T004 in parallel (both skills)
3. After T003/T004 complete: Launch T005 (README) + T006 (CONTRIBUTING.md) in parallel
4. After T006: Launch T007 + T008 + T009 + T010 in parallel (GitHub templates)
5. Launch T011 + T012 + T013 in parallel (cross-cutting docs)
6. T014-T016 sequentially (depend on everything above)

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Each user story is independently completable and testable
- Commit after each phase or logical group
- No automated tests — validation is manual via skills CLI
- The user-provided skill content (coderabbit-fix-loop, deepsource-fix-loop) must be used verbatim as the SKILL.md body, with YAML frontmatter prepended
