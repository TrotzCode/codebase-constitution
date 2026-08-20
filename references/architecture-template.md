# Architecture — {Project Name}

**Last Updated:** {YYYY-MM-DD}
**Project stage:** {Prototype / Pilot / Production}

> The quality bar expected of this project is defined per stage in `docs/project-stage.md`. Everything in this file must be read against that bar. Requirements here are current — when the stage or a decision changes, update this file and record the change in an ADR.

## 1. Current Stage & Quality Bar

This project is at **{Prototype / Pilot / Production}**. Apply the stage-appropriate quality bar from `docs/project-stage.md`. In short, evaluate the codebase across these stable dimensions, expecting stronger evidence as the project matures — **not** a bigger technology stack:

| Dimension | Prototype | Pilot | Production |
|---|---|---|---|
| Functional correctness | Core workflow works reliably | Stronger regression coverage | Explicit, verified requirements |
| Security | Basic floor: no committed secrets, input validation, no invented auth | Real authentication/authorization | Hardening and review |
| Privacy | Controlled/synthetic data | Real data handled intentionally | Defined handling paths |
| Data durability | Fine with synthetic data | Automated backups + tested restore | Defined retention/recovery |
| Recoverability | Simple recovery path | Designed, tested recovery | Planned, exercised recovery |
| Observability | Logs enough to debug | Useful error reporting | Monitoring/alerting |
| Deployment safety | Minimal | Controlled, staging where justified | Safe deploy + rollback |
| Performance | Reasonable for the slice | Adequate for real users | Based on measured need |
| Reliability | Core path dependable | Managed operational failure modes | Explicit, validated |

**Principle:** *Choose the simplest conventional solution sufficient for the current requirements; introduce more architecture only when there is evidence it is needed.* This file states defaults and decision rules — not universal mandates. Before adding a repository layer, a cache, a queue, or soft-delete columns, ask whether the current stage and slice genuinely need it.

## 2. Tech Stack

| Layer | Choice | Version | Purpose |
|:---|:---|:---|:---|
| **Runtime** | {Python / Node / Go / ...} | {3.13 / 22 / 1.23} | Primary runtime |
| **Web Framework** | {FastAPI / Express / Next.js / Gin} | {latest} | Web/API server |
| **Database** | {PostgreSQL / SQLite / MongoDB} | ... | Primary store |
| **ORM / DB Client** | {SQLAlchemy / Prisma / Drizzle} | | Data access |
| **Auth** | {None yet / framework-native / managed provider} | | See §Auth; not blindly JWT |
| **Queue** | {None until justified} | — | Park until shown needed |
| **Cache** | {None until justified} | — | Park until shown needed |
| **Test Framework** | {pytest / vitest / go test} | — | Automated suite |
| **Static Analysis** | {ruff / eslint / golangci-lint} | — | Linting |
| **Formatting** | {ruff / prettier / gofmt} | — | Enforced formatting |
| **Type Checking** | {mypy / TypeScript} | — | Static typing |
| **Dependency Engine** | {uv / npm / go mod} | — | Package management |

### Approved External Dependencies

Do **not** install unlisted third-party packages. Adding one requires an ADR and, to respect privacy, must be documented in `.env.example` only. List approved deps here:

```
{FastAPI / SQLAlchemy / alembic / pydantic / httpx / pytest / ...}
```

## 3. Authentication (default: the simplest thing that is actually needed)

Apply this order of preference — do not default to JWT:

1. **No authentication** where there is genuinely no need to distinguish users (e.g. internal/staged tool, demo over a personal network).
2. **Framework-native or managed provider**: a library that comes with the framework (e.g. session auth, Supabase/Clerk/Auth0-managed) that is simpler and safer than carving a token scheme.
3. **JWT / refresh tokens** only when its semantics are actually required (e.g. stateless distributed services, a mobile/CLI client, cross-origin tokens, short-lived access with audience scoping).

Record the choice and why in the ADR. Islands where auth is absent/partial must be called out.

## 4. Project Structure

Start **flat**; extract modules only when a domain shows multiple entry points, its own language, distinct change reasons, or hard-to-test dependencies. (Progressive extraction.)

```
{project-root}/
├── src/{app}/
│   ├── api/          # thin route handlers
│   ├── models/       # ORM definitions
│   ├── schemas/      # request/response validation (Pydantic / Zod)
│   ├── services/     # pure business logic
│   └── core/         # config, middleware, db session
├── tests/
├── docs/
│   ├── specs/        # architecture.md, ontology.md, project-stage.md
│   ├── features/     # feature contracts
│   ├── adr/          # decision records
│   └── api/          # OpenAPI
├── migrations/
├── scripts/
└── AGENTS.md
```

For larger systems, adopt the modular-by-domain variant (each module owns `api/models/schemas/services` under `src/{app}/<domain>/`, sharing only `core/`).

## 5. Naming Conventions

| Scope | Convention | Example |
|:---|:---|:---|
| Files / modules | {snake_case / kebab-case} | `user_profile.py` |
| Classes | PascalCase | `UserProfileService` |
| Functions / methods | {snake_case / camelCase} | `calculate_total_spent()` |
| API route paths | kebab-case | `/api/v1/user-profiles` |
| DB tables | snake_case, plural | `user_profiles` |
| DB columns | snake_case | `billing_address_id` |
| JSON response keys | camelCase | `"billingAddressId"` |
| Git branches | kebab-case | `feat/add-user-auth` |
| Environment vars | UPPER_SNAKE_CASE | `DATABASE_URL` |

## 6. Simplicity & Evidence

> **Read before you climb.** Understand the problem and trace the real code flow before simplifying; the smallest change in the wrong place is a second bug.

Choose the smallest design that satisfies the **current, confirmed** requirement. Stop at the first option that works:

1. Does this need to exist now? If for a hypothetical future, park it.
2. Does the codebase already have a suitable pattern/helper? Reuse it.
3. Does the stdlib/framework/database provide it? Prefer it.
4. Does an approved dependency provide it? Reuse it.
5. Otherwise, write the smallest clear implementation that preserves validation, error-handling, security, accessibility, and tests.

**Evidence before abstraction:** do not add factories, plugin systems, queues, caches, event buses, or services "because it might be useful." Add one only when there are two real implementations/consumers, repetitive changes in 3+ places, an external boundary needing a test double, a measured perf/ops require, or an ADR reason. Temporary simple implementations are marked `simplify:` with a ceiling + upgrade path and listed in `docs/parking-lot.md`.

## 7. Module Architecture & Dependencies

- `core/` — shared only, must not import modules.
- Domain modules import from `core/` only, never other modules.
- External-SDK calls sit at a narrow, concrete adapter; add an interface only when a real test-double / second-provider / replacement need appears.

## 8. Reversible vs. expensive decisions

Spend more care on the expensive column; change the cheap one freely (styling, logging lib). Expensive: language/framework, database, core data model, auth provider, API boundaries.

## 9. Data Layer (defaults — revisit on evidence)

- {ORM} {async/sync} session pattern throughout.
- Keep route/transport handlers simple. It is fine for a small prototype to call the ORM directly for straightforward CRUD/read operations.
- Non-trivial or reusable business logic lives outside the handler (a service or helper), not duplicated across route handlers.
- Introduce a service, repository, or data-access abstraction only when it provides a concrete benefit (a repeated complex query, a second store, a real test double) — not as a mandatory layer.
- Avoid raw SQL scattered through route handlers. Where raw SQL is genuinely the simplest appropriate solution, keep it localized in a clear data-access place rather than embedding SQL throughout transport/UI code.
- Schema changes via {Alembic / Prisma Migrate} migrations.
- Primary keys: **use the framework default** (auto-increment `bigint`, or UUID when the framework defaults to it). **UUIDv7 is not required**; choose it only when distributed/offline-friendly ids justify it.
- Soft delete: use nullable `deleted_at` **only where** you need actual recovery of past states, and record an ADR where you deliberately decide a hard `DELETE` is correct. Not universally.
- Connection pooling: the framework/DB driver's built-in pool first; do not stand up PgBouncer/extra pooling infra unless measurements demonstrate a need.

### Protected data operations
Destructive or hard-to-reverse changes (dropping columns, deleting rows, changing identifiers, destructive type conversions, rewriting persisted data, non-reversible migrations) require stepping through an approval gate before execution: state what will change, what data may be affected or lost, whether a backup is required, and how rollback/recovery works. This gate is reserved for genuinely destructive or hard-to-reverse operations — ordinary additive migrations (new column/table/index) are routine until there is evidence to the contrary.

### Code Alignment — Correct vs. Incorrect

The key goal is keeping handlers simple and not duplicating non-trivial logic. For a straightforward read/CRUD this can stay in the handler or call the ORM directly. Once the logic becomes non-trivial or reusable, pull it out:

```python
# ❌ Non-trivial, reusable business logic and DB work stuffed into a route handler
@router.post("/api/v1/orders")
async def create_order(payload: OrderSchema, db: AsyncSession = Depends(get_db)):
    item = await db.execute(select(Item).where(Item.id == payload.item_id))
    if not item:
        raise HTTPException(status_code=400, detail="Item missing")
    total = compute_discount(payload.items, user_tier(payload.user_id))  # logic that will be reused
    db.add(Order(item_id=payload.item_id, quantity=payload.quantity, total=total))
    await db.commit()

# ✅ Handler stays thin; non-trivial/reusable logic lives in one place (a helper or service)
@router.post("/api/v1/orders")
async def create_order(payload: OrderSchema, orders: OrderService = Depends()):
    return await orders.create(payload)
```

Match the choice to the case: a simple CRUD read may call the ORM directly in the handler; reserve a service/repository layer where it genuinely earns its keep.

## 10. Static Typing

**Every function signature: complete type annotations.** Typing is a valuable **guardrail** — it catches a broad class of mechanical errors early, but it is a safety net, not a claim that it catches *most* AI bugs. Keep it strict; don't overstate it.

- All params/returns explicitly typed.
- `Optional[X]` / `X | None` for nullable values.
- Collections `list[X]`, `dict[K,V]`.
- No bare `Any` unless interfacing with untyped third-party code.
- Run `{mypy --strict}` / `{tsc --noEmit}` before commit; no `# type: ignore` without a documented reason.

## 11. API Conventions

- Base path `/api/v1` unless justified otherwise.
- Error contract (single format):
```json
{ "error": { "code": "VALIDATION_ERROR", "message": "", "details": {} } }
```
- Pagination envelope (cursor or offset):
```json
{ "data": [], "meta": { "cursor": "…", "has_more": true } }
```
- Query params `?cursor=&limit=50`.

## 12. Testing

- **Baseline (applies now):** every non-trivial logic leaves at least ONE runnable check that fails if the logic breaks (assert self-check or one small test; no framework/fixtures needed). Trivial one-liners don't need a test.
- **Proportional verification (applies now):** every change requires appropriate verification. Non-trivial business logic, calculations, transformations, regressions and important behaviours should normally have automated tests. Trivial plumbing, exploratory UI work, configuration and similar changes may be covered by an existing automated check plus manual/golden-path verification. Nothing is considered done merely because the AI believes it works. Acceptance criteria exist before implementation; when you fix a bug, strongly prefer a regression test whenever the failure can reasonably be reproduced automatically.
- **Three verification levels:** (1) Automated (tests/lint/types where applicable) — does it behave per rules? (2) Human functional — does it do what's asked? (3) Demo/golden path — from a fresh state, run the single end-to-end workflow that proves value.
- Maintain the documented **golden path**; every significant change ends by running it.
- Test files mirror code structure (`tests/unit/services/...` mirrors `src/{app}/services/`).
- Run full suite pre-commit; a single test during dev.
- **Coverage target (deferred-stage):** 80% is a pilot/production aspiration, not a prototype mandate.

## 13. Security

- Zero secrets in code — env or secret manager; commit only `.env.example`.
- Input validation at every trust boundary.
- ORM parameterized everywhere; raw `f"SELECT…"` forbidden.
- CSRF for session auth; HTTPS in any deployed environment.
- Rate-limit only auth endpoints that legitimately need it (introduce when user-exposure justifies).

**Security floor (Prototype minimum, always):** never commit credentials; don't invent auth (use established pattern/provider); no public admin/debug endpoints; validate input; never execute user strings as code/SQL/shell; keep deps reasonably current; least privilege for demo users; back up before risky DB changes. Pen-testing/threat model/formal review are pilot/production-phase.

## 14. Privacy

- **Safe by default:** source, synthetic demo data, public API docs.
- **Check-first (never default):** real customer data, production DB dumps, contracts, credentials, personal data. Do not paste findings into prompts; human decides, scoped.
- Rather than learning against a developer DB, prefer reproducible synthetic fixtures.

## 15. Observability floor

- **Logs** — enough to reconstruct what happened.
- **Health** — a `/health` endpoint certifying alive.
- **Error reporting** — failures logged with enough context to diagnose; never log secrets.
No monitoring stack required for Prototype; add (Grafana/Prometheus etc.) only when a stage argues.

## 16. Reproducibility

- Lock dependency files.
- `.env.example` (never `.env`).
- Document install/start/test/config in README.
- If only one machine can run it, it isn't reproducible yet.

## 17. AI Execution Directive (stable across stages)

1. **Import compliance** — audit imports; block unlisted ones; ask.
2. **Terminology** — cross-check against ontology.md.
3. **Pre-flight** — run AGENTS.md checks first.
4. **Architectural deviation** — pause; output a conflict note; obtain confirmation.
5. **Performance** — avoid nested loops/N+1 in API handlers; eager-load related entities.
6. **Typing** — complete annotations; run the checker.
7. **Constitution maintenance** — update here, ADR, and parking lot when the stack/stage changes.
8. **Approval gate before implementation** — first inspect + report plan, risks, and tests; wait for human OK.
9. **State assumptions/least confidence** — report uncertainty; cite installed version vs memory; disclose untested claims.
10. **Root-cause** — fix the shared function across all callers, not only the one fatal path.
11. **Destructive data gate** — see §9.
12. **Deterministic over AI judgment** — prefer real tooling; never claim a check passed when you didn't run it; an AI "looks fine" review is not a green CI.

## 18. Feature Development Workflow

Every feature follows this cycle; don't skip steps.

- **Step 0 — Write a feature contract** (Feature/User/User outcome/Happy path/Acceptance criteria/**Not included**/**Assumptions & constraints**/golden path if demo-critical) into `docs/features/`. Implementation bounded by this contract only.
- **Step 1 — Plan**: read architecture.md, ontology.md, parking-lot (resolve any fired trigger first), list files/layers you'll touch.
- **Step 2 — Design the verification**: before writing implementation, ensure acceptance criteria → expected behaviour are clear; if it helps, write the test first (TDD), otherwise decide the concrete check that comes in the same change.
- **Step 3 — Implement** the minimal code; follow correct-vs-incorrect examples; use the pre-flight.
- **Step 4 — Verify**: `pytest` / `npm test` / `go test ./...`, `{mypy --strict}` / `{tsc --noEmit}`, linter, constitution check on changed files.
- **Step 5 — Commit** with `git add` + conventional message (`feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`).

**Failure handling:** fail → revisit step; a failed verification never silently bypassed; an architecture bug → Update mode first.

## 19. Parked Decisions

`docs/parking-lot.md` catalogs decisions deliberately deferred vs. forgotten. Each item has a revisit trigger. When the stage changes (or a trigger condition is met), re-inspect the list, surface entries now due into the review, and move resolved items to ADRs.