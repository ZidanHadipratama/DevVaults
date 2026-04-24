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
Phase 1 complete. Phase 2 complete. Phase 3 complete. Phase 4 complete.

## Tech Stack
- **Knowledge store:** Obsidian vault (local Markdown)
- **MCP bridge:** `obsidian-mcp-server` (npx, cyanheads)
- **Obsidian plugin:** Local REST API (coddingtonbear, `https://127.0.0.1:27124/`)
- **Skills format:** `.md` skill files via `/skill-creator`
- **Target agents:** Claude Code + Codex CLI

## Agent Routing
| Role | Primary | Backup |
|------|---------|--------|
| Planner | Claude Opus (`/model opusplan`) | Codex |
| Executor | Codex CLI | Claude Sonnet |

**Active role:** Executor

## Current Assignment
- **Phase:** Quick Tasks — QT-2 executed (pending live eval)
- **Last completed:** QT-2.4 — all 5 skills globally installed to `~/.claude/skills/`. Commit `31fcaf9`.
- **Pending:** Live end-to-end test of `analyze-and-save` (skipped due to context limit)
- **Handoff entry:** See `docs/log.md` — 2026-04-24 Claude Sonnet [handoff]
- **Next action:** Run `/analyze-and-save /home/ikktaa/app/IWantJobPrivate` to validate new skill, then mark QT-2 done in `docs/quick-tasks.md`

## Key Paths
| Resource | Path |
|----------|------|
| Obsidian vault | `/home/ikktaa/Documents/DevVault` |
| Skills (Claude) | `.claude/skills/` |
| MCP config | `~/.claude.json` |
| Phase 1 target repo | `/home/ikktaa/app/IWantJobPrivate` |

## Skill Creation Rule
**All new skills must start from `skill-creator` scaffolds.** Final tracked skill files may then be customized for the target agent.

## Recommended Next Action
Run `/frugent-plan` to plan the next milestone or phase.
