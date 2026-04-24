---
name: analyze-feature
description: Deep-dive into ONE specific feature from a code repository and produce a single PRD 5.3 feature note in-conversation. Use when the user wants a vault note for a specific feature, says "analyze the auth feature", "extract [feature] from [project]", or follows up after analyze-project with a feature from the candidate list. Second step in the DevVault pipeline after analyze-project. Output feeds into save-to-vault.
---

# Analyze Feature

Produce a single reusable feature note. Your reader is a developer who has never seen this codebase — they need to know what it does, which files to copy, what the traps are, and what to change.

## Inputs

- **ProjectName** — vault project name (e.g., `IWantJob`)
- **feature-slug** — feature identifier (e.g., `llm-routing`, `auth`)
- **repo path** — absolute path to the repository

## Step 1: Locate

List repo top-level. Scan 1–2 entry point files to confirm feature exists and get bearings. Don't deep-read yet.

## Step 2: Read ≤5 Files

Choose files that best explain structure and behavior:
- Entry point
- Core logic
- Key schema/model (if applicable)
- Config/wiring
- Tests (if they reveal intent better than source)

Stop at 3 if picture is clear. Mark unclear parts `(unclear — needs deeper read)` rather than reading more.

## Step 3: Write Feature Note (PRD 5.3)

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

# <Feature Name> — <ProjectName>

## What it does
One paragraph. Problem solved, output produced, key behavior.

## Key Components

### `<ClassName>` / `<filename>`
**File:** `<path>`

| Function / Class | Responsibility |
|-----------------|----------------|
| `fn(args)` | What it does |

## Workflow

```
Step 1: ...
  → Step 2: ...
  → result
```

## Reuse Notes
- What to copy as-is
- **Gotcha:** Specific non-obvious trap (vague gotchas are useless)
- External deps, env vars, config required

## Adaptation Checklist
- [ ] Copy X from Y
- [ ] Replace Z with your own
- [ ] Wire dependency A
- [ ] Watch out for gotcha B
```

## Quality Bar

Someone reading this note must answer without opening source code:
1. What does this feature do?
2. Which files to copy?
3. What are the non-obvious traps?
4. What to change for my project?

## Constraints

- Read-only. No disk writes. Output is in-conversation markdown only.
- Max 5 source file reads
- No line numbers
- Stay focused on this one feature — no project-wide summaries
