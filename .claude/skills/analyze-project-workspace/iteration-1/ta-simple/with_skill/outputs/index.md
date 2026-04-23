---
project: TA
stack: [FastAPI, React, Gemini, Supabase, Python, TypeScript]
repo: /home/ikktaa/app/TA
last_indexed: 2026-04-23
---

# TA (Syntra)

## Summary
AI-powered business process automation tool. Takes user Q&A about a business, generates a structured Summary (source of truth), then chains AI calls to produce Roles, Flows, Mermaid diagrams, SOPs, and Tasks. Self-healing background worker detects and fills generation gaps automatically.

## Features
- [[TA/ai-chain-of-thought-pipeline]] — Sequential AI generation pipeline: Q&A → Summary → Flows → Diagrams → SOPs → Tasks
- [[TA/auto-healer]] — Background repair worker with 3-cycle circuit breaker, fills missing generation nodes
- [[TA/quickfill-ai]] — Context-aware answer inference for unanswered form questions
- [[TA/apqc-classifier]] — Hierarchical process tree classifier using Gemini with rule files
- [[TA/jwt-auth]] — Custom JWT auth on FastAPI with bcrypt, OAuth2PasswordBearer, retry backoff
- [[TA/mermaid-diagram-canvas]] — React Mermaid.js renderer with pan/zoom and async error handling

## Architecture Notes
Generation is sequential and chained — each step depends on the Summary as source of truth. The auto-healer runs as a background task, detecting gaps in the graph (e.g., Flow with no Diagram) and repairing them in up to 3 cycles. Frontend uses React with a state machine pattern (6 ranks from `created` to `tasks_generated`).
