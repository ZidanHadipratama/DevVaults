# DevVault — Product Requirements Document

**Version:** 1.0  
**Author:** Mochamad Zidan Hadipratama  
**Date:** 2025-04-22  
**Status:** Draft

---

## 1. Overview

### 1.1 Problem Statement

Developers constantly re-solve the same problems across projects. When starting a new project, a developer mentally searches their experience: *"I already built something like this in Project X — let me check how I did it."* They then navigate to that project, read the raw code, and adapt it.

AI coding agents (Claude Code, OpenAI Codex) cannot do this. They have no memory of your past projects. Every new session starts from zero. The developer's accumulated codebase knowledge — the real institutional value — is invisible to the agent.

### 1.2 Solution

**DevVault** is a personal codebase knowledge management system (KMS) for AI agents. It analyzes your past projects, extracts structured knowledge (project summaries, features, workflows, and precise file locations), and stores it in an Obsidian vault. When working on a new project, any AI agent can query the vault via MCP and retrieve the exact reference it needs — then open the raw code directly.

### 1.3 Design Philosophy

- **Plain text first.** All knowledge lives as Markdown in Obsidian. No proprietary formats, no vendor lock-in, Git-trackable.
- **Notes are indexes, not code.** Notes point to raw code via file paths and function names. The agent reads the actual source — not a description of it.
- **Agent-agnostic.** Works with Claude Code, OpenAI Codex CLI, Gemini CLI, or any MCP-compatible agent.
- **Human-readable.** A developer can browse the vault directly in Obsidian without any AI involvement.
- **Privacy-first.** Everything runs locally. No code is sent to the cloud.

---

## 2. Goals

### 2.1 Primary Goals

| # | Goal |
|---|------|
| G1 | Allow any AI agent to retrieve feature-level knowledge from past projects |
| G2 | Enable cross-agent knowledge sharing (Claude + Codex working in parallel on the same vault) |
| G3 | Produce structured, human-readable notes in Obsidian |
| G4 | Point agents to exact raw code (file path + function name), not just descriptions |

### 2.2 Non-Goals (v1.0)

- No semantic/vector search (Zilliz, ChromaDB) — future consideration
- No automatic re-indexing on file change (manual trigger via skill)
- No team/shared vault (single developer, local-first)
- No cloud sync beyond what the user sets up in Obsidian (iCloud, Git)

---

## 3. System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Developer                            │
│  "Hey Claude, use /retrieve-feature to check how I         │
│   handled LLM routing in IWantJob, then implement          │
│   it here."                                                 │
└──────────────────────────┬──────────────────────────────────┘
                           │
          ┌────────────────▼────────────────┐
          │          AI Agent Layer          │
          │   Claude Code  │  OpenAI Codex  │
          │   (primary)    │  (parallel)    │
          └───────┬────────┴────────┬───────┘
                  │                 │
          ┌───────▼─────────────────▼───────┐
          │         Skills Layer            │
          │  /analyze-project               │
          │  /save-to-vault                 │
          │  /retrieve-feature              │
          └───────┬─────────────────┬───────┘
                  │                 │
          ┌───────▼───────┐ ┌───────▼───────┐
          │  Obsidian MCP │ │  Filesystem   │
          │    Server     │ │  (raw code)   │
          └───────┬───────┘ └───────────────┘
                  │
          ┌───────▼───────┐
          │ Obsidian Vault│
          │  (local .md)  │
          └───────────────┘
