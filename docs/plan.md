# DevVault — Active Phase Plan

**Active phase:** Phase 1 — Foundation
**Last updated:** 2026-04-23
**Planner:** Claude Opus | **Executor:** Claude Sonnet | **Backup:** Codex CLI

---

<task id="1.1" complexity="standard">
  <context>
    Vault path: /home/ikktaa/Documents/DevVault
    Projects/ dir already created by frugent-init.
    Schema reference: PRD Section 5 (note schemas) and Section 6 (vault structure).
    contracts.md: templates must be reusable blanks with no project-specific data.
  </context>
  <action>
    Create the following files inside /home/ikktaa/Documents/DevVault/:

    Projects/_index.md — global project list table (headers only, no rows yet):
      | Project | Stack | Last Indexed | Features |

    Projects/_agent-status.md — multi-agent coordination table (headers + empty):
      | Agent | Task | Status | Note |

    Templates/project-index.md — blank project index template matching PRD Section 5.2 schema.
      Include YAML frontmatter placeholders and all section headers.

    Templates/feature-note.md — blank feature note template matching PRD Section 5.3 schema.
      Include YAML frontmatter placeholders, all section headers, and Adaptation Checklist.
  </action>
  <verify>
    - /home/ikktaa/Documents/DevVault/Projects/_index.md exists with correct table headers
    - /home/ikktaa/Documents/DevVault/Projects/_agent-status.md exists with correct table headers
    - /home/ikktaa/Documents/DevVault/Templates/project-index.md exists with all PRD 5.2 fields
    - /home/ikktaa/Documents/DevVault/Templates/feature-note.md exists with all PRD 5.3 fields including Adaptation Checklist
  </verify>
  <status>done</status>
</task>

<task id="1.2" complexity="standard">
  <context>
    MCP server: obsidian-mcp-server (npx, cyanheads package).
    Config target: ~/.claude.json (user-level, inherited by all projects).
    Obsidian plugin required: Local REST API (coddingtonbear), default port 27123.
    Vault path not needed in config — Obsidian REST API resolves it internally.
    OBSIDIAN_API_KEY must be left as placeholder; user fills after installing plugin.
    contracts.md: Obsidian must be running for MCP to work. Skills must health-check before writing.
  </context>
  <action>
    1. Check if ~/.claude.json exists. Read it if so.
    2. Add or merge the obsidian MCP server entry:
       {
         "mcpServers": {
           "obsidian": {
             "command": "npx",
             "args": ["-y", "obsidian-mcp-server"],
             "env": {
               "OBSIDIAN_API_KEY": "REPLACE_WITH_YOUR_LOCAL_REST_API_KEY",
               "OBSIDIAN_BASE_URL": "http://127.0.0.1:27123"
             }
           }
         }
       }
    3. Write a setup note to docs/mcp-setup.md explaining:
       - How to install Local REST API plugin in Obsidian
       - Where to find the API key (Obsidian → Settings → Local REST API)
       - How to replace the placeholder in ~/.claude.json
       - How to verify MCP works (restart Claude Code, run /mcp)
       - Gotcha: Obsidian must be running for MCP to work
  </action>
  <verify>
    - ~/.claude.json contains obsidian mcpServers entry
    - OBSIDIAN_API_KEY value is the placeholder string (not a real key)
    - docs/mcp-setup.md exists with plugin install instructions
  </verify>
  <status>done</status>
</task>

<task id="1.3" complexity="complex">
  <context>
    Depends on: task 1.1 (vault structure must exist).
    IWantJob repo: /home/ikktaa/app/IWantJobPrivate
    Features to index (5): llm-routing, resume-tailoring, autofill-engine, auth-byok, supabase-auth
    Note: IWantJobPrivate is OSS version — no payment-midtrans (commercial only)
    Schema: PRD Section 5.2 (project index) and 5.3 (feature note).
    contracts.md: function/class names only — never line numbers. Include Reuse Notes + Adaptation Checklist in every feature note.
    Anti-paralysis rule: if reading >5 files without writing a note, stop and raise [blocker].
    MCP may not be live yet (API key placeholder). Write notes directly to vault filesystem if MCP unavailable.
  </context>
  <action>
    Read IWantJob codebase at /home/ikktaa/app/IWantJobPrivate.
    For each of the 5 features, identify:
      - All files involved
      - Key functions/classes and their responsibility
      - Data flow (request → processing → response)
      - External services, env vars, config
      - Reuse gotchas and warnings

    Write to /home/ikktaa/Documents/DevVault/Projects/IWantJob/:
      - index.md (PRD Section 5.2 schema)
      - llm-routing.md
      - resume-tailoring.md
      - autofill-engine.md
      - auth-byok.md
      - supabase-auth.md

    Update /home/ikktaa/Documents/DevVault/Projects/_index.md with IWantJob row.
  </action>
  <verify>
    - /home/ikktaa/Documents/DevVault/Projects/IWantJob/ contains index.md + 5 feature notes
    - Each feature note has: YAML frontmatter with files[], tags[], last_indexed; all required sections; Reuse Notes; Adaptation Checklist
    - _index.md has IWantJob row with stack and feature count
    - Zero line number references in any note
  </verify>
  <status>done</status>
</task>

<task id="1.4" complexity="standard">
  <context>
    Depends on: task 1.2 (MCP configured) + task 1.3 (notes exist).
    This is the acceptance gate for Phase 1. Phase is NOT complete until this passes.
    User must have Obsidian running with Local REST API plugin active and API key filled in ~/.claude.json.
    Test cases: T1, T2, T3 in docs/test-cases.md.
  </context>
  <action>
    1. Confirm MCP connection: ask Claude to list vault contents via obsidian MCP tool.
    2. Read Projects/IWantJob/llm-routing.md via MCP.
    3. Open the first file listed in that note's files[] frontmatter from /home/ikktaa/app/IWantJobPrivate.
    4. Confirm raw code is readable and matches what the note describes.
    5. Update docs/test-cases.md: mark T1 and T2 as pass or fail with notes.
    6. If any step fails, append [blocker] to docs/log.md with exact error message.
  </action>
  <verify>
    - T1 (MCP read) and T2 (raw file open) marked in test-cases.md
    - If both pass: Phase 1 complete — create git commit
    - If either fails: [blocker] logged, phase stays in-progress
  </verify>
  <status>done</status>
</task>

---

## Dependencies
- 1.1 and 1.2 are independent — run in parallel
- 1.3 depends on 1.1
- 1.4 depends on 1.2 + 1.3

## Commit checkpoint
Phase 1 completion: `git commit` covering vault structure, MCP config, IWantJob notes, test results.
