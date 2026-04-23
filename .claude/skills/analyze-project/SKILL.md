---
name: analyze-project
description: Scans a code repository and produces structured knowledge-base note drafts (in-conversation, no disk writes) for the DevVault personal KMS. Use this skill whenever the user wants to index a project, analyze a codebase for reuse, extract features from a repo, scan a project for vault notes, or says things like "analyze [repo]", "index [project]", "extract features from [path]", "scan [codebase] for vault", or "what features does [project] have". Output feeds into the save-to-vault skill. Always use this skill instead of doing an ad-hoc codebase scan when the goal is to produce structured, reusable knowledge-base notes.
---

# Analyze Project

You are producing structured knowledge-base note drafts from a code repository. These drafts will be saved to an Obsidian vault via the `save-to-vault` skill — so accuracy and reusability matter more than speed.

## What you produce

For every project you analyze, output **in-conversation** (never write to disk):

1. **One project index** (PRD 5.2 schema) — a bird's-eye view of the whole project
2. **3–6 feature notes** (PRD 5.3 schema) — one per distinct, reusable feature

The goal is for another developer (or AI agent) to read these notes and know exactly what to copy, what to change, and what to watch out for when reusing the feature in a new project.

## Step 1: Orient yourself

Read these files first (they're fast, high-signal):
- `README.md` or `docs/` overview
- Root config files (`package.json`, `pyproject.toml`, `requirements.txt`, `docker-compose.yml`)
- Top-level directory listing

From this alone you should be able to identify the tech stack and the rough shape of the project. Don't read source files yet.

## Step 2: Identify features

A "feature" is a self-contained capability a developer might want to copy into a new project. Good features:
- Have a clear name and boundary ("auth", "pdf-export", "llm-routing")
- Span 1–5 source files
- Would be meaningful to someone reading the vault 6 months later

Aim for 3–6 features. If the project is small, 3 is fine. Don't invent features — identify what's actually there.

List the features you plan to cover before reading any source files. This prevents rabbit-holing.

## Step 3: Write feature notes one at a time

For each feature:
1. Read ≤5 source files. Choose the files that best explain the feature's structure and behavior — entry points, core logic, key schemas.
2. Write the feature note draft immediately. Don't read more files first.

The ≤5 file limit exists because the best notes come from focused reading, not exhaustive scanning. If you can't explain a feature from 5 files, you're probably trying to cover too much. Split it or narrow the scope.

If you've read 5 files and feel the picture is incomplete, stop and write what you know. Mark unclear parts with `(unclear — needs deeper read)` rather than reading more.

## Step 4: Write the project index last

After all feature notes are drafted, write the project index. It should synthesize what you learned — don't just restate the feature list.

---

## Output schemas

### Feature note (PRD 5.3)

Output each feature note as a fenced markdown block:

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

### Project index (PRD 5.2)

```markdown
---
project: <ProjectName>
stack: [<Framework>, <Database>, <Language>, ...]
repo: <local path or github url>
last_indexed: <YYYY-MM-DD>
---

# <ProjectName>

## Summary
1–2 paragraphs. What the project does, who it's for, the core technical approach.

## Features
- [[<ProjectName>/<feature-slug>]] — one-line description
- [[<ProjectName>/<feature-slug>]] — one-line description

## Architecture Notes
How the pieces connect. Key design decisions. Anything that would surprise a developer picking up the project cold.
```

---

## Quality bar

A good note lets a developer (or AI agent) answer these questions without reading the source code:
- What does this feature do?
- Which files do I copy?
- What are the non-obvious traps?
- What do I need to change for my project?

If the draft doesn't answer all four, add more detail to Reuse Notes and the Adaptation Checklist.

## What NOT to do

- Don't write line numbers — they rot immediately as code changes
- Don't read source files before listing your planned features
- Don't write to disk — all output is in-conversation markdown
- Don't exceed 5 source files per feature — split instead
