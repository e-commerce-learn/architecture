# 0004. Microservices from Phase 1, independent-projects monorepo

- **Status:** Accepted (supersedes the original "monolith first" plan)
- **Date:** 2026-08-08
- **Owner (team):** all

> **Note:** Partly overridden by [ADR 0011](0011-polyrepo-e-commerce-learn-org.md) — the monorepo part
> (one monorepo of independent projects, living inside `Learn-folder`). Everything else still applies.

## Context

The original plan was a monolith first, split into microservices later (after Cart & Orders or
Payments). The user wants to learn microservices patterns hands-on rather than defer them.

## Decision

Build as microservices starting from **Phase 1 (Auth & Users)**.

- **One monorepo, independent projects.** A `backend/` folder with one subfolder per service, each a fully
  standalone NestJS project (own `package.json`, own `node_modules`, own `nest new` scaffold, own
  `Dockerfile`) — e.g. `backend/gateway/`, `backend/auth/`, `backend/orders/`. Nothing links them except
  being sibling folders. The project lives inside the `Learn-folder` umbrella repo as a subdirectory.
- **Zero shared code, categorically.** No `libs/` folder. Each service has its own DTOs, interceptors,
  `ApiResponse` wrapper. (Replaces the earlier target of a shared `libs/` for guards, decorators,
  interceptors, ApiResponse, RabbitMQ event types.)
- **No Consul / service registry.** Docker Compose's built-in DNS (service name → container IP) is enough
  for a fixed, small, single-machine service set. Revisit only for Kubernetes or dynamic scaling.
- **Auth is centralized.** One Auth service owns login, registration, password hashing, issuing JWTs.
  Verification happens **once, at the Gateway**; downstream services receive already-authenticated requests
  and never re-verify a JWT. There is no per-service guard code at all.
- **Routing:** an API Gateway is the single entry point, forwarding by path over Docker's internal network.
- **Database isolation, technically enforced:** one Postgres container, one database per service
  (`auth_db`, `users_db`, …), each with its own role granted `CONNECT` only on its own database (public
  `CONNECT` revoked). Postgres has no cross-database queries without extensions (`dblink`/`postgres_fdw`),
  so cross-service table access is blocked structurally and by permissions. Automated by PLAT-3.
- RabbitMQ is the transport for async communication between services, once needed.

## Alternatives considered

- **Monolith first** — less upfront complexity; rejected for learning value.
- **NestJS `apps/` + `libs/` workspace (Nx-style integrated monorepo)** — shares one root `package.json` /
  `node_modules`, i.e. shared dependency versions: coupling that contradicts zero shared code.
- **Separate git repos per service** — would break the existing `Learn-folder` organization.

## Consequences

- Good: each service can be built, tested and deployed independently; moving one to its own repo is a copy.
- Accepted cost: distributed auth, service boundaries decided before the domain is proven, duplicated
  boilerplate per service. The user made this call knowing the cost.
- Follow-ups: independent CI/CD per service (future PLAT tickets); expand/contract for cross-service
  contract changes (e.g. JWT format between IDN and the gateway).
