---
name: analyze-and-save
description: One-command orchestrator that fully indexes a code repository into the DevVault Obsidian vault. Use whenever the user wants to index a project, save a repo to DevVault, analyze and save a codebase to Obsidian, or says "analyze and save [repo]", "index [project] to obsidian", "add [repo] to devvault". Primary DevVault indexing skill — prefer over running analyze-project + analyze-feature + save-to-vault separately.
---

# Analyze and Save (Codex)

Index a full repository into the DevVault vault in one command. Two phases with a hard approval gate between them.

> **Note:** This is the Codex version. Phase 2 runs features sequentially (Codex cannot spawn parallel subagents). For parallel execution use the Claude Code version.

## Inputs

- **repo path** — absolute path to the repository (required)
- **ProjectName** — vault name (optional; default to repo directory name)

---

## Phase 1: Map the Project

Read ≤3 files: README, root config, top-level dir listing.

Output:

**Project overview** — 2–3 sentences: what it does, who it's for, core approach.

**Feature candidate table:**
```
| # | Feature | Slug | Description | Key files (hint) |
|---|---------|------|-------------|-----------------|
| 1 | Auth | auth | ... | backend/app/dependencies.py |
```

Aim for 3–6 features. A good feature: self-contained, clear boundary, 1–5 files, meaningful 6 months later.

**Then stop and ask:**
> "Here's what I found. Approve this feature list to start indexing, or tell me what to change."

Do not proceed to Phase 2 until user approves. No vault writes before this gate.

---

## Phase 2: Index (after approval)

### 2.1 — MCP Health Check

Read `Projects/status.md` via obsidian MCP. If unreachable, stop:
> **MCP unreachable.** Ensure Obsidian is running with Local REST API plugin enabled.

### 2.2 — Register Task

Append to `Projects/status.md`:
```
| <ProjectName> | analyze-and-save | IN PROGRESS | <ISO timestamp> |
```

### 2.3 — Index Features (sequential)

For each approved feature, one at a time:

1. List repo top-level. Scan 1–2 entry files to locate feature.
2. Read ≤5 files (entry point, core logic, schema, config, tests).
3. Write PRD 5.3 feature note (see schema below).
4. Write to vault via MCP: draft at `Projects/<ProjectName>/<slug>_draft.md` → rename to `Projects/<ProjectName>/<slug>.md`.
5. Report `✓ <slug>` or `✗ <slug>: <error>`.

**PRD 5.3 schema:**
```markdown
---
project: <ProjectName>
feature: <feature-slug>
type: implementation
files:
  - <path/to/file>
tags: [<tag1>, <tag2>]
last_indexed: <YYYY-MM-DD>
---

# <Feature Name> — <ProjectName>

## What it does
One paragraph.

## Key Components
### `<Class>` / `<file>`
**File:** `<path>`
| Function / Class | Responsibility |
|-----------------|----------------|
| `fn(args)` | What it does |

## Workflow
```
Step 1 → Step 2 → result
```

## Reuse Notes
- Copy as-is: ...
- **Gotcha:** Specific non-obvious trap
- Deps/env vars required

## Adaptation Checklist
- [ ] Copy X from Y
- [ ] Replace Z
- [ ] Wire A
```

### 2.4 — Write Project Index

After all features done, write PRD 5.2 project index:
```markdown
---
project: <ProjectName>
stack: [<Framework>, <Language>]
repo: <repo-path>
last_indexed: <YYYY-MM-DD>
---

# <ProjectName>

## Summary
2 paragraphs synthesizing the project.

## Features
- [[Projects/<ProjectName>/<slug>|<Feature Name>]] — description

## Architecture Notes
How pieces connect, key design decisions.
```

Write via MCP: draft `Projects/<ProjectName>/<ProjectName>_draft.md` → rename to `Projects/<ProjectName>/<ProjectName>.md`.

### 2.5 — Update Index Files

**projects.md** — append row:
```
| [[Projects/<ProjectName>/<ProjectName>\|<ProjectName>]] | <stack> | <N features> | <YYYY-MM-DD> |
```

**status.md** — replace IN PROGRESS row with DONE.

### 2.6 — Print Summary

```
## analyze-and-save complete — <ProjectName>

Features: N/N indexed
✓ slug-1  ✓ slug-2  ✗ slug-3: <error if any>

Project index: Projects/<ProjectName>/<ProjectName>.md ✓
projects.md: row added ✓
status.md: DONE ✓
```

---

## Constraints

- Phase 2 only after user approval
- All vault writes via obsidian MCP only — never Write tool
- Draft → rename mandatory for all vault files
- Abort at 2.1 if MCP unreachable
