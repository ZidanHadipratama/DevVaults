---
name: save-to-vault
description: Saves analyze-project note drafts to the DevVault Obsidian vault via MCP (obsidian-mcp-server). Use this skill whenever the user wants to save, write, or persist note drafts to the vault, says things like "save to vault", "write to vault", "persist these notes", "save [ProjectName] to DevVault", or "save the drafts". Always use this skill instead of writing vault files directly — it enforces the draft→rename safety pattern and keeps projects.md and status.md in sync. Requires analyze-project output (or equivalent note drafts) as input.
---

# Save to Vault

You are writing analyze-project note drafts into the DevVault Obsidian vault via MCP. Safety and consistency matter: always use the draft→rename pattern, always verify MCP is reachable before writing anything, and always update the vault's index files.

## What you need before starting

- **Project name**: the canonical name for the project (e.g., "IWantJob", "TA", "JobPilot")
- **Note drafts**: one project index draft + N feature note drafts, produced by `analyze-project` (or provided inline by the user)

If drafts are missing, tell the user to run `/analyze-project <repo-path>` first and pipe the output here.

---

## Step 1 — MCP Health Check

Read `Projects/status.md` using the obsidian MCP tool (check available MCP tools at invocation time — look for tools named like `obsidian_read_file`, `read_file`, or equivalent).

If the call **fails or returns an error**, stop immediately:

> **MCP unreachable.** Ensure Obsidian is running with the Local REST API plugin enabled.
> Aborting — no files written.

Do not proceed past this step if MCP is unreachable. Partial writes are worse than no writes.

---

## Step 2 — Register Task

Read `Projects/status.md`, append a new table row, write back via MCP:

```
| <ProjectName> | save-to-vault | IN PROGRESS | <ISO timestamp> |
```

The table header is:
```
| Project | Task | Status | Timestamp |
|---------|------|--------|-----------|
```

This prevents two agents from writing the same project simultaneously.

---

## Step 3 — Write Feature Notes (draft → rename)

For each feature note draft:

1. Derive the **feature slug**: lowercase the feature name, replace spaces and slashes with hyphens (e.g., "LLM Routing" → `llm-routing`, "Auth / BYOK" → `auth-byok`)
2. Write the draft to `Projects/<ProjectName>/<feature-slug>_draft.md` via MCP
3. On success, rename/overwrite to `Projects/<ProjectName>/<feature-slug>.md` via MCP
4. If a write fails: log it (`⚠ Skipped: <feature-slug> — <error>`) and continue with remaining notes. Do not abort the whole run.

The draft suffix exists so that if a rename fails mid-run, the vault never contains a partial file at the final path.

---

## Step 4 — Write Project File (draft → rename)

The project's main file is named after the project itself, not `index.md`.

1. Write `Projects/<ProjectName>/<ProjectName>_draft.md` via MCP
2. On success, rename/overwrite to `Projects/<ProjectName>/<ProjectName>.md` via MCP

---

## Step 5 — Update projects.md

Read `Projects/projects.md` via MCP. Append a new row for this project:

```
| [[Projects/<ProjectName>/<ProjectName>\|<ProjectName>]] | <stack summary from index draft> | <feature count> | <today's date YYYY-MM-DD> |
```

Write the updated content back via MCP.

If `projects.md` doesn't contain a table header yet, append both the header and the row:

```markdown
| Project | Stack | Features | Last Indexed |
|---------|-------|----------|--------------|
| [[Projects/<ProjectName>/<ProjectName>\|<ProjectName>]] | <stack> | <N> | <date> |
```

---

## Step 6 — Complete Registration

Update `Projects/status.md` via MCP — change the IN PROGRESS row to DONE:

```
| <ProjectName> | save-to-vault | DONE | <ISO timestamp> |
```

Read the current content first, replace the IN PROGRESS row, then write back.

---

## Step 7 — Output Summary

Print a structured summary:

```
## save-to-vault complete — <ProjectName>

**Written:**
- Projects/<ProjectName>/<ProjectName>.md
- Projects/<ProjectName>/<feature-slug>.md  (×N)

**projects.md:** row added ✓

**Skipped:** (none)  ← or list skipped files with reason

**status.md:** DONE ✓
```

---

## Constraints

- **MCP only.** Never use the `Write` tool or filesystem paths. All writes go through obsidian MCP tools.
- **Draft → rename is mandatory.** Write `<name>_draft.md` first, then rename. Never write directly to the final path.
- **Abort on MCP failure at Step 1.** If health check fails, stop with clear error message. No partial state.
- **Partial failures are OK after Step 2.** If individual note writes fail, log and skip — don't abort the whole run.
- **Feature slug derivation**: lowercase, spaces → hyphens, strip special chars (e.g., `auth/byok` → `auth-byok`).
- **Vault paths are relative to Obsidian vault root** — obsidian-mcp-server resolves absolute paths internally. Use `Projects/` prefix, not the full filesystem path.

## MCP Tool Discovery

At invocation time, check which obsidian MCP tools are available. Common names:
- `obsidian_read_file` or `read_file` — reads a vault file by relative path
- `obsidian_write_file` or `write_file` — writes/overwrites a vault file
- `obsidian_rename_file` or `move_file` — renames or moves a vault file

If rename isn't available, simulate it: write to final path, then delete the draft. If delete isn't available, just leave the `_draft` file — it's inert.

**Escaping warning:** Obsidian note content often contains `[[link\|alias]]` syntax with backslashes. When building write payloads, always write content to a temp file and use `--data-binary @file` rather than passing content as a shell variable — backslash sequences get mangled in bash variable expansion.
