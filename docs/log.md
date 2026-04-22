# DevVault — Log

## 2026-04-23 — Claude Sonnet [progress]
- **Task completed:** 1.4 — E2E MCP verify. Phase 1 complete.
- **Verification:** T1.4a (MCP read llm-routing.md) pass. T1.4b (raw file ai_service.py matches note) pass. All T1.1–T1.4 tests pass.
- **Files modified:** `docs/plan.md` (1.4 done), `docs/roadmap.md` (Phase 1 complete), `docs/test-cases.md` (all T1 marked pass)
- **Next task:** Phase 2 — Skills. Run `/frugent-plan` to plan Phase 2 (3 skills via `/skill-creator`).

## 2026-04-23 — Claude Sonnet [progress]
- **Task completed:** 1.3 — IWantJob indexing (full)
- **Files created:**
  - `/home/ikktaa/Documents/DevVault/Projects/IWantJob/auth-byok.md`
  - `/home/ikktaa/Documents/DevVault/Projects/IWantJob/supabase-auth.md`
  - `/home/ikktaa/Documents/DevVault/Projects/IWantJob/index.md`
- **Files modified:**
  - `/home/ikktaa/Documents/DevVault/Projects/_index.md` — IWantJob row added
  - `/home/ikktaa/app/DevVault/docs/plan.md` — task 1.3 marked done
- **Verification:** IWantJob/ contains index.md + 5 feature notes, all with YAML frontmatter + Reuse Notes + Adaptation Checklist. _index.md has IWantJob row. Zero line numbers in any note.
- **Next task:** 1.4 — E2E MCP verify. **Blocked on user:** install Obsidian Local REST API plugin + fill API key in `~/.claude.json`. See `docs/mcp-setup.md`.

## 2026-04-23 — Claude Sonnet [handoff]
- **Session summary:** Phase 1 execution. Tasks 1.1+1.2 completed in parallel. Task 1.3 started — 3 of 5 feature notes written before context limit.
- **Completed:**
  - Task 1.1: vault structure (`_index.md`, `_agent-status.md`, `Templates/project-index.md`, `Templates/feature-note.md`)
  - Task 1.2: MCP config (`~/.claude.json` obsidian entry merged, `docs/mcp-setup.md` written)
  - Task 1.3 partial: `llm-routing.md`, `resume-tailoring.md`, `autofill-engine.md` written to vault
- **In progress:** Task 1.3 — IWantJob indexing
  - Done: `Projects/IWantJob/llm-routing.md`, `resume-tailoring.md`, `autofill-engine.md`
  - Remaining: `auth-byok.md`, `supabase-auth.md`, `IWantJob/index.md`, update `Projects/_index.md` with IWantJob row
  - Key files to read for auth-byok: `extension/src/lib/storage.ts`, `extension/src/components/options/AIConfigCard.tsx`, `backend/app/dependencies.py` (already read — `AIConfig` dataclass + `get_ai_config` are the BYOK mechanism)
  - Key files to read for supabase-auth: `backend/app/dependencies.py` (already read — `RequestAuthContext`, `get_auth_context`, `_build_self_hosted_auth_context`), `supabase/migrations/009_hosted_auth_user_sync.sql`, `extension/src/lib/storage.ts`
- **Technical decisions:**
  - IWantJobPrivate is the OSS version — no payment-midtrans feature (commercial only). Plan updated to 5 features.
  - Notes written directly to filesystem (MCP not yet live — API key placeholder).
  - `dependencies.py` already reveals both auth-byok (`AIConfig` + `get_ai_config`) and supabase-auth (`RequestAuthContext`, `get_auth_context`) mechanisms — read it first in next session.
- **Learned lessons:**
  - `dependencies.py` covers both auth-byok AND supabase-auth — reading it once covers both features. Start next session by reading storage.ts + AIConfigCard.tsx, then write auth-byok immediately.
  - Resume + resume_relevance.py both needed for resume-tailoring note — read together.
  - Anti-paralysis rule: read ≤5 files per feature before writing. Enforced successfully.
