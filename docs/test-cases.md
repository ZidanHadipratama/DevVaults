# Test Cases

## Phase 1 — Foundation

| # | Test | Expected | Status |
|---|------|----------|--------|
| T1.1a | Vault structure: `Projects/_index.md` exists with correct headers | File present, table headers match PRD | pass |
| T1.1b | Vault structure: `Templates/feature-note.md` has all PRD 5.3 fields + Adaptation Checklist | All sections present | pass |
| T1.2a | `~/.claude.json` has obsidian mcpServers entry | JSON key present, API key is placeholder | pass |
| T1.2b | `docs/mcp-setup.md` exists with plugin instructions | File present and readable | pass |
| T1.3a | `Projects/IWantJob/` has index.md + 5 feature notes (IWantJobPrivate — no payment feature) | 6 files present | pass |
| T1.3b | Each feature note has YAML frontmatter with files[], tags[], last_indexed | No line numbers in any note | pass |
| T1.3c | `Projects/_index.md` has IWantJob row | Row present with stack + feature count | pass |
| T1.4a | Claude reads `Projects/IWantJob/llm-routing.md` via MCP | Note contents returned | pass |
| T1.4b | Claude opens raw file from note's files[] via IWantJob repo | Raw code returned and matches note | pass |

## Phase 2 — Skills

| # | Test | Expected | Status |
|---|------|----------|--------|
| T4 | `/analyze-project` on Syntra | Structured feature notes output, no vault writes | pending |
| T5 | `/save-to-vault` after analyze | Notes written to vault, `_index.md` updated | pending |
| T6 | `/retrieve-feature IWantJob llm-routing` | Summary + raw code + adaptation plan | pending |

## Phase 3 — Multi-Agent

| # | Test | Expected | Status |
|---|------|----------|--------|
| T7 | Claude + Codex read vault simultaneously | No errors, both get correct data | pending |
| T8 | Concurrent write attempt on same note | `_draft` suffix prevents conflict | pending |