```

### 3.1 Components

| Component | Description | Technology |
|-----------|-------------|------------|
| **Skills** | Reusable instruction sets for AI agents | `.md` skill files in `CLAUDE.md` / `AGENTS.md` |
| **Obsidian Vault** | Persistent knowledge store | Local Markdown files |
| **Obsidian MCP Server** | Bridge between agents and vault | `obsidian-mcp-server` (community) |
| **Raw Codebase** | Source of truth for actual implementation | Local filesystem |

---

## 4. Skills Specification

Skills are Markdown instruction files that tell an AI agent *exactly* what to do. They are stored in `.claude/skills/` (for Claude Code) and `.openai/skills/` or referenced in `AGENTS.md` (for Codex).

### 4.1 Skill: `/analyze-project`

**Purpose:** Read a codebase and extract structured knowledge.

**Trigger:** `Use /analyze-project on [repo-path]`

**Agent instructions:**
1. Read the root directory structure
2. Read `README.md`, `package.json` / `pyproject.toml`, and any existing docs
3. Identify all discrete features (e.g., auth, LLM routing, payment, file upload)
4. For each feature:
   - Identify all files involved
   - Identify key functions/classes and their responsibility
   - Map the data flow (request → processing → response)
   - Note any external services, environment variables, or config involved
   - Note any reuse warnings (gotchas, breaking assumptions)
5. Output structured Markdown matching the note schema (Section 5)

**Output:** Structured Markdown — passed to `/save-to-vault`

**Runs on:** Claude Code, Codex CLI  
**Reads:** Raw codebase files  
**Writes:** Nothing (read-only skill)

---

### 4.2 Skill: `/save-to-vault`

**Purpose:** Write analyzed project knowledge into Obsidian vault.

**Trigger:** `Use /save-to-vault with [analyze-project output] for project [project-name]`

**Agent instructions:**
1. Connect to Obsidian via MCP server
2. Create or update the project index note at `Projects/[project-name]/index.md`
3. For each feature in the analysis output:
   - Create or update a feature note at `Projects/[project-name]/[feature-name].md`
   - Follow the note schema exactly (Section 5)
4. Update the global index note at `Projects/_index.md` with the project entry
5. Confirm all notes written successfully

**Runs on:** Claude Code, Codex CLI  
**Reads:** Skill output from `/analyze-project`  
**Writes:** Obsidian vault via MCP

---

### 4.3 Skill: `/retrieve-feature`

**Purpose:** Retrieve feature knowledge from vault and prepare for adaptation.

**Trigger:** `Use /retrieve-feature [project-name] [feature-name-or-keyword]`

**Agent instructions:**
1. Connect to Obsidian via MCP server
2. Search vault for `Projects/[project-name]/[feature-name].md`
3. Read the feature note
4. Read the actual raw code files listed in the note's `files` frontmatter
5. Synthesize: summarize what the feature does, what the key code patterns are, and what needs to change to adapt it to the current project
6. Present: (a) the summary, (b) the raw code of each key function, (c) an adaptation plan

**Runs on:** Claude Code, Codex CLI  
**Reads:** Obsidian vault (via MCP) + raw source files  
**Writes:** Nothing (read-only skill)

---

### 4.4 Multi-Agent Coordination

When running Claude Code and Codex in parallel:

- **Claude Code** owns vault writes (`/analyze-project` + `/save-to-vault`)
- **Codex** owns vault reads (`/retrieve-feature`) and implementation
- Both reference the same Obsidian vault via MCP — no duplication
- Agents must not write to the same feature note simultaneously (note-level lock by naming convention: agent appends `_draft` suffix during write, renames on completion)

**Coordination file:** `Projects/_agent-status.md`

```markdown
# Agent Status

| Agent | Task | Status | Note |
|-------|------|--------|------|
| claude-code | analyze IWantJob/llm-routing | done | 2025-04-22 |
| codex | implement llm-routing in NewProject | in-progress | - |
```

Both agents read and update this file via MCP before starting any task.

---

## 5. Note Schema

### 5.1 Global Index (`Projects/_index.md`)

```markdown
# DevVault Index

| Project | Stack | Last Indexed | Features |
|---------|-------|-------------|----------|
| IWantJob | FastAPI, Plasmo, TypeScript, Supabase, LiteLLM | 2025-04-22 | 6 |
| Syntra | FastAPI, React, PostgreSQL | 2025-03-10 | 4 |
| Catty | FastAPI, LangChain, ChromaDB | 2025-02-01 | 3 |
```

---

### 5.2 Project Index (`Projects/[project-name]/index.md`)

```markdown
---
project: IWantJob
stack: [FastAPI, Plasmo, TypeScript, Supabase, LiteLLM]
repo: github.com/ZidanHadipratama/IWantJob
last_indexed: 2025-04-22
---

# IWantJob

## Summary
Chrome extension + FastAPI backend that tailors resumes and auto-fills
job applications using AI. Supports BYOK, self-hosted Ollama, and a
hosted tier. Built with Plasmo + React + TypeScript (extension) and
FastAPI async (backend).

## Features
- [[IWantJob/llm-routing]] — Multi-provider LLM routing via LiteLLM
- [[IWantJob/resume-tailoring]] — AI-powered resume tailoring per job
- [[IWantJob/autofill-engine]] — Chrome extension DOM auto-fill
- [[IWantJob/auth-byok]] — BYOK API key management
- [[IWantJob/payment-midtrans]] — Midtrans local payment integration
- [[IWantJob/supabase-auth]] — Supabase JWT auth flow

