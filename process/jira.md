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

Every decision or piece of work agreed in conversation gets a ticket in the owning team's space (tell the
user the key). Decisions also get an ADR — [`adr/README.md`](../adr/README.md). Decision tickets carry the
label `decision`.

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

## Gotchas

- Jira now calls projects "spaces"; the top-menu "Projects" is the separate Atlas app.
- The sprint update API needs `name`, `startDate` and `endDate` every time, not just the goal.
