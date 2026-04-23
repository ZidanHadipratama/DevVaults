---
project: TA
stack: [FastAPI, React, Gemini, Supabase, Python, TypeScript]
repo: /home/ikktaa/app/TA
last_indexed: 2026-04-23
---

# TA (Syntra)

## Summary
AI-powered business process automation. Chains multiple Gemini calls to generate business process artifacts from user Q&A. Includes self-healing repair, JWT auth, React frontend with Mermaid diagrams and resizable panels.

## Features
- [[TA/jwt-auth-custom]] — Custom JWT + bcrypt, OAuth2PasswordBearer, retry backoff
- [[TA/ai-pipeline-multi-step]] — Chained generation pipeline Q&A → Summary → Roles → Flows → Diagrams → SOPs → Tasks
- [[TA/auto-healer-background-repair]] — Self-healing background job with 3-cycle circuit breaker
- [[TA/mermaid-swimlane-generator]] — Mermaid TD diagrams with role-based subgraphs and ghost node layout fix
- [[TA/gemini-rate-limiter]] — Sliding window rate limiter (2 req/min Pro, 10/min Flash)
- [[TA/parallel-ai-executor]] — asyncio.Semaphore executor with return_exceptions and filtered results
- [[TA/quickfill-context-inferencer]] — Q&A context inference for unanswered questions
- [[TA/workflow-state-machine]] — 6-rank ordered state machine with self-healing advancement
- [[TA/frontend-auth-context]] — AuthContext with cookie hydration, QueryClient clear, useRequireAuth
- [[TA/useResizable-hook]] — Mouse-drag panel resizer with localStorage persistence
- [[TA/useAutoSave-hook]] — Debounced save hook with forceSave() and skip-first-render
- [[TA/api-logging-middleware]] — FastAPI BaseHTTPMiddleware with body caching and header redaction
- [[TA/role-auto-provisioner]] — AI-extracted roles with placeholder User accounts
- [[TA/task-scheduler-ai]] — AI time_offset_hours assignment with deterministic post-processing

## Architecture Notes
Frontend is React SPA. Backend is FastAPI. Generation is sequential but each step persists to Supabase, enabling recovery. Auth is custom JWT (not Supabase Auth). Parallel execution controlled by configurable semaphore.
