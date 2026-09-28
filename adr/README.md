# Architecture Decision Records (ADRs)

One file per significant decision: what we decided, why, what we rejected, and what it costs us.
Decisions are **never edited after they're accepted**. If a decision changes, write a new ADR that
overrides the old one. The old ADR stays untouched except for a note under its header pointing to the new
one (e.g. `> **Note:** Partly overridden by ADR NNNN — …`); the new ADR lists what it overrides.

## Two levels ([ADR 0011](0011-polyrepo-e-commerce-learn-org.md))

| Level | Lives in | Covers |
|---|---|---|
| **Global** | this folder (`architecture/adr/`) | Rules for every repo and team |
| **Local** | `<repo>/docs/adr/` in each repo | Decisions only that repo cares about |

A local ADR can't override a global one; changing a global rule takes a new global ADR here.
Every ADR states its level in the `Scope:` field. ADRs 0001–0010 were written before that field existed
and are all **global**.

## When to write one

- A choice that's hard or expensive to reverse (architecture, data ownership, tooling, process).
- A choice where a future reader would ask "why on earth did they do it this way?".
- Not for things a ticket or code comment already explains.

## How

1. Copy [`0000-template.md`](0000-template.md) to `NNNN-short-title.md` (next free number).
2. Fill it in; link the Jira ticket(s) that implement it.
3. Add a row to the index below **and** to [`CLAUDE.md`](../CLAUDE.md) → Decisions (with the one-line rule
   Claude must follow). A local ADR goes in the index of its own repo's `docs/adr/README.md` instead.

## Index

| # | Decision | Status | Date |
|---|---|---|---|
| [0001](0001-no-orm.md) | Raw SQL only, no ORM | Accepted | — |
| [0002](0002-reset-sqlite-to-postgres.md) | Reset: rebuild on PostgreSQL instead of SQLite | Accepted | 2026-08-08 |
| [0003](0003-restart-docs-from-zero.md) | Delete all docs and restart them alongside the code | Accepted | 2026-08-08 |
| [0004](0004-microservices-from-phase-1.md) | Microservices from Phase 1, independent-projects monorepo | Accepted (partly overridden by [0011](0011-polyrepo-e-commerce-learn-org.md)) | 2026-08-08 |
| [0005](0005-folder-layout.md) | Folder layout: `backend/`, `frontend/`, `infra/` | Accepted (overridden by [0011](0011-polyrepo-e-commerce-learn-org.md)) | 2026-09-25 |
| [0006](0006-frontends-crm-and-storefront.md) | Two frontends: Angular CRM, low-priority storefront | Accepted | 2026-09-25 |
| [0007](0007-naming-snake-case-db-camel-case-api.md) | `snake_case` in the DB, `camelCase` in the API | Accepted | — |
| [0008](0008-multi-team-ownership-and-compose-layout.md) | Simulated multi-team ownership; per-service compose fragments + root `include:` | Accepted (partly overridden by [0011](0011-polyrepo-e-commerce-learn-org.md)) | 2026-09-25 |
| [0009](0009-jira-team-spaces.md) | Jira: one company-managed space per team, shared workflow | Accepted | 2026-09-25 |
| [0010](0010-record-decisions-as-adrs.md) | Record decisions as ADRs; DoR/DoD; initiative labels | Accepted | 2026-09-26 |
| [0011](0011-polyrepo-e-commerce-learn-org.md) | Polyrepo in the `e-commerce-learn` GitHub org (overrides parts of 0004, 0008; all of 0005) | Accepted (partly overridden by [0012](0012-auth-and-users-separate-services.md)) | 2026-09-27 |
| [0012](0012-auth-and-users-separate-services.md) | Auth and Users are two separate services (`backend-auth`, `backend-users`) | Accepted | 2026-09-28 |

Where an older ADR mentions paths like `docs/adr/`, `CLAUDE.md` or `backend/<service>/`, it means the old
monorepo (`Learn-folder/projects/ecommerce/`) where it was written. The accepted text isn't edited; the
current homes are this folder, [`../CLAUDE.md`](../CLAUDE.md) and the repos listed in
[`../services.md`](../services.md).
