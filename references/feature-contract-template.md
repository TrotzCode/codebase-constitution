# Feature Contract — {Feature Name}

Write this short contract BEFORE planning or implementing the feature. It defines the
boundaries and the definition of done for one feature, and it is the bounded unit of
work at every project stage. Store it under `docs/features/`.

| Field | Content |
|:---|:---|
| **Feature** | A short name, e.g. "View shipment status" |
| **Status** | `proposed` / `active` / `done` — changed only by contract completion plus human reprioritization |
| **User** | Who this is for, e.g. "Industrial CO₂ supplier" |
| **User outcome** | What they get out of it, e.g. "Understand where my shipment is and when it arrives" |
| **Happy path** | Numbered steps, e.g. 1) Open shipments → 2) Select shipment → 3) See status, volume, ETA |
| **Acceptance criteria** | Concrete, checkable statements that define done, e.g. "three example shipments appear", "status is visually clear", "detail page survives a browser refresh", "an unknown shipment ID shows a sensible error" |
| **Not included** | *Functionality* deliberately outside scope this iteration: editing, notifications, real logistics integration |
| **Assumptions / constraints** | *Conditions under which the current implementation is considered valid*. When one of these changes, the contract must be revisited. Examples: "uses synthetic demo data", "single controlled user", "desktop browser only", "data resets between demo sessions", "external integration is mocked". |

The distinction matters:
- **Not included** = features you intentionally do not build now.
- **Assumption / constraint** = the conditions under which what you *do* build is valid.

## Golden path (if this is the demo-critical feature)

The one complete user journey this feature supports, run from a **known starting state**
(e.g. a fresh seeded database) as the demo/reproducibility check.

## Working agreement with the AI

- Implement only this contract.
- Propose any scope expansion or any change to an *assumption* BEFORE implementing it —
  never silently extend "Not included" or silently assume a changed constraint.
- The acceptance bar may raise as the project matures without changing this mechanism.

## Shortcut ledger (optional)

List any `simplify:` shortcuts the implementation used, with their ceiling and upgrade
path, so they can be harvested into `docs/parking-lot.md`.