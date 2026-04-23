# DevVault — Agent Skills

This file documents the DevVault skills available to AI agents (Claude Code, Codex CLI, and others).

## Prerequisites

- **Obsidian** must be running with the **Local REST API** plugin enabled
- **MCP config:** `~/.claude.json` must have the `obsidian` entry (see `docs/mcp-setup.md`)
- **MCP URL:** `https://127.0.0.1:27124/` (HTTPS, port 27124)
- **Vault path:** `/home/ikktaa/Documents/DevVault`

---

## Skills

### `analyze-project`
**File:** `.claude/skills/analyze-project/SKILL.md`
**Purpose:** Scans a code repository and produces a project overview + feature candidate list (in-conversation). Read-only — no disk writes. Does NOT produce full feature notes — use `analyze-feature` for that.

**Invoke:**
```
/analyze-project /path/to/repo
```

**Output:** One project index draft (PRD 5.2 schema) + feature candidate list (names + 1-line descriptions). Feed each candidate into `analyze-feature`.

**When to use:** First step when indexing a new project. Gets the lay of the land without deep-diving any feature.

---

### `analyze-feature`
**File:** `.claude/skills/analyze-feature/SKILL.md`
**Purpose:** Deep-dives into ONE specific feature and produces a single feature note draft (PRD 5.3 schema, in-conversation). Reads up to 5 source files. Feed output into `save-to-vault`.

**Invoke:**
```
/analyze-feature ProjectName feature-slug /path/to/repo
```

**Examples:**
```
/analyze-feature IWantJob llm-routing /home/ikktaa/app/IWantJobPrivate
/analyze-feature IWantJob auth /home/ikktaa/app/IWantJobPrivate
/analyze-feature TA ai-chain-of-thought-pipeline /home/ikktaa/app/TA
```

**Output:** One feature note with YAML frontmatter, What it does, Key Components table, Workflow, Reuse Notes (with specific Gotchas), and Adaptation Checklist.

**When to use:** After `analyze-project` produces a feature candidate list. Run once per feature you want to index.

---

**Codex procedure for `analyze-feature`** (no `.claude/skills/` in Codex — follow inline):

1. Read up to 2 files to locate the feature in the repo
2. Read up to 5 files (entry point, core logic, schema/model, config/wiring, tests)
3. Stop at 5 — mark unclear parts `(unclear — needs deeper read)` rather than reading more
4. Output a fenced markdown block with this structure:

```markdown
---
project: <ProjectName>
feature: <feature-slug>
type: implementation
files:
  - <path/to/file1>
  - <path/to/file2>
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
\`\`\`
Step 1: ...
  → Step 2: ...
  → result
\`\`\`

## Reuse Notes
- What to copy as-is
- **Gotcha:** Non-obvious trap (be specific — vague gotchas are useless)
- External deps, env vars, config required

## Adaptation Checklist
- [ ] Copy X from Y
- [ ] Replace Z with your own
- [ ] Wire dependency A
- [ ] Watch out for gotcha B
```

Quality bar: someone reading the note must be able to answer without opening source code — what does it do, which files to copy, what are the traps, what to change.

---

### `save-to-vault`
**File:** `.claude/skills/save-to-vault/SKILL.md`
**Purpose:** Saves note drafts (project index + feature notes) to the Obsidian vault via MCP. Enforces draft→rename safety pattern. Updates `projects.md` and `status.md`.

**Invoke:**
```
/save-to-vault ProjectName
```
(Requires project index + feature note drafts already in conversation.)

**Behavior:**
1. Verifies MCP connection — aborts if unreachable
2. Registers task in `Projects/status.md` (table row)
3. Writes each feature note as `<slug>_draft.md`, renames to `<slug>.md` on success
4. Writes project file as `<ProjectName>.md` (not `index.md`)
5. Updates `Projects/projects.md` with new project row
6. Marks task DONE in `status.md`

**When to use:** After `analyze-project` + `analyze-feature` produce all drafts, to persist them to the vault.

---

### `retrieve-feature`
**File:** `.claude/skills/retrieve-feature/SKILL.md`
**Purpose:** Retrieves a vault feature note via MCP, reads the referenced raw source file, and produces a structured adaptation plan for reuse in a new project. Read-only.

**Invoke:**
```
/retrieve-feature ProjectName feature-name
/retrieve-feature ProjectName feature-name /path/to/repo   ← optional repo path
```

**Examples:**
```
/retrieve-feature IWantJob llm-routing
/retrieve-feature TA ai-chain-of-thought-pipeline
/retrieve-feature IWantJob auth /home/ikktaa/app/IWantJobPrivate
```

**Output:** Note summary + key components table + raw code excerpt + adaptation plan (copy-as-is / must-change / gotchas / effort estimate).

**When to use:** When starting a new project and wanting to reuse a feature from a past project indexed in the vault.

---

## Typical Workflow

**Index a new project (full pipeline):**
```
/analyze-project /path/to/repo
  → review project index + feature candidate list

/analyze-feature ProjectName feature-1 /path/to/repo
/analyze-feature ProjectName feature-2 /path/to/repo
/analyze-feature ProjectName feature-3 /path/to/repo
  → review each feature note draft

/save-to-vault ProjectName
  → persists all drafts to vault
```

**Reuse a feature in a new project:**
```
/retrieve-feature ProjectName feature-name
  → read adaptation plan, copy what fits
```

---

## Vault Structure

```
/home/ikktaa/Documents/DevVault/
├── Projects/
│   ├── projects.md            ← master project list
│   ├── status.md              ← multi-agent coordination
│   ├── IWantJob/
│   │   ├── IWantJob.md        ← project index
│   │   ├── llm-routing.md
│   │   └── ...
│   └── <ProjectName>/
│       ├── <ProjectName>.md   ← project index
│       └── <feature-slug>.md
└── Templates/
    ├── project-index.md
    └── feature-note.md
```
