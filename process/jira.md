# Jira

Site `aslanabdullayevdev.atlassian.net` (Free plan). Claude uses it through the `atlassian` MCP server.
**Jira is the source of truth for the backlog** — don't copy ticket lists into docs.
Why it's set up this way: [ADR 0009](../adr/0009-jira-team-spaces.md), [ADR 0010](../adr/0010-record-decisions-as-adrs.md).

## Spaces (one per team, not per service)

| Space | Team | Owns | Board |
|---|---|---|---|
| `PLAT` | Platform/DevOps | `architecture`, `infra-*` repos, CI, gateway | PLAT board |
| `IDN` | Identity | `backend-auth`, `backend-users` | IDN board |
| `COM`, `CRM`, `SHOP` (future) | Commerce / CRM FE / Storefront FE | — | create when the phase starts |

Repo → team mapping: [`services.md`](../services.md).

**New team space:** Create → Scrum → Company-managed → "Share settings with an existing space" → Platform.
Sharing creates **no board** — create one from inside the new space and map its columns.

## Workflow

`To Do` → `Ready For Development` → `In Progress` → `In Review` → `Done`

| Status | Means |
|---|---|
| To Do | In the backlog, not refined yet |
| Ready For Development | Meets the [Definition of Ready](definition-of-ready-done.md#definition-of-ready) |
| In Progress | Someone is working on it now |
| In Review | Changes are ready: PR open, being reviewed |
| Done | Meets the [Definition of Done](definition-of-ready-done.md#definition-of-done) **and the user confirmed** |

Move tickets as work actually happens. Transitions from any status are allowed. There is no Testing status —
testing happens in In Progress, and in CI during In Review.

## Work types

| Type | Use for |
|---|---|
| Epic | A team's larger goal, in its own space |
| Story | User-visible value |
| Task | Technical work |
| Sub-task | A piece of a Task/Story |
| Bug | Something broken |

## Descriptions

Every ticket uses: **Background / Why / What to do / Acceptance criteria / Out of scope / Depends on**.

## Decisions

Tickets are opened **just in time** ([ADR 0014](../adr/0014-tickets-just-in-time.md)):

- **A decision made** → an ADR right away ([`adr/README.md`](../adr/README.md)).
- **Work agreed for later** → [`roadmap.md`](../roadmap.md), not a ticket.
- **Work about to start** (about 1–2 sprints away) → a ticket in the owning team's space (tell the user the
  key). Its roadmap note moves into the ticket.
- **An open question blocking upcoming work** → a ticket with the label `decision`.

## Cross-team work

- Dependencies between tickets: **"blocks" links**.
- One goal across teams: a shared label `init-<name>` (e.g. `init-polyrepo`, `init-phase1-auth`);
  search `labels = init-<name>`.

## Blocked tickets

1. Add a "blocks" link from the blocker.
2. Add the label `blocked`.
3. Comment what unblocks it.
4. Keep it in the backlog, not in a sprint.

When the blocker is Done, remove the label. All blocked work: `labels = blocked`. (Jira's "Flag" isn't on
this site's edit screens, so the label is used instead.)

## Sprints

- 1-week sprints, one per team space. The user has ~5–6 hours a week — size sprints to that.
- The user moves tickets into sprints and starts/closes sprints. Jira UI configuration is done by the user;
  Claude verifies through the API.

### Sprint planning

Because later work lives in the roadmap, not in Jira ([ADR 0014](../adr/0014-tickets-just-in-time.md)), the
Jira backlog alone is never the full picture. Before choosing a sprint's work, Claude:

1. Reads [`roadmap.md`](../roadmap.md) (feature phases, Platform steps, scope notes) and any ADRs whose
   follow-ups aren't ticketed yet.
2. Lists the Jira backlog of the team space(s) being planned.
3. Proposes candidates from **both** sources: roadmap steps that are due now may take priority over existing
   backlog tickets.
4. The user picks. Roadmap items picked are turned into tickets (Background / Why / … format), and their
   notes move from the roadmap into the ticket.

Stale backlog tickets found along the way (outdated paths, decided elsewhere) are flagged to the user.

## Gotchas

- Jira now calls projects "spaces"; the top-menu "Projects" is the separate Atlas app.
- The sprint update API needs `name`, `startDate` and `endDate` every time, not just the goal.
