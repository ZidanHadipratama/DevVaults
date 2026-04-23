# DevVault — Roadmap

**Version:** 1.0  
**Last updated:** 2026-04-23

---

## Phase 1 — Foundation
**Goal:** Validate note schema on one real project. Prove MCP connection works end-to-end.

**Success criteria:**
- Obsidian vault at `/home/ikktaa/Documents/DevVault` has correct folder structure
- Local REST API plugin configured and reachable at port 27123
- `obsidian-mcp-server` wired in `~/.claude.json`
- IWantJob manually indexed (1 project index + 5 feature notes — OSS version, no payment)
- Agent can read a note via MCP and open the referenced raw file

**Status:** complete

---

## Phase 2 — Skills
**Goal:** Automate analyze → save → retrieve pipeline via skills.

**Success criteria:**
- `/analyze-project` skill created via `/skill-creator`, tested on Syntra or Catty
- `/save-to-vault` skill created via `/skill-creator`, tested full pipeline
- `/retrieve-feature` skill created via `/skill-creator`, tested retrieval + adaptation plan
- All skills in `.claude/skills/` and referenced in `CLAUDE.md` + `AGENTS.md`

**Status:** complete

---

## Phase 3 — Multi-Agent
**Goal:** Claude Code + Codex operate on same vault simultaneously without conflicts.

**Success criteria:**
- `_agent-status.md` coordination pattern in place
- Claude + Codex tested reading vault simultaneously
- `AGENTS.md` configured for Codex skill access
- Skills symlinked globally for cross-project access

**Status:** pending

---

## Phase 4 — Refinement (ongoing)
**Goal:** Polish, hardening, optional vector search evaluation.

**Tasks:**
- Add Adaptation Checklist to all feature notes
- Add tags for cross-project feature search
- Evaluate vector search (Zilliz/ChromaDB) need
- Consider auto-indexing hook on `git commit`

**Status:** pending
