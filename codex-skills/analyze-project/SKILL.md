---
name: analyze-project
description: Scan a repository and produce a project overview + feature candidate list for DevVault. Use when the user wants a lightweight project map before deep-diving features, or wants to see what's indexable before committing to a full vault write. Output feeds into analyze-feature (one call per feature) then save-to-vault. Does NOT produce full feature notes — use analyze-feature or analyze-and-save for that.
---

# Analyze Project

Produce a lightweight project overview and feature candidate list. No deep feature reads. No disk writes.

## Step 1: Orient

Read these files (≤3 total):
- `README.md` or `docs/` overview
- Root config (`package.json`, `pyproject.toml`, `requirements.txt`, etc.)
- Top-level directory listing

Identify tech stack and rough project shape. Do not read source files yet.

## Step 2: Identify Feature Candidates

A feature: self-contained reusable capability with clear boundary, spans 1–5 files, meaningful 6 months later. Examples: auth, llm-routing, pdf-export, payments.

Briefly scan 1–2 files per candidate (entry points only) to confirm existence and get a one-line description. Do not deep-read.

Aim for 3–6 candidates.

## Step 3: Write Project Index

Use this schema:

```markdown
---
project: <ProjectName>
stack: [<Framework>, <Database>, <Language>]
repo: <local path or URL>
last_indexed: <YYYY-MM-DD>
---

# <ProjectName>

## Summary
1–2 paragraphs. What it does, who it's for, core approach.

## Features
- `<feature-slug>` — one-line description

## Architecture Notes
How pieces connect. Key design decisions. What would surprise someone picking it up cold.
```

## Step 4: Output Feature Candidate Table

```
## Feature Candidates

Run /analyze-feature <ProjectName> <slug> <repo-path> for each:

| Feature | Slug | Description | Key files (hint) |
|---------|------|-------------|-----------------|
| Auth | auth | ... | backend/app/dependencies.py |
```

## Constraints

- Read-only. No disk writes.
- Max 2 files scanned per feature candidate (location only, not deep read)
- Max 10 total file reads
- No line numbers in output
