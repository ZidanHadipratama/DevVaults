# DevVault

Personal codebase knowledge management system for AI agents. Indexes past projects as structured Markdown in Obsidian so that agents (Claude Code, Codex, others) can query via MCP to retrieve exact feature references and raw code — every new project starts with full institutional memory, not from zero.

---

## How It Works

```
Analyze → Save → Retrieve

/analyze-project /path/to/repo
        ↓
  structured note drafts (in-conversation)
        ↓
/save-to-vault ProjectName
        ↓
  notes persisted to Obsidian vault via MCP
        ↓
/retrieve-feature ProjectName feature-name
        ↓
  note + raw code + adaptation plan
```

All notes are plain Markdown in a local Obsidian vault. No cloud, no database, no sync required.

---

## Prerequisites

| Requirement | Details |
|-------------|---------|
| [Obsidian](https://obsidian.md) | Must be running during vault operations |
| [Local REST API plugin](https://github.com/coddingtonbear/obsidian-local-rest-api) | `coddingtonbear` — enable in Community Plugins |
| [obsidian-mcp-server](https://github.com/cyanheads/obsidian-mcp-server) | MCP bridge (`npx`, no install needed) |
| [Claude Code](https://claude.ai/code) | CLI with `/skill-creator` plugin |

---

## Setup

### 1. Install Obsidian and Local REST API plugin

1. Download and install [Obsidian](https://obsidian.md).
2. Open Obsidian → **Settings → Community Plugins → Browse** → search `Local REST API` by `coddingtonbear` → Install → Enable.
3. Note your API key: **Settings → Local REST API → API Key**.

### 2. Create the vault

Create a vault at `~/Documents/DevVault` (or any path — update `OBSIDIAN_BASE_URL` and vault path accordingly).

Inside the vault, create this folder structure:

```
~/Documents/DevVault/
├── Projects/
│   ├── projects.md
│   └── status.md
└── Templates/
```

### 3. Clone this repo

```bash
git clone git@github.com:ZidanHadipratama/DevVaults.git ~/app/DevVault
```

### 4. Configure MCP in `~/.claude.json`

Add the `obsidian` entry to `mcpServers`:

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["-y", "@cyanheads/obsidian-mcp-server"],
      "env": {
        "OBSIDIAN_API_KEY": "your-api-key-here",
        "OBSIDIAN_BASE_URL": "https://127.0.0.1:27124/"
      }
    }
  }
}
```

Replace `your-api-key-here` with the key from Obsidian's Local REST API settings.

### 5. Install skill-creator

```bash
claude plugins install skill-creator
```

Restart Claude Code after install for the plugin to appear.

### 6. Verify setup

```bash
# In Claude Code, run:
/retrieve-feature IWantJob llm-routing
```

If MCP is connected and IWantJob is indexed, you'll see a note summary + adaptation plan. If not indexed yet, run `/analyze-project` on any repo first.

---

## Skills

### `/analyze-project`

Scans a code repository and produces structured knowledge-base note drafts in-conversation. Read-only — no disk or vault writes.

```
/analyze-project /path/to/repo
```

**Output:** One project index draft + 3–6 feature note drafts in Markdown. Feed into `/save-to-vault` to persist.

---

### `/save-to-vault`

Saves `/analyze-project` note drafts to the Obsidian vault via MCP. Enforces draft→rename safety pattern. Updates `projects.md` and `status.md`.

```
/save-to-vault ProjectName
```

Requires analyze-project drafts already in conversation context.

**What it does:**
1. Verifies MCP connection
2. Registers task in `Projects/status.md`
3. Writes each feature note as `<slug>_draft.md`, renames to `<slug>.md` on success
4. Writes project index as `Projects/<ProjectName>/<ProjectName>.md`
5. Updates `Projects/projects.md`
6. Marks task DONE in `status.md`

---

### `/retrieve-feature`

Retrieves a vault feature note via MCP, reads the referenced raw source file, and produces a structured adaptation plan for reuse in a new project. Read-only.

```
/retrieve-feature ProjectName feature-name
/retrieve-feature ProjectName feature-name /path/to/new-repo
```

**Examples:**

```
/retrieve-feature IWantJob llm-routing
/retrieve-feature IWantJob auth-byok /home/user/app/NewProject
```

**Output:** Note summary + key components table + raw code excerpt + adaptation plan (copy-as-is / must-change / gotchas / effort estimate).

---

## Vault Structure

See [AGENTS.md](AGENTS.md) for full vault structure and multi-agent coordination details.

```
~/Documents/DevVault/
├── Projects/
│   ├── projects.md            ← master project list
│   ├── status.md              ← agent coordination (lock table)
│   ├── IWantJob/
│   │   ├── IWantJob.md        ← project index
│   │   ├── llm-routing.md
│   │   └── ...
│   └── <ProjectName>/
│       ├── <ProjectName>.md
│       └── <feature-slug>.md
└── Templates/
    ├── project-index.md
    └── feature-note.md
```

---

## Typical Workflow

**Index a new project:**

```
/analyze-project ~/app/MyProject
→ review drafts in conversation
/save-to-vault MyProject
```

**Reuse a feature in a new project:**

```
/retrieve-feature MyProject some-feature
→ read adaptation plan, copy what fits
```

---

## Roadmap

| Phase | Goal | Status |
|-------|------|--------|
| 1 — Foundation | Vault structure, MCP setup, IWantJob indexed | complete |
| 2 — Skills | analyze → save → retrieve pipeline | complete |
| 3 — Multi-Agent | Claude + Codex on same vault simultaneously | deferred |
| 4 — Docs & Release | README, setup guide, GitHub release | in-progress |

---

## Design Principles

- **Plain text first** — all notes are Markdown, readable without tooling
- **Notes are indexes, not code** — vault stores references and structured summaries, not source
- **Agent-agnostic** — any agent with MCP access can query the vault
- **Privacy-first** — everything runs locally, no cloud sync
