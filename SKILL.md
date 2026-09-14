---
name: codebase-constitution
description: "Define a codebase constitution so AI agents produce consistent code."
version: 3.5.2
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [project-setup, conventions, agents-md, architecture, adr, ai-agents, interactive, ontology, maturity, prototype, pilot, production]
    related_skills: [plan, test-driven-development, requesting-code-review]
---

# Codebase Constitution — Interactive Bootstrapper

Define a **project constitution** for AI-assisted development. Run this skill when you start a new coding project, when you check a file against the rules, or when a project moves between maturity stages.

**One constitution follows a project through its entire life.** There is no separate Prototype, Pilot, or Production constitution. The constitution is a living body of project knowledge that evolves as the project changes. The documents change at different rates:

- **`AGENTS.md`** (project root) — holds only **stable operating invariants** and should change rarely.
- **`docs/architecture.md`** — describes the architecture that *is true now*, and evolves as the project changes. When architecture changes, update the current architecture and record significant decisions in ADRs — do not retain obsolete architecture as if it were still current.
- **`docs/ontology.md`** — the *current* domain language. May evolve rapidly during Prototype, then become more stable as the product matures.
- **`docs/project-stage.md`** — a short file recording the current maturity stage, its objective, its quality bar, and the graduation criteria for the next stage.
- **`docs/adr/`** — records the reasoning behind significant architecture changes.
- **`docs/parking-lot.md`** — intentionally deferred decisions, each with a concrete revisit trigger.
- **`docs/features/`** — feature contracts: the bounded specification of individual changes, at **every** stage.

Do not regenerate the whole constitution when the stage changes. Update the relevant documents and record decisions in ADRs.

### Document precedence

Each source owns one concern. When they appear to conflict, the owner of the affected concern wins:

- **`AGENTS.md`** — owns the stable operating invariants.
- **`docs/project-stage.md`** — owns the current maturity stage, its quality bar, and its accepted shortcuts.
- **`docs/architecture.md`** — owns the current technical architecture and implementation conventions.
- **`docs/ontology.md`** — owns the canonical domain concepts and terminology.
- **Active feature contract** — owns the scope and acceptance criteria of the current change.
- **ADRs** — explain *why* significant past decisions were made; they do not override the current architecture.
- **`docs/parking-lot.md`** — lists deferred decisions; it is not current architecture.

If two genuinely authoritative documents conflict within their own areas (e.g. stage vs. architecture disagree on a requirement), **stop and surface the contradiction** for the human rather than silently choosing one.

The committed constitution files hold product knowledge — what the product is, why it is shaped this way, what is deferred, and what good means at its stage. Ordering and live work-state belong to the supervising firstmate backlog, which assigns work, tracks state, and pages the human; no constitution document assigns, sequences, or tracks work. Where stale timing prose in a document disagrees with the backlog about what comes next, the backlog wins — flag the prose as drift instead of guessing.

## Project Maturity Stage

Every project has a maturity stage, recorded in `docs/project-stage.md`. The stage determines **what engineering investment is justified**, not whether disciplined development applies.

| Stage | Intent |
|---|---|
| **Prototype** | Deliberate, controlled validation of the core value proposition. The developer controls how it is used; synthetic or controlled data, and explicit, deliberate and reversible shortcuts, are acceptable where they accelerate learning without undermining basic correctness or safety. |
| **Pilot** | Real external users begin depending on the application, and/or real customer data is retained. Reliability, data durability, security, and recoverability matter substantially more. |
| **Production** | The application is operated as a dependable product. Operational requirements such as security, recovery, monitoring, deployment safety, performance, and reliability must be explicit. |

**Do not confuse maturity with deployment environment.** Keep terminology explicit:

- **Project stage:** Prototype / Pilot / Production
- **Environment:** local / test / staging / production

A new startup project defaults to **Prototype** unless the user says otherwise. Use **Prototype**, never "Demo", as the stage name.

### What Prototype means (and what it does not)

