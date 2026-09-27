# 0002. Reset: rebuild on PostgreSQL instead of SQLite

- **Status:** Accepted
- **Date:** 2026-08-08
- **Owner (team):** all

## Context

The project was paused for several months. The original build used SQLite. On resuming, the
implementation-specific material no longer fit where the project was heading.

## Decision

- All source code and the entire `database/` directory were deleted.
- Every module's technical doc (`<name>.tech.md`) was trimmed back to a target design (endpoints/DTOs +
  Planned). Implementation specifics describing the old SQLite build (schema, example payloads, flow
  diagrams with literal queries, file structure, gotchas) were removed so they wouldn't bias the rebuild.
- Business docs (`<name>.md`) and `roadmap.md` were kept at this point (they capture product intent, not
  implementation) — later deleted too, see [0003](0003-restart-docs-from-zero.md).
- Rebuild from scratch on **PostgreSQL**.
- **The Postgres connection layer, Docker setup and schema are written by the user directly** as the
  relearning exercise.

## Alternatives considered

- **Keep SQLite** — simpler, but not the industry-standard engine for this kind of multi-service stack.
- **Port the old code** — would carry old assumptions into the new architecture.

## Consequences

- Good: clean start on the engine used in real production stacks.
- Cost: everything is rebuilt.
- **Rule:** do not pre-build DB layer code (`DatabaseModule`/`DatabaseService`, `schema.sql`, compose/DB
  setup) unless the user explicitly asks. Exceptions so far: `docker-compose.yml` (2026-08-08) and
  auth's `DatabaseModule`/`DatabaseService`, both written at the user's explicit request.
