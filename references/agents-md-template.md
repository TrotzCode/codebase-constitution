# AGENTS.md — {Project Name}

**{One-line description of what this project does}**

This file is the entry point to the project constitution. Key details live in the files below; this file just orients you.

## Quick Reference

| Layer | Choice |
|-------|--------|
| Language | {Python 3.13 / TypeScript / Go 1.23} |
| Framework | {FastAPI / Next.js / Gin} |
| Database | {PostgreSQL 16 / SQLite} |
| ORM | {SQLAlchemy async / Prisma} |
| Auth | {JWT / OAuth2 / None yet — see parking-lot} |
| Testing | {pytest / vitest / go test} |

## Source of Truth

For full details on conventions, read these files in order:

1. **`docs/specs/architecture.md`** — Full tech stack, project structure, naming, API conventions, data layer rules, security constraints, AI guardrails
2. **`docs/specs/ontology.md`** — Domain glossary — the exact terms this project uses (read this before writing any user-facing text)
3. **`docs/features/`** — Feature contracts — per-feature scope, acceptance criteria, and out-of-scope. Read the contract for the feature you're implementing before writing code.
4. **`docs/adr/0001-stack-decisions.md`** — Why each decision was made
5. **`docs/parking-lot.md`** — Decisions explicitly deferred, with review triggers

## Don't Do

- Do NOT add dependencies outside the approved tech stack without an ADR
- Do NOT mix sync and async database access
- Do NOT put business logic in route handlers
- Do NOT use raw SQL when the ORM can express the query
- Do NOT introduce new domain terms not in ontology.md
- Do NOT skip updating architecture.md when the stack changes
- Do NOT add abstractions, dependencies, configuration, caches, queues, or feature flags for hypothetical future needs
- Do NOT remove or disable an existing test to make the suite pass
- Do NOT refactor unrelated code "while you're here" — deletion over addition, boring over clever, fewest files possible
- Do NOT put customer data, credentials, or personal data into prompts by default — treat it as check-first (see architecture.md → Privacy)

## Pre-Flight Check (mandatory)

Before creating or modifying any file, the AI agent MUST:

1. **Quick-scan** the relevant ontology terms — are you using the right names?
2. **Verify** the file goes in the right directory per architecture.md
3. **Check** that no forbidden dependencies are introduced
4. **Confirm** the code pattern matches what architecture.md specifies (same ORM, same error format, same auth pattern)
5. **Apply the simplicity ladder** in architecture.md — reuse existing or native capability before adding code; park speculative work with a concrete upgrade trigger
6. **Read before you climb** — trace the real flow of the code you touch before proposing a change; and if you could not verify something (tests, runtime), say so explicitly.

This is a 10-second self-check, not a full review. It prevents the most common AI-generated inconsistencies.