# DevVault — Active Phase Plan

**Active phase:** Phase 4 — Docs & Release
**Last updated:** 2026-04-23
**Planner:** Claude Opus | **Executor:** Claude Sonnet | **Backup:** Codex CLI

---

<task id="4.1" complexity="standard">
  <context>
    Repo is public-facing. README is first thing anyone sees on GitHub.
    Must cover: what DevVault is, why it exists, prerequisites, MCP setup, skill installation, all 3 skill workflows with examples.
    Vault path: /home/ikktaa/Documents/DevVault (local only — do not include in README as absolute path, describe generically).
    Skills live at .claude/skills/ — explain how to install them.
    MCP base URL: https://127.0.0.1:27124/ (HTTPS, port 27124).
    Obsidian Local REST API plugin required.
  </context>
  <action>
    Write README.md at repo root (/home/ikktaa/app/DevVault/README.md).
    Must include:
    1. What DevVault is (1 paragraph — personal codebase KMS for AI agents)
    2. How it works (analyze → save → retrieve pipeline, diagram optional)
    3. Prerequisites (Obsidian + Local REST API plugin, obsidian-mcp-server, Claude Code with skill-creator)
    4. Setup (MCP config in ~/.claude.json, API key)
    5. Skills — one section per skill: purpose, invocation syntax, example output
    6. Vault structure (reference AGENTS.md or inline the tree)
    7. Phase roadmap / what's coming next (brief)
  </action>
  <verify>
    - README.md exists at repo root
    - Covers all 3 skills with invocation examples
    - Prerequisites section is complete (someone can set up from scratch)
    - No hardcoded absolute paths (use ~/Documents/DevVault pattern)
    - Readable as a standalone document without needing to read other files
  </verify>
  <status>done</status>
</task>

<task id="4.2" complexity="standard">
  <context>
    Depends on: 4.1 (README sets the high-level picture; setup guide is the hands-on complement).
    New machine setup requires: install Obsidian, install Local REST API plugin, get API key, configure ~/.claude.json, install skill-creator plugin, verify MCP, clone repo, test with /retrieve-feature.
    MCP config pattern: python3 -c "import json; ..." merge approach (safe JSON merge).
    skill-creator install: claude plugins install skill-creator (requires session restart).
    Obsidian Local REST API plugin: Settings → Community Plugins → Browse → "Local REST API" by coddingtonbear.
  </context>
  <action>
    Write docs/setup.md — complete step-by-step setup guide for a new machine.
    Must include:
    1. Install Obsidian (link to obsidian.md)
    2. Install Local REST API plugin — exact UI steps + where to find API key
    3. Create vault at ~/Documents/DevVault (or custom path)
    4. Clone this repo
    5. Configure ~/.claude.json — MCP entry with OBSIDIAN_API_KEY and OBSIDIAN_BASE_URL
    6. Install skill-creator plugin (claude plugins install skill-creator + restart)
    7. Verify setup — run /retrieve-feature IWantJob llm-routing (if IWantJob is indexed) or run /analyze-project on any repo
    8. Troubleshooting section: MCP unreachable, API key wrong, port mismatch
  </action>
  <verify>
    - docs/setup.md exists
    - All 8 sections present
    - ~/.claude.json MCP config example included (with placeholder API key)
    - Troubleshooting covers the 3 most common failure modes
    - Someone unfamiliar with the project can follow it start to finish
  </verify>
  <status>done</status>
</task>

<task id="4.3" complexity="standard">
  <context>
    Depends on: 4.1 + 4.2 (docs must exist before push).
    GitHub remote: git@github.com:ZidanHadipratama/DevVaults.git
    Current branch: master
    .gitignore already excludes vault notes (/home/ikktaa/Documents/DevVault/**).
    Verify .gitignore is correct before push — vault notes must NOT be pushed.
    This is first push to remote — need to set upstream.
  </context>
  <action>
    1. Verify SSH access to git@github.com:ZidanHadipratama/DevVaults.git
    2. Confirm .gitignore excludes vault notes and no sensitive files staged
    3. Add remote: git remote add origin git@github.com:ZidanHadipratama/DevVaults.git
       (or update if remote already exists)
    4. Commit docs if any unstaged changes remain
    5. Push: git push -u origin master
  </action>
  <verify>
    - git remote -v shows correct origin
    - git push succeeded (no errors)
    - GitHub repo at github.com/ZidanHadipratama/DevVaults shows README.md on landing page
    - No vault notes (.md files from /Documents/DevVault) in pushed commit
  </verify>
  <status>pending</status>
</task>

---

## Dependencies
- 4.1 is independent — start here
- 4.2 depends on 4.1 (setup guide references README concepts)
- 4.3 depends on 4.1 + 4.2 (push after docs complete)

## Commit checkpoint
`feat(phase-4): add README and setup guide` before push.
Then push to origin master.
