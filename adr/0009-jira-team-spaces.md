# 0009. Jira: one company-managed space per team, shared workflow

- **Status:** Accepted
- **Date:** 2026-09-25
- **Owner (team):** Platform
- **Jira:** site `aslanabdullayevdev.atlassian.net` (Free plan)

## Context

The user wanted every decision to create a backlog item that moves through a real workflow, in line with
[0008](0008-multi-team-ownership-and-compose-layout.md) (simulate a multi-team org).

## Decision

- **Jira** (Free), accessed by Claude through the Atlassian MCP server (`atlassian`, user scope).
- **One Jira space (= project) per team, not per microservice.** Services are tracked inside the owning
  team's space. Spaces are created when a team has work.

  | Space | Team | Owns |
  |---|---|---|
  | `PLAT` | Platform/DevOps | infra compose, Postgres/RabbitMQ/Redis, CI, gateway |
  | `IDN` | Identity | `backend/auth`, `backend/users` |
  | `COM`, `CRM`, `SHOP` (future) | Commerce / CRM FE / Storefront FE | — |

- **Company-managed spaces with one shared workflow**, `PLAT` as the source. New team space: Create →
  Scrum → Company-managed → "Share settings with an existing space" → Platform. Sharing creates **no
  board**: create one from inside the new space and map its columns.
- **Workflow (global statuses):** `To Do` → `Ready For Development` (To Do category: refined, dev not
  started) → `In Progress` → `In Review` (PR open, reviewed via `/code-review`, CodeRabbit, CI) → `Done`.
  "Allow transitions from any status" on all. No `Testing` status (no QA team; testing happens in
  In Progress and CI in In Review).
- **Work types (shared by all teams):** Epic, Story (user-visible value), Task (technical work), Bug, Sub-task.
  Each team has its own epics and tickets in its own space.
- **Components:** skipped for now.
- **Cross-team work:** each team's own epic + "blocks / is blocked by" links; the initiative level is
  emulated with labels (see [0010](0010-record-decisions-as-adrs.md)).

## Alternatives considered

- **GitHub Projects** — free and close to the code, but not what most multi-team companies use.
- **In-repo markdown backlog** — no board, not realistic.
- **One Jira space for everything, with components per team** — hides ownership, which is what we practice.
- **One space per microservice** — too many boards, and services change owners.
- **Team-managed spaces** — each space keeps private statuses, so every new team copies the workflow by hand.

## Consequences

- Good: realistic setup; shared workflow edited once for all teams.
- Cost: one manual board setup per new team space; Jira UI configuration is done by the user by hand.
- Gotchas learned: Jira now calls projects "spaces"; the top-menu "Projects" is the separate Atlas app;
  `ViewStatuses.jspa` lists global statuses only.