A prototype *may* use explicit, deliberate, and reversible shortcuts to shorten time-to-learning — for example synthetic data, manual operational steps, reduced edge-case coverage, deferred infrastructure, or mocked/temporary integrations where appropriate. Where material, document these shortcuts.

Prototype does **not** mean low-quality or disposable code by default. The core implementation still follows the constitution: bounded changes, understandable structure, verification before completion, no committed secrets, reproducibility, basic security and privacy, explicit uncertainty, and controlled dependencies.

**Avoid shortcuts that create hidden coupling, corrupt data, weaken basic security, or make later maturation unnecessarily hard.** The stage determines which investment is justified, never whether the discipline applies.

## Core Principles

These principles guide every interview decision and generated file:

- **One living constitution.** The same rule set follows the project through Prototype → Pilot → Production. It evolves — it is not regenerated or forked per stage.
- **Maturity is a quality bar, not a technology stack.** The stage raises the expected evidence and brings new concerns into scope. It never mandates specific technologies (no blanket rule like "Production requires Kubernetes" or "Pilot requires a particular auth provider").
- **Simplest sufficient architecture first.** Choose the simplest conventional solution sufficient for the current requirements, and introduce additional architecture only when there is evidence it is needed.
- **Smallest vertical slice first.** Deliver the smallest end-to-end slice that creates a meaningful user outcome, not a bare "one endpoint, one table, one route" formula.
- **Progressive extraction.** Start flat; extract into modules when a domain has its own language, multiple entry points, distinct change reasons, or difficult-to-test dependencies.
- **Park, don't guess.** When a decision is not yet justified, record it with a concrete revisit trigger. A parked decision with a trigger beats a random choice you will reverse later. Distinguish "we intentionally do not need this yet" from "we forgot about this".
- **Minimum-sufficient, progressive disclosure.** Constitution documents contain only information useful for the project as it exists now. The templates define available sections, not mandatory ones. Do not generate speculative, empty, or "not applicable yet" sections merely because a template lists them. As real concerns emerge (auth, retained customer data, external APIs, workers, deploy complexity, observability), grow the relevant documents by adding sections — rather than dropping every template section up front.

## When to Use

- Starting a new project that will be built with AI assistance
- "Help me set up a new project for AI coding" or "bootstrap a project constitution for [description]"
- "Check this file against the constitution"
- "Review my readiness for Pilot / Production" — a maturity stage transition
- **Not for:** one-off scripts or throwaway experiments (use the `spike` skill).

## How to Invoke

### Setup mode (default)

> Load codebase-constitution skill.
> I want to build [describe the project — as little or as much as you have].

### Check mode

> Load codebase-constitution skill.
> Check [path/to/file.py] against the constitution.

### Review mode / Stage transition

When a project is moving between maturity stages, inspect the *existing* constitution and codebase rather than generating a fresh one:

> Load codebase-constitution skill.
> Review my readiness to move from Prototype to Pilot (or: "now that we have external users, prepare constitution for Pilot").

---

## The Bootstrap Interview (proportionate)

The agent runs this interview. The maturity stage decides which questions are relevant. Ask only what the first useful vertical slice needs to be built safely.

Confirm the maturity stage up front (default **Prototype** for a new project) and state it during bootstrap so it is a visible assumption, not an invisible one. If the user says the project is already at Pilot or Production, set and record the stage accordingly.

### Three decision types

1. **Required during bootstrap** — the decision's absence blocks a safe first slice: project purpose, intended user, first core workflow, language/runtime, basic framework, persistence requirement, broad deployment target, and run/test commands.
2. **Conventional defaults** — low-impact choices (formatter, linter, package manager, test framework, directory conventions) get a boring conventional default recommended, without forcing the user to hand-pick each one.
3. **Defer until triggered** — queues/workers, caching, rate limiting, elaborate module boundaries, scaling, advanced storage architecture, API versioning, specialized infrastructure. Park these with a revisit trigger when foreseeable but not yet justified.

