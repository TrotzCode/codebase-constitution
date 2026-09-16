# AGENTS.md — {Project Name}

**{One-line description}**

This file is the entry point to the project constitution. It holds the project's **stable operating invariants** and should change rarely. Stage-specific detail lives in `docs/project-stage.md` and `docs/architecture.md` — read those before doing stage-sensitive work.

## Primary objective

> Make the smallest correct change that satisfies the active feature contract while preserving existing behaviour and respecting the current project maturity stage ({Prototype / Pilot / Production}).

## Quick Reference

Each slot states the current reality separately from the approved target, so an aspiration never reads as a fact. The target column holds an approved change or "—" when none is approved.

| Layer | Current reality (what the code uses today) | Approved target (or —) |
|---|---|---|
| Language | {Python / TypeScript / Go / ...} | {—} |
| Framework | {FastAPI / Next.js / ...} | {—} |
| Database | {in-memory / SQLite / PostgreSQL / None yet} | {—} |
| ORM | {SQLAlchemy / Prisma / ...} | {—} |
| Auth | {None yet / framework-native / managed provider} | {—} |
| Testing | {pytest / vitest / go test} | {—} |

## Source of Truth

Read in order when a task needs detail:

1. **`docs/project-stage.md`** — current maturity stage, its objective, quality bar, accepted shortcuts, and graduation criteria.
2. **`docs/architecture.md`** — the architecture that is true today: stack, structure, naming, data layer, typing, API conventions, security/privacy, observability, testing.
3. **`docs/ontology.md`** — the canonical domain vocabulary. Read before naming anything or writing user-facing text.
4. **`docs/features/`** — the feature contract for the change you're implementing. Scope control lives here.
5. **`docs/adr/`** — why significant decisions were made.
6. **`docs/parking-lot.md`** — deliberately deferred decisions, with revisit triggers. Check whether one of your triggers has fired.

### Document ownership
- This file (`AGENTS.md`) owns the stable operating invariants.
- `docs/project-stage.md` owns the current maturity stage and its quality bar.
- `docs/architecture.md` owns the current technical architecture and conventions.
- `docs/ontology.md` owns canonical domain terminology.
- The **active feature contract** owns the scope and acceptance criteria of the current change.
- ADRs explain why past decisions were made; they don't override current architecture. `docs/parking-lot.md` lists deferred decisions; it isn't current architecture.

If two authoritative sources genuinely conflict within their own areas, **stop and surface the conflict** rather than silently choosing one.

The committed constitution files hold product knowledge — what this product is, why it is shaped this way, what is deferred, and what good means at the current stage. Ordering and live work-state belong to the supervising firstmate backlog, which assigns work, tracks state, and pages the human; no constitution document assigns, sequences, or tracks work. Where stale timing prose in a document disagrees with the backlog about what comes next, the backlog wins — flag the prose as drift instead of guessing.

## Stable operating invariants

- Inspect and understand the code before you edit it; plan before substantial implementation
- The active feature contract controls scope; do not silently expand it
- Make the smallest change that satisfies the requirement; do not refactor unrelated code
- Do not commit secrets; do not introduce significant dependencies or architecture without justification
- Verify before claiming completion; say explicitly what you could not verify
- Diagnose root causes rather than repeatedly applying speculative fixes
- Preserve existing behaviour unless the feature contract intentionally changes it
- Record significant architectural decisions in `docs/adr/`

## Don't Do

- Do NOT add dependencies outside the approved stack without an ADR
- Do NOT put non-trivial or reusable business logic in route handlers (simple CRUD/read may stay in the handler; extract logic that is complex or reused)
- Do NOT use raw SQL when the ORM can express the query
- Do NOT introduce domain terms not in ontology.md
- Do NOT skip updating architecture.md (or an ADR) when the stack or stage changes
- Do NOT add abstractions, caches, queues, feature flags, or config for hypothetical future needs — park them with a trigger instead
- Do NOT remove or disable an existing test to make the suite pass
- Do NOT refactor unrelated code "while you're here" — deletion over addition, boring over clever, fewest files possible
- Do NOT put customer data, credentials, or personal data into prompts by default — check-first (see architecture.md → Privacy)
- Do NOT treat an AI "looks fine" review as a substitute for a passing deterministic check (tests/lint/types)
- Do NOT impose a production technology or workflow that this stage does not justify — follow `docs/project-stage.md`

## Pre-Flight Check (mandatory)

Before creating or modifying any file, the AI must:

1. Quick-scan the relevant ontology terms — are you using the canonical names?
2. Verify the file goes in the right directory per architecture.md
3. Check no forbidden dependency is introduced
4. Confirm the code pattern matches architecture.md (same stack, error format, auth, data layer)
5. Apply the simplicity ladder (reuse existing capability; park speculative work with a trigger)
6. Read before you climb — trace the real flow of the code you touch; if you could not verify something, say so explicitly

This is a ~10-second self-check that prevents the most common AI-generated inconsistencies.

Finish: after the work is done and before claiming completion, re-check what you touched against the invariants above and flag any project document the work proved wrong — a changed stack or convention, a fired parking-lot revisit condition, an obsolete shortcut or assumption. Put the flag in the completion report with the fix scoped; do not redesign the docs on your own.

---

<!-- generator: codebase-constitution v{version} — keep this footer on new generated outputs and update the version on regeneration -->