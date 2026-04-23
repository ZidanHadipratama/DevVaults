# DevVault — Contracts

## Schema Contracts

### Note schema (feature note)
All feature notes MUST include:
- YAML frontmatter: `project`, `feature`, `type`, `files[]`, `tags[]`, `last_indexed`
- Sections: What it does, Key Components (table), Workflow (diagram), Reuse Notes, Adaptation Checklist
- References: function/class names only — NEVER line numbers

### Note schema (project index)
All project index notes MUST include:
- YAML frontmatter: `project`, `stack[]`, `repo`, `last_indexed`
- Sections: Summary, Features (wikilinks to feature notes), Architecture Notes
- Final vault path: `Projects/<ProjectName>/<ProjectName>.md` (do not use `index.md`)

### Global index (`projects.md`)
Must stay updated after every `/save-to-vault` run:
`| Project | Stack | Features | Last Indexed |`

---

## Skill Contracts

### Creation rule
All new DevVault skills MUST start from `skill-creator` scaffolds. Final tracked skill files may then be customized for the target agent.

### Skill file location
- Claude Code: `.claude/skills/`
- Codex tracked copies: `codex-skills/`
- Codex installed copies: `~/.codex/skills/`

### Skill behavior
- `/analyze-project` — read-only, no writes. Input: repo path. Output: note drafts (in-conversation).
- `/save-to-vault` — writes via MCP only, never direct filesystem. Input: note drafts. Requires MCP health check first.
- `/retrieve-feature` — read-only, no writes. Input: project name + feature name. Output: note summary + raw code + adaptation plan.

### Skill I/O contracts
- `analyze-project` output feeds directly into `save-to-vault` input — same session or copy-paste between sessions.
- `save-to-vault` must check `Projects/status.md` before any write. Write order: `_draft` → final path.
- `retrieve-feature` reads from vault via MCP + opens raw file from source repo path in note frontmatter.
- Tracked Codex skill copies under `codex-skills/` should match the installed copies under `~/.codex/skills/` for the same skill names.

---

## MCP Contracts

### Server
- Package: `obsidian-mcp-server` (npx, cyanheads)
- Config: `~/.claude.json` (user-level)
- Obsidian must be running for MCP to work

### Health check
Skills must verify MCP connection before writing. If unreachable → abort with clear error, do not partially write.

### Base URL
Current MCP base URL is `https://127.0.0.1:27124/`.

---

## Multi-Agent Contracts

### Write coordination
- Check `Projects/status.md` before any vault write
- Register task in status table before starting
- Use `_draft` suffix on note filename during write, rename on completion
- Update status table on completion
- Project index notes finalize at `Projects/<ProjectName>/<ProjectName>.md`

### Ownership
- Claude Code owns the original Claude skill set under `.claude/skills/`
- Codex owns the Codex skill set under `codex-skills/` and installed copies under `~/.codex/skills/`
- Both agents can read the vault
- Vault writes remain serialized per note even if both agents are active

---

## Frugent Contracts

### Skill creation
Use `skill-creator` scaffolds for all new skill folders. Document the resulting skill work in `docs/log.md`.

### Log entries
After every task: append `[progress]` to `docs/log.md`
On failure (3 attempts): append `[blocker]` to `docs/log.md`
Out-of-scope ideas: append `[suggestion]` to `docs/log.md`