## Architecture Notes
Backend is stateless FastAPI. Auth via Supabase JWTs validated on every
request. LLM calls routed through LiteLLM to abstract provider differences.
Extension communicates with backend via REST.
```

---

### 5.3 Feature Note (`Projects/[project-name]/[feature-name].md`)

```markdown
---
project: IWantJob
feature: llm-routing
type: implementation
files:
  - backend/services/llm_router.py
  - backend/core/config.py
  - backend/api/routes/completion.py
tags: [llm, routing, litellm, multi-provider, fastapi]
last_indexed: 2025-04-22
---

# LLM Routing — IWantJob

## What it does
Routes LLM requests to the correct provider based on the user's
configured tier: BYOK (user-supplied API key), hosted (platform key),
or Ollama (local inference). Abstracts provider differences via LiteLLM.

## Key Components

### `LLMRouter` class
**File:** `backend/services/llm_router.py`

| Function | Responsibility |
|----------|----------------|
| `route_request(user_id, messages)` | Main entry point. Fetches user config, selects provider, calls LiteLLM |
| `_build_litellm_params(config, messages)` | Normalizes params across providers into LiteLLM format |
| `_handle_ollama_fallback(config)` | Sets base_url override for local Ollama endpoint |

### `LLMConfig` model
**File:** `backend/core/config.py`

Pydantic model holding: `provider`, `model_name`, `api_key` (nullable),
`ollama_base_url` (nullable), `tier` (enum: byok | hosted | ollama)

### Route handler
**File:** `backend/api/routes/completion.py`

Receives request → validates JWT → calls `LLMRouter.route_request()` →
streams response back to client.

## Workflow

```
POST /completion
  → validate Supabase JWT
  → fetch LLMConfig from Supabase for user_id
  → LLMRouter.route_request(user_id, messages)
    → _build_litellm_params()
    → if tier == ollama: _handle_ollama_fallback()
    → litellm.completion(**params, stream=True)
  → stream response to client
```

## Reuse Notes
- Reusable as-is for any FastAPI + LiteLLM project
- **Gotcha:** Ollama requires `base_url` override — easy to miss
- **Gotcha:** LiteLLM model string format differs by provider
  (e.g., `gpt-4o` vs `anthropic/claude-3-5-sonnet`)
- BYOK keys are never stored server-side — fetched from Supabase,
  used per-request, not cached

## Adaptation Checklist
When reusing in a new project:
- [ ] Replace Supabase auth with your auth system
- [ ] Update `LLMConfig` model fields for your providers
- [ ] Adjust streaming response format if needed
- [ ] Set OLLAMA_BASE_URL in .env
```

---

## 6. Obsidian Vault Structure

```
ObsidianVault/
├── Projects/
│   ├── _index.md                    ← Global project list
│   ├── _agent-status.md             ← Multi-agent coordination
│   ├── IWantJob/
│   │   ├── index.md                 ← Project summary + feature list
│   │   ├── llm-routing.md
│   │   ├── resume-tailoring.md
│   │   ├── autofill-engine.md
│   │   ├── auth-byok.md
│   │   ├── payment-midtrans.md
│   │   └── supabase-auth.md
│   ├── Syntra/
│   │   ├── index.md
│   │   └── ...
│   └── Catty/
│       ├── index.md
│       └── ...
└── Templates/
    ├── project-index.md             ← Template for new project index
    └── feature-note.md              ← Template for new feature note
```

---

## 7. Setup & Configuration

### 7.1 Obsidian Setup

1. Install [Obsidian](https://obsidian.md)
2. Create a new vault (e.g., `~/DevVault`)
3. Install community plugin: **Local REST API**
4. Enable the plugin and note the API key and port (default: `27123`)

### 7.2 MCP Server Setup

Add to your Claude Code MCP config (`~/.claude/mcp.json`):

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["-y", "obsidian-mcp-server"],
      "env": {
        "OBSIDIAN_API_KEY": "your-local-rest-api-key",
        "OBSIDIAN_PORT": "27123"
      }
    }
  }
}
```

For Codex CLI, add the same MCP server reference in `AGENTS.md` or the Codex config file.

### 7.3 Skills Setup

```
your-project/
├── CLAUDE.md           ← Claude Code reads this automatically
├── AGENTS.md           ← Codex reads this automatically
└── .claude/
    └── skills/
        ├── analyze-project.md
        ├── save-to-vault.md
        └── retrieve-feature.md
```

For cross-project access, symlink the skills:
```bash
ln -s ~/DevVault/skills .claude/skills
```

---

## 8. Usage Workflows

### 8.1 Indexing a Project (One-time or after major changes)

```
Developer → Claude Code:
"Use /analyze-project on ~/projects/IWantJob, 
 then use /save-to-vault to save it."

Claude Code:
1. Reads IWantJob codebase
2. Produces structured analysis
3. Writes notes to Obsidian vault via MCP
4. Confirms: "Indexed 6 features for IWantJob"
```

