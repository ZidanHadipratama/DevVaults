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
- `status.md` coordination pattern in place
- Claude + Codex tested reading vault simultaneously
- `AGENTS.md` configured for Codex skill access
- Skills symlinked globally for cross-project access

**Status:** deferred — Codex to complete

---

## Phase 4 — Docs & Release
**Goal:** Project is documented, guiding, and publicly available on GitHub.

**Success criteria:**
- `README.md` at repo root covers what DevVault is, prerequisites, setup, and all 3 skill workflows
- `docs/setup.md` has step-by-step setup for a new machine
- Repo pushed to `git@github.com:ZidanHadipratama/DevVaults.git`

**Status:** complete
