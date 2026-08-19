# Skills Ecosystem — npx skills

The `npx skills` CLI is a package manager for the open agent skills ecosystem. Use it to discover, install, and manage reusable agent skills from community sources.

This is relevant when setting up a project constitution because you may want to install existing skills for project-specific conventions, ADR templates, coding standards, or domain glossaries.

## Key Commands

| Command | Purpose |
|---------|---------|
| `npx skills find [query]` | Search for skills by keyword |
| `npx skills find [query] --owner <owner>` | Search scoped to a GitHub owner |
| `npx skills add <owner/repo@skill>` | Install a specific skill |
| `npx skills add <owner/repo@skill> -g -y` | Install globally, skip prompts |
| `npx skills update` | Update all installed skills |
| `npx skills init` | Create a new skill |

## Relevant Skills for Project Constitution

These skills were discovered during ecosystem survey and are relevant to constitution setup:

| Skill | Installs | What it provides |
|-------|----------|-----------------|
| `giuseppe-trisciuoglio/developer-kit@constitution` | 1.4K | `docs/specs/architecture.md` + `docs/specs/ontology.md` — two-file constitution with validation check |
| `wshobson/agents@architecture-decision-records` | 14K | ADR template with Decision Drivers, pros/cons table, risks & mitigations |
| `affaan-m/ecc@architecture-decision-records` | 7.6K | Alternative ADR skill |
| `sundial-org/awesome-openclaw-skills@ontology` | 3.5K | Standalone domain glossary / ontology skill |
| `bencium/bencium-marketplace@bencium-code-conventions` | 2.5K | Opinionated personal conventions document |
| `vasilyu1983/ai-agents-public@software-architecture-design` | 1.1K | Structured software architecture design |
| `assimovt/productskills@user-interview` | 167 | Requirements-gathering interview |

## Browse the Catalog

- **Leaderboard:** https://skills.sh/ — ranked by installs
- **Search:** `npx skills find <topic>` — keyword search

## Quality Heuristics

Before recommending a skill from the ecosystem, check:

1. **Install count** — 1K+ is solid; <100 is unproven
2. **Source reputation** — `vercel-labs`, `anthropics`, `microsoft`, or well-known authors
3. **GitHub stars** on the source repo — <100 stars = treat with skepticism

## Related

The `npx skills` ecosystem is separate from the Hermes skills system (`~/.hermes/skills/`). Hermes skills are curated in-repo or user-local SKILL.md files. The `npx skills` ecosystem is cross-agent (works with Claude Code, Cursor, etc.) and community-maintained.