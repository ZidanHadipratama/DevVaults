# DevVault

Personal codebase knowledge management system for AI agents. Indexes past projects as structured Markdown in Obsidian so that agents (Claude Code, Codex, others) can query via MCP to retrieve exact feature references and raw code — every new project starts with full institutional memory, not from zero.

---

## How It Works

```
Index:
  /analyze-and-save /path/to/repo
        ↓
  project overview + feature list (review + approve)
        ↓
  parallel subagents: one per feature → vault via MCP
        ↓
  project indexed ✓

Retrieve:
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

```
/retrieve-feature IWantJob llm-routing
```

If MCP is connected and IWantJob is indexed, you'll see a note summary + adaptation plan. If not indexed yet, run `/analyze-and-save` on any repo first.

---

## Skills

### `/analyze-and-save` — primary indexing skill

One command to fully index a project into the vault. Shows you the project map and feature list, waits for your approval, then indexes all features in parallel via subagents.

```
/analyze-and-save /path/to/repo
/analyze-and-save /path/to/repo ProjectName
```

**Flow:**
1. Scans repo (README + config + dir listing)
2. Shows project overview + feature candidate table
3. Waits for your approval or modifications
4. Spawns parallel subagents — one per feature — each reads ≤5 files and writes to vault
5. Writes project index, updates `projects.md` and `status.md`

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
/retrieve-feature IWantJob auth /home/user/app/NewProject
```

**Output:** Note summary + key components table + raw code excerpt + adaptation plan (copy-as-is / must-change / gotchas / effort estimate).

---

## Advanced / Granular Control

Use these when you need step-by-step control instead of the full pipeline:

### `/analyze-project`

Lightweight scan — produces project overview + feature candidate list only. No deep feature reads, no vault writes.

```
/analyze-project /path/to/repo
```

### `/analyze-feature`

Deep-dives into one specific feature and produces a single PRD 5.3 feature note in-conversation.

```
/analyze-feature ProjectName feature-slug /path/to/repo
```

### `/save-to-vault`

Persists note drafts already in conversation to the vault via MCP.

```
/save-to-vault ProjectName
```

---

## Typical Workflows

**Index a new project (one command):**

```
/analyze-and-save ~/app/MyProject
→ review feature list, approve
→ all features indexed to vault automatically
```

**Index selectively (granular):**

```
/analyze-project ~/app/MyProject
→ review feature candidates

/analyze-feature MyProject auth ~/app/MyProject
/analyze-feature MyProject llm-routing ~/app/MyProject

/save-to-vault MyProject
```

**Reuse a feature in a new project:**

```
/retrieve-feature MyProject some-feature
→ read adaptation plan, copy what fits
```

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

## Roadmap

| Phase | Goal | Status |
|-------|------|--------|
| 1 — Foundation | Vault structure, MCP setup, IWantJob indexed | complete |
| 2 — Skills | analyze → save → retrieve pipeline | complete |
| 3 — Multi-Agent | Claude + Codex on same vault simultaneously | deferred |
| 4 — Docs & Release | README, setup guide, GitHub release | complete |

---

## Design Principles

- **Plain text first** — all notes are Markdown, readable without tooling
- **Notes are indexes, not code** — vault stores references and structured summaries, not source
- **Agent-agnostic** — any agent with MCP access can query the vault
- **Privacy-first** — everything runs locally, no cloud sync
