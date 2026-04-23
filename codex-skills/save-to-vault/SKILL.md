---
name: save-to-vault
description: Persist DevVault project and feature note drafts into the Obsidian vault through MCP. Use when the user wants to save drafts, write analyzed notes into the vault, register a project in DevVault, or persist analyze-project output. Enforce an MCP health check, status registration, draft-to-final writes, and updates to projects.md and status.md.
---

# Save to Vault

Write DevVault drafts into the Obsidian vault safely and consistently.

## Required Inputs

- Project name
- One project index draft
- One or more feature note drafts

If the drafts are missing, stop and ask the user to run `$analyze-project` first or provide equivalent drafts.

## Tooling Rule

Use Obsidian MCP capabilities only for vault operations. Prefer write-capable MCP tools exposed in the session. If the session only exposes read-only MCP access, stop and tell the user that vault writes are unavailable in the current Codex session.

## Workflow

1. Verify MCP access before any write.
2. Register an `IN PROGRESS` row in `Projects/status.md`.
3. Write every feature note to a `_draft.md` path, then promote it to the final path.
4. Write the project index using the same draft-to-final pattern.
5. Update `Projects/projects.md`.
6. Mark the status row `DONE`.

## Step 1: MCP Health Check

Read `Projects/status.md` through the Obsidian MCP connection. If that fails, abort without writing anything and tell the user that Obsidian or the Local REST API connection is unavailable.

## Step 2: Register the Write

Add a row in `Projects/status.md`:

```text
| <ProjectName> | save-to-vault | IN PROGRESS | <ISO timestamp> |
```

If the table header is missing, create it first:

```text
| Project | Task | Status | Timestamp |
|---------|------|--------|-----------|
```

## Step 3: Write Feature Notes

For each feature note:
- derive the slug from the note frontmatter or title
- normalize to lowercase hyphen-case
- write `Projects/<ProjectName>/<feature-slug>_draft.md`
- then rename or move it to `Projects/<ProjectName>/<feature-slug>.md`

If a single feature write fails after registration, continue with the remaining notes and record the skipped file in the final summary.

## Step 4: Write Project Index

Write:

```text
Projects/<ProjectName>/<ProjectName>_draft.md
```

Then promote it to:

```text
Projects/<ProjectName>/<ProjectName>.md
```

Do not use `index.md`.

## Step 5: Update `projects.md`

Append a row to `Projects/projects.md`:

```text
| [[Projects/<ProjectName>/<ProjectName>|<ProjectName>]] | <stack summary> | <feature count> | <YYYY-MM-DD> |
```

If the table does not exist yet, add this header first:

```text
| Project | Stack | Features | Last Indexed |
|---------|-------|----------|--------------|
```

## Step 6: Mark Complete

Replace the in-progress row in `Projects/status.md` with:

```text
| <ProjectName> | save-to-vault | DONE | <ISO timestamp> |
```

## Final Output

Return a short structured summary:
- written files
- skipped files, if any
- whether `projects.md` was updated
- whether `status.md` was marked `DONE`

## Constraints

- Never write directly to final note paths first.
- Abort immediately if the initial MCP health check fails.
- Use vault-relative paths under `Projects/`.
- Do not fall back to direct filesystem writes into the vault.
