# Parking Lot — Deferred Decisions

This document tracks engineering decisions that were intentionally deferred during setup, each with a **concrete revisit trigger**. Its purpose is to make deliberate deferral safe: it separates *"we intentionally do not need this yet"* from *"we forgot about this"*.

> **Rule:** No parked decision stays parked without a concrete revisit trigger — a boundary, measurement, or milestone that makes it undeniably due for review. "Later" is not a trigger.

---

## How to use this document

1. When a trigger condition appears met, the agent proposes the item to the human and waits — the agent does not start, move, or resolve a parked item by itself. Only after the human decides, write code that depends on it. The single direction is: agent proposes, human decides.
2. When a trigger occurs and the human has decided, move the resolved decision to an ADR (`docs/adr/`) and mark it "Resolved → ADR-{N}".
3. During a **stage-transition review** (e.g. Prototype → Pilot), re-read every trigger and surface entries whose condition is now true — e.g. "application becomes externally accessible", "real customer data arrives", "a second user depends on recovery".
4. Delete entries only once resolved by a human decision. Never let items age silently.

---

## Deferred decisions

### SIMP-001: {Deliberate simplification / known ceiling}

Used when the project intentionally chooses a simpler current implementation over a more capable one.

| Field | Value |
|---|---|
| **Current simple approach** | e.g. "Run report generation in the web process" |
| **Why adequate now** | e.g. "Reports complete in under five seconds for our controlled users" |
| **Ceiling / risk** | e.g. "A long report can exceed the request timeout" |
| **Review when** | e.g. "Reports exceed 30 seconds, or the app enters a stage with external users" |
| **Likely upgrade** | e.g. "Move generation to a background queue" |
| **Status** | `Active simplification` / `Resolved → ADR-{N}` |

A simplification with no ceiling and no trigger is an undocumented shortcut, not a deliberate decision.

---

### PARK-001: {Short title}

| Field | Value |
|---|---|
| **Domain** | e.g. "Data Storage" |
| **Date parked** | YYYY-MM-DD |
| **Revisit when** | Concrete, checkable condition. e.g. "Before real customer data is retained", "When the app becomes externally accessible — enters Pilot" |
| **Partial guidance** | Leaning/tendency, e.g. "leaning PostgreSQL, open to change" |
| **Status** | `Parked` / `In review` / `Resolved → ADR-{N}` |

**Why deferred:** one-two lines on why this wasn't justified yet.

**What to evaluate at revisit:** the options, the trade-offs that matter, and what evidence would push a decision the other way.

---

### PARK-002: {Next deferred decision}

...

---

## Due-for-review

A working list populated during stage reviews of items whose revisit condition now appears met. The AI does **not** act on these; it presents them to the human who decides what to implement, deepen, or re-defer.

## Decisions summary

| # | Domain | Decision | Status | Revisit trigger |
|---|---|---|---|---|
| PARK-001 | Data Storage | "PostgreSQL likely" | Parked | Before real data retention |
| SIMP-001 | Reports | In-process generation | Active | Past 30s or external users |
| ... | ... | ... | ... | ... |

---

## Quick reference

- **Decided so far:** see `docs/adr/`.
- **Deferred now:** items above.
- **Next review:** {trigger or milestone, e.g. "on entering Pilot"}.