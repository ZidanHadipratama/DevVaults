---
project: TA
feature: ai-chain-of-thought-pipeline
type: implementation
files:
  - backend/summarizer.py
  - backend/flow_generator.py
  - backend/main.py
tags: [ai, pipeline, gemini, chain-of-thought, fastapi, sequential-generation]
last_indexed: 2026-04-23
---

# AI Chain-of-Thought Pipeline — TA

## What it does
Sequential AI generation system: user Q&A feeds into a Business Summary (source of truth), which then drives downstream generation of Roles, Flows, Mermaid diagrams, SOPs, and Tasks. Each step uses the Summary as context, ensuring consistency across the full output graph.

## Key Components

### `summarizer.py`
**File:** `backend/summarizer.py`

| Function | Responsibility |
|----------|----------------|
| `generate_summary(qa_pairs)` | Converts Q&A list into structured Business Summary via Gemini. Summary becomes the anchor for all downstream generation. |

### `flow_generator.py`
**File:** `backend/flow_generator.py`

| Function | Responsibility |
|----------|----------------|
| `generate_flows(summary)` | Produces process Flow objects from Summary. Each Flow has trigger, steps, and linked roles. |
| `generate_diagrams(flows)` | Converts Flows to Mermaid diagram specs. |
| `generate_sops(diagrams)` | Generates SOPs from diagrams. |
| `generate_tasks(sops)` | Assigns tasks with time offsets and recurrence from SOPs. |

### `main.py`
**File:** `backend/main.py`

| Function | Responsibility |
|----------|----------------|
| Pipeline orchestration | Triggers each generation step sequentially, persists results to Supabase between steps. |

## Workflow

```
User submits Q&A form
  → generate_summary(qa_pairs) → Business Summary [persisted]
  → generate_flows(summary) → Flow[] [persisted]
  → generate_diagrams(flows) → Diagram[] [persisted]
  → generate_sops(diagrams) → SOP[] [persisted]
  → generate_tasks(sops) → Task[] [persisted]
  → workflow status advances rank (created → tasks_generated)
```

## Reuse Notes
- Summary-as-source-of-truth pattern prevents downstream hallucination — always anchor to one canonical document.
- Each step is independently re-runnable — persisting between steps means partial failures recover without restarting.
- **Gotcha:** Generation order is fixed. Skipping steps (e.g., generating Tasks without SOPs) breaks context chain.
- **Gotcha:** Gemini rate limits hit fast on multi-step pipelines. The rate limiter (separate module) is required, not optional.

## Adaptation Checklist
When reusing in a new project:
- [ ] Copy individual generator functions — each is independently callable
- [ ] Keep Summary as the shared context document passed to each step
- [ ] Persist each step's output before calling the next
- [ ] Wire rate limiter before deploying (2 req/min Pro, 10 req/min Flash)
- [ ] Replace Supabase persistence with your DB layer
