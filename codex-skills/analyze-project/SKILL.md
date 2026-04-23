---
name: analyze-project
description: Analyze a software repository and draft DevVault-ready Markdown notes for later vault storage. Use when the user wants to index a project, scan a repo for reusable features, extract structured implementation notes, or prepare project and feature drafts for save-to-vault. Keep the work read-only and return the notes in conversation.
---

# Analyze Project

Produce DevVault note drafts from a repository without writing to disk.

## Workflow

1. Orient on the repository before reading source files.
2. Identify 3-6 reusable features before deep inspection.
3. Read only the most explanatory files for one feature at a time.
4. Draft each feature note immediately after reading it.
5. Draft the project index last.

## Step 1: Orient

Read the fast, high-signal files first:
- `README.md` and top-level docs
- root config such as `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`
- top-level directory listing

Do not start with a deep source walk. First determine the stack and rough system shape.

## Step 2: Choose Features

A feature should be a reusable capability with a clear boundary, such as auth, LLM routing, export, sync, or payments.

Before reading source files, list the features you plan to cover. Aim for 3-6 features. If a feature would need more than about 5 files to explain, narrow it or split it.

## Step 3: Read and Draft Per Feature

For each feature:
- Read no more than about 5 files.
- Favor entry points, core logic, schemas, and one integration file.
- Write the note as soon as the feature is understandable.
- If something remains fuzzy after 5 files, mark it as unclear instead of continuing to dig.

Focus on reuse value:
- what the feature does
- which files matter
- what can be copied
- what must change
- what can go wrong

## Feature Note Format

Return each feature note as a fenced Markdown block using this structure:

```markdown
---
project: <ProjectName>
feature: <feature-slug>
type: implementation
files:
  - <path/to/file1>
  - <path/to/file2>
tags: [<tag1>, <tag2>]
last_indexed: <YYYY-MM-DD>
---

# <Feature Name> - <ProjectName>

## What it does
One concise paragraph describing the capability and its behavior.

## Key Components

### `<ClassName>` / `<filename>`
**File:** `<path>`

| Function / Class | Responsibility |
|-----------------|----------------|
| `fn_name(args)` | What it does |

## Workflow

```text
Step 1: ...
  -> Step 2: ...
  -> Step 3: result
```

## Reuse Notes
- What to copy as-is
- **Gotcha:** Non-obvious trap
- External deps, config, env vars, or assumptions

## Adaptation Checklist
- [ ] Copy the core files
- [ ] Replace project-specific wiring
- [ ] Wire dependencies or config
- [ ] Re-check the gotchas
```

## Project Index Format

After finishing all feature notes, return one fenced Markdown block:

```markdown
---
project: <ProjectName>
stack: [<Framework>, <Database>, <Language>]
repo: <local path or URL>
last_indexed: <YYYY-MM-DD>
---

# <ProjectName>

## Summary
1-2 paragraphs on what the project does and how it is built.

## Features
- [[<ProjectName>/<feature-slug>]] - one-line description

## Architecture Notes
How the pieces connect, plus any surprising design choices.
```

## Constraints

- Stay read-only.
- Do not write files.
- Do not use line numbers in notes.
- Prefer concrete identifiers over vague summaries.
- Optimize for future reuse, not exhaustive documentation.
