# Ontology — {Project Name}

**Last Updated:** {YYYY-MM-DD}

This document defines the Ubiquitous Language for this project. Every term here is the **canonical** name — use these exact words in code, APIs, docs, and user interfaces. Do not introduce synonyms.

> 🚫 **Critical Guardrail:** AIs must NEVER invent synonyms. If a concept isn't in this document, define it or ask the user before writing code.
> 🆔 **ID Format Rule:** Inconsistent ID formats between related entities cause bugs. Strict format enforcement is mandatory.

---

## 🗺️ System Visual Map

This Mermaid class diagram is the definitive blueprint for entity relationships. AI agents must parse this diagram before generating any code that references these entities.

```mermaid
classDiagram
    direction LR
    class {TermA} {
        +String {term_a}_id
    }
    class {TermB} {
        +String {term_b}_id
    }
    {TermA} "1" --> "many" {TermB} : {relationship_label}
```

---

## 🔍 Glossary Quick Reference

| Term | Short Definition | ID Format | Core? | Forbidden Synonyms |
|:---|:---|:---|:---|:---|
| {Term} | {Brief definition} | {format} | **Yes** | `{synonyms}` |
| {Term} | {Brief definition} | {format} | **No** | `{synonyms}` |

---

## 🧩 Core Concepts

### {Term Name}

| Field | Value |
|:---|:---|
| **Definition** | One or two sentences explaining what this is |
| **Core concept** | **Yes** / **No** *(core = used across the system, not just one module)* |
| **ID Format** | `{UUIDv7 / auto-increment integer / prefixed string like mtr_<uuid>}` |
| **Forbidden Synonyms** | `{Comma-separated list of terms the AI must NEVER use. REQUIRED field}` |
| **Related Terms** | `{Related term}`, `{Another related term}` |
| **Used In** | `{API endpoints, DB tables, UI screens where this appears}` |

**Explicit Structural Relationships:**
- **{RelatedConceptA}**: `{1:N / N:M / 1:1}` — *e.g., "Building has many Meters (1:N)"*
- **{RelatedConceptB}**: `{1:N / N:M / 1:1}`

**Execution Notes for AI Agents:**
- **Contextual Choice:** {When to use this term vs a related term}
- **Formatting Rules:** {Any specific pluralization exceptions or capitalization rules}
- **ID Implementation Pitfalls:** {e.g., "prefix must be lowercase" or "do not store prefix in the database"}

---

### {Next Term}

| Field | Value |
|:---|:---|
| **Definition** | ... |
| **Core concept** | **Yes** |
| **ID Format** | `...` |
| **Forbidden Synonyms** | `...` |
| **Related Terms** | ... |
| **Used In** | ... |

**Explicit Structural Relationships:**
- {Related}: {cardinality}

**Execution Notes:**
- ...

---

## 🛠️ Multi-Entity Structural Rules

### Cardinality Enforcement (for AI code generation)

| Rule | Description |
|:---|:---|
| **1:N — direct foreign key** | The "many" side table **must** hold a direct foreign key pointing to the "one" side table |
| **N:M — join table** | A dedicated join table **must** be generated holding both foreign keys |
| **Strict restraint** | Do **NOT** generate an intermediate join table for simple 1:N relationships |

### ID Consistency Rules

- Foreign key variables **must** match the data type and format of their target parent ID exactly
- If Building ID is `UUIDv7`, then `building_id` in Meter **must** be `UUIDv7` — never integer, never string
- If Meter ID is prefixed `mtr_<uuid>`, all references to meter IDs in code must store the full prefixed string, not just the UUID portion

---

## 📏 Naming Convention Pipeline

When generating any source code, database tables, or APIs, the AI agent must convert ontology terms using these exact transformations:

| Target | Output Format | Example |
|:---|:---|:---|
| **Class names (OOP)** | PascalCase of term | `{Term}Controller` |
| **Database tables** | snake_case plural | `{terms}` |
| **API route paths** | kebab-case | `/api/v1/{terms}` |
| **JSON payload keys** | camelCase | `{term}Id` |
| **Backend variables** | snake_case (Python) / camelCase (TypeScript) | `{term}_id` / `{term}Id` |
| **ID variables** | Match the ID format exactly | `{term}_id` (UUID/int), `{term}_code` (prefixed string) |
| **Forbidden synonyms** | Never appear in any output | `{list from glossary}` |

---

## ⏳ Unresolved Terminology

{If any terms were debated and not settled, list them here with the open question. Otherwise delete this section.}