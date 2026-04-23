---
name: retrieve-feature
description: Retrieves a DevVault feature note from the Obsidian vault via MCP, reads the referenced raw source file from the original repo, and produces a structured adaptation plan for reuse. Use this skill whenever the user wants to look up a feature from a past project, retrieve code from the vault, get an adaptation plan for reusing existing code, or says things like "retrieve [feature] from [project]", "show me [project] [feature]", "how does [project] handle [feature]", "get [feature] from vault", or "I want to reuse the [feature] from [project]". Always use this skill instead of manually reading vault notes — it synthesizes note + raw code + adaptation guidance in one step.
---

# Retrieve Feature

You are retrieving a feature from the DevVault knowledge base and producing a ready-to-use adaptation plan. The goal is to give the developer everything they need to reuse the feature in a new project — note summary, raw code, and concrete guidance — without them having to dig through files themselves.

## Input format

The user invokes this with a project name and feature name:
- `IWantJob llm-routing`
- `TA ai-chain-of-thought-pipeline`
- `IWantJob auth-byok /home/user/repos/IWantJob` ← optional repo path as third arg

---

## Step 1 — Read the vault note

Read `Projects/<ProjectName>/<feature-name>.md` from the Obsidian vault using the MCP obsidian tool (check available MCP tools at invocation time — look for tools named `obsidian_read_file`, `read_file`, or equivalent).

If the file doesn't exist:
> "Note not found: `Projects/<ProjectName>/<feature-name>.md`. Run `/analyze-project <repo-path>` first, then `/save-to-vault <ProjectName>` to index it."

---

## Step 2 — Parse frontmatter

Extract from the YAML frontmatter block:
- `files[]` — source file paths, relative to repo root
- `project` — project name
- `feature` — feature slug
- `repo:` — source repo path (if present)

Then resolve the repo path using this priority:
1. Third argument from user (if provided)
2. `repo:` field in frontmatter
3. Ask: "Where is the `<ProjectName>` repo on your machine?"

---

## Step 3 — Read the primary source file

Read the **first file** from `files[]` using the resolved repo path + the relative path from frontmatter. Use the `Read` tool (local filesystem, not MCP).

If the file isn't found, show the full `files[]` list and say: "File not found at `<path>`. Here are all files listed in this note — check if the repo is at a different path."

If `files[]` is empty: skip this step and note it in the output.

---

## Step 4 — Produce the adaptation report

Output using this structure:

---

## Feature: `<feature-name>` — <ProjectName>

### Summary
<The "What it does" section from the vault note — verbatim>

### Key Components
<The "Key Components" section from the vault note — verbatim>

### Raw Code: `<repo-relative/path/to/file>`
```<language>
<code excerpt>
```
*For files ≤150 lines: show the full file. For larger files: show all imports + top-level class/function signatures + the 40–60 most important lines (the core logic, not boilerplate). Annotate which section you're showing if you excerpted.*

### Reuse Notes
<The "Reuse Notes" section from the vault note — verbatim>

### Adaptation Plan
Based on the checklist in the note and the raw code above:

**Copy as-is:**
- <what can be taken verbatim — be specific, name the classes/functions>

**Must change:**
- <what requires modification and why — reference actual code identifiers>

**Watch out for:**
- <gotchas from Reuse Notes, expanded with evidence from the code>

**Estimated effort:** <trivial | half-day | 1–2 days | major>

---

The adaptation plan should go beyond restating the checklist — use what you see in the raw code to make it concrete. If you saw a hardcoded config value, say so. If a class has tight coupling to a specific DB client, name it.

---

## Step 5 — Offer follow-up

After the report, offer:
> "Want to see another file from this feature? Files listed: `<files[]>`"

If the user says yes or names a file, read it with the `Read` tool and append it to the report.

---

## Constraints

- Vault reads: MCP tool only
- Source file reads: `Read` tool only (local filesystem)
- No vault writes of any kind
- Read-only throughout