**Estimated time:** 5–15 minutes per project depending on size.

---

### 8.2 Retrieving and Reusing a Feature (Day-to-day)

```
Developer → Claude Code or Codex:
"I need to implement LLM routing in NewProject. 
 I already did this in IWantJob. 
 Use /retrieve-feature IWantJob llm-routing,
 analyze the reference, and tell me how to adapt it here."

Agent:
1. Reads IWantJob/llm-routing.md from Obsidian via MCP
2. Opens backend/services/llm_router.py from IWantJob repo
3. Reads raw code
4. Produces adaptation plan for NewProject
```

---

### 8.3 Parallel Agent Workflow (Claude + Codex)

```
Claude Code task:
"Index these three projects into DevVault: 
 IWantJob, Syntra, Catty. 
 Use /analyze-project then /save-to-vault for each."

Simultaneously — Codex task:
"I'm building NewProject. Use /retrieve-feature 
 to check if I've built a payment integration before.
 Search across all projects."

Both agents:
→ Read _agent-status.md first
→ Register their task
→ Operate on separate notes — no conflicts
→ Update _agent-status.md on completion
```

---

## 9. Phased Roadmap

### Phase 1 — Foundation (Week 1–2)
- [ ] Define final note schema (validate on one project manually)
- [ ] Set up Obsidian vault with folder structure
- [ ] Install and configure Obsidian Local REST API plugin
- [ ] Configure Obsidian MCP server in Claude Code
- [ ] Manually index one project (IWantJob) to validate schema

### Phase 2 — Skills (Week 3–4)
- [ ] Write `analyze-project.md` skill
- [ ] Test `/analyze-project` on Syntra and Catty
- [ ] Write `save-to-vault.md` skill
- [ ] Test full analyze → save pipeline
- [ ] Write `retrieve-feature.md` skill
- [ ] Test full retrieve → adapt workflow

### Phase 3 — Multi-Agent (Week 5)
- [ ] Add `_agent-status.md` coordination pattern
- [ ] Test Claude Code + Codex reading same vault simultaneously
- [ ] Add to `AGENTS.md` for Codex compatibility
- [ ] Symlink skills for global access across projects

### Phase 4 — Refinement (Ongoing)
- [ ] Add Adaptation Checklist to all feature notes
- [ ] Add tags for cross-project feature search
- [ ] Evaluate whether vector search (Zilliz/ChromaDB) is needed
- [ ] Consider auto-indexing hook on `git commit`

---

## 10. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Notes go stale as code evolves | High | Re-run `/analyze-project` after major feature changes. Track `last_indexed` date in frontmatter. |
| Line numbers used as anchors | High | Use function/class names only — never line numbers in notes |
| Agent writes conflicting notes simultaneously | Medium | `_agent-status.md` coordination + `_draft` suffix convention |
| Obsidian Local REST API not running | Medium | Add health check step to skill preamble |
| Note schema inconsistency across projects | Medium | Templates in `Vault/Templates/` enforce structure |

---

## 11. Success Metrics

| Metric | Target |
|--------|--------|
| Time to retrieve + adapt a past feature | < 5 minutes |
| Notes accurately reflect codebase | Verified on 3 projects at Phase 2 completion |
| Claude + Codex parallel operation without conflict | 0 write conflicts in Phase 3 testing |
| Developer skips manual codebase navigation | 80%+ of reuse cases handled via vault |

---

## Appendix A — Skill File Template

```markdown
---
skill: analyze-project
version: 1.0
compatible_agents: [claude-code, codex-cli, gemini-cli]
mcp_required: false
---

# Skill: /analyze-project

## Purpose
[one line]

## When to use
[trigger conditions]

## Steps
1. ...
2. ...

## Output format
[schema or example]

## Notes
[gotchas, assumptions]
```

---

## Appendix B — Comparison: DevVault vs Alternatives

| | DevVault | Zilliz + claude-context | Obsidian only (manual) |
|--|---------|------------------------|------------------------|
| Setup effort | Medium | High | Low |
| Retrieval quality | High (structured) | High (semantic) | Low (manual search) |
| Multi-agent support | ✅ | ❌ (not designed for) | ❌ |
| Works offline | ✅ | ❌ | ✅ |
| Human-readable | ✅ | ❌ (vector DB) | ✅ |
| Cost | Free | Free tier → paid | Free |
| Raw code access | ✅ (via file paths) | ✅ (direct index) | Manual |
| Reuse notes / gotchas | ✅ | ❌ | Manual |
