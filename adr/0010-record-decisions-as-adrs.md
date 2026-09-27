# 0010. Record decisions as ADRs; Definition of Ready/Done; initiative labels

- **Status:** Accepted
- **Date:** 2026-09-26
- **Owner (team):** Platform
- **Jira:** PLAT-7

## Context

Decisions lived as long paragraphs in `CLAUDE.md` → Decisions: hard to link from tickets, easy to edit
silently, and mixed with rules for Claude. There was also no explicit bar for when a ticket may enter a
sprint or be closed, and no way to see one goal across team spaces (Jira's Initiative level is Premium-only).

## Decision

1. **ADRs.** Every significant decision gets one file in `docs/adr/NNNN-title.md` (template: `0000-template.md`),
   linked to its Jira ticket(s). Accepted ADRs aren't edited; changes are new ADRs that supersede old ones.
   `CLAUDE.md` → Decisions becomes an **index** with the one-line rule Claude must follow per ADR.
2. **Definition of Ready / Definition of Done** (in `CLAUDE.md` → For Claude → Task management).
3. **Initiatives via labels.** Tickets serving one cross-team goal share a label `init-<name>`
   (first: `init-phase1-auth`). A saved filter `labels = init-<name>` and a cross-team board show them together.

## Alternatives considered

- **Keep decisions in `CLAUDE.md`** — works, but not linkable or reviewable one by one.
- **Jira Premium for initiatives** — paid.
- **One epic spanning several team spaces** — blurs ownership.

## Consequences

- Good: each decision is reviewable, linkable from Jira, and has an explicit history.
- Cost: writing an ADR per decision; keeping the `CLAUDE.md` index in sync.
