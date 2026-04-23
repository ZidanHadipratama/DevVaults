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

### Global index (`_index.md`)
Must stay updated after every `/save-to-vault` run:
`| Project | Stack | Last Indexed | Features |`

---

## Skill Contracts

### Creation rule
All skills MUST be created via `/skill-creator` in Claude Code. No hand-written skill `.md` files.

### Skill file location
- Claude Code: `.claude/skills/`
- Codex: referenced in `AGENTS.md`
- Global access: symlink `~/.devvault/skills` → `.claude/skills`

### Skill behavior
- `/analyze-project` — read-only, no writes. Input: repo path. Output: note drafts (in-conversation).
- `/save-to-vault` — writes via MCP only, never direct filesystem. Input: note drafts. Requires MCP health check first.
- `/retrieve-feature` — read-only, no writes. Input: project name + feature name. Output: note summary + raw code + adaptation plan.

### Skill I/O contracts
- `analyze-project` output feeds directly into `save-to-vault` input — same session or copy-paste between sessions.
- `save-to-vault` must check `_agent-status.md` before any write. Write order: _draft → rename.
- `retrieve-feature` reads from vault via MCP + opens raw file from source repo path in note frontmatter.

---

## MCP Contracts

### Server
- Package: `obsidian-mcp-server` (npx, cyanheads)
- Config: `~/.claude.json` (user-level)
- Obsidian must be running for MCP to work

### Health check
Skills must verify MCP connection before writing. If unreachable → abort with clear error, do not partially write.

---

## Multi-Agent Contracts

### Write coordination
- Check `Projects/_agent-status.md` before any vault write
- Register task in status table before starting
- Use `_draft` suffix on note filename during write, rename on completion
- Update status table on completion

### Ownership
- Claude Code owns vault writes (analyze + save)
- Codex owns vault reads + implementation (retrieve)
- Both can read; only one writes per note at a time

---

## Frugent Contracts

### Skill creation
Use `/skill-creator` for all skill files. Document in log.md after each skill created.

### Log entries
After every task: append `[progress]` to `docs/log.md`
On failure (3 attempts): append `[blocker]` to `docs/log.md`
Out-of-scope ideas: append `[suggestion]` to `docs/log.md`
