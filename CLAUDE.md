# e-commerce-learn — Claude Instructions (global)

> Applies to **every repo** in the `e-commerce-learn` org. Each repo's own `CLAUDE.md` adds repo-specific
> rules and can't contradict this file (same rule as global vs local ADRs).
> Read this file at the start of every session, then the repo's own `CLAUDE.md`.

## Contents

1. [How to work with the user](#how-to-work-with-the-user)
2. [Where things live](#where-things-live)
3. [Write triggers](#write-triggers)
4. [Global conventions](#global-conventions)
5. [Decisions](#decisions)

---
---

## How to work with the user

- **Learning project.** The user is learning backend, SQL, infra and team process by doing. Explain the *why*
  first, briefly and plainly.
- **Ask, don't decide implicitly.** When unsure about any decision (naming, structure, process, scope), ask.
  Mark suggestions as proposals.
- **Never commit or push unless the user explicitly asks** — every time.
- **The user writes the DB layer** — schemas, migrations, init scripts, `DatabaseModule`/`DatabaseService`.
  Don't pre-write it unless asked ([ADR 0002](adr/0002-reset-sqlite-to-postgres.md)).
- **Never reconstruct deleted docs** from memory or git; write fresh with the user
  ([ADR 0003](adr/0003-restart-docs-from-zero.md)).
- **Update docs as part of the task**, without being asked — see [Write triggers](#write-triggers).

---
---

## Where things live

| What | Where |
|---|---|
| Global decisions | [`adr/`](adr/README.md) — a repo's local decisions go in `<repo>/docs/adr/` |
| Repos, owners, ports, databases | [`services.md`](services.md) |
| Jira: spaces, workflow, labels, blocked rules | [`process/jira.md`](process/jira.md) |
| Definition of Ready / Done | [`process/definition-of-ready-done.md`](process/definition-of-ready-done.md) |
| Branches, commits, PRs, repo settings | [`process/git-workflow.md`](process/git-workflow.md) |
| Build phases + open scope notes | [`roadmap.md`](roadmap.md) |
| Repo-specific setup, docs, gotchas | That repo's `README.md` / `CLAUDE.md` |
| Backlog, ticket status | Jira — never copied into docs |

Local layout: `~/Desktop/Code/e-commerce-learn/<group>/<repo>`; Claude sessions start from
`~/Desktop/Code/e-commerce-learn/`.

---
---

## Write triggers

Update these immediately when the event happens — don't wait to be asked.

| Trigger | Update |
|---|---|
| Decision made that affects every repo | New global ADR in `adr/` + row in `adr/README.md` + row in [Decisions](#decisions) below + Jira ticket |
| Decision made that affects one repo | Local ADR in that repo's `docs/adr/` + its index |
| An ADR overrides an older one | Note under the old ADR's header; "(partly) overridden by NNNN" in the index — nothing else in the old ADR changes |
| New repo created | Row in `services.md` (matching PR in `architecture`); repo checklist in `process/git-workflow.md` |
| Service port / database / owner changed | `services.md` |
| Phase started or completed | `roadmap.md` (move its scope notes into the service's docs when it starts) |
| Jira / review / git process changed | The matching file in `process/` |
| Global convention established | [Global conventions](#global-conventions) below |
| User says "remember" / "memorize" | Claude memory **and** the matching doc — never just one |

---
---

## Global conventions

| Convention | Rule | Source |
|---|---|---|
| No ORM | Raw SQL prepared statements only. Never suggest an ORM or query builder. | [0001](adr/0001-no-orm.md) |
| Naming | `snake_case` in the DB, `camelCase` in the API; map in the service layer, never return raw column names. | [0007](adr/0007-naming-snake-case-db-camel-case-api.md) |
| Zero shared code | No shared libraries between services; each repo has its own DTOs, interceptors, response wrapper. | [0004](adr/0004-microservices-from-phase-1.md) |
| Auth | JWT issued by the auth service, verified **only at the gateway**; downstream services never re-verify. | [0004](adr/0004-microservices-from-phase-1.md) |
| DB isolation | One Postgres container, one database + one role per service, `CONNECT` only on its own database. | [0004](adr/0004-microservices-from-phase-1.md) |
| Compose | Dev + CI only; each repo owns its compose file + `.env`. How repos run together locally: PLAT-13. | [0008](adr/0008-multi-team-ownership-and-compose-layout.md), [0011](adr/0011-polyrepo-e-commerce-learn-org.md) |

Carried over from the pre-reset design and **not confirmed yet** — don't treat as rules until the linked
decision is made:

| Idea | Decided in |
|---|---|
| Response shape `ApiResponse<T> { message, data }` | PLAT-26 (API conventions) |
| Permissions named `module:action` | IDN-13 (authZ) |
| One schema file as the single source of truth per service | PLAT-25 (migrations) |
| Soft deletes via an `is_deleted` flag, never hard delete | No ticket yet — decide with the user when the first delete is built |

---
---

## Decisions

Full records in [`adr/`](adr/README.md). This index holds the rule Claude must follow for each.

| ADR | Decision | Rule for Claude |
|---|---|---|
| [0001](adr/0001-no-orm.md) | Raw SQL only, no ORM | Never suggest an ORM or query builder, regardless of complexity. |
| [0002](adr/0002-reset-sqlite-to-postgres.md) | 2026-08-08 reset: rebuild on PostgreSQL | Don't pre-write DB layer code unless explicitly asked — the user writes it. |
| [0003](adr/0003-restart-docs-from-zero.md) | All docs deleted, restarted with the code | Never reconstruct deleted docs from memory/git; write fresh with the user. |
| [0004](adr/0004-microservices-from-phase-1.md) | Microservices from Phase 1 (monorepo part overridden by 0011) | Each service standalone; zero shared code; no Consul; JWT verified only at the gateway; one DB + role per service. |
| [0005](adr/0005-folder-layout.md) | Folder layout (overridden by 0011) | Use repo prefixes + local group folders instead. Create repos only when their work starts. |
| [0006](adr/0006-frontends-crm-and-storefront.md) | Angular CRM + low-priority storefront | Don't start the CRM before Categories, Inventory and Customer Profiles are done; frontends call only the gateway. |
| [0007](adr/0007-naming-snake-case-db-camel-case-api.md) | `snake_case` DB, `camelCase` API | Map in the service layer; never return raw column names. |
| [0008](adr/0008-multi-team-ownership-and-compose-layout.md) | Simulated multi-team ownership (root `include:` overridden by 0011) | Frame infra choices by owning team. Each repo owns its compose file + `.env`; compose is dev/CI only. |
| [0009](adr/0009-jira-team-spaces.md) | Jira: one space per team, shared workflow | Follow [`process/jira.md`](process/jira.md). |
| [0010](adr/0010-record-decisions-as-adrs.md) | ADRs, DoR/DoD, initiative labels | Record decisions as ADRs; apply [DoR/DoD](process/definition-of-ready-done.md); label initiative tickets `init-<name>`. |
| [0011](adr/0011-polyrepo-e-commerce-learn-org.md) | Polyrepo in the `e-commerce-learn` org (repo name `backend-identity` overridden by 0012) | Prefixed repo names; clone to `~/Desktop/Code/e-commerce-learn/<group>/<repo>`; squash merge only; global ADRs here, local in `<repo>/docs/adr/`, local can't override global. |
| [0012](adr/0012-auth-and-users-separate-services.md) | Auth and Users are two services | Name repos after the service, not the team: `backend-auth` (credentials, JWTs, `auth_db`), `backend-users` (accounts/profiles, `users_db`), both Identity-owned. |
