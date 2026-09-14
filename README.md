# Codebase Constitution

AI-generated code works best with clear guardrails. This skill defines practical codebase rules
and conventions that help your AI assistant produce consistent, maintainable code that is useful
beyond a quick prototype.

The skill runs a short interview with you, then generates the **constitution** — the set of
instruction files that keep an AI coding assistant consistent across sessions. It is the "house
rules" file set.

> Terminology: the artifact this skill produces is called the **constitution** (or "constitution
> files"). The generator is this skill, `codebase-constitution`.

## One constitution, one lifecycle

A single set of governed documents follows a project through its maturity stages:
**Prototype → Pilot → Production**. The stage determines what engineering investment is
justified, not whether disciplined development applies — a Prototype is fast and lightweight,
not low-quality. The constitution evolves as the project changes; it is never regenerated or
forked per stage.

Maturity is a **quality bar**, not a technology stack. Requirements grow from "core workflow
works reliably" through "real users and durable data" to "explicit, verified operational
requirements" — it never mandates a specific infrastructure just because of the stage.

## What it generates

The constitution is stored *inside* the project folder so it is versioned with the code. Each
document owns one concern, and together they form a precedence-ordered set:

- `AGENTS.md` — short entry point, auto-loaded every session; holds the stable operating
  invariants, and its "Source of Truth" list points at the detail files below.
- `docs/project-stage.md` — the **normative maturity target**: current stage, objective, quality
  bar, accepted shortcuts, deferred concerns, and graduation criteria.
- `docs/architecture.md` — the **current technical reality**: how the system is actually built
  today (stack, structure, conventions), kept proportionate to the stage — no empty or
  "not applicable yet" sections.
- `docs/ontology.md` — canonical domain vocabulary; describes the domain model, not the
  implementation model (it does not prescribe tables, classes, or endpoints).
- `docs/features/` — per-feature **contracts**: scope, acceptance criteria, plus both
  *not-included* and *assumptions/constraints*.
- `docs/adr/` — Architecture Decision Records, **only for consequential/non-obvious decisions**;
  conventional stack choices are recorded in architecture.md instead.
- `docs/parking-lot.md` — intentionally deferred decisions, each with a concrete revisit trigger,
  distinguishing "we don't need this yet" from "we forgot."
- `opencode.json` and `.gitignore`.

## Key rules it bakes in (v3.5.2)

- **Feature contract first** (Step 0): user, outcome, happy path, acceptance criteria, an explicit
  *not-included*, and *assumptions/constraints* — before any code.
- **Smallest vertical slice**: deliver the smallest end-to-end change that creates a meaningful
  user outcome, not a rigid one-endpoint-one-table formula.
- **Understand → plan → implement**: the AI inspects, proposes, and **waits for approval** before
  writing code.
- **Minimum-sufficient / progressive disclosure**: templates are a menu of available sections,
  not mandatory ones; a small Prototype gets short documents that grow as real concerns emerge.
- **Keep route handlers simple, control complexity not layers**: simple CRUD/read may call the
  ORM directly; extract non-trivial or reusable logic; add service/repository layers only when
  they earn a concrete benefit.
- **Proportional verification**: every change gets appropriate verification; nothing is done
  merely because the AI believes it works. Tests are preferred for non-trivial logic; trivial
  plumbing may rely on existing checks + manual/golden-path.
- **Three-tier verification**: automated + human functional + demo **golden path**, and an AI
  "looks fine" review is not a substitute for a passing deterministic check.
- **Prototype security floor + privacy**: minimum safety rules and an explicit "data the AI may
  see" check-first policy.
- **Reproducible prototype/demo data**: golden path runs from a known, seeded state; real customer
  data never casually becomes test/AI input.
- **Protected data changes**: destructive or hard-to-reverse schema/data operations go through an
  explicit approval gate; ordinary additive migrations are routine.
- **Observability floor + reproducibility**: logs/health/error reporting; lockfiles
  and a runnable-on-any-machine README from day one.
- **Root-cause fixes** (grep every caller), **no disabling tests**, `simplify:` markers harvested
  into the parking lot.

## Maturity review (stage transitions)

When a project moves between stages, the skill runs a readiness **gap analysis** comparing
`docs/project-stage.md` (what should be true) against `docs/architecture.md` + code + tests (what
is actually true). Each item resolves to Satisfied (with evidence) / Gap / Not applicable /
Explicitly deferred (with risk + trigger). The output supports a human decision — it never
certifies a project "passed" into the next stage.

## Document precedence

When sources appear to conflict, the owner of the affected concern wins: AGENTS.md owns stable
invariants, `project-stage.md` owns the stage/quality bar, `architecture.md` owns current
technical reality, `ontology.md` owns terminology, the active feature contract owns the current
change's scope, ADRs explain *why* but don't override current architecture, and the parking lot
is not current architecture. If two authoritative documents conflict in their own areas, stop
and surface the contradiction.

## OpenCode wiring

Only `AGENTS.md` is loaded via `instructions`; it points at the detail files on demand.
OpenCode's `references` are not used for in-project docs — they are reserved for external
directories / Git repositories.

## Use

In Hermes:

> Load codebase-constitution skill.
> I want to build [describe your project].

After bootstrap, the skill also supports **Check** (validate a file against the constitution),
**Review** (stage-transition readiness), and **Update** (change a decision without
re-interviewing). For product thinking first, pair with the `product-roadmap` skill.

## License

MIT — see [LICENSE](LICENSE).