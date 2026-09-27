# 0003. Delete all docs and restart them alongside the code

- **Status:** Accepted
- **Date:** 2026-08-08
- **Owner (team):** all

## Context

After [0002](0002-reset-sqlite-to-postgres.md), a review pass showed several business docs still contained
previous-implementation detail (exact deleted class/decorator names like `JwtAuthGuard`, `@IsPublic()` in
sequence diagrams and prose), despite earlier trimming.

## Decision

The user deleted every remaining doc — `core.md`, `core.tech.md`, `auth.md`, `auth.tech.md`,
`categories.md`, `categories.tech.md`, `users.md`, `users.tech.md`, `roadmap.md`, `errors.md` — instead
of chasing leftover detail doc by doc. Only `CLAUDE.md` and `README.md` remained. This was deliberate.

## Alternatives considered

- **Keep trimming docs one by one** — slow, and old implementation detail kept leaking through.

## Consequences

- Good: no stale design biasing the rebuild.
- Cost: every module's docs are written fresh when the module is picked up.
- **Rule:** never reconstruct or restore a deleted doc from memory or git history; write docs fresh with
  the user when the module starts. Doc locations for the microservices layout: IDN-4.
