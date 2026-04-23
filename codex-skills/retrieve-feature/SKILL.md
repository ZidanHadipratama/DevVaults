---
name: retrieve-feature
description: Retrieve a DevVault feature note from the Obsidian vault, reopen the referenced source code from the original repository, and produce a concrete adaptation plan. Use when the user wants to look up how an older project implemented a feature, reuse code from a past project, inspect a stored vault note, or fetch a feature summary plus raw code for adaptation.
---

# Retrieve Feature

Read a stored DevVault feature note, inspect the referenced source file, and turn that into practical reuse guidance.

## Workflow

1. Read the feature note from the vault through MCP.
2. Parse its frontmatter for `files`, `project`, `feature`, and `repo`.
3. Resolve the repository path.
4. Read the primary source file from disk.
5. Produce a summary, code excerpt, and adaptation plan.

## Step 1: Read the Note

Read:

```text
Projects/<ProjectName>/<feature-name>.md
```

Use Obsidian MCP access, preferably via MCP resources or write/read tools exposed in the session.

If the note does not exist, say:

```text
Note not found: Projects/<ProjectName>/<feature-name>.md
Run $analyze-project on the repo, then $save-to-vault <ProjectName> to index it.
```

## Step 2: Parse Frontmatter

Extract:
- `files[]`
- `project`
- `feature`
- `repo`

Resolve the repo path with this priority:
1. path explicitly supplied by the user
2. `repo:` in frontmatter
3. ask the user for the local repo path

## Step 3: Read the Primary Source File

Read the first path in `files[]` from the resolved repository root.

If the file is missing:
- report the full attempted path
- show the full `files[]` list
- ask the user to confirm the repo location

If `files[]` is empty, continue without source code and say so explicitly.

## Output Format

Use this structure:

```markdown
## Feature: `<feature-name>` - <ProjectName>

### Summary
<What it does section, quoted or closely summarized>

### Key Components
<Key Components section, preserved or closely summarized>

### Raw Code: `<repo-relative/path>`
```<language>
<full file if short, otherwise imports + signatures + the core logic excerpt>
```

### Reuse Notes
<Reuse Notes section, preserved or closely summarized>

### Adaptation Plan
**Copy as-is:**
- ...

**Must change:**
- ...

**Watch out for:**
- ...

**Estimated effort:** trivial | half-day | 1-2 days | major
```

For large files, prefer imports, top-level signatures, and roughly 40-60 important lines rather than boilerplate.

## Follow-up

After the report, offer the remaining files from `files[]` and read more if the user asks.

## Constraints

- Stay read-only.
- Read vault content through MCP, not by directly opening the vault on disk.
- Read source code from the local repository, not from the vault note.
- Make the adaptation plan concrete by naming actual files, functions, classes, configs, or external services.
