# architecture

Global architecture docs for the **e-commerce-learn** platform: decisions (ADRs), service catalog, team
process and roadmap. Start here.

## What we're building

A multi-vendor e-commerce marketplace, built from scratch as a **learning project**: NestJS + raw SQL
(no ORM — every query is a hand-written prepared statement), microservices from day one, and the org shape of
a real company — separate Platform, backend, frontend and product teams, each owning its own repos. One person
plays every team.

When finished:

- 🏪 **Multiple vendors** list and sell their own products alongside the platform's own inventory
- 👤 **Customers** register, browse, cart, check out, pay and track deliveries
- 🛡️ **Staff** manage operations with role-based access (admin, warehouse, support, finance)
- ⚡ **Real-time** order and stock notifications via WebSockets
- ⚙️ **Background jobs** handle payouts, campaign expiry and search sync
- 🔍 **Search** with SQL full-text search first, Meilisearch later
- 📡 **Significant events** flow through RabbitMQ for async processing and vendor webhooks

## Tech stack

| Concern | Choice |
|---|---|
| Services | NestJS 11, TypeScript — one independent project per repo, zero shared code |
| Database | PostgreSQL 16 — raw SQL only, one database + role per service |
| Auth | JWT issued by the auth service, verified only at the gateway |
| Real-time | WebSockets |
| Background jobs | BullMQ, cron |
| Cache / queue | Redis |
| Message broker | RabbitMQ |
| Search | SQL FTS → Meilisearch |
| Local runtime | Docker Compose (dev + CI only) |
| Frontends | Angular CRM; customer storefront later |

## Repos

Full table with owners, ports and databases: [`services.md`](services.md).

| Repo | One line |
|---|---|
| [`architecture`](https://github.com/e-commerce-learn/architecture) | This repo — global docs |
| `infra-postgres` | PostgreSQL for all services (planned) |
| `backend-auth` | Login, registration, JWTs (planned) |
| `backend-users` | User accounts and profiles (planned, no repo yet) |

## Start here

| I want to… | Read |
|---|---|
| Understand why things are the way they are | [`adr/`](adr/README.md) — every decision, with alternatives |
| Find a service, its owner or its port | [`services.md`](services.md) |
| Pick up or create a ticket | [`process/jira.md`](process/jira.md) |
| Know when a ticket may start / is finished | [`process/definition-of-ready-done.md`](process/definition-of-ready-done.md) |
| Branch, commit, open a PR | [`process/git-workflow.md`](process/git-workflow.md) |
| See what gets built in which order | [`roadmap.md`](roadmap.md) |
| Work with Claude in this org | [`CLAUDE.md`](CLAUDE.md) |

Tickets live in Jira (`aslanabdullayevdev.atlassian.net`) — the source of truth for the backlog.
