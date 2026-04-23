---
name: analyze-project
description: Scans a code repository and produces a project overview + feature candidate list (in-conversation, no disk writes) for the DevVault personal KMS. Use this skill whenever the user wants to index a project, get a project overview, or says things like "analyze [repo]", "index [project]", "scan [codebase] for vault". Output feeds into analyze-feature (per feature deep dive) and then save-to-vault. Does NOT produce full feature notes — use analyze-feature for that.
---

# Analyze Project

You are producing a lightweight project overview and feature candidate list from a code repository. The goal is to map the project quickly — not to deep-dive into any feature yet.

Full feature notes come from running `/analyze-feature` on each candidate. This skill only sets up that pipeline.

## What you produce

Output **in-conversation** (never write to disk):

1. **One project index draft** (PRD 5.2 schema) — bird's-eye view of the whole project
2. **Feature candidate list** — 3–6 features with name + 1-line description each

Do NOT write feature notes here. That's `/analyze-feature`'s job.

---

## Step 1: Orient yourself

Read these files first (fast, high-signal):
- `README.md` or `docs/` overview
- Root config files (`package.json`, `pyproject.toml`, `requirements.txt`, `docker-compose.yml`)
- Top-level directory listing

From this alone identify tech stack and rough project shape. Don't read source files yet.

---

## Step 2: Identify feature candidates

A "feature" is a self-contained capability a developer might want to copy into a new project. Good features:
- Have a clear name and boundary ("auth", "pdf-export", "llm-routing")
- Span 1–5 source files
- Would be meaningful to someone reading the vault 6 months later

Aim for 3–6 features. Don't invent features — identify what's actually there.

For each candidate, briefly scan (1–2 files max) to confirm it exists and get a one-line description. Do not do a full read.

---

## Step 3: Write the project index

Write the project index using the PRD 5.2 schema below. Synthesize what you learned from Step 1 — don't just restate the feature list.

---

## Output schemas

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
- `<feature-slug>` — one-line description
- `<feature-slug>` — one-line description

## Architecture Notes
How the pieces connect. Key design decisions. Anything that would surprise a developer picking up the project cold.
```

### Feature candidate list

After the project index, output:

```
## Feature Candidates

To generate full notes for each feature, run:
  /analyze-feature <ProjectName> <feature-slug> <repo-path>

| Feature | Description | Key files (rough) |
|---------|-------------|-------------------|
| `auth` | JWT-based auth with refresh tokens | `auth/`, `middleware/auth.py` |
| `llm-routing` | Routes prompts to model based on task type | `services/llm.py` |
```

---

## What NOT to do

- Don't read more than 2 files per feature candidate in this step
- Don't write full feature notes — that's `/analyze-feature`
- Don't write to disk — all output is in-conversation markdown
- Don't exceed 10 total file reads across the whole skill run
