# Architecture Decision Record Template

ADRs go in `docs/adr/` and are numbered sequentially (0001, 0002, ...). Name them descriptively: `0001-use-postgresql.md`, `0002-api-versioning.md`.

Write for another AI agent (or a future you) who has no memory of this conversation. Include enough context that the rationale is self-contained.

---

# ADR-{NUMBER}: {Title — short, descriptive}

**Date:** {YYYY-MM-DD}

**Status:** {Proposed / Accepted / Deprecated / Superseded by ADR-{N}}

## Context

What prompted this decision? What problem are we solving? What constraints and trade-offs exist?

Write 2-5 sentences explaining the situation without assuming the reader knows the backstory. Include:
- The problem or opportunity
- Key constraints (budget, timeline, team skill, existing systems)
- Any external requirements (compliance, performance targets, scale expectations)

## Decision Drivers

The non-functional requirements that influenced this decision:

1. **{Driver 1}** — e.g., "Must handle 10K concurrent users"
2. **{Driver 2}** — e.g., "Team already knows Python"
3. **{Driver 3}** — e.g., "Must support full-text search without additional services"

## Options Considered

| Option | Pros | Cons |
|--------|------|------|
| **{Option A}** (chosen) | {Key advantage} | {Key drawback} |
| {Option B} | {Key advantage} | {Key drawback} |
| {Option C} | {Key advantage} | {Key drawback} |

## Decision

We chose **{Option A}**.

## Rationale

{Option A} was chosen because:

1. **{Primary reason}** — {brief justification referencing a driver above}
2. **{Secondary reason}** — {brief justification}
3. **{Tertiary reason}** — {if applicable}

## Consequences

### Positive

- {Benefit 1}
- {Benefit 2}

### Negative / Trade-offs

- {Drawback 1}
- {Drawback 2}

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| {Risk description} | {Low/Med/High} | {Low/Med/High} | {How we address it} |
| {Risk description} | {Low/Med/High} | {Low/Med/High} | {How we address it} |

## Compliance Notes (for AI agents)

When working on this project:

- {If X: do Y}
- {Avoid Z}
- {Prefer pattern: concrete example}

---

## Example: Stack Decision ADR

# ADR-0001: Use FastAPI with SQLAlchemy async

**Date:** 2026-08-17

**Status:** Accepted

## Context

We are building a REST API for a SaaS product. The system will handle ~10,000 concurrent users and needs a complex product catalog with hierarchical categories, transaction processing for orders and payments, and full-text search for products.

The team has Python experience but no prior async Python experience. We need ACID compliance for financial transactions and automatic API documentation.

## Decision Drivers

1. **Async performance** — must handle 10K concurrent I/O-heavy requests
2. **Python ecosystem** — team knows Python, wants to stay in it
3. **Auto-documentation** — API docs must stay in sync with code
4. **ACID compliance** — financial transactions require strong consistency
5. **Built-in full-text search** — avoid adding Elasticsearch initially

## Options Considered

| Option | Pros | Cons |
|--------|------|------|
| **FastAPI + SQLAlchemy async** | Native async, auto OpenAPI, mature ORM, great docs | Steeper async learning curve |
| Flask + SQLAlchemy sync | Simple, well-known | Sync-only, no auto docs |
| Django REST Framework | Full-featured, includes ORM + admin | Heavy, sync-only |
| Express + Prisma | Fast runtime, great DX | Team doesn't know Node |

## Decision

We chose **FastAPI + SQLAlchemy async**.

## Rationale

1. **Async performance** matches our concurrency needs (driver 1)
2. **Automatic OpenAPI** generation reduces documentation drift (driver 3)
3. **Team's Python familiarity** means faster onboarding (driver 2)
4. **SQLAlchemy 2.x async** provides the same ORM patterns the team knows
5. **Built-in search** (PostgreSQL full-text via SQLAlchemy) avoids a separate search service (driver 5)

## Consequences

### Positive
- Auto-generated OpenAPI docs stay in sync with code
- Async endpoints handle I/O-bound workloads efficiently
- Pydantic schemas serve as both validation and documentation

### Negative / Trade-offs
- SQLAlchemy async has some gotchas (connection pooling, session lifecycle)
- Team needs to learn async patterns (but training investment is modest)

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Async session leaks | Medium | High | Use `async_scoped_session` from Day 1; add session lifecycle tests |
| Async learning curve | Medium | Low | Pair-program first endpoints; add async linting rules |
| Full-text search scaling | Low | Medium | Design query layer so Elasticsearch can be swapped in later |

## Compliance Notes (for AI agents)

- All route handlers MUST be `async def`
- All DB queries MUST use `async with Session()` context manager
- Do NOT import `sqlalchemy.orm.Session` directly — use the project's `db.py` session factory
- Every endpoint gets: status code, response model, and summary in the decorator