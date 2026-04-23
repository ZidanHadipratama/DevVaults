---
name: analyze-feature
description: Deep-dives into ONE specific feature from a code repository and produces a single feature note draft (PRD 5.3 schema) in-conversation. Use this skill whenever the user wants to generate a vault note for a specific feature, says things like "analyze the auth feature in [repo]", "extract [feature] from [project]", "deep dive into [feature-name]", "get the feature note for [slug]", or follows up after analyze-project with a feature from the candidate list. This is the second step in the DevVault pipeline: analyze-project produces the project overview + feature list, then analyze-feature is called once per feature to generate the full note. Output feeds directly into save-to-vault.
---

# Analyze Feature

You are producing a single, reusable feature note from a code repository. This note will be saved to an Obsidian vault and read by developers (or AI agents) months later who need to understand and reuse this feature in a new project.

Your audience is someone who has never seen this codebase. They need to know: what does this do, which files matter, what are the traps, and what do I change when I copy it?

## Inputs

You will receive:
- **ProjectName** — the vault project name (e.g., `IWantJob`)
- **feature-slug** — the feature identifier (e.g., `llm-routing`, `auth`, `pdf-export`)
- **repo path** — absolute path to the repository (e.g., `/home/user/app/IWantJobPrivate`)

If the repo path is missing, ask for it before proceeding.

---

## Step 1: Locate the feature

Before reading any files:
1. List the repo's top-level directory structure
2. Identify which directories/files are most likely to contain this feature
3. Briefly scan 1–2 entry point files (e.g., routes, controllers, service files) to confirm the feature exists and get your bearings

Don't start the deep read yet. Just locate it.

---

## Step 2: Read up to 5 files

Choose the ≤5 files that best explain this feature's structure and behavior:
- Entry point (the file that kicks off the feature)
- Core logic (the meat of the implementation)
- Key schema or data model (if applicable)
- Config or wiring (how it's registered/connected)
- Tests (if they exist and reveal intent better than the source)

Read them in order of importance. If you get a clear picture from 3 files, stop at 3 — don't read more for the sake of it.

If after 5 files the picture is still incomplete, write what you know and mark unclear parts with `(unclear — needs deeper read)`. Don't read more files.

---

## Step 3: Write the feature note

Use the PRD 5.3 schema below. Write for reuse, not documentation — the goal is for someone to copy this feature into a new project with confidence.

Good notes are specific about traps. If there's a non-obvious gotcha (a config value that must match a header, a race condition, a dependency that's easy to miss), call it out explicitly. Vague notes ("make sure to configure properly") are useless. Specific notes ("OBSIDIAN_BASE_URL must use HTTPS even on localhost — the plugin rejects HTTP") save real time.

---

## Output schema (PRD 5.3)

Output the feature note as a fenced markdown block:

```markdown
---
project: <ProjectName>
feature: <feature-slug>
type: implementation
files:
  - <path/to/file1>
  - <path/to/file2>
tags: [<tag1>, <tag2>, ...]
last_indexed: <YYYY-MM-DD>
---

# <Feature Name> — <ProjectName>

## What it does
One paragraph. What problem it solves, what it produces, key behavior.

## Key Components

### `<ClassName>` / `<filename>`
**File:** `<path>`

| Function / Class | Responsibility |
|-----------------|----------------|
| `fn_name(args)` | What it does |

(Repeat for each major component)

## Workflow

\`\`\`
Step 1: ...
  → Step 2: ...
  → Step 3: result
\`\`\`

## Reuse Notes
- What to copy as-is
- **Gotcha:** A non-obvious trap that will waste time if missed
- **Gotcha:** Another one
- Any external deps, env vars, or config required

## Adaptation Checklist
When reusing in a new project:
- [ ] Copy X from Y
- [ ] Replace Z with your own implementation
- [ ] Wire dependency A
- [ ] Watch out for gotcha B
```

---

## Quality bar

Before finishing, check: can someone read this note and answer all four questions without opening the source code?

1. What does this feature do?
2. Which files do I copy?
3. What are the non-obvious traps?
4. What do I need to change for my project?

If any answer is vague or missing, add more detail to Reuse Notes and Adaptation Checklist.

---

## What NOT to do

- Don't write line numbers — they rot as code changes
- Don't exceed 5 source file reads — split into two features if needed
- Don't write to disk — output is in-conversation markdown only
- Don't summarize the whole project — stay focused on this one feature