**General rule: bootstrap only decisions whose absence would prevent the first useful vertical slice from being built safely. Default or defer the rest.**

### Question flow rules

1. Always recommend first; list the recommendation before alternatives.
2. One domain at a time — no databases before the language is set.
3. Give a 1–2 sentence rationale per recommendation.
4. Default to park, not guess; offer a trigger when the user hesitates.
5. Respect the stage: a Prototype is not questioned about production-scale operational concerns;
   record such concerns that can be deferred to the parking lot with their trigger.
6. After every domain, summarise: "Decided: N. Parked: M."
7. Present the full decision table, ask for confirmation, then generate files.

### Domain 1 — Project Identity & Purpose (required)

Project name, one-line description, who it is for, and the value proposition being validated. Capture the **core domain terms** used here — they seed `docs/ontology.md`.

### Domain 2 — Core Workflow (required)

The one workflow, end to end, that proves value (e.g. "record a reading, see a month of a building's energy"). This becomes the first feature contract and the golden path.

### Domain 3 — Language & Runtime (required)

| Language | Best for | Consider if... |
|---|---|---|
| **Python** | APIs, data processing, automation (recommended default) | Rich ecosystem, readable |
| **TypeScript/Node.js** | Web + API in one language, real-time | One language across a stack |
| **Go** | CLI tools, high-performance services, single binary | Fast compile, no GC surprises |
| **Rust** | Systems programming, performance-critical | Maximum performance and safety |
| **Java/Kotlin** | Enterprise, large teams | Already in the JVM ecosystem |

If genuinely undecided, park it (default Python or TypeScript).

### Domain 4 — Web Framework (required for API/web)

Python: **FastAPI** (recommended) | Django | Flask | Starlette.
TS: **Next.js** (recommended for React) | Express | Hono | Fastify.
Go: **Chi** (recommended) | Gin | Echo.

### Domain 5 — Persistence (required)

Pick the store that the core slice needs, or park it:
| Store | Best when |
|---|---|
| **PostgreSQL** | Most networked / multi-user projects (default) |
| **SQLite** | Truly local prototype; zero admin |
| **Supabase** | Hosted Postgres + auth + storage |

Do not add cache, queue, object-storage, or vector infrastructure until the slice shows it is needed.

### Domain 6 — Authentication (choose the minimal appropriate option)

Follow the second principle: default to **no authentication where there is genuinely no need**, then the simplest **established, framework-native library or managed provider**. Choose JWT only when its stateless/refresh/cross-domain semantics are actually required.

### Domain 7 — Frontend (if applicable)

React + Vite (recommended) | Next.js | SvelteKit | Vue/Nuxt | htmx | API-only. Tailwind CSS (recommended) | CSS Modules | styled-components.

### Domain 8 — Testing & verification (recommended defaults)

Choose the conventional framework (`pytest`/`vitest`/`go test`). Set the baseline: every non-trivial logic leaves at least ONE runnable check that fails if the logic breaks; trivial one-liners need no test. Acquire the expected behaviour or acceptance criteria *before* implementation, and run the appropriate automated verification *as part of the same change*; test-first is encouraged where it clearly adds value, not forced universally.

### Domain 9 — Infrastructure (defer)

Always offer a conventional default (managed hosting is the usual choice) and actively park deployment/CI details that do not block the slice.

### Domain 10 — Ontology (lightweight at Prototype)

Ask only for the **core and the workflow**: each term's definition and ID placeholder (auto-increment is fine; UUID is fine where the framework defaults to it). Only include forbidden synonyms where two terms would genuinely cause ambiguity or semantic drift.

### Domain 11 — Shared vocabulary & modularity (optional)

If the user chooses modularity now, capture it in architecture.md; otherwise start flat and extract later.

---

## File Generation

After the interview, generate this set. Apply the minimum-sufficient principle: **the templates define available sections, not mandatory ones** — include only what the project's current stage genuinely needs, and grow later as concerns become real.

### AGENTS.md (project root — short entry point)

A ~20–30 line file the AI reads every session. It contains the positive primary objective, a stack quick-reference, the **stable invariant** protect/verify/plan rules, the Don't-do list, pointers to the detail files, and the pre-flight self-check. **Detailed, stage-specific requirements stay outside AGENTS.md** (in project-stage.md and architecture.md) so it stays short. See `references/agents-md-template.md`.

### docs/project-stage.md

The **normative maturity target**: what quality bar applies now, which shortcuts are acceptable, what is intentionally deferred, and what graduation criteria apply to the next stage. Concise, not a design doc — stage, objective, quality bar, assumptions, accepted shortcuts, deferred concerns, graduation criteria.

Example Prototype intent: "Validate that the target user gets value from the core workflow while keeping experiments small, understandable and reversible." Include the Prototype quality baseline — the core workflow works reliably, relevant automated checks pass, manual golden-path verification is possible, no secrets are committed, demo data is reproducible where relevant, deployment is recoverable, meaningful failures can be diagnosed, and important shortcuts are explicit rather than hidden. Do not prematurely require production-layer concerns (infrastructure, scaling, monitoring, formal security process, exhaustive coverage).

See `references/project-stage-template.md`.

### docs/architecture.md

A **descriptive account of current technical reality** — how the system is actually built today, not aspirations. Include only what is true now and useful. For a small early Prototype this is short and may only need: the system shape, the current stack, the simple project structure, the important implementation conventions, any external dependencies/integrations, and any current architectural constraints. Do not include speculative, empty, or "not applicable yet" sections. As real concerns appear (auth, real data, external APIs, workers, deploy complexity, observability), grow the document by adding the relevant sections. See `references/architecture-template.md`.

### docs/ontology.md

The current domain language. Define only the concepts that are actually useful to the work at hand (canonical term, definition, whether it is a core concept, and only the forbidden synonyms that would genuinely cause ambiguity). Ontology does not prescribe the implementation model — whether a concept is persisted, calculated, transient, or externally sourced is an architecture/data-model decision, recorded in architecture.md or an ADR. Keep it proportionate at Prototype; grow it as the domain language stabilises or expands. See `references/ontology-template.md`.

### docs/adr/

An ADR records the rationale behind a genuinely consequential or non-obvious decision — one a future developer or AI agent could reasonably reopen or accidentally reverse without understanding why. Write an ADR when preserving the reasoning is likely to prevent that (e.g. changing database technology, choosing an authentication strategy, introducing background jobs, choosing tenant isolation, or another expensive commitment). **Do not write an ADR for every conventional stack choice** — a conventional "FastAPI + SQLite for a small Prototype" decision is simply recorded in architecture.md. See `references/adr-template.md`.

### docs/parking-lot.md

Intentionally deferred decisions, each with a concrete revisit trigger, so deliberate deferral is distinguished from a forgotten concern. This is not current architecture — the implemented reality lives in architecture.md. Include only decisions genuinely deferred given the current stage, not every hypothetical future concern. See `references/parking-lot-template.md`.

### docs/features/

Per-feature contract files written at the start of each feature (Step 0), each with scope, acceptance criteria, assumptions/constraints, and the golden path when demo-critical. See `references/feature-contract-template.md`.

### opencode.json

Configures the coding tool to load only `AGENTS.md` into session; it points at the other files on demand. Do not register in-project files as `references` (external dirs only). See `references/opencode-setup.md`.

### .gitignore

Stack-aware ignore rules. See `references/gitignore-template.md`.

---

## Maturity as a Quality Bar (not a technology stack)

Evaluate engineering quality across stable dimensions, and bind the *evidence expected* to the stage. Grow the evidence as the project matures; do not jump straight to a stack.

