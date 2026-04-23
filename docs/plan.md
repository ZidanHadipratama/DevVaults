# DevVault — Active Phase Plan

**Active phase:** Phase 3 — Codex Skills & Multi-Agent Alignment
**Last updated:** 2026-04-23
**Planner:** Claude Opus | **Executor:** Codex CLI | **Backup:** Claude Sonnet

---

<task id="3.1" complexity="standard">
  <context>
    Claude skills already exist in `.claude/skills/`.
    Codex skill scaffolds and installed copies now exist but are not yet reflected in the Frugent plan.
    The user explicitly wants Codex skills implemented using `skill-creator`.
    Codex runtime location is `~/.codex/skills/`.
    Repo-tracked Codex copies should live under `codex-skills/`.
  </context>
  <action>
    Normalize the three Codex skills from `skill-creator` scaffolds:
    1. `analyze-project`
    2. `save-to-vault`
    3. `retrieve-feature`
    Keep tracked copies in `codex-skills/`.
    Ensure installed copies exist in `~/.codex/skills/`.
    Ensure each skill has:
    - final `SKILL.md`
    - `agents/openai.yaml`
    - no placeholder TODO sections
  </action>
  <verify>
    - `codex-skills/analyze-project/SKILL.md` exists
    - `codex-skills/save-to-vault/SKILL.md` exists
    - `codex-skills/retrieve-feature/SKILL.md` exists
    - each skill has `agents/openai.yaml`
    - no TODO placeholders remain in any Codex skill
  </verify>
  <status>done</status>
</task>

<task id="3.2" complexity="standard">
  <context>
    Current planning docs still contain stale references to `_index.md`, `_agent-status.md`, `index.md`, and port `27123`.
    Current runtime conventions are `Projects/projects.md`, `Projects/status.md`, project main file `<ProjectName>.md`, and MCP base URL `https://127.0.0.1:27124/`.
    Codex skill locations must be represented alongside Claude skill locations.
  </context>
  <action>
    Update the planning surface and contracts:
    1. align `docs/contracts.md` with current vault naming
    2. add Codex skill locations and ownership rules
    3. refresh `docs/test-cases.md` so completed phases are marked accurately
    4. add Phase 3 test cases for Codex skill parity and validation
  </action>
  <verify>
    - no stale `_index.md` / `_agent-status.md` naming remains in `docs/contracts.md`
    - `docs/contracts.md` names `codex-skills/` and `~/.codex/skills/`
    - `docs/test-cases.md` includes Phase 3 Codex tasks
    - completed Phase 4 tests are marked `pass`
  </verify>
  <status>done</status>
</task>

<task id="3.3" complexity="standard">
  <context>
    Depends on: 3.1 + 3.2.
    The Codex skills are present but not yet recorded as a validated phase result.
    `quick_validate.py` is available through the `skill-creator` system skill, but this environment currently lacks Python `yaml`.
  </context>
  <action>
    Validate and checkpoint the Codex phase:
    1. verify Codex discovers the three installed skills
    2. record the validation path and any environment limitations
    3. prepare the phase checkpoint commit after the implementation and docs are aligned
  </action>
  <verify>
    - installed skill copies exist under `~/.codex/skills/`
    - validation notes mention the `quick_validate.py` dependency gap if still present
    - checkpoint commit message is ready: `feat(phase-3): add codex skills and align contracts`
  </verify>
  <status>done</status>
</task>

---

## Dependencies
- 3.1 is independent — start here
- 3.2 depends on 3.1
- 3.3 depends on 3.1 + 3.2

## Commit checkpoint
`feat(phase-3): add codex skills and align contracts`
