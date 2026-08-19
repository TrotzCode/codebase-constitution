# Architecture — {Project Name}

**Last Updated:** {YYYY-MM-DD}

## 🏗️ Tech Stack

| Layer | Choice | Version | Purpose |
|:---|:---|:---|:---|
| **Runtime** | {Python / Node / Go / ...} | `{3.13 / 22 / 1.23}` | Primary language runtime |
| **Web Framework** | {FastAPI / Express / Next.js / Gin / ...} | `{latest}` | API / web server |
| **Database** | {PostgreSQL / SQLite / MongoDB / ...} | `{16 / 3 / ...}` | Primary data store |
| **ORM / DB Client** | {SQLAlchemy async / Prisma / Drizzle / ...} | `{latest}` | Data access layer |
| **Auth Strategy** | {JWT / OAuth2 / Session / ...} | `—` | Authentication & authorization |
| **Queue Layer** | {Celery / Bull / None} | `—` | Background job processing |
| **Cache Layer** | {Redis / Memcached / None} | `—` | Caching layer |
| **Test Framework** | {pytest / vitest / go test / ...} | `—` | Automated test suite |
| **Static Analysis** | {ruff / eslint / golangci-lint / ...} | `—` | Code quality & linting |
| **Code Formatter** | {ruff / prettier / gofmt / ...} | `—` | Enforced formatting |
| **Type Checking** | {mypy / TypeScript / ...} | `—` | Static type checking |
| **Dependency Engine** | {uv / npm / go mod / ...} | `—` | Package management |

### ⛔ Approved External Dependencies

AIs are **strictly forbidden** from installing unlisted third-party packages. Adding an unapproved dependency requires an Architecture Decision Record (ADR).

```
{FastAPI / SQLAlchemy / alembic / pydantic / httpx / pytest / ...}
```

---

## 📁 Project Structure

Choose one structure based on project complexity:

### Flat (simple — start here)
Good for small projects, prototypes, and MVPs. All code grouped by technical layer.

```
{project-root}/
├── src/{app-name}/
│   ├── api/           — Route handlers (thin boundary layer, calls services only)
│   ├── models/        — ORM definitions (tables matching ontology.md)
│   ├── schemas/       — Request/response validation (Pydantic / Zod)
│   ├── services/      — Pure business logic (framework-agnostic)
│   ├── core/          — Config, middleware, DB sessions, app initializers
│   └── tasks/         — Background job workers
├── tests/
│   ├── unit/          — Service tests (mocked DB layer)
│   └── integration/   — Full API pipeline (real test DB)
├── docs/
│   ├── specs/         — architecture.md and ontology.md
│   ├── features/      — per-feature contracts (feature-contract template)
│   ├── adr/           — Architecture Decision Records
│   └── api/           — Generated OpenAPI schemas
├── migrations/        — DB schema version snapshots
├── scripts/           — Dev/ops helper scripts
└── AGENTS.md          — Core system entry point for AI context loading
```

### Modular by domain (for larger systems)
Each domain is self-contained with its own api/models/schemas/services. Better isolation, clearer ownership, scales to more features. Use when the flat structure starts feeling crowded.

```
{project-root}/
├── src/{app-name}/
│   ├── core/             — Shared across all modules
│   │   ├── database.py   — DB session factory
│   │   ├── config.py     — App configuration
│   │   └── middleware.py — Auth, error handling, rate limiting
│   ├── users/            — User management module
│   │   ├── api.py        — Route handlers (/api/v1/users/*)
│   │   ├── models.py     — ORM models (user table)
│   │   ├── schemas.py    — Pydantic request/response schemas
│   │   └── services.py   — Business logic
│   ├── billing/          — Billing module (separate domain)
│   │   ├── api.py        — Route handlers (/api/v1/billing/*)
│   │   ├── models.py     — ORM models (invoice, payment tables)
│   │   ├── schemas.py    — Request/response schemas
│   │   └── services.py   — Business logic
│   └── inventory/        — Inventory module
│       ├── api.py
│       ├── models.py
│       ├── schemas.py
│       └── services.py
├── tests/
│   ├── unit/
│   │   ├── users/
│   │   ├── billing/
│   │   └── inventory/
│   └── integration/
│       └── ...
├── migrations/
├── docs/
├── scripts/
└── AGENTS.md
```

---

## 📏 Naming Conventions

| Scope | Convention | Example |
|:---|:---|:---|
| **Files / modules** | {snake_case / kebab-case} | `user_profile.py` |
| **Classes** | PascalCase | `UserProfileService` |
| **Functions / methods** | {snake_case / camelCase} | `calculate_total_spent()` |
| **API route paths** | kebab-case | `/api/v1/user-profiles` |
| **DB tables** | snake_case, plural | `user_profiles` |
| **DB columns** | snake_case | `billing_address_id` |
| **JSON response keys** | camelCase | `"billingAddressId"` |
| **Git branches** | kebab-case | `feat/add-user-auth` |
| Environment variables | UPPER_SNAKE_CASE | `DATABASE_URL` |

---

## ✂️ Simplicity and Evidence Rules

> **Read before you climb.** The ladder below runs AFTER you understand the problem, never
> instead of it — read the task and the code it touches, and trace the real flow end to end
> before picking a rung. Lazy about the solution; never about reading. The smallest change in
> the wrong place is not lazy, it is a second bug.

Choose the smallest design that satisfies the *current, confirmed* requirement.
Before adding code, stop at the first option that works:

1. **Does this need to exist now?** If it supports only a hypothetical future, park it.
2. **Does the codebase already have a suitable pattern or helper?** Reuse it.
3. **Does the language standard library, framework, database, or browser provide it?** Prefer that.
4. **Does an approved, already-installed dependency provide it?** Reuse it.
5. **Otherwise:** write the smallest clear implementation that preserves validation, error handling, security, accessibility, and tests.

### Simplicity maxims

- Deletion over addition. Boring over clever. Fewest files possible.
- The smallest change that satisfies the requirement — do not refactor unrelated code "while you're here."
- When two standard-library approaches are the same size, choose the edge-case-correct one: laziness means less code, not a flimsier algorithm.

### Do Not Simplify These Away

- Input validation at trust boundaries
- Authentication, authorization, secrets handling, and SQL parameterization
- Error handling that prevents data loss or corrupt state
- Accessibility basics in user-facing UI
- Tests for non-trivial business logic, security-sensitive logic, parsers, calculations, and state changes

### Evidence Required Before Abstraction

Do not add a generic abstraction, factory, plugin system, configuration option, background queue, cache, event bus, or microservice solely because it might be useful later. Add one only when at least one concrete trigger is true:

- There are two real current implementations or consumers.
- The same change is being made in three or more places.
- A confirmed external boundary needs a test double or vendor replacement.
- A measured performance, reliability, or operational requirement demands it.
- An ADR records a project-specific reason.

When deliberately choosing a temporary simple implementation with a known ceiling, mark it inline with a `simplify:` comment naming its **ceiling and upgrade path**, AND list it in `docs/parking-lot.md` so it is harvested rather than forgotten. "Later" must not become "never". Example: `# simplify: in-process jobs until work can exceed the request timeout; then introduce a queue`.

---

## 🧱 Module Architecture & Dependencies

### Dependency Direction Rules

These rules prevent circular imports and ensure modules can be developed and tested independently.

```
core/  ←  users/  ←  (no cross-module deps)
core/  ←  billing/
core/  ←  inventory/
```

- **core/** — foundational: database sessions, config, shared middleware. Must NOT import from any module.
- **Domain modules** (users/, billing/, inventory/) — can import from **core/**. Must NOT import from other domain modules.
- If two modules need to share stable data shapes, define a small shared type or a public service contract under core/; do not create a generic shared layer for one incidental call.
- If module A needs module B synchronously in the same application, call B's documented service boundary. Use an event/message pattern only for genuinely asynchronous, retryable, or independently processed work.

### Interface / Adapter Pattern for External Services

Put external-service SDK calls at a narrow boundary, never throughout business logic. Start with a concrete adapter when there is one provider; introduce a separate interface only when a real test double, second provider, or vendor-replacement need exists. This keeps the dependency isolated without creating an abstraction with no consumer.

```python
# ✅ CORRECT: one narrow concrete adapter; business logic does not import the SDK
# src/{app}/billing/payment_gateway.py
import stripe

class StripePaymentGateway:
    async def charge(self, amount: int, token: str) -> str:
        charge = await stripe.Charge.create_async(amount=amount, source=token)
        return str(charge.id)

# src/{app}/billing/services.py
from billing.payment_gateway import StripePaymentGateway

class BillingService:
    def __init__(self, payment: StripePaymentGateway):
        self._payment = payment

    async def process_payment(self, invoice_id: str, amount: int) -> str:
        return await self._payment.charge(amount, invoice_id)
```

```python
# ❌ INCORRECT: SDK call spread through business logic and route handlers
import stripe

class BillingService:
    async def process_payment(self, amount: int, token: str) -> str:
        return stripe.Charge.create(amount=amount, source=token)
```

### Module Boundaries

- A module owns its database tables. No other module may query them directly — use the module's service layer.
- A module owns its API routes. Route prefixes match module names: `/api/v1/users/*`, `/api/v1/billing/*`.
- Keep the flat structure for as long as it works. Extract a module when a feature has its own domain language, multiple entry points, different change reasons, or direct dependencies are becoming difficult to test. File length is a review signal, not an automatic trigger.

---

## 🧭 Reversible vs. expensive decisions

Decisions differ hugely in what it costs to reverse them later. Spend much more thought on the
expensive list, and don't get stuck on the cheap one — you can change that casually.

- **Cheap to change later (don't overthink):** button styling, UI library details, logging library.
- **Expensive to change later (think hard BEFORE):** programming language / framework, database,
  core data model, authentication provider, API boundaries, deployment architecture.

Architecture Decision Records (see `docs/adr/`) exist so neither the human nor the AI re-litigates
a decided point — and so design effort stays focused on the expensive column.

## 🎯 Prototype defaults (complexity budget)

Every additional moving part must justify itself. Prototype defaults:

- 1 application, 1 database, 1 deployment target, 1 authentication mechanism
- As few external services as practical; prefer boring, mainstream, conventional choices

If the AI proposes a queue, cache, broker, worker, object storage, or microservices for a prototype,
ask: **"Why isn't the simpler architecture sufficient for the current requirements?"** Be suspicious
of proposals framed with: *scalable, enterprise, event-driven, distributed, microservices,
abstraction layer, plugin architecture* — unless the prototype actually needs them.

The simple implementation stays under a `simplify:` marker + parking-lot entry (Simplicity Rules)
until a concrete trigger forces an upgrade.

---

## 💾 Data Layer Rules

- {ORM} {async/sync} session pattern throughout
- All DB queries go through repository/service classes — never injected into route handlers
- Schema changes go through {Alembic / Prisma Migrate / ...} migrations
- Every table has: `id` (UUIDv7), `created_at` (UTC), `updated_at` (UTC)
- Soft deletes required — add nullable `deleted_at` column to every table. No hard `DELETE` without an overriding ADR.
- Connection pooling via {PgBouncer / built-in pool / ...}

### Code Alignment — Correct vs. Incorrect

AIs commonly write lazy code that merges layers. Below shows the exact pattern to avoid and the correct alternative.

```python
# ❌ INCORRECT: Business logic and DB query leaked into a route handler
@router.post("/api/v1/orders")
async def create_order(payload: OrderSchema, db: AsyncSession = Depends(get_db)):
    # Violates Data Layer Rule: raw query in handler
    item = await db.execute(select(Item).where(Item.id == payload.item_id))
    if not item:
        raise HTTPException(status_code=400, detail="Item missing")
    new_order = Order(item_id=payload.item_id, quantity=payload.quantity)
    db.add(new_order)
    await db.commit()
    return new_order
```

```python
# ✅ CORRECT: Route handler only handles transport. Service owns the logic.
@router.post("/api/v1/orders")
async def create_order(payload: OrderSchema, service: OrderService = Depends()):
    return await service.create_order(payload)
```

---

## 📝 Static Typing Rules

**Every function signature MUST have complete type annotations.** The AI must never write untyped code. This is the single most important guardrail for AI-assisted development — type checkers catch the majority of AI-generated bugs before runtime.

### Rules

- All function parameters and return values **must** have explicit type annotations
- Use `Optional[X]` / `X | None` for nullable values — never use a bare type where `None` is possible
- Use `list[X]` / `List[X]` and `dict[K, V]` / `Dict[K, V]` for collections
- Never use `Any` unless interfacing with untyped third-party code
- Run `{mypy --strict} / {tsc --noEmit}` before every commit
- All type annotations must pass `{mypy --strict}` / `{tsc --noEmit}` — no `# type: ignore` without a documented reason

### Code Alignment — Typed vs. Untyped

```python
# ❌ INCORRECT: Missing type annotations — AI will produce runtime errors here
def create_order(payload, db):
    order = Order(item_id=payload.item_id, quantity=payload.quantity)
    db.add(order)
    return order
```

```python
# ✅ CORRECT: Fully annotated — type checker catches errors instantly
from sqlalchemy.ext.asyncio import AsyncSession
from schemas.order import OrderCreate
from models.order import Order

async def create_order(payload: OrderCreate, db: AsyncSession) -> Order:
    order: Order = Order(item_id=payload.item_id, quantity=payload.quantity)
    db.add(order)
    await db.commit()
    await db.refresh(order)
    return order
```

---

## 🌐 API Conventions

- **Base path:** `/api/v1`
- **Authentication:** {JWT Bearer token in Authorization header / ...}

### Error Contract

Every failed request must return this exact structure:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable explanation of what failed.",
    "details": {}
  }
}
```

### Pagination Contract

List endpoints return this envelope:

```json
{
  "data": [],
  "meta": {
    "cursor": "eyJpZCI6IDQyfQ==",
    "has_more": true
  }
}
```

- Style: {cursor-based / offset-based}, query params `?cursor=<id>&limit=50`
- Rate limiting: {None yet / 100 req/min per user}
- Idempotency: {Not required / Use Idempotency-Key header}

---

## 🧪 Testing Rules

- **Prototype baseline (applies now):** non-trivial logic leaves at least ONE runnable check — the
  smallest thing that fails if the logic breaks (an assert-based self-check or one small test; no
  frameworks or fixtures required). Trivial one-liners need no test.
- **Three verification levels when a change is made:**
  1. **Automated** — tests, lint, type check: "does it behave per the specified rules?"
  2. **Human functional** — the operator clicks through it: "does it actually do what was asked?"
  3. **Demo / golden path** — run the single end-to-end workflow that proves value, from a fresh
     session: "would I trust showing this to someone?"
- Maintain a documented **golden path**; every significant change ends by running it.
- **Definition of done:** acceptance criteria met + automated checks pass + manually exercised +
  golden path still works + diff contains only intentional changes.
- Do NOT remove or disable an existing test to make the suite pass.
- Test files mirror code structure: `tests/unit/services/` → `src/{app}/services/`.
- Run the full suite before every commit; a single test during development.
- **Production-phase target (deferred):** the strict 80% line coverage and full TDD on every
  endpoint/service is a production (phase C) hardening target, not a day-one prototype mandate.

---

## 🔒 Security Constraints

- **Zero secrets in code** — use environment variables or a secrets manager
- **Input validation** — every external input runs through {Pydantic / Zod} schemas
- **SQL injection** — ORM parameterized queries only. Raw `f"SELECT * FROM ..."` is **forbidden**
- CSRF protection for session-based auth
- HTTPS enforced in production
- Rate limiting on auth endpoints

### Prototype security floor (minimum even for a demo)

- Never commit credentials.
- Don't invent authentication yourself — use an established pattern/provider.
- Don't expose admin / debug endpoints publicly.
- Use HTTPS in any deployed environment.
- Validate user input.
- Don't execute user-provided strings as code, SQL, or shell commands.
- Keep dependencies reasonably current.
- Give demo users the least privilege they need.
- Back up data before risky database changes.

Actual penetration testing, threat modelling, and a full security review belong to the production
phase (phase C).

---

## 🔒 Privacy: data the AI agent may see

An operating rule for AI-assisted engineering. Two buckets:

- **Safe by default** — source code, synthetic demo data, public API documentation.
- **Check first (never by default)** — customer information, production database dumps, contracts,
  credentials, internal company documents, personal data.

Do not paste credentials into prompts. If real data is required for a task, the human decides
consciously and scoped. When in doubt, treat it as check-first.

---

## 🩺 Observability floor (minimum to diagnose failure)

A beginner otherwise meets "the app doesn't work" with no way to diagnose it. Three concepts:

- **Logs** — notable events logged, so the operator can see *what happened* (`docker logs` or equivalent).
- **Health check** — a `/health` endpoint certifying the application is *alive*.
- **Error reporting** — failures logged with enough context to *diagnose*; never log secrets.

No monitoring stack (Grafana/Prometheus/etc.) is required for a prototype — these three suffice.

---

## 🔁 Reproducibility

The project must be runnable on another machine, not just the laptop that built it.

- Commit dependency lockfiles.
- Ship `.env.example` (never the real `.env`).
- Document in README: install, start, test, configure.

If only one machine can run it, the project is not reproducible yet.

---

## 🤖 AI Execution Directive

When generating code, analyzing files, or submitting changes, the AI agent **must** verify against these boundaries:

1. **Import Compliance** — Audit every package statement. If an import is missing from the Approved Dependencies section, abort and prompt the user. Do not install unapproved packages.
2. **Terminology Check** — Cross-check all names against `docs/specs/ontology.md`. Reject any undocumented synonym.
3. **Pre-Flight Check** — Run the verification steps in `AGENTS.md` before modifying any file.
4. **Architectural Deviation** — If a user instruction forces a pattern that violates this file, pause, output a conflict notice, and request confirmation before proceeding.
5. **Performance Constraint** — Do not add nested loops or N+1 queries in API handlers. Use batch loading or eager loading when fetching related entities.
6. **Static Typing** — Every function must have complete type annotations. Run `{mypy --strict} / {tsc --noEmit}` before commit. Never use `Any` without justification.
7. **Constitution Maintenance** — When the stack changes, update architecture.md, the ADR, and the parking lot. An outdated constitution actively misleads.
8. **Approval Gate** — Before implementing any feature, first inspect the relevant code and (without changing anything) report: how it currently works, which files you think need changing, your proposed implementation, risks/ambiguities, and the tests you would add. Wait for the human's approval before writing code.
9. **State Assumptions** — Report which parts of your plan or answer you are least certain about. For libraries/APIs, check the installed version and use its actual documentation rather than memory. If you did not run the tests, say so explicitly — an untested change is incomplete.
10. **Root-Cause Fixes** — A bug report names a symptom. Grep every caller of the function you touch and fix the shared function once; patching only the path a ticket names leaves a sibling caller still broken. One guard in the right place is a smaller diff than one per caller.

---

## 🔄 Feature Development Workflow

Every feature follows this exact cycle. The AI agent must not skip steps.

### Step 0: Write a feature contract
- Produce a short contract (Feature, User, User outcome, Happy path, Acceptance criteria, Not
  included) using the `feature-contract-template`; store it under `docs/features/`.
- Implement only this contract. Propose any scope expansion BEFORE implementing it.
- See `docs/specs/architecture.md` "Feature Development Workflow" for the golden-path guidance.

### Step 1: Plan
- Read the relevant parts of `docs/specs/architecture.md` and `docs/specs/ontology.md`
- Check `docs/parking-lot.md` — if a parked item is now needed, resolve it first via Update mode
- Identify which files will change and which layers they touch (api/ → services/ → models/)

### Step 2: Write tests first
- Write the test before the implementation code
- Tests must reflect the ontology terms and naming conventions
- See `test-driven-development` skill for the full TDD cycle

### Step 3: Implement
- Write the minimal code to make tests pass
- Follow the correct vs. incorrect code alignment examples in the Data Layer section above
- After writing, run the pre-flight check from AGENTS.md

### Step 4: Verify
- Run the full test suite: `pytest` / `npm test` / `go test ./...`
- Run the type checker: `{mypy --strict} / {tsc --noEmit} / {go vet}`
- Run the constitution check on new/modified files (see Check action)
- Run the linter: `ruff check` / `eslint` / `golangci-lint`

### Step 5: Commit
- Confirm all tests pass
- `git add <files>` + `git commit -m "type(scope): description"` using conventional commits
- Types: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`

### Handling failures
- If tests fail → return to Step 3 (implement)
- If constitution check fails → fix violations, never bypass
- If architecture rule is wrong → use Update mode to fix the constitution first, then continue

---

## ⏳ Parked Decisions

Certain system components are explicitly deferred. Review `docs/parking-lot.md` before attempting to architect these paths. Each parked item has a concrete trigger condition for when to revisit it.