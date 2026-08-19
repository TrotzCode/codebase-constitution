# Parking Lot — Deferred Decisions

This document tracks architectural and design decisions that were intentionally deferred during project initialization. Each entry has a trigger condition that determines when it should be resolved.

> **Rule:** No parked decision stays parked without a concrete trigger for when to revisit it.

---

## How to Use This

1. When a parked decision is resolved, move it to an ADR (`docs/adr/`) and note "Resolved by ADR-{N}" here.
2. If a trigger condition passes and the decision is still parked, resolve it before writing code that depends on it.
3. Delete entries only when resolved. Don't accumulate stale parked items.

---

## Parked Decisions

### SIMP-001: {Deliberate simplification / known ceiling}

Use this entry when the project intentionally chooses a simpler current implementation over a more capable one.

| Field | Value |
|-------|-------|
| **Current simple approach** | e.g. "Run report generation in the web process" |
| **Why adequate now** | e.g. "Reports complete in under five seconds for pilot users" |
| **Ceiling / risk** | e.g. "A long report can exceed request timeout" |
| **Upgrade trigger** | e.g. "Before reports exceed 30 seconds or 100 reports/day" |
| **Likely upgrade** | e.g. "Move generation to a background queue" |
| **Status** | `Active simplification` / `Resolved → ADR-{N}` |

**Rule:** A simplification without a concrete ceiling and trigger is an undocumented shortcut, not a deliberate decision.

---

### PARK-001: {Short title of the decision}

| Field | Value |
|-------|-------|
| **Domain** | e.g. "Data Storage" |
| **Date Parked** | YYYY-MM-DD |
| **Trigger** | What needs to happen before this must be decided? |
| **Partial Guidance** | Any hints or tendencies (e.g., "leaning toward PostgreSQL but open") |
| **Status** | `Parked` / `In Review` / `Resolved → ADR-{N}` |

**Why deferred:**
Brief explanation of why this wasn't decided during the initial interview.

**Suggested approach when revisited:**
What to evaluate, what options to consider, what trade-offs matter.

---

### PARK-002: {Next parked decision}

...

---

## Decisions Summary

| # | Domain | Decision | Status | Trigger |
|---|--------|----------|--------|---------|
| PARK-001 | Data Storage | PostgreSQL likely, ORM TBD | Parked | Before writing first model |
| PARK-002 | Auth | JWT likely, details TBD | Parked | Before adding any user-facing feature |
| ... | ... | ... | ... | ... |

---

## Quick Reference

- **Decided today:** See `docs/adr/0001-stack-decisions.md`
- **Parked items:** Listed above
- **Next review:** {Suggested date or milestone, e.g. "end of first sprint"}