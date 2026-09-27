# 0011. Polyrepo in the `e-commerce-learn` GitHub org

- **Status:** Accepted
- **Date:** 2026-09-27 (decided 2026-09-26)
- **Owner (team):** Platform
- **Scope:** global
- **Jira:** PLAT-10 (epics PLAT-8, IDN-5)

> **Note:** Overrides [0004](0004-microservices-from-phase-1.md) (monorepo part),
> [0005](0005-folder-layout.md) (all), [0008](0008-multi-team-ownership-and-compose-layout.md)
> (root `include:` part). See [What this overrides](#what-this-overrides).

## Context

A core purpose of this project is to practice how a real org with separate teams organizes its work
([0008](0008-multi-team-ownership-and-compose-layout.md)). Real teams typically work in their own repos,
each with its own settings, CI, reviews and history. That separation is hard to practice while everything
lives in one folder inside the `Learn-folder` umbrella repo.

## Decision

1. **Polyrepo in the GitHub org `e-commerce-learn`.** Repos are public.
2. **Repo names on GitHub use prefixes:** `architecture`, `infra-<tool>`, `backend-<service>`,
   `frontend-<app>`.
3. **Locally, repos live in real group folders** under `~/Desktop/Code/e-commerce-learn/`, keeping the prefix
   in the folder name:
   ```
   e-commerce-learn/
   ├── architecture/
   ├── infra/infra-postgres/
   ├── backend/backend-identity/
   └── frontend/frontend-<app>/
   ```
4. **Infra is split per tool** (`infra-postgres`, later `infra-rabbitmq`, …). One team can own many repos.
5. **Fresh start, no history import.** `projects/ecommerce/` is removed from `Learn-folder` once the migration
   to polyrepo is finished (PLAT-14); its earlier history stays in `Learn-folder`'s git log.
6. **Same settings in every repo:** squash merge only, auto-delete head branches. The per-repo settings
   checklist lives in `architecture/process/`.
7. **Two-level ADRs:** global ADRs in `architecture/adr/`, local ADRs in `<repo>/docs/adr/`. The template gets
   a `Scope: global | <repo>` field. A local ADR can't override a global one; changing a global rule takes a
   new global ADR.
8. **The `architecture` repo holds:** `README.md`, `CLAUDE.md`, `adr/`, `services.md` (service catalog +
   owners), `process/`, `roadmap.md`. `conventions/`, `diagrams/`, `events/`, `local-dev/` are added when
   their work starts.
9. **No GitHub teams for now.** Ownership is recorded in `services.md`.
10. **Not decided here:** how services and infra run together locally across repos — discussed when PLAT-13
    starts.

## What this overrides

| ADR | Overridden part | Still applies |
|---|---|---|
| [0004](0004-microservices-from-phase-1.md) | "One monorepo, independent projects … inside `Learn-folder`" | Microservices, zero shared code, no Consul, JWT verified at the gateway, one DB + role per service |
| [0005](0005-folder-layout.md) | All of it | The `backend` / `frontend` / `infra` split lives on as repo prefixes and local group folders |
| [0008](0008-multi-team-ownership-and-compose-layout.md) | The root `compose.yaml` that `include:`s every fragment | Team ownership (now by repo instead of folder); each repo owns its compose fragment + `.env`; compose is dev/CI only |

## Alternatives considered

- **Stay in `Learn-folder`** — no practice with separate repos; project history and branches stay mixed with
  unrelated projects.
- **One ecommerce monorepo in the org** — still one repo with one set of settings, so it doesn't simulate
  separate teams.
- **Polyrepo under the personal account** — no org to hold shared settings or group the repos as one company.
- **Git submodules / meta-repo** — pinned commits that must be bumped by hand couple repos that are meant to be
  independent.

## Consequences

- Good: each team gets its own repo — history, settings, CI and reviews — like a real org. Moving a service
  was designed to be a copy ([0004](0004-microservices-from-phase-1.md)), so the migration stays small.
- Cost: a change spanning several repos needs several PRs; no single `docker compose up` until PLAT-13 is
  decided; global docs and each repo's local docs can drift apart.
- Follow-ups: PLAT-11 (`architecture`), PLAT-12 (`infra-postgres`), IDN-6 (`backend-identity`), PLAT-13
  (local dev across repos), PLAT-14 (delete `projects/ecommerce/`).
