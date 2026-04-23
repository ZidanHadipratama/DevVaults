# Test Cases

## Phase 1 — Foundation

| # | Test | Expected | Status |
|---|------|----------|--------|
| T1.1a | Vault structure: `Projects/projects.md` and `Projects/status.md` exist with correct headers | Files present, table headers match current contract | pass |
| T1.1b | Vault structure: `Templates/feature-note.md` has all PRD 5.3 fields + Adaptation Checklist | All sections present | pass |
| T1.2a | `~/.claude.json` has obsidian mcpServers entry | JSON key present, API key is placeholder, base URL uses `https://127.0.0.1:27124/` | pass |
| T1.2b | `docs/mcp-setup.md` exists with plugin instructions | File present and readable | pass |
| T1.3a | `Projects/IWantJob/` has `IWantJob.md` + 5 feature notes (IWantJobPrivate — no payment feature) | 6 files present | pass |
| T1.3b | Each feature note has YAML frontmatter with files[], tags[], last_indexed | No line numbers in any note | pass |
| T1.3c | `Projects/projects.md` has IWantJob row | Row present with stack + feature count | pass |
| T1.4a | Claude reads `Projects/IWantJob/llm-routing.md` via MCP | Note contents returned | pass |
| T1.4b | Claude opens raw file from note's files[] via IWantJob repo | Raw code returned and matches note | pass |

## Phase 2 — Skills

| # | Test | Expected | Status |
|---|------|----------|--------|
| T2.1a | `analyze-project` skill file exists at `.claude/skills/analyze-project/SKILL.md` | File present, created via `/skill-creator` | pass |
| T2.1b | Run `analyze-project` on Syntra or Catty | ≥2 feature note drafts with valid PRD 5.3 schema, no vault writes | pass |
| T2.2a | `save-to-vault` skill file exists at `.claude/skills/save-to-vault/SKILL.md` | File present, created via `/skill-creator` | pass |
| T2.2b | Run `save-to-vault` with drafts from T2.1b | Notes in vault via MCP, `projects.md` updated, `status.md` updated | pass |
| T2.3a | `retrieve-feature` skill file exists at `.claude/skills/retrieve-feature/SKILL.md` | File present, created via `/skill-creator` | pass |
| T2.3b | Run `retrieve-feature IWantJob llm-routing` | Note summary + `ai_service.py` excerpt + adaptation plan returned, no vault writes | pass |
| T2.3c | `AGENTS.md` exists at repo root with all 3 skills documented | File present, all 3 skills listed with invocation instructions | pass |

## Phase 3 — Codex Skills & Multi-Agent Alignment

| # | Test | Expected | Status |
|---|------|----------|--------|
| T3.1a | `codex-skills/analyze-project/SKILL.md` exists | Tracked Codex skill present with final frontmatter | pass |
| T3.1b | `codex-skills/save-to-vault/SKILL.md` exists | Tracked Codex skill present with final frontmatter | pass |
| T3.1c | `codex-skills/retrieve-feature/SKILL.md` exists | Tracked Codex skill present with final frontmatter | pass |
| T3.1d | Each Codex skill has `agents/openai.yaml` | UI metadata present for all 3 skills | pass |
| T3.1e | No placeholder TODO text remains in `codex-skills/` | Skill bodies are finalized | pass |
| T3.2a | Installed skill copies exist under `~/.codex/skills/` | Codex can discover all 3 installed skills | pass |
| T3.2b | `docs/contracts.md` uses current vault naming | `projects.md`, `status.md`, `<ProjectName>.md` only | pass |
| T3.2c | `docs/test-cases.md` reflects Codex skill locations and active phase | Phase 3 tasks listed accurately | pass |
| T3.3a | Codex validation path is recorded | Installed skill locations verified and `quick_validate.py` run against all 3 skills | pass |
| T3.3b | Validator dependency path is handled | `quick_validate.py` runs successfully from an isolated venv with `PyYAML` installed | pass |

## Phase 4 — Docs & Release

| # | Test | Expected | Status |
|---|------|----------|--------|
| T4.1a | README.md exists at repo root | File present, all 7 sections present | pass |
| T4.1b | README covers all 3 skills with invocation examples | Each skill has purpose + syntax + example | pass |
| T4.2a | docs/setup.md exists with 8 sections | File present, complete start-to-finish guide | pass |
| T4.2b | ~/.claude.json config example in setup.md | Placeholder API key, correct MCP URL format | pass |
| T4.3a | git remote origin points to ZidanHadipratama/DevVaults | `git remote -v` shows correct URL | pass |
| T4.3b | Push succeeds, README visible on GitHub landing page | No vault notes in pushed commit | pass |