- **Files modified this session:**
  - `/home/ikktaa/app/DevVault/docs/plan.md` — tasks 1.1+1.2 marked done, repo path updated to IWantJobPrivate, features updated to 5
  - `/home/ikktaa/app/DevVault/docs/briefing.md` — repo path updated
  - `/home/ikktaa/app/DevVault/docs/mcp-setup.md` — created
  - `~/.claude.json` — obsidian mcpServers entry added
  - `/home/ikktaa/Documents/DevVault/Projects/_index.md` — created (empty)
  - `/home/ikktaa/Documents/DevVault/Projects/_agent-status.md` — created
  - `/home/ikktaa/Documents/DevVault/Templates/project-index.md` — created
  - `/home/ikktaa/Documents/DevVault/Templates/feature-note.md` — created
  - `/home/ikktaa/Documents/DevVault/Projects/IWantJob/llm-routing.md` — created
  - `/home/ikktaa/Documents/DevVault/Projects/IWantJob/resume-tailoring.md` — created
  - `/home/ikktaa/Documents/DevVault/Projects/IWantJob/autofill-engine.md` — created
- **Known issues:** None broken. Task 1.4 (MCP verify) blocked until user installs Obsidian Local REST API plugin + fills API key.
- **Resume instructions:**
  1. Read `docs/briefing.md`, `docs/plan.md`, last 5 entries of `docs/log.md`
  2. Current task: 1.3 (in progress). Run `/frugent-execute` to continue.
  3. Read `extension/src/lib/storage.ts` + `extension/src/components/options/AIConfigCard.tsx` → write `Projects/IWantJob/auth-byok.md`
  4. Read `supabase/migrations/009_hosted_auth_user_sync.sql` + `extension/src/lib/storage.ts` (already read in step 3) → write `Projects/IWantJob/supabase-auth.md` (note: `backend/app/dependencies.py` already documented in llm-routing note — reference it, don't re-read)
  5. Write `Projects/IWantJob/index.md` (PRD 5.2 schema, 5 features, stack: FastAPI + Plasmo + TypeScript + Supabase + LiteLLM)
  6. Update `Projects/_index.md` with IWantJob row
  7. Mark task 1.3 done in plan.md, append [progress] to log.md
  8. Proceed to task 1.4 (MCP verify) — requires Obsidian running with API key filled

## 2026-04-23 — Claude Sonnet [progress]
- **Tasks completed:** 1.1 (vault structure) + 1.2 (MCP config) — executed in parallel
- **Files created:** `Projects/_index.md`, `Projects/_agent-status.md`, `Templates/project-index.md`, `Templates/feature-note.md`, `docs/mcp-setup.md`
- **Files modified:** `~/.claude.json` (obsidian mcpServers entry merged)
- **Verification:** all 7 verify criteria passed
- **Blocker for 1.4:** user must install Local REST API plugin in Obsidian + fill API key in `~/.claude.json` — see `docs/mcp-setup.md`
- **Next task:** Task 1.3 — index IWantJob (depends on 1.1 ✓). Run `/frugent-execute` to continue.

## 2026-04-23 — Claude Opus [progress]
- **Task completed:** Phase 1 planned with XML contracts (4 tasks)
- **Files changed:** `docs/plan.md`
- **Plan:** 1.1 vault structure, 1.2 MCP config, 1.3 index IWantJob, 1.4 verify E2E — 1.1+1.2 parallel, 1.3 depends on 1.1, 1.4 gates phase completion
- **Verification:** plan.md written with XML contracts, dependencies mapped
- **Next task:** Run `/frugent-execute` to start Phase 1 (begin with 1.1 + 1.2 in parallel)

## 2026-04-23 — Claude Sonnet [progress]
- **Task completed:** Frugent initialized via conversational scan + PRD analysis
- **Files changed:** `docs/briefing.md`, `docs/roadmap.md`, `docs/plan.md`, `docs/contracts.md`, `docs/codebase-analysis.md`, `docs/log.md`, `docs/quick-tasks.md`
- **Vault dirs created:** `/home/ikktaa/Documents/DevVault/Projects/`
- **User vision captured:** Personal codebase KMS — AI agents retrieve structured knowledge from past projects via Obsidian MCP, enabling cross-project reuse without manual navigation
- **Key constraint:** All skills must be created via `/skill-creator` in Claude Code
- **Verification:** Docs scaffolded, vault dir exists, no code written yet (greenfield)
- **Next task:** Run `/frugent-plan` → then execute Phase 1 (vault structure + MCP config + IWantJob indexing)
