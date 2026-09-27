# 0005. Folder layout: `backend/`, `frontend/`, `infra/`

- **Status:** Accepted
- **Date:** 2026-09-25
- **Owner (team):** Platform
- **Jira:** PLAT-1

> **Note:** Overridden by [ADR 0011](0011-polyrepo-e-commerce-learn-org.md) — the layout moves to separate
> repos in the `e-commerce-learn` org.

## Context

The project lived at `projects/e_commerce_sql/ecommerce_api/`: a wrapper folder holding nothing but the
real root, four separate `.idea/` folders, and a name (`ecommerce_api`) that stopped fitting once two
frontends were planned.

## Decision

Flatten to a single root, `Learn-folder/projects/ecommerce/`:

```
ecommerce/                 project root — CLAUDE.md, README.md, compose.yaml, single .idea/
├── backend/               one independent NestJS project per service
│   ├── gateway/           single entry point for both frontends; JWT verified here
│   ├── auth/              (exists)
│   └── <service>/         users, catalog, orders, … as phases land
├── frontend/              one independent project per app — frontends call only the gateway
│   ├── crm/               Angular, internal operators
│   └── storefront/        customer-facing, very low priority
├── infra/                 Platform-owned shared containers + config (compose.yaml, postgres/init/)
└── docs/                  cross-service docs (this ADR folder)
```

- No `packages/` / `libs/` (consistent with [0004](0004-microservices-from-phase-1.md)).
- Folders are created when their work starts, not pre-scaffolded empty.
- The Compose volume name is pinned to `ecommerce_api_pgdata` so existing Postgres data survived the rename.
- The git branch keeps the name `ecommerce_api`.

## Alternatives considered

- **`apps/` + `packages/`** (Nx/Turborepo convention) — only pays off with shared code, which we don't have.

## Consequences

- Good: one root, one IDE project, room for frontends and infra side by side.
- Cost: every path reference had to be updated once (done in PLAT-1).
