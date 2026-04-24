# Quick Tasks

## QT-1 — Refactor analyze pipeline (2026-04-24)
**Status:** done
**Result:** Split `analyze-project` into lightweight overview + `analyze-feature` per-feature deep dive. Updated `AGENTS.md` with new two-step workflow + inline Codex procedure for `analyze-feature`. Eval: with-skill 7/7 assertions vs baseline 2/7.

---

## QT-2 — analyze-and-save orchestrator + codex + README + global install (2026-04-24)
**Status:** pending

<task id="QT-2.1" complexity="complex">
  <context>
    New master skill that replaces the manual 3-skill pipeline for vault indexing.
    Must: run analyze-project logic (overview + feature list), pause for user approval/modification, then spawn parallel subagents — one per feature — each running analyze-feature logic + saving directly to vault via MCP.
    Key constraint: approval gate between Phase 1 and Phase 2. User must be able to add/remove/rename features before any vault writes.
    Existing skills for reference: .claude/skills/analyze-project/SKILL.md, .claude/skills/analyze-feature/SKILL.md, .claude/skills/save-to-vault/SKILL.md
    Must be created via /skill-creator.
  </context>
  <action>
    Build .claude/skills/analyze-and-save/SKILL.md via /skill-creator.
    Skill flow:
    Phase 1: scan repo (README, root config, dir listing) → output project overview + feature candidate table → PAUSE for user approval
    Phase 2: for each approved feature, spawn parallel subagent → subagent runs analyze-feature logic (≤5 files) + writes note to vault via MCP → main agent collects summary
    Invocation: /analyze-and-save /path/to/repo [ProjectName]
    Run evals on at least 1 real repo (IWantJobPrivate).
  </action>
  <verify>
    - .claude/skills/analyze-and-save/SKILL.md exists
    - Skill appears in available skills list
    - Approval gate documented in skill (Phase 1 pauses, Phase 2 only runs after approval)
    - Parallel subagent pattern documented
    - Eval run against IWantJobPrivate passes qualitative check
  </verify>
  <status>pending</status>
</task>

<task id="QT-2.2" complexity="standard">
  <context>
    codex-skills/ dir has: analyze-project, save-to-vault, retrieve-feature — all from old pipeline.
    analyze-project needs to match new lightweight version (overview + feature list only, no deep dive).
    analyze-feature doesn't exist in codex-skills/ yet.
    analyze-and-save doesn't exist in codex-skills/ yet — needs Codex-adapted version (sequential, not parallel; Codex cannot spawn subagents).
    Reference: .claude/skills/analyze-project/SKILL.md (updated), .claude/skills/analyze-feature/SKILL.md, .claude/skills/analyze-and-save/SKILL.md (once built in QT-2.1), AGENTS.md inline Codex procedure.
    Depends on: QT-2.1 (analyze-and-save must exist to use as reference).
  </context>
  <action>
    1. Rewrite codex-skills/analyze-project/SKILL.md to match new lightweight version (strip deep-dive steps).
    2. Create codex-skills/analyze-feature/SKILL.md — single-feature deep dive for Codex (PRD 5.3 schema, same as .claude/skills/analyze-feature/SKILL.md).
    3. Create codex-skills/analyze-and-save/SKILL.md — Codex-adapted orchestrator:
       - Same Phase 1 (overview + feature list + approval gate) as Claude Code version
       - Phase 2 is sequential not parallel (Codex has no subagent spawning)
       - Phase 2: for each approved feature, run analyze-feature logic + save to vault via MCP, one at a time
       - Note sequential limitation explicitly in skill so user knows it's slower than Claude Code version
  </action>
  <verify>
    - codex-skills/analyze-project/SKILL.md no longer contains deep feature read steps
    - codex-skills/analyze-feature/SKILL.md exists with PRD 5.3 output schema
    - codex-skills/analyze-and-save/SKILL.md exists with sequential Phase 2 and approval gate
    - All 3 files consistent with .claude/skills/ equivalents
  </verify>
  <status>pending</status>
</task>

<task id="QT-2.3" complexity="standard">
  <context>
    README.md currently shows old one-shot analyze-project workflow.
    New primary workflow: /analyze-and-save (one command, orchestrated).
    Granular skills (analyze-project, analyze-feature, save-to-vault) stay documented as advanced section.
    Reference: current README.md, AGENTS.md for skill descriptions.
  </context>
  <action>
    Update README.md:
    1. Add /analyze-and-save as primary skill (before other skills, or as its own "Quick Start" section)
    2. Update "Typical Workflow" section to show one-command pipeline
    3. Rename existing 3-skill section to "Advanced / Granular Control"
    4. Add /analyze-feature entry (currently missing from README)
  </action>
  <verify>
    - README.md has /analyze-and-save documented with invocation example
    - Typical Workflow shows new pipeline
    - All 5 skills documented (analyze-and-save, analyze-project, analyze-feature, save-to-vault, retrieve-feature)
  </verify>
  <status>pending</status>
</task>

<task id="QT-2.4" complexity="standard">
  <context>
    DevVault skills currently only available in /home/ikktaa/app/DevVault project scope.
    Global skills dir: ~/.claude/skills/ (confirmed exists, has other skills like gsd-*)
    Skills to install: analyze-and-save, analyze-project, analyze-feature, save-to-vault, retrieve-feature
    Install = copy skill dirs to ~/.claude/skills/ so they appear in available skills in any project.
    Depends on QT-2.1 (analyze-and-save must exist before install).
  </context>
  <action>
    Copy all 5 .claude/skills/* dirs to ~/.claude/skills/:
    - analyze-and-save
    - analyze-project
    - analyze-feature
    - save-to-vault
    - retrieve-feature
    Verify each appears in available skills list after copy.
  </action>
  <verify>
    - 5 skill dirs exist under ~/.claude/skills/
    - Skills appear in available skills list (system reminder confirms)
  </verify>
  <status>pending</status>
</task>
