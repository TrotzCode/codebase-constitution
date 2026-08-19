# Roadmap — {Project Name}

**Last Updated:** {YYYY-MM-DD}

This document is the product roadmap. It tracks everything we plan to build, in priority order. The AI agent reads this file to understand what to work on next and in what order.

> 🎯 **Rule:** Before starting any new feature, check this file. Always work on the highest-priority item in **Active** or **Ready** status unless the user explicitly says otherwise.

---

## Milestones

### M1: {First milestone name — e.g., "MVP — Single Building"}
**Target:** {YYYY-MM-DD}
**Goal:** {One sentence describing the milestone goal}

### M2: {Second milestone}
**Target:** {YYYY-MM-DD}
**Goal:** ...

---

## Feature Backlog

### Auth

| Priority | Feature | Status | Notes |
|----------|---------|--------|-------|
| P0 | User registration with email/password | ✅ Done | Basic flow working |
| P0 | User login with JWT | ✅ Done | Token expiry TBD |
| P1 | Password reset flow | 🔧 Active | In progress |
| P2 | OAuth2 (Google login) | 📋 Ready | Depends on auth provider config |
| P3 | Role-based access (admin vs user) | ⏳ Parked | Needed for multi-tenant |

### Buildings

| Priority | Feature | Status | Notes |
|----------|---------|--------|-------|
| P0 | Create and list buildings | ✅ Done | CRUD with address fields |
| P0 | Assign meters to buildings | 📋 Ready | Depends on meter model |
| P1 | Building dashboard (overview) | ⏳ Parked | After measurement data exists |

### Measurements

| Priority | Feature | Status | Notes |
|----------|---------|--------|-------|
| P1 | Record energy reading | ⏳ Parked | Needs meter assignment first |
| P2 | Daily aggregation | ⏳ Parked | Background job |

### Billing

| Priority | Feature | Status | Notes |
|----------|---------|--------|-------|
| P1 | Generate monthly invoice | ⏳ Parked | Needs measurement data |
| P2 | Payment integration | ⏳ Parked | Depends on payment provider decision |

---

## Nice-to-Haves

Features that should exist eventually but aren't blocking any milestone:

- Export to CSV/PDF
- Dark mode
- Email notifications on threshold breach
- Mobile-responsive dashboard
- Multi-language support

---

## Status Legend

| Status | Meaning |
|--------|---------|
| ✅ Done | Built and deployed |
| 🔧 Active | Currently being worked on |
| 📋 Ready | Next in queue, spec is clear |
| ⏳ Parked | Will do later, has blocker or dependency |
| 💡 Proposed | Idea that needs spec before it can be prioritized |

## Rules for AI Agents

- When user says "what should I work on next?" — read this file and suggest the highest-priority item in 📋 Ready status
- When a feature is completed, change its status to ✅ Done and update the date
- When a parked feature has its blocker resolved, move it to 📋 Ready
- If a new feature is proposed during a session, add it to the backlog with status 💡 Proposed and ask the user to prioritize it