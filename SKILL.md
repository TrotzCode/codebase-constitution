---
name: codebase-constitution
description: "Define a codebase constitution so AI agents produce consistent code."
version: 3.4.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [project-setup, conventions, agents-md, architecture, adr, ai-agents, interactive, ontology]
    related_skills: [plan, opencode, test-driven-development, requesting-code-review]
---

# Codebase Constitution — Interactive Bootstrapper

Define a **project constitution** for AI-assisted development. Run this skill whenever you start a new coding project. It will:

1. **Interview you** about 10 decision domains, offering recommendations with pros/cons
2. **Park unknowns** — decisions you're not ready to make are explicitly documented as deferred
3. **Generate a two-component system**:
   - **Constitution** — AGENTS.md (entry point), docs/specs/architecture.md (tech), docs/specs/ontology.md (domain language)
   - **Roadmap** — docs/roadmap.md (prioritized feature backlog, milestones, nice-to-haves)
4. **Record ADRs and a parking lot** — so future sessions don't re-litigate decisions

After setup, the skill also supports a **check** action to validate code against the constitution, and an **update** action to modify the constitution when decisions change.

## Core Principles

These principles guide every interview decision and file generation step:

- **Two-component system.** Every project gets a **Constitution** (what the rules are) and a **Roadmap** (what to build and in what order). The constitution prevents spaghetti; the roadmap prevents scope creep.
- **Smallest meaningful feature first.** Always start with one endpoint, one table, one route. Before planning or implementing, write a short **feature contract** (who it's for, the happy path, acceptance criteria, and what is explicitly out of scope) and get human approval on the plan before the AI writes code. Go through the full Contract → Understand → Plan → Test → Implement → Verify → Commit cycle. After three endpoints you'll know whether to extract modules, tighten rules, or refine the ontology.
- **Progressive extraction.** Start with the flat structure (all code layered under src/{app}/). Extract into domain modules when a feature has its own language, multiple entry points, distinct change reasons, or difficult-to-test dependencies. Modularity too early is premature abstraction.
- **Smallest safe solution.** Reuse existing, standard-library, native-platform, or approved-dependency capability before writing custom code. Do not simplify away validation, security, error handling, accessibility, or tests. Every deliberate shortcut needs a ceiling and an upgrade trigger.
- **Park, don't guess.** When you're not sure about a decision, record what you know and define a trigger for when to revisit it. A parked decision with a review trigger beats a random choice you'll reverse later.

## When to Use

- Starting a new project that will be built with AI assistance
- User says "help me set up a new project for AI coding"
- User says "I want to bootstrap a project but I don't know all the decisions yet"
- User says "set up a project constitution for [description]"
- User says "check this file against the constitution"
- User says "update the constitution" or "I changed my mind about [decision]"
- **Don't use for:** one-off scripts, throwaway prototypes (use `spike` skill)

## How to invoke

### Setup mode (default)

Call this skill with a brief description of what you want to build:

> Load codebase-constitution skill.
> I want to build [describe your project — as little or as much as you have].

The agent will walk through the decision domains, asking questions and giving recommendations.

### Check mode

Validate a file against the constitution after coding:

> Load codebase-constitution skill.
> Check [path/to/file.py] against the constitution.

The agent reads both constitution files and validates the target against architectural rules, security constraints, and ontology terms.

### Update mode

Update the constitution when a decision changes or a parked item is resolved:

> Load codebase-constitution skill.
> Update the constitution: I decided to use MongoDB instead of PostgreSQL.

The agent updates the relevant files (AGENTS.md, architecture.md, ADR, parking lot) surgically — no re-interview needed.

---

## Procedure — The Interactive Interview

The agent runs this interview. Answer each question; if you don't know yet, say "park it" and the decision goes into the parking-lot document.

### Question Flow Rules

1. **Always recommend first.** When presenting options, list your recommendation first.
2. **One domain at a time.** Don't ask about databases before the language is decided.
3. **Explain why, not just what.** Give 1-2 sentences of rationale with each recommendation.
4. **Default to park, not to guess.** If the user hesitates, offer to park it. A parked decision with a review trigger is better than a random choice they'll reverse later.
5. **After every domain**, summarize: "Decided: N items. Parked: N items."
6. **After all domains are complete**, present the full decision table, ask for confirmation, then generate all files.

---

### Domain 1: Project Identity

**Goal:** Name and one-line description.

Ask the user:
- Project name
- One-sentence description of what it does
- What problem it solves

If the user says "I don't have a name yet," suggest 3-5 reasonable names based on their description.

Also collect the **core domain terms** during this phase — what are the key concepts this project deals with? (e.g., "energy consumption, buildings, sensors, tenants, invoices"). These go into the ontology later.

---

### Domain 2: Language & Runtime

**Goal:** Primary language for the project.

| Language | Best for | Consider if… |
|----------|----------|-------------|
| **Python** | APIs, data processing, ML, automation | User knows Python, needs rich ecosystem |
| **TypeScript/Node.js** | Web apps, full-stack, real-time | Needs frontend + backend in one language, large ecosystem |
| **Go** | CLI tools, high-performance APIs, microservices | Needs fast compile, single binary deploy |
| **Rust** | Systems programming, performance-critical | Needs maximum performance, safety |
| **Java/Kotlin** | Enterprise, large teams | Already in Java ecosystem, needs JVM |
| **Go + Python** | Mixed stack | Heavy data processing + fast API layer together |

**Park if:** user genuinely doesn't know. Suggest Python or TypeScript as a safe default.

---

### Domain 3: Web Framework

**Goal:** Framework for the API or web server.

If Python:
| Framework | Best for |
|-----------|----------|
| **FastAPI** | REST/JSON APIs, async, auto-docs (recommended for most projects) |
| Django | Full-featured, includes ORM + admin panel |
| Flask | Simple, minimal, well-known |
| Starlette | Low-level async |

If TypeScript:
| Framework | Best for |
|-----------|----------|
| **Next.js** | Full-stack React apps (recommended if React frontend) |
| Express | Minimal REST APIs |
| Hono | Fast, edge-compatible (Cloudflare Workers, Bun) |
| Fastify | Fast, schema-based |

If Go:
| Framework | Best for |
|-----------|----------|
| **Chi** | Lightweight, idiomatic (recommended for most) |
| Gin | Fast, popular |
| Echo | Full-featured |

**Park if:** undecided. Recommend FastAPI (Python) or Next.js (TypeScript).

---

### Domain 4: Data Storage

**Goal:** Database, ORM/query builder, file storage.

Databases:
| Database | Best for |
|----------|----------|
| **PostgreSQL** | Most projects — relational, reliable, extensible (default) |
| **SQLite** | Prototypes, local-only, embedded |
| Supabase | Hosted Postgres + auth + storage out of the box |
| MongoDB | Document data, flexible schema |

ORM:
- Python: **SQLAlchemy 2.x async** | SQLModel | Django ORM
- TypeScript: **Prisma** | Drizzle | Kysely | TypeORM

File storage: **Local disk** (prototypes) | **S3 / B2 / Supabase Storage** (production)

**Park if:** undecided. PostgreSQL as default. Park ORM choice if needed.

---

### Domain 5: API Design

**Goal:** API style, auth, error format, pagination.

API style: **REST** (default) | GraphQL | tRPC | WebSocket (supplementary)

Auth:
| Approach | Best for |
|----------|----------|
| **JWT + refresh tokens** | Most APIs (recommended default) |
| **Session-based** | Server-rendered apps |
| **OAuth2 / OIDC** | Social login, enterprise SSO |
| **Supabase Auth / Clerk / Auth0** | Turnkey solution |
| **Park** | Don't need auth for MVP |

Error format (recommend this standard):
```json
{ "error": { "code": "VALIDATION_ERROR", "message": "Human-readable", "details": {} } }
```

Pagination: **Cursor-based** | Offset-based

**Park if:** auth isn't critical. Record leaning as a note.

---

### Domain 6: Frontend (if applicable)

**Goal:** Frontend framework, CSS approach.

| Framework | Best for |
|-----------|----------|
| **React + Vite** | Most web apps (recommended default) |
| **Next.js** | Full-stack React with SSR |
| SvelteKit | Less code, fast |
| Vue + Nuxt | Popular alternative |
| htmx | Minimal JS, server-rendered |
| No frontend (API-only) | Backend/microservice |

CSS: **Tailwind CSS** | CSS Modules | styled-components | shadcn/ui

**Park if:** undecided. API-only if no UI mentioned.

---

### Domain 7: Testing

**Goal:** Framework, strategy.

- Python: **pytest + pytest-asyncio** | unittest
- TypeScript: **vitest** | jest
- Go: **go test + testify** | go test

Strategy (recommend):
- Unit tests for services (mocked DB)
- Integration tests for API endpoints (real DB)
- Prototype baseline: at least ONE runnable check per non-trivial logic (no frameworks/fixtures required)
- Coverage target: 80% is a **production-phase** target, not a day-one mandate — defer it

**Park if:** not urgent. Record framework choice.

---

### Domain 8: Infrastructure

**Goal:** Hosting, CI/CD, containers.

Hosting: **Railway / Fly.io / Render** | Vercel | AWS / GCP / Azure | Self-hosted
CI/CD: **GitHub Actions** | GitLab CI
Containers: **Docker** | Skip for now

**Always offer to park this domain.** Hosting decisions rarely block prototyping.

---

### Domain 9: Ontology — Domain Glossary

**Goal:** Establish the Ubiquitous Language for the project, including ID formats.

After the technical domains, ask:

"Let's define the key terms this project uses. I'll suggest terms based on your description, and you can add more. Each term needs: name, definition, whether it's a core concept, its **ID format**, and any **forbidden synonyms** (terms the AI must never use instead)."

For a project about "tracking energy usage in buildings," the agent would suggest:
- **Building** — physical structure with energy metering. ID: `bldg_<uuid_prefix>` (e.g. `bldg_a1b2c3`). Forbidden synonyms: property, facility, site, location.
- **Energy Consumption** — measured usage over time (kWh). ID: integer auto-increment. Forbidden synonyms: usage, power draw, load.
- **Meter** — device that measures consumption. Relationship: Building **has many** Meters (1:N). ID: `mtr_<uuid_prefix>`. Forbidden synonyms: sensor, reader, gauge.
- **Tenant** — occupant of a building unit. ID: UUIDv7. Forbidden synonyms: leaser, occupant, renter, client, customer.
- **Invoice** — bill based on consumption. ID: `inv_<yyyyMMdd>-<seq>`. Forbidden synonyms: bill, statement, charge.

**Always ask about ID format** for every term — "should IDs be UUIDv7, auto-increment integers, or a prefixed string like `mtr_abc123`?" Inconsistent ID formats between entities cause bugs that only surface in production.

**Always ask about relationships** — for each pair of related terms, clarify the cardinality (1:N, N:M, 1:1). The check action validates code against these relationships.

**Always ask about forbidden synonyms** — explicitly list terms the AI must never use. This is the most effective guard against terminology drift.

Collect 5-15 terms. If the user wants to skip, park it. The ontology is critical for AI agents to use consistent terminology.

---

### Domain 10: Modularity & Module Boundaries (advanced — optional)

**Goal:** Decide whether to use flat structure (start here) or modular-by-domain structure.

**Ask at the end:** the project is now well-defined. Offer this as an optional discussion:

"Would you like to think about how to split the system into independent modules, or should we start with the flat structure and extract modules when needed?"

If the user wants modularity, identify the natural module boundaries. For example, a building energy tracking system might have:
- **core/** — shared database sessions, config, middleware
- **buildings/** — building registry, floor plans, meter assignments
- **measurements/** — energy readings, sensors, data aggregation
- **billing/** — invoicing, pricing, tenant charges
- **reports/** — dashboards, exports, analytics

**Rules for modules (add to architecture.md):**
- Each module owns its own database tables — no cross-module table access
- Each module owns its own API routes — prefix matches module name (`/api/v1/buildings/*`)
- Modules can import from **core/** only — never from other modules
- External SDK calls stay behind a narrow adapter; add a separate interface only when there is a real test-double, second-provider, or replacement need

- **Start with flat structure if:** the project has fewer than ~5 domains or the user isn't sure. Extract when a domain has its own language, multiple entry points, distinct change reasons, or difficult-to-test dependencies — not only because of a line-count threshold.

---

## File Generation

After the interview, generate these files:

### AGENTS.md (project root — short entry point)

A 20-30 line file the AI reads on every session. Contains just:
- Brief project name and description
- Quick-reference tech stack table
- Links to architecture.md and ontology.md for details
- The Don't-Do list (most important section)
- Link to parking-lot.md

See `references/agents-md-template.md`.

### docs/specs/architecture.md (detailed technical constitution)

Contains ALL the technical decisions from the interview:
- Full tech stack table with version pinning and approved dependencies
- **Project structure** — flat (start here) or modular-by-domain (for larger systems)
- **Module architecture** — dependency direction rules, interface/adapter pattern for external services
- Naming conventions table
- **Static Typing Rules** — complete annotations required, with typed vs. untyped code examples
- Data layer rules with **correct vs. incorrect code examples** (prevents layer-merging)
- API conventions with structured JSON contracts (error format, pagination)
- Testing rules
- Security constraints
- **Feature Development Workflow** — the exact 5-step cycle (Plan → Test → Implement → Verify → Commit)
- AI Execution Directive — numbered rules with consequences for violations

See `references/architecture-template.md`.

### docs/specs/ontology.md (domain glossary)

Contains every term collected during the ontology interview:
- Mermaid class diagram of entity relationships (parsed by AI before code generation)
- Term with definition, ID format, forbidden synonyms, and relationship cardinality
- Multi-entity structural rules (foreign key enforcement, ID consistency)
- Naming convention pipeline table (class → DB → API → JSON → variables)
- Quick-reference glossary

See `references/ontology-template.md`.

### docs/adr/0001-stack-decisions.md

ADR capturing every decision. Both decided and parked items. See `references/adr-template.md`.

### docs/parking-lot.md

Every deferred decision with:
- Why it was deferred
- Concrete review trigger
- Any partial guidance

See `references/parking-lot-template.md`.

### docs/features/ — Feature contracts

Per-feature contracts, written at the start of each feature (Step 0 of the Feature Development
Workflow). Each defines: Feature, User, User outcome, Happy path, Acceptance criteria, Not
included, and the golden path if demo-critical. See `references/feature-contract-template.md`.

### opencode.json (project root)

Configures OpenCode to load the constitution efficiently. Only **AGENTS.md** is loaded into every
session (~200 tokens). It is the single entry point that *points at* the detail files
(architecture, ontology, features, ADR, parking lot, roadmap) in its "Source of Truth" list, so the
AI reads the full detail file only when a task needs it.

We deliberately do **not** register the project's constitution files in `references`: OpenCode's
`references` are for **external directories and Git repositories** (content outside the current
project), not for files already inside it. AGENTS.md is the documented way to surface in-project
files to the agent.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    "AGENTS.md"
  ],
  "permission": {
    "*": "allow",
    "bash": {
      "*": "ask",
      "git *": "allow",
      "grep *": "allow",
      "python3 -m pytest*": "allow",
      "npm test*": "allow",
      "go test*": "allow"
    },
    "webfetch": "allow",
    "external_directory": "ask"
  }
}
```

**Token cost:** AGENTS.md at ~200 tokens per turn — the only thing auto-loaded. It carries one-line
pointers to the detail files (architecture.md, ontology.md, features/, ADR/, parking-lot.md,
roadmap.md), which the AI opens only when relevant. No reference descriptions are needed for
in-project docs, so this is cheaper (~200 tokens/turn) than the older references-based approach.

This config:
- Loads AGENTS.md (short entry point) on every session — keeps the AI oriented and points it at the detail files it should read on demand
- Does NOT register in-project doc files as `references` — those are surfaced via AGENTS.md, the correct in-project pattern; `references` stays reserved for external directories/repos
- Allows all reading/writing/editing tools by default
- Requires approval for unknown shell commands (safe git/grep/test commands auto-approved)
- Asks before accessing files outside the project directory
- Pass `--auto` to skip approval prompts entirely during focused work

**Note:** OpenCode also has a built-in `/init` command (run in the TUI) that scans your repo and generates or improves AGENTS.md. Our skill generates a more comprehensive constitution, but `/init` is useful for quick regenerations later.

### .gitignore (project root)

Generate a standard `.gitignore` for the chosen stack. At minimum exclude:
- `__pycache__/`, `*.pyc` (Python)
- `node_modules/` (Node)
- `.env`, `.env.local`
- `*.db`, `*.sqlite3`
- `dist/`, `build/`
- IDE directories (`.idea/`, `.vscode/`, `*.swp`)

See `references/gitignore-template.md`.

### docs/roadmap.md — Feature Roadmap & Backlog

A living document that tracks what to build and in what order. Generated with three sections:
- **Milestones** — high-level goals with target dates
- **Feature Backlog** — prioritized features by domain, each with priority (P0–P3) and status
- **Nice-to-Haves** — unbounded ideas for later

The roadmap is listed in AGENTS.md's "Source of Truth" set, so the AI reads it on demand when planning. See `references/roadmap-template.md`.

> **Prefer the `product-roadmap` skill** for generating this file. That skill runs the product-strategy interview (vision, users, MVP scope, priorities, milestones, scope guardrails) that this technical skill does not. This skill can still generate a basic roadmap.md, but use `product-roadmap` when product thinking is needed first.

### .opencode/commands/roadmap.md — OpenCode Custom Command (optional)

A `/roadmap` command for the OpenCode TUI. Without arguments, it shows current roadmap status and suggests the next feature. With a feature name as argument, it reads the roadmap + architecture + ontology and creates a detailed plan.

```bash
# In OpenCode TUI:
/roadmap                   # Show status and suggest next feature
/roadmap plan user-auth    # Plan a specific feature
```

See `references/opencode-roadmap-command.md`.

---

## Next Steps — What To Do After Generation

Your project constitution is ready. Here's what to do next:

### 1. Initialize git (recommended)
```bash
cd /path/to/project
git init
git add .
git commit -m "feat: initial project constitution"
```

### 2. Set up the actual project

You now have the rules — but not the code. Create the project with your chosen framework:

- **FastAPI:** `uv init` or `pip install fastapi`, then create `src/app/main.py` matching the structure in architecture.md
- **Next.js:** `npx create-next-app@latest ./ --typescript --tailwind`
- **Express:** `npm init`, then `npm install express`

> The AI agent you use for coding (OpenCode, Claude Code) will read AGENTS.md automatically on every session and follow the conventions. You don't need to re-explain anything.

### 3. Start with your first feature

Pick the smallest meaningful feature and tell your AI agent:

> Write a feature contract for [feature name], then plan and implement it following AGENTS.md conventions.

The AI will read the constitution, see the project structure, use the correct terms from ontology.md, and follow the data layer rules from architecture.md — all without you repeating yourself.

If you want the AI to work without approval prompts during focused sessions, start OpenCode with `--auto`:

```bash
opencode --auto
```

### 4. Use the roadmap

Your project includes a roadmap at `docs/roadmap.md`. In the OpenCode TUI, type `/roadmap` to see what's next, or `/roadmap plan <feature>` to plan a specific feature. Keep the roadmap updated as you go — move completed items to ✅ Done, add new ideas as 💡 Proposed.

### 5. When things change

- **Parked decision comes up?** → Use `Update mode` to resolve it without re-interviewing
- **Stack changes?** → Use `Update mode`: "Update the constitution: we switched from SQLite to PostgreSQL"
- **Something feels wrong?** → Run `Check mode` on the suspicious file

---

## Troubleshooting

### The AI is not following the constitution

1. **Check that AGENTS.md exists in the project root** — some tools read it only if present
2. **Verify opencode.json has the `instructions` field** pointing to AGENTS.md
3. **Restart the coding session** — some AI tools load the constitution only at session start
4. **Try an explicit prompt:** "Read AGENTS.md and follow the conventions in it"
5. **Review the pre-flight check** in AGENTS.md — it lists what the AI should verify before every file

### The constitution is getting outdated

- Run `Update mode` with your changed decision
- Update the `Last Updated` date in both architecture.md and ontology.md
- Move resolved parked items from parking-lot to ADRs

### Multiple AI agents produce inconsistent code

This is exactly what the constitution prevents, but if it happens:
1. Check that all agents have access to the same set of files (AGENTS.md + docs/specs/)
2. Verify no agent has its own conflicting rules in CLAUDE.md or .cursorrules
3. Add more entries to the **Forbidden Synonyms** lists in ontology.md
4. Add more **correct vs. incorrect code examples** in architecture.md

---

## Check Action

When invoked with a target file path, the agent performs a **Constitution Check**:

### Procedure

1. Read `docs/specs/architecture.md` and `docs/specs/ontology.md`
2. Read the target file
3. Validate against:
   - **Tech stack rules** — is the code using approved libraries? Any unknown imports?
   - **Architectural rules** — is the code in the right layer? (e.g., no business logic in route handlers)
   - **Security constraints** — any hardcoded secrets, missing input validation, SQL injection vectors?
   - **Simplicity / YAGNI** — any speculative abstraction, duplicate helper, unnecessary dependency, or complexity without evidence?
   - **Naming conventions** — do names match the project's conventions?
   - **Ontology terms** — does the code use the correct domain terms?
4. Output a **Constitution Check Report**:

```
## Constitution Check Report
Target: src/api/users.py
Date: 2026-08-17

### Tech Stack Check
| Rule | Status | Detail |
|------|--------|--------|
| Uses approved ORM | ✅ OK | SQLAlchemy import detected |
| No unknown deps | ✅ OK | All imports in approved list |

### Architecture Check
| Rule | Status | Detail |
|------|--------|--------|
| No business logic in handlers | ❌ FAIL | line 45-52: user creation logic in POST handler |
| Repository pattern for DB | ✅ OK | Uses users_repo.py |

### Security Check
| Rule | Status | Detail |
|------|--------|--------|
| No hardcoded secrets | ✅ OK | No secrets found |
| Input validation | ✅ OK | Pydantic schema present |

### Simplicity Check
| Rule | Status | Detail |
|------|--------|--------|
| Reuses existing capability | ✅ OK | Uses the project's existing pagination helper |
| No speculative abstraction | ⚠️ WARN | `NotificationFactory` has one product and one caller; use a direct service until a second implementation exists |

### Ontology Check
| Rule | Status | Detail |
|------|--------|--------|
| "customer" vs "user" | ⚠️ WARN | Ontology says "user", file uses "customer" on line 12 |
| Building→Meter cardinality | ❌ FAIL | Ontology says Meter belongs to one Building (1:N). Code on line 78 allows a Meter to belong to multiple Buildings (N:M) |
| Forbidden synonym "sensor" for Meter | ❌ FAIL | Line 34: variable `sensor_reading` uses forbidden synonym. Should be `meter_reading` |
| ID format: Meter IDs | ⚠️ WARN | Ontology says prefixed `mtr_*`, code uses bare UUID on line 23 |
```

### Severity Levels
- **❌ FAIL** — Must fix before merge
- **⚠️ WARN** — Should fix, non-blocking
- **✅ OK** — Passes

---

## References

- `references/agents-md-template.md` — Short AGENTS.md entry point template
- `references/architecture-template.md` — Detailed docs/specs/architecture.md template
- `references/ontology-template.md` — Domain glossary template for docs/specs/ontology.md
- `references/adr-template.md` — Architecture Decision Record template (with Decision Drivers)
- `references/parking-lot-template.md` — Template for deferred decisions
- `references/feature-contract-template.md` — Per-feature contract (scope, acceptance criteria, out-of-scope boundary)
- `references/roadmap-template.md` — Feature roadmap and backlog template
- `references/opencode-roadmap-command.md` — OpenCode `/roadmap` custom command
- `references/gitignore-template.md` — Stack-aware .gitignore entries
- `references/env-template.md` — Environment variables example file
- `references/skills-ecosystem.md` — The `npx skills` ecosystem: commands, relevant skills, install commands

## Pitfalls

- **AGENTS.md too long.** Keep it at 20-30 lines. Put the detail in architecture.md and ontology.md.
- **Parking decisions without a review trigger.** Every parked item needs a concrete condition ("before adding auth"), not "later."
- **Not updating the constitution.** When a parked decision is resolved, update architecture.md, the ADR, and the parking lot. An outdated constitution actively misleads.
- **Skipping the ontology.** Domain terms are where AI agents make the most inconsistent guesses. "User" vs "Customer" vs "Account" — the ontology settles this once.
- **Over-recommending without context.** The user has basic programming knowledge but no software engineering experience. Frame recommendations simply.
- **Skipping the Don't-Do list.** This is the most effective section. AIs are bad at knowing what NOT to do.

## Verification

The project is ready for AI-assisted development when:

- [ ] AGENTS.md exists (short entry point, ~20-30 lines)
- [ ] `docs/specs/architecture.md` exists with full tech stack, rules, security constraints, and development workflow
- [ ] `docs/specs/ontology.md` exists with 5-15 domain terms defined
- [ ] A feature contract exists for the first feature, stored under `docs/features/`
- [ ] `docs/adr/0001-stack-decisions.md` records all interview decisions
- [ ] `docs/parking-lot.md` exists with every deferred decision and a review trigger
- [ ] `docs/roadmap.md` exists with feature backlog, milestones, and priorities
- [ ] `.gitignore` exists for the chosen stack
- [ ] `opencode.json` exists with AGENTS.md in `instructions`, and no in-project files in `references` (those are reserved for external dirs/repos)
- [ ] Running a "constitution check" on a generated file passes (test by checking a file from the same project)
- [ ] The AI agent can describe the development workflow when asked: "What's the process for building a new feature?"