| Dimension | Prototype | Pilot | Production |
|---|---|---|---|
| Functional correctness | Core workflow works reliably | Stronger regression coverage | Explicit verified requirements |
| Security | Basic floor: no committed secrets, input validation, no invented auth | Real authentication/authorization | Hardening and review |
| Privacy | Controlled/synthetic data | Real customer data handled intentionally | Defined handling and access rules |
| Data durability | Fine with synthetic data | Automated backups and tested restore | Defined retention and recovery |
| Recoverability | Simple recovery path | Designed, tested recovery | Planned, exercised recovery |
| Observability | Logs enough to debug | Useful error reporting | Monitoring and alerting to the service |
| Deployment safety | Minimal | Controlled/deployed to staging where proven | Safe deployment and rollback |
| Performance | Reasonable for the slice | Good enough for actual use | Based on measured need |
| Reliability | Minimal, credible for the slice | Managed operational failure modes | Explicit, continuously validated |

Production means that requirements are **explicit and verified**, not that more infrastructure was added.

---

## Feature Contracts — the bounded unit (all stages)

Feature contracts stay central at every stage. Each contract separates:

- **Not included** — functionality deliberately outside scope this iteration.
- **Assumption / constraint** — a condition under which the current implementation is considered valid (e.g. `synthetic demo data`, `single controlled user`, `desktop browser`, `data resets between sessions`, `external integration mocked`). If a constraint becomes false, revisit the implementation.

A mature project raises the acceptance bar without replacing the mechanism. See `references/feature-contract-template.md`.

## Reproducible prototype/demo data

Prefer a simple, explicit mechanism for generating or resetting known synthetic state (a seed script, a fixture file, or a reset endpoint) so the **golden path runs from a known starting point** rather than whatever floats in a developer DB. Real customer or production data must never casually become test or AI-development input; treat that as check-first.

## Destructive data changes — approval gate

Before a destructive or hard-to-reverse operation, the agent must say clearly: what changes, what data may be affected or lost, whether a backup is required, and how rollback/recovery works. Examples: dropping columns, deleting rows, rewriting identifiers, destructive type conversions or migrations, and persisted-data rewrites.

Do **not** apply the same ceremony to ordinary additive migrations. Additive (new column/table/index) is routine until evidence says otherwise.

---

## The stable AI workflow (every stage)

These are constitutional invariants from Prototype through Production:

- inspect and understand before you edit code
- plan before substantial implementation
- the active feature contract controls scope
- prefer small and bounded changes
- do not silently expand scope
- make the smallest change that satisfies the requirement
- do not refactor unrelated code
- do not commit secrets
- do not introduce significant dependencies or architecture without justification
- verify before claiming completion; say explicitly what you could not verify
- diagnose root causes; find, don't keep patching symptoms
- preserve existing behaviour unless the feature contract intentionally changes it
- record significant architecture decisions

Give `AGENTS.md` a positive primary objective: "make the smallest correct change that satisfies the active feature contract while preserving existing behaviour and respecting the current project stage." Keep the detailed, stage-specific requirements in `docs/project-stage.md` and `docs/architecture.md` so `AGENTS.md` remains short.

## Parking-lot triggers useful for maturation

Most parking-lot entries should carry an explicit revisit trigger, e.g.:

> Decision: Do not implement rate limiting yet.
> Reason: Prototype is accessible only to controlled users.
> Revisit when: the app becomes externally accessible, or we enter Pilot.

During a stage review, inspect these triggers and surface entries whose revisit condition is now met. Use the parking lot to make deliberate deferral safe — distinguish "we intentionally do not need this yet" from "we forgot this".

---

## Keep specialist concerns separate

`codebase-constitution` coordinates engineering maturity; it does not pretend to replace specialist reviews. At Production readiness it may record (in ADRs / project-stage.md) that specific specialist reviews are *required and what their outcome was* — e.g. security review, production-readiness review, infrastructure/deployment review, database-migration review. The constitution need not contain a full methodology for each discipline here. Those may become specialist skills later.

---

## Reduced architectural prescription (apply)

Keep the following as defaults and decision principles, not universal mandates:

- **Smallest vertical slice** that creates a user outcome (not "one endpoint, one table, one route" as a fixed formula).
- **Keep route/transport handlers simple; control complexity, not mandatory layers.** Non-trivial or reusable business logic lives outside the handler. Raw SQL scattered through handlers is avoided; if raw SQL is genuinely the simplest appropriate solution, keep it localized in a clear data-access place (a thin module or helper) rather than embedding it throughout transport/UI code. Introduce service or repository/data-access abstractions only when they prove a concrete benefit.
- **UUIDv7 not required per table.** Auto-increment (or the framework default) is a good default.
- **Soft delete not required per table.** Enable where a recovery need justifies; hard delete is acceptable where no such justification exists.
- **No forced connection-pooling infrastructure.** A built-in pool usually suffices until evidence says otherwise.
- **Authentication** is not JWT-first: prefer no auth when unnecessary, then a framework-native or managed provider; choose JWT only when its semantics are actually needed.
- **Strong typing** is an important guardrail, but do not claim it catches "most AI bugs"; it is a safety net.
- **Use a proportional verification rule.** Every change requires appropriate verification; nothing is done merely because the AI believes it works. Non-trivial business logic, calculations, transformations, regressions and important behaviours should normally have automated tests; trivial plumbing, exploratory UI work, configuration and similar changes may be covered by an existing automated check plus manual/golden-path verification. Acceptance criteria exist before implementation, relevant deterministic checks run, and when a bug is fixed, strongly prefer a regression test whenever the failure can reasonably be reproduced automatically.

These become defaults and decision principles rather than requirements.

---

## Deterministic verification vs AI judgment

Keep the constitution/check behavior, but distinguish two levels:

- **Deterministic checks** — tests, linter, type checker, formatter, dependency/security scans where configured.
- **AI review** — unnecessary complexity, architectural fit, ontology consistency, suspicious scope expansion, questionable assumptions.

An AI constitution review is **not** equivalent to a passing deterministic check. Prefer deterministic tooling for any rule that can be automatically enforced. Put the distinction in the Check Report output.

---

## Check Action (Constitution Check)

1. Read `docs/architecture.md`, `docs/ontology.md`, and `docs/project-stage.md`.
2. Read the target file.
3. Validate by concern, and by category:
   - **Deterministic**: if configured, actually run the tests/linter/type-checker/security-scan on the target.
   - **AI judgement**: review for stack compliance, architecture fit, security, privacy, simplicity/YAGNI, naming, ontology.
4. Produce a report: every rule is FAIL / WARN / OK, with the kind of verification (deterministic or AI) alongside each line.

A Check covers the conformance of the single touched file only; whole-constitution coherence is the supervisor's standing ownership.

---

## Review Action (Stage Transition)

Run during a stage boundary, e.g. Prototype → Pilot. The existing constitution stays; you produce a **gap analysis**.

The review compares **target vs. reality**:
- `docs/project-stage.md` = what *should* be true at this maturity
- `docs/architecture.md` + code + tests = what *is* actually true today
If project-stage.md requires something the architecture or code does not yet implement, treat that as a **maturity gap**, not a document-precedence conflict. Separately, verify the achieved stage is genuinely represented across the four yes/no buckets.

1. Read project-stage.md, architecture.md, ontology.md, and the parking lot. Read the actual codebase shape.
2. Compare the current stage against the target stage using the quality-bar (§Maturity); identify the meaningful gaps.
3. Inspect parking-lot reuse triggers whose condition is now met.
4. Surface requirements/ risks / capability gaps first; then propose the simplest correct solution; do not introduce new tech just because the stage changed.
5. Present to the human for decision: which gaps to correct now and which to defer.
6. Evolve the constitution to reflect the decided changes (architecture.md, project-stage.md, AGENTS.md if needed, ADRs for consequential decisions, and the parking lot).

