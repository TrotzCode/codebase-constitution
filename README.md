# Codebase Constitution

AI-generated code works best with clear guardrails. This skill defines practical codebase rules
and conventions that help your AI assistant produce consistent, maintainable code that is useful
beyond a quick prototype.

The skill runs a short interview with you, then generates the **constitution** — the set of
instruction files that keep an AI coding assistant consistent across sessions.
It is the "house rules" file set, plus a roadmap.

> Terminology: the artifact this skill produces is called the **constitution** (or "constitution
> files"). "Conventions" is reserved for per-file style only (naming conventions, conventional
> commits). The generator is this skill, `codebase-constitution`.

## What it generates

The constitution is stored *inside* the project folder so it is versioned with the code:

- `AGENTS.md` — short entry point, auto-loaded every session; its "Source of Truth" list points at
  the detail files below.
- `docs/specs/architecture.md` — tech stack, project structure, data-layer rules, API contracts,
  security constraints, AI guardrails, development workflow.
- `docs/specs/ontology.md` — domain glossary: agreed terms, ID formats, forbidden synonyms,
  entity relationships (partly your data model).
- `docs/features/` — per-feature **contracts** (scope, acceptance criteria, out-of-scope).
- `docs/adr/` — Architecture Decision Records (why each choice was made).
- `docs/parking-lot.md` — deferred decisions with review triggers, plus harvested `simplify:`
  shortcuts.
- `docs/roadmap.md` — feature backlog, milestones, priorities.
- `opencode.json`, `.gitignore`, `.env.example`.

## Key rules it bakes in (v3.4.0)

- **Feature contract first** (Step 0): user, outcome, happy path, acceptance criteria, and an
  explicit *not-included* list — before any code.
- **Understand → plan → implement**: the AI inspects, proposes, and **waits for approval** before
  writing code.
- **Read before you climb**: the simplicity ladder runs after the real flow is traced.
- **Three-tier testing**: automated + human functional + demo **golden path**; a passing test only
  proves its assertions pass.
- **Prototype defaults** (complexity budget): 1 app, 1 DB, 1 deploy target, 1 auth mechanism;
  boring technology wins.
- **Prototype security floor + privacy**: minimum safety rules, and an explicit "data the AI may
  see" check-first policy.
- **Observability + reproducibility**: logs/health/error reporting; lockfiles, `.env.example`, and
  a runnable-on-any-machine README from day one.
- **Root-cause fixes** (grep every caller), **no disabling tests**, `simplify:` markers harvested
  into the parking lot.

## OpenCode wiring

Only `AGENTS.md` is loaded via `instructions`; it points at the detail files on demand.
OpenCode's `references` are **not** used for in-project docs — they are reserved for external
directories / Git repositories. This matches OpenCode's documented model and avoids a
misconfigured, silently-ineffective references block.

## Use

In Hermes:

> Load codebase-constitution skill.
> I want to build [describe your project].

After bootstrap, the skill also supports **Check** (validate a file against the constitution) and
**Update** (change a decision without re-interviewing). For product thinking first, pair with the
`product-roadmap` skill.

## License

MIT — see [LICENSE](LICENSE).