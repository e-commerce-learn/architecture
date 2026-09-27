# Service catalog

One row per repo in the [`e-commerce-learn`](https://github.com/e-commerce-learn) org. Ownership lives here
(no GitHub teams yet — [ADR 0011](adr/0011-polyrepo-e-commerce-learn-org.md)). **When a new repo is set up, add its row
in a matching PR here in `architecture`** (this file can't be changed from another repo's PR).

Local path = where the repo is cloned under `~/Desktop/Code/e-commerce-learn/`.

| Repo | Local path | Owner team | Jira space | What it is | Port (host) | Database | Status |
|---|---|---|---|---|---|---|---|
| [`architecture`](https://github.com/e-commerce-learn/architecture) | `architecture/` | Platform | `PLAT` | Global docs: ADRs, process, service catalog, roadmap | — | — | Live |
| `infra-postgres` | `infra/infra-postgres/` | Platform | `PLAT` | PostgreSQL 16 for all services — one database + one role per service ([ADR 0004](adr/0004-microservices-from-phase-1.md)) | 5432 | hosts `auth_db` (role `auth_service`) | Planned — PLAT-12. Runs today from the old monorepo copy (container `ecommerce-postgres-1`, volume `ecommerce_api_pgdata`) |
| `backend-identity` | `backend/backend-identity/` | Identity | `IDN` | NestJS 11 service: login, registration, issuing JWTs (the NestJS project is still named `auth`) | 3000 (`PORT` env, default) | `auth_db` as `auth_service` | Planned — IDN-6. Code lives today in the old monorepo at `backend/auth/` |

## Planned, no repo yet

| Repo | Owner team | Notes |
|---|---|---|
| `backend-gateway` | Platform | Single entry point; JWT verified here only ([ADR 0004](adr/0004-microservices-from-phase-1.md)). Design: PLAT-27 |
| `frontend-crm` | CRM FE (future) | Angular; starts after Categories, Inventory and Customer Profiles ([ADR 0006](adr/0006-frontends-crm-and-storefront.md)) |
| `frontend-storefront` | Storefront FE (future) | Very low priority ([ADR 0006](adr/0006-frontends-crm-and-storefront.md)) |
