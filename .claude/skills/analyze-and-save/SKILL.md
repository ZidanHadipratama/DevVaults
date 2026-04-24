---
name: analyze-and-save
description: One-command orchestrator that fully indexes a code repository into the DevVault Obsidian vault. Use this skill whenever the user wants to index a project, save a repo to DevVault, analyze and save a codebase to Obsidian, or says things like "analyze and save [repo]", "index [project] to obsidian", "add [repo] to devvault", "index this project", "save this codebase to the vault". This is the primary DevVault indexing skill — prefer it over running analyze-project + analyze-feature + save-to-vault separately unless the user explicitly wants granular control.
---

# Analyze and Save

You are indexing a full code repository into the DevVault Obsidian vault in one command. The goal is to produce reusable, high-quality vault notes with minimal user effort — one approval gate, then everything runs.

The flow has two phases with a hard stop between them: first you map the project and show it to the user, then (after approval) you deep-dive each feature and write everything to the vault in parallel.

## Inputs

- **repo path** — absolute path to the repository (required)
- **ProjectName** — vault project name (optional; default to repo directory name)

---

## Phase 1: Map the Project

Read these files to understand the project (≤3 reads total):
1. `README.md` or `docs/` overview
2. Root config (`package.json`, `pyproject.toml`, `requirements.txt`, `Cargo.toml`, etc.)
3. Top-level directory listing

From this, produce:

**1. Project overview** — 2–3 sentences: what it does, who it's for, the core technical approach.

**2. Feature candidate table** — 3–6 reusable features you spotted:

```
| # | Feature | Slug | Description | Key files (hint) |
|---|---------|------|-------------|-----------------|
| 1 | LLM Routing | llm-routing | Per-request provider selection via headers | backend/app/services/ai_service.py |
| 2 | Auth | auth | Stateless header-based Supabase auth | backend/app/dependencies.py |
```

A good feature: self-contained capability, clear boundary, spans 1–5 files, meaningful to someone 6 months later.

**Then stop and ask:**

> "Here's what I found. Approve this feature list to start indexing, or tell me what to change (add, remove, rename, split features)."

Do not proceed to Phase 2 until the user replies with approval or modifications. No vault writes before this gate.

---

## Phase 2: Index (runs after approval)

### Step 2.1 — MCP health check

Verify Obsidian MCP is reachable by reading `Projects/status.md`. If unreachable, stop:

> **MCP unreachable.** Ensure Obsidian is running with the Local REST API plugin enabled. No vault writes made.

### Step 2.2 — Register task

Append to `Projects/status.md`:
```
| <ProjectName> | analyze-and-save | IN PROGRESS | <ISO timestamp> |
```

### Step 2.3 — Spawn parallel subagents (one per feature)

For each approved feature, spawn a subagent with this task prompt (fill in the placeholders):

---
**Subagent task:**

You are indexing one feature from a code repository into an Obsidian vault.

**Inputs:**
- ProjectName: `<ProjectName>`
- Feature name: `<Feature Name>`
- Feature slug: `<feature-slug>`
- Repo path: `<repo-path>`
- Vault write path: `Projects/<ProjectName>/<feature-slug>.md`
- MCP base URL: `https://127.0.0.1:27124/`

**Step 1 — Locate the feature**
List the repo's top-level directory. Scan 1–2 entry point files to confirm the feature exists and get your bearings.

**Step 2 — Read up to 5 files**
Choose the ≤5 files that best explain this feature: entry point, core logic, key schema/model, config/wiring, tests. Stop earlier if picture is clear. Mark unclear parts `(unclear — needs deeper read)` rather than reading more.

**Step 3 — Produce the feature note**
Write a PRD 5.3 feature note with this exact structure:
```markdown
---
project: <ProjectName>
feature: <feature-slug>
type: implementation
files:
  - <path/to/file1>
tags: [<tag1>, <tag2>]
last_indexed: <YYYY-MM-DD>
---

# <Feature Name> — <ProjectName>

## What it does
One paragraph. Problem solved, output produced, key behavior.

## Key Components
### `<ClassName>` / `<filename>`
**File:** `<path>`
| Function / Class | Responsibility |
|-----------------|----------------|
| `fn(args)` | What it does |

## Workflow
```
Step 1: ...
  → Step 2: ...
  → result
```

## Reuse Notes
- What to copy as-is
- **Gotcha:** Specific non-obvious trap
- External deps, env vars, config required

## Adaptation Checklist
- [ ] Copy X from Y
- [ ] Replace Z with your own
- [ ] Wire dependency A
```

Quality check: can someone answer these without opening source code?
1. What does this feature do?
2. Which files to copy?
3. What are the non-obvious traps?
4. What to change for my project?

**Step 4 — Write to vault via MCP**
Use the obsidian MCP tool to write the note:
1. Write to `Projects/<ProjectName>/<feature-slug>_draft.md` first
2. On success, write final content to `Projects/<ProjectName>/<feature-slug>.md`
3. If write fails, report the error — do not retry more than once

Report back: `✓ <feature-slug>` on success or `✗ <feature-slug>: <error>` on failure.

---

Spawn all subagents in the same turn so they run in parallel.

### Step 2.4 — After all subagents complete

Collect results. Then:

**Write project index** to vault:
```markdown
---
project: <ProjectName>
stack: [<Framework>, <Language>, ...]
repo: <repo-path>
last_indexed: <YYYY-MM-DD>
---

# <ProjectName>

## Summary
<2 paragraphs synthesizing the project — what it does, who it's for, core approach>

## Features
- [[Projects/<ProjectName>/<slug>|<Feature Name>]] — one-line description
(one line per feature)

## Architecture Notes
<How pieces connect, key design decisions, what would surprise someone picking it up cold>
```

Write to vault: draft at `Projects/<ProjectName>/<ProjectName>_draft.md` → rename to `Projects/<ProjectName>/<ProjectName>.md`.

**Update `Projects/projects.md`** — append row:
```
| [[Projects/<ProjectName>/<ProjectName>\|<ProjectName>]] | <stack> | <feature count> | <today YYYY-MM-DD> |
```

**Mark DONE in `Projects/status.md`** — replace IN PROGRESS row with DONE.

**Print summary:**
```
## analyze-and-save complete — <ProjectName>

Features indexed: N/N
✓ feature-slug-1
✓ feature-slug-2
✗ feature-slug-3: <error if any>

Project index: Projects/<ProjectName>/<ProjectName>.md ✓
projects.md: row added ✓
status.md: DONE ✓
```

---

## Constraints

- Phase 2 only runs after user explicitly approves the feature list
- All vault writes go through obsidian MCP — never use the Write tool for vault files
- Draft → rename is mandatory for all vault writes (feature notes and project index)
- Subagents read source files directly from the filesystem (Read tool on repo path)
- If MCP is unreachable at Step 2.1, abort with clear message — no partial state