Prototype → Pilot should surface, for example: real authentication and user lifecycle, privacy implications of real customer data, automated and restore-tested backups, staging where justified, improved error reporting and observability, stronger safeguards for destructive schema changes, and the prototype shortcuts whose revisit trigger has now been met. This is only a gap analysis — never automatic technology addition.

### Readiness / evidence checklist (repeatable, not a certificate)

For each transition, assess the relevant items from the quality-bar dimensions (§Maturity) against **concrete project evidence** — configuration, tests, backup/restore procedure, deployment process, the actual auth implementation, logs/monitoring, and so on. Avoid vague terms; resolve each item to one of:

- **Satisfied** — with the evidence that supports it
- **Gap** — with what is missing
- **Not applicable** — with the reason
- **Explicitly deferred** — with the risk/reason and a revisit trigger

This output is a decision aid, not a verdict. Do **not** declare a project "passed" into the next stage because boxes are checked — proficiency does not certify readiness. Present the evidence, list the gaps and risks, note what should probably be addressed before adopting the target stage, and leave the actual transition decision to the human.

Keep requirements contextual to the product. For example, Production does not automatically require load testing; it requires that performance needs have been **explicitly considered** and that there is adequate evidence for the actual requirement (for a small, predictable-load product, an explicit capacity note plus reasonable headroom may suffice).

---

## Next Steps — After Generation

1. `git init`, then initial commit.
2. Create the project with the chosen stack, wired to the structure in architecture.md.
3. Choose the first feature: the smallest vertical slice — and have the coding agent write a feature contract before writing code.
4. Use project-stage.md as the current bar.
5. Conduct a deliberate Review pass before each stage boundary.

---

## Troubleshooting

- **AI not following the constitution:** confirm AGENTS.md is found by the tool, no per-agent CLAUDE.md/.cursorrules overrides it, and restart the session.
- **Multiple agents produce inconsistent code.** Enforce a single source-of-truth set of files across all agents; forbid per-agent shadow rules; lean on deterministic tooling where possible.

---

## Pitfalls

- **AGENTS.md too long.** Keep it short; push stage requirements into project-stage.md / architecture.md.
- **Parking without a trigger.** A parked item needs a concrete review condition, not "later".
- **Not updating the constitution.** When a decision or the stage changes, update architecture.md, ADRs, and parking lot; an outdated constitution actively misleads.
- **Confusing stage with env.** Prototype / Pilot / Production are stages; local/test/staging are environments.
- **"Demo" at start of life.** Use "Prototype" throughout this skill.
- **Confusing maturity with infrastructure.** Production means requirements are explicit and verified, not that more infrastructure was added.

---

## Verification (Definition of Done)

- [ ] Constitution documents are **proportionate to the current stage** — no speculative, empty, or "not applicable yet" sections; each file contains only what is useful now.
- [ ] `AGENTS.md` exists, short, with a positive primary objective and stable invariant list.
- [ ] `docs/architecture.md` exists and states the *current technical reality* (what is actually built today), including the current stage and the dimension table.
- [ ] `docs/ontology.md` exists, with only the concepts currently useful to define; it does not prescribe a DB schema, classes, or endpoints.
- [ ] `docs/project-stage.md` exists: stage, objective, quality bar, shortcuts, graduation criteria.
- [ ] A feature contract exists for the first feature, under `docs/features/`.
- [ ] ADRs (if present) exist only for genuinely consequential/non-obvious decisions; a conventional stack choice is recorded in architecture.md, not forced into an ADR.
- [ ] `docs/parking-lot.md` exists whenever anything is genuinely deferred, and is omitted only when nothing is; each entry has a concrete revisit trigger.
- [ ] `.gitignore` and `opencode.json` exist; opencode.json loads only AGENTS.md.
- [ ] A "constitution check" on a file passes, and the report distinguishes deterministic vs AI-judgement checks.
- [ ] Link closure: every Source of Truth entry in `AGENTS.md` resolves to an existing file, and every emitted constitution document under `docs/` is covered by a Source of Truth entry.