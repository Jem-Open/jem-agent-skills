# Data Model: Add Fix-Loop Skills and Public Repository Setup

**Date**: 2026-03-09
**Feature**: 001-add-fix-loop-skills

## Entities

### Skill

A self-contained SKILL.md file providing AI agent instructions.

**Identity**: Unique by `name` field in YAML frontmatter, which must
match the parent directory name under `skills/`.

**Fields**:
- `name` (string, required): Unique identifier. Lowercase,
  hyphen-separated. Must match directory name.
- `description` (string, required): One-line summary of what the
  skill does and when to use it.
- `metadata.internal` (boolean, optional): If true, hidden from
  normal discovery unless `INSTALL_INTERNAL_SKILLS=1` is set.
- Body content (markdown): Complete, self-contained instructions
  for the AI agent.

**Validation rules**:
- `name` must be lowercase, contain only `[a-z0-9-]`, and match
  the directory name.
- `description` must be non-empty and under 500 characters.
- YAML frontmatter must be valid (delimited by `---` lines).
- Body content must be non-empty.

**Lifecycle**:
- Created: Contributor adds `skills/<name>/SKILL.md` via PR.
- Published: PR merged to main; discoverable by skills CLI.
- Updated: Contributor modifies SKILL.md content via PR.
- Deprecated: SKILL.md removed or `metadata.internal: true` set.

### Skills Directory

The `skills/` directory at repository root.

**Structure invariant**: Each immediate child is a directory named
after the skill it contains, with exactly one `SKILL.md` file inside.

```
skills/
├── <skill-name>/
│   └── SKILL.md
```

**Validation rules**:
- No files at `skills/` level (only directories).
- Each skill directory must contain a `SKILL.md` file.
- Directory name must match the `name` field in SKILL.md frontmatter.

## Relationships

```
Skills Directory (1) ──contains──> (N) Skill
```

No relationships between skills (Principle I: Self-Contained).
No external data storage. All state is in the git repository.
