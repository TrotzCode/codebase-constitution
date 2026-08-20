# Project Stage — {Project Name}

**Last Updated:** {YYYY-MM-DD}

This short file states the current maturity stage of the project and the quality bar that applies right now. It is intentionally brief — the detail lives in `docs/architecture.md`. Keep this file in sync whenever the project crosses a stage boundary.

## Current Stage

**{Prototype / Pilot / Production}**

### Current objective

One or two sentences on what this stage is trying to establish. For a Prototype:

> Validate that the target user gets value from the core workflow while keeping experiments small, understandable and reversible.

### Quality bar appropriate to this stage

What must be true at this stage. The bar always includes fundamentals (core workflow works reliably, relevant automated checks pass, manual golden-path verification is possible, no secrets are committed, demo/test data is reproducible where relevant, deployment is recoverable, meaningful failures can be diagnosed, important shortcuts are explicit rather than hidden). It deliberately excludes concerns the stage does not yet justify (production infra, scaling, comprehensive monitoring, formal security process, exhaustive test coverage) unless evidence changes that.

## Assumptions

Important conditions the current stage relies on. When one of these stops being true, revisit the design:

- {e.g. synthetic/demo data only; a single controlled user; desktop browser only}
- {e.g. no real customer data retained yet}

## Accepted shortcuts

Explicit, deliberate, reversible shortcuts taken to accelerate learning. For each: what it is, why it is acceptable now, and its ceiling/upgrade path (see `docs/parking-lot.md`).

- {e.g. "Reports generated in-process; fine until a report exceeds ~10s or sends email."}
- {e.g. "External integration mocked; acceptable until a real provider API key is issued."}

## Engineering concerns explicitly deferred

Queues/workers, caching, rate limiting, API versioning, monitoring stack, staging environment, formal security review, resilient infra, horizontal scaling — *only if* they are genuinely not justified at this stage. Everything deferred belongs in `docs/parking-lot.md` with a revisit trigger.

## Graduation criteria — moving to the next stage

Checkable conditions that, when met, mean the project should be reviewed for promotion to the next stage. These inform the **Review / Stage Transition** procedure.

| Current stage | Transition to | Graduation criteria (examples) |
|---|---|---|
| Prototype | Pilot | A real external user will depend on it, and/or real customer data will be retained |
| Pilot | Production | The product must operate as a general, always-on dependable service |

For example, Prototype → Pilot graduation typically asks: is authentication and user lifecycle real now? Is customer data handled intentionally? Are backups automated and restore-tested? Is error reporting good enough to diagnose issues reported by real users? Which deferred shortcuts must be reviewed?

The buttons below are inputs to a human decision — meeting them does not itself promote the project. The stage transition is approved by a person after reviewing evidence, gaps, and risks. Each criterion should resolve to Satisfied (with evidence) / Gap / Not applicable (with reason) / Explicitly deferred (with risk and revisit trigger), evaluated against concrete project state (config, tests, backup/restore procedure, deployment process, actual auth and observability), never against vague intent.

---

## Notes

- Do not put a technology roadmap here. This file records the *stage*, not the stack.
- When the stage changes, update this file and run a stage transition review of the constitution (architecture.md, ontology.md, ADRs, parking lot).