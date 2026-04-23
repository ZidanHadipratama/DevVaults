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
Phase 1 complete. Phase 2 complete. Phase 3 deferred (Codex). Phase 4 in-progress — task 4.1 next.

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
- **Phase:** Phase 4 — Docs & Release
- **Task:** 4.3 — GitHub push (pending)
- **Last completed:** 4.2 — docs/setup.md (done); 4.1 — README.md (done)
- **Next action:** Run `/frugent-execute` — verify SSH access to git@github.com:ZidanHadipratama/DevVaults.git, then push

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
