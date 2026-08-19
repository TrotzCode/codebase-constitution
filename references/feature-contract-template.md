# Feature Contract — {Feature Name}

Write this short contract BEFORE planning or implementing the feature. It defines the
boundaries and the definition of done for one feature. Store it under `docs/features/`.

| Field | Content |
|:---|:---|
| **Feature** | A short name, e.g. "View shipment status" |
| **User** | Who this is for, e.g. "Industrial CO₂ supplier" |
| **User outcome** | What they get out of it, e.g. "Understand where my shipment is and when it arrives" |
| **Happy path** | Numbered steps the user takes, e.g. 1) Open shipments → 2) Select shipment → 3) See status, volume, and ETA |
| **Acceptance criteria** | Concrete, checkable statements that define "done", e.g. "three example shipments appear", "status is visually understandable", "detail page works after a browser refresh", "an unknown shipment ID shows a sensible error" |
| **Not included** | Explicitly what is out of scope this iteration, e.g. editing, notifications, real logistics integration |

## Golden path (if this is the demo-critical feature)

The one complete user journey this feature supports, run from a fresh session as the demo check.

## Working agreement with the AI

Implement only this contract. Propose any scope expansion BEFORE implementing it — never silently
extend the "Not included" list.

## Shortcut ledger (optional)

List any `simplify:` shortcuts the implementation used, with their ceiling and upgrade path,
so they can be harvested into `docs/parking-lot.md`.