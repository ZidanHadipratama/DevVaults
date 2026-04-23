# DevVault — Project Briefing

## Project
- **Name:** DevVault
- **Type:** Personal codebase KMS for AI agents
- **Working dir:** `/home/ikktaa/app/DevVault`
- **Vault path:** `/home/ikktaa/Documents/DevVault`

## Vision
Personal knowledge base that indexes past projects as structured Markdown in Obsidian. AI agents query via MCP to retrieve exact feature references + raw code — so every new project starts with full institutional memory, not from zero.

Design pillars: plain text first, notes are indexes (not code), agent-agnostic, human-readable, privacy-first (all local).

## Current State
Phase 1 complete. Phase 2 complete. All 3 skills live: `analyze-project`, `save-to-vault`, `retrieve-feature`.

## Tech Stack
- **Knowledge store:** Obsidian vault (local Markdown)
- **MCP bridge:** `obsidian-mcp-server` (npx, cyanheads)
- **Obsidian plugin:** Local REST API (coddingtonbear, port 27123)
- **Skills format:** `.md` skill files via `/skill-creator`
- **Target agents:** Claude Code + Codex CLI

## Agent Routing
| Role | Primary | Backup |
|------|---------|--------|
| Planner | Claude Opus (`/model opusplan`) | Codex |
| Executor | Claude Sonnet | Codex |

**Active role:** Executor

## Current Assignment
- **Phase:** Phase 2 — Skills
- **Task:** Phase 2 complete. Next: Phase 3 — Multi-Agent
- **Handoff entry:** See `docs/log.md` — 2026-04-23 Claude Sonnet [progress]
- **Next action:** Run `/frugent-plan` to plan Phase 3.

## Key Paths
| Resource | Path |
|----------|------|
| Obsidian vault | `/home/ikktaa/Documents/DevVault` |
| Skills (Claude) | `.claude/skills/` |
| MCP config | `~/.claude.json` |
| Phase 1 target repo | `/home/ikktaa/app/IWantJobPrivate` |

## Skill Creation Rule
**All skills must be created using `/skill-creator` in Claude Code.** Do not hand-write skill `.md` files directly.

## Recommended Next Action
Run `/frugent-plan` to build the phased roadmap.
