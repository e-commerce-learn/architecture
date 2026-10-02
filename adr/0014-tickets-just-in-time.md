# 0014. Jira tickets just in time; decisions in ADRs, later work in the roadmap

- **Status:** Accepted
- **Date:** 2026-09-28
- **Owner (team):** Platform
- **Scope:** global
- **Jira:** PLAT-36

## Context

`process/jira.md` said "every decision or piece of work agreed in conversation gets a ticket". In practice
that meant tickets for work months away. They went stale (IDN-3 and PLAT-3 still pointed at monorepo paths
after the move to polyrepo) and buried the real next steps. On 2026-09-28 several decision tickets were
opened for tools that won't be touched for months (PLAT-33, PLAT-34, PLAT-35).

## Decision

Each kind of information has one home:

| What | Where | When |
|---|---|---|
| A decision already made, and why | ADR in `adr/` (or a repo's `docs/adr/`) | Right away |
| Later work and its order | [`roadmap.md`](../roadmap.md) | Right away |
| Work about to start | Jira ticket in the owning team's space | When it's about 1–2 sprints away (refinement) |
| An open question that blocks upcoming work | Jira ticket, label `decision` | When the work it blocks is about to start |

A Jira ticket means "we're about to do this", not "someone once said this". When work is refined into a
ticket, its roadmap note moves into the ticket and is deleted from the roadmap.

Because of that, **sprint planning starts from the roadmap, not only the Jira backlog**: Claude reads
`roadmap.md` and un-ticketed ADR follow-ups first, then proposes candidates from both; roadmap items may take
priority over existing backlog tickets. Steps: [`process/jira.md`](../process/jira.md#sprint-planning).

## Alternatives considered

- **Ticket for everything (the old rule)**: nothing gets lost, but the backlog grows stale and noisy.
- **Only the roadmap, no decision tickets at all**: loses tracking of open questions that block upcoming work.

## Consequences

- Good: a short backlog of real, current work; decisions are findable in one place (`adr/`).
- Cost: the roadmap must be kept up to date; ideas only said in conversation must be written there.
- Existing tickets for far-off work (e.g. PLAT-33, PLAT-34, PLAT-35) stay until the user closes or deletes
  them. No new ones are opened for far-off work.
