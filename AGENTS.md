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
**Purpose:** Scans a code repository and produces structured knowledge-base note drafts (in-conversation). Read-only — no disk writes.

**Invoke:**
```
/analyze-project /path/to/repo
```

**Output:** One project index draft (PRD 5.2 schema) + 3–6 feature note drafts (PRD 5.3 schema), all in-conversation Markdown. Feed output into `save-to-vault` to persist.

**When to use:** Before indexing a new project, or when you need structured feature extraction from a codebase.

---

### `save-to-vault`
**File:** `.claude/skills/save-to-vault/SKILL.md`
**Purpose:** Saves `analyze-project` note drafts to the Obsidian vault via MCP. Enforces draft→rename safety pattern. Updates `projects.md` and `status.md`.

**Invoke:**
```
/save-to-vault ProjectName
```
(Requires analyze-project note drafts already in conversation.)

**Behavior:**
1. Verifies MCP connection — aborts if unreachable
2. Registers task in `Projects/status.md` (table row)
3. Writes each feature note as `<slug>_draft.md`, renames to `<slug>.md` on success
4. Writes project file as `<ProjectName>.md` (not `index.md`)
5. Updates `Projects/projects.md` with new project row
6. Marks task DONE in `status.md`

**When to use:** After `analyze-project` produces drafts, to persist them to the vault.

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
/retrieve-feature IWantJob auth-byok /home/ikktaa/app/IWantJobPrivate
```

**Output:** Note summary + key components table + raw code excerpt + adaptation plan (copy-as-is / must-change / gotchas / effort estimate).

**When to use:** When starting a new project and wanting to reuse a feature from a past project indexed in the vault.

---

## Typical Workflow

```
New project to index:
  /analyze-project /path/to/repo
  → (review drafts in conversation)
  /save-to-vault ProjectName

Reuse a feature in a new project:
  /retrieve-feature ProjectName feature-name
  → (read adaptation plan, copy files)
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
