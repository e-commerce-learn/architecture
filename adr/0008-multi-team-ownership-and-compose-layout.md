# 0008. Simulated multi-team ownership; per-service compose fragments + root `include:`

- **Status:** Accepted
- **Date:** 2026-09-25 (fully-separate-projects alternative evaluated and rejected 2026-09-26)
- **Owner (team):** Platform
- **Jira:** PLAT-2

> **Note:** Partly overridden by [ADR 0011](0011-polyrepo-e-commerce-learn-org.md) — the root `compose.yaml`
> with `include:`. Team ownership and per-repo compose fragments still apply.

## Context

**A core purpose of this project** is to practice how a real org with separate frontend, backend,
DevOps/platform and product teams organizes repos, containers and deployment — not just the app code.
One person plays every role, but structure decisions should mimic that setup.

## Decision

| Concern | Owner (simulated team) | Lives in |
|---|---|---|
| Service code + `Dockerfile` + service compose fragment | That service's backend team | `backend/<service>/` (`Dockerfile`, `compose.yaml`) |
| Frontend app + `Dockerfile` + compose fragment | That frontend's team | `frontend/<app>/` |
| Shared infra containers (Postgres, later RabbitMQ/Redis/Meilisearch) + config | Platform/DevOps | `infra/compose.yaml`, `infra/<tool>/…` |
| Root compose — composition only, no service definitions | Platform/DevOps | `compose.yaml` at project root, `include:`s every fragment |
| API gateway | Platform/DevOps | `backend/gateway/` |
| Folder → team mapping | Platform | `CODEOWNERS` (when a remote/CI exists) |
| CI | Each team, per-service pipeline | path filters (`projects/ecommerce/backend/auth/**`); workflows live in `Learn-folder/.github/workflows/` |
| Production deploy (future) | Platform | not Compose — Kubernetes/Helm or similar, in `deploy/` or a separate repo (GitOps) |

Rules:
- **One compose fragment per service, owned by that service's folder.** The root `compose.yaml` only
  `include:`s fragments (Compose ≥ 2.20). Each fragment resolves relative paths and its `.env` from its own
  folder.
- **The root is the only entry point.** Run subsets by name (`docker compose up auth` pulls in its
  `depends_on`) or by profiles (`backend`, `frontend`). Fragments are not run standalone.
- **Compose = local dev + CI only.** No `docker-compose.prod.yml`.
- **Frontend devs run their app natively** (`ng serve`) against the gateway; frontend containers exist for
  production builds.
- **Backend devs** run the service they're changing natively (`DB_HOST=localhost`) or in a container
  (`DB_HOST=postgres`), everything else as containers.
- **Each service owns its env** (`backend/<service>/.env`); shared infra credentials belong to `infra/`.

## Alternatives considered

- **One big hand-edited compose file** — every team edits the same file; merge conflicts, unclear ownership.
- **Fully separate compose projects, no root file** (rejected 2026-09-26). Each project gets its own
  network, so services can't resolve each other; it needs a manually created external network
  (`docker network create`), service names unique across all projects (duplicate names make DNS return
  several IPs), no cross-project `depends_on`, and one `up` per folder. Polyrepo companies do this, but still
  keep a platform-owned composition somewhere. In a monorepo, the thin root `include:` file is the
  standard place.

## Consequences

- Good: teams stay independent (each edits only its own file) while one command runs the system, on one
  network, with `depends_on`.
- Cost: the root file needs one `include:` line per new service.
- Worth doing anyway later: DB connection retry in every service; network segmentation (`edge` / `services`
  / `data` networks) once the gateway exists.
