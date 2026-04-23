# DevVault — Active Phase Plan

**Active phase:** Phase 2 — Skills
**Last updated:** 2026-04-23
**Planner:** Claude Opus | **Executor:** Claude Sonnet | **Backup:** Codex CLI

---

<task id="2.1" complexity="complex">
  <context>
    Skill creation rule: ALL skills must be created via /skill-creator in Claude Code. No hand-written .md files.
    Skill location: .claude/skills/
    Note schema contracts: docs/contracts.md (feature note schema, project index schema).
    Anti-paralysis rule: read ≤5 files per feature before writing each note draft.
    Target test repo: /home/ikktaa/app/Syntra OR /home/ikktaa/app/Catty (executor picks whichever exists).
    Output format: note drafts matching PRD Section 5.3 schema (YAML frontmatter + all sections + Adaptation Checklist).
    This skill is READ-ONLY — no vault writes.
  </context>
  <action>
    Use /skill-creator to create the analyze-project skill.
    The skill must:
    1. Accept a repo path as input.
    2. Scan the repo to identify distinct features (target: 3-6 per project).
    3. For each feature: read ≤5 source files, identify key functions/classes, data flow, external deps, and reuse gotchas.
    4. Output structured note drafts (in-conversation, not written to disk) matching PRD 5.3 schema.
    5. Output a project summary matching PRD 5.2 schema.
    6. Enforce anti-paralysis: if reading >5 files without producing output, stop and list what was found so far.

    After skill is created, test it on Syntra or Catty (whichever exists at /home/ikktaa/app/).
    Verify output drafts match note schema before marking done.
  </action>
  <verify>
    - .claude/skills/analyze-project.md exists
    - Skill created via /skill-creator (not hand-written)
    - Test run on Syntra or Catty produces at least 2 feature note drafts
    - Each draft has: YAML frontmatter with files[], tags[], last_indexed; all required sections; Reuse Notes; Adaptation Checklist
    - No vault files written during test run
  </verify>
  <status>done</status>
</task>

<task id="2.2" complexity="standard">
  <context>
    Depends on: task 2.1 (analyze-project must exist and produce valid drafts).
    Skill creation rule: use /skill-creator only.
    contracts.md: save-to-vault writes via MCP only (obsidian-mcp-server). No direct filesystem writes.
    MCP health check required before writing: if MCP unreachable, abort with error — do not partially write.
    Multi-agent coordination: check _agent-status.md before write, use _draft suffix during write, rename on completion.
    _index.md must be updated after every save run.
    Vault path: /home/ikktaa/Documents/DevVault/Projects/
  </context>
  <action>
    Use /skill-creator to create the save-to-vault skill.
    The skill must:
    1. Accept note drafts (output from analyze-project) as input.
    2. Verify MCP connection before any write. Abort if unreachable.
    3. Check Projects/_agent-status.md — register task before writing.
    4. For each note: write with _draft suffix first, then rename to final name on success.
    5. Write project index (index.md) and all feature notes to Projects/<ProjectName>/.
    6. Update Projects/_index.md with new project row.
    7. Update _agent-status.md on completion.

    After skill is created, test it by running analyze-project on the same test repo from 2.1,
    then piping drafts into save-to-vault. Verify files appear in vault.
  </action>
  <verify>
    - .claude/skills/save-to-vault.md exists
    - Skill created via /skill-creator
    - Test run writes notes to vault via MCP (not direct filesystem)
    - _index.md updated with test project row
    - _agent-status.md updated
    - No partial writes (draft → rename pattern confirmed)
  </verify>
  <status>done</status>
</task>

<task id="2.3" complexity="standard">
  <context>
    Depends on: task 2.2 (vault must have at least one indexed project — IWantJob from Phase 1 qualifies).
    Skill creation rule: use /skill-creator only.
    This skill is READ-ONLY — no vault writes.
    Reads vault note via MCP, opens referenced raw file from source repo, produces adaptation plan.
    After this task: create AGENTS.md documenting all 3 skills for Codex access.
    Skill location for Codex: referenced in AGENTS.md at repo root.
  </context>
  <action>
    Use /skill-creator to create the retrieve-feature skill.
    The skill must:
    1. Accept project name + feature name as input (e.g., "IWantJob llm-routing").
    2. Read the vault note via MCP (obsidian tool).
    3. Open the first file listed in the note's files[] frontmatter from the source repo.
    4. Produce a structured output: note summary + raw code excerpt + adaptation plan (what to copy, what to change for a new project).
    5. Read-only — no vault writes.

    After skill is created, test it: retrieve-feature IWantJob llm-routing.
    Verify output includes note content + raw ai_service.py excerpt + adaptation steps.

    Then create AGENTS.md at repo root (/home/ikktaa/app/DevVault/AGENTS.md) documenting:
    - All 3 skills and their purpose
    - How Codex can invoke them
    - Vault path and MCP connection requirement
  </action>
  <verify>
    - .claude/skills/retrieve-feature.md exists
    - Skill created via /skill-creator
    - Test retrieval of IWantJob/llm-routing returns note + raw code + adaptation plan
    - AGENTS.md exists at repo root with all 3 skills documented
    - No vault writes during test run
  </verify>
  <status>done</status>
</task>

---

## Dependencies
- 2.1 is independent — start here
- 2.2 depends on 2.1 (needs valid draft output to test)
- 2.3 is independent of 2.2 (IWantJob vault notes from Phase 1 are sufficient for testing)

## Commit checkpoint
Phase 2 completion: `feat(phase-2): add analyze-project, save-to-vault, retrieve-feature skills`
Scope: .claude/skills/ (3 files) + AGENTS.md
