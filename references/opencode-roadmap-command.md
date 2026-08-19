# Roadmap Command

OpenCode custom command for working through the project roadmap.

Place this file at:
- **Location:** `.opencode/commands/roadmap.md` (project-root)
- **Usage in TUI:** `/roadmap` (show next feature) or `/roadmap plan M1-feature-name` (plan a specific feature)

---

## How It Works

This file can be used as a reference when generating the project, but the actual command file that goes in `.opencode/commands/` should contain:

```markdown
---
description: Show roadmap status and suggest next feature to build
agent: plan
---

Read @docs/roadmap.md and show the current roadmap status.

1. Summarize what's Done, what's Active, and what's Ready next
2. Suggest the highest-priority item in 📋 Ready status
3. Ask the user: "Should I plan this feature?"
```

For planning a specific feature from the roadmap, with arguments:

```markdown
---
description: Plan a specific feature from the roadmap
agent: plan
---

Read @docs/roadmap.md and @docs/specs/architecture.md and @docs/specs/ontology.md.

The user wants to work on: $ARGUMENTS

1. Find this feature in the roadmap
2. Read the relevant architecture rules and ontology terms
3. Create a detailed implementation plan following the Feature Development Workflow in architecture.md
4. Present: files to create/modify, test strategy, dependencies, ontology terms to use
```

## Generated file

When the codebase-constitution skill generates this command, the exact content is:

`.opencode/commands/roadmap.md`:
```
---
description: Show roadmap, suggest next feature, or plan a specific one
agent: plan
---

You are in roadmap planning mode. Read @docs/roadmap.md to understand the current state.

If no arguments given:
1. Summarize Done / Active / Ready / Parked items by milestone
2. Suggest the highest-priority Ready item
3. Ask if the user wants to plan it

If arguments given (a feature name or ID):
1. Find it in the roadmap
2. Also read @docs/specs/architecture.md and @docs/specs/ontology.md
3. Create a detailed plan: files, tests, ontology terms, data layer rules
4. Present the plan and ask for confirmation
```

The `agent: plan` setting ensures this command runs with read-only permissions — it can analyze the codebase and suggest changes without making any modifications.