# Ontology — {Project Name}

**Last Updated:** {YYYY-MM-DD}
**Project stage:** {Prototype / Pilot / Production}

> This document defines the project's **canonical domain vocabulary**. In the current stage, the vocabulary may still be evolving (especially in Prototype). When you introduce or change a term, update this file so the language stays coherent. Keep terms deliberately chosen — adding vocabulary is not free.

## How to use this ontology

- **Ontology answers:** what are the important concepts in this domain, what do they mean, and how do they relate?
- **Ontology describes the domain model; it does not prescribe the implementation model.** An ontology entry does not, by itself, decide whether a concept is persisted, calculated, transient, externally sourced, or exposed via an API. That belongs to the current architecture / data model (architecture.md or the relevant ADR).
- **Canonical model language** — use consistently in code, APIs, and definitions: domain entities, important code concepts, API resources, and architecture documentation.
- **User-facing language** — UI strings and explanatory prose may use clearer natural-language labels or synonyms, as long as the underlying meaning is unambiguous.
- **Forbidden synonyms** — name a term as forbidden only when two terms would genuinely create ambiguity or semantic drift. Do not over-list.

---

## Glossary Quick Reference

| Term | Short Definition | Core? | Notes |
|:---|:---|:---|:---|
| {Term} | {Brief definition} | **Yes** | {optional note, e.g. persistence relevance} |

---

## 1. Concepts

### {Term Name}

| Field | Value |
|:---|:---|
| **Definition** | One or two sentences |
| **Core concept** | **Yes / No** (core = used across the system, not just one module) |
| **Related terms** | {related term}, {related term} |
| **Forbidden synonyms** | {only if two terms would genuinely be ambiguous; else —} |
| **_Optional_ persistence note** | Only if materially relevant to understanding the concept, e.g. "stored in the database; derived field is calculated on read." If not material, omit. |

### {Next Term}

...

---

## 2. Relationships

List only the relationships the project actually encodes (cardinalities N:N / 1:N / 1:1). Don't invent entities solely to hold ontology structure; a concept may be modelled purely in code (enums, computed values) without a table.

---

## 3. Terms deliberately absent

If a term you might expect (e.g. "Account") is deliberately *not* canonical here, note it here so an AI does not reintroduce it.