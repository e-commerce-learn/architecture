# 0012. Auth and Users are two separate services

- **Status:** Accepted
- **Date:** 2026-09-28
- **Owner (team):** Identity
- **Scope:** global
- **Jira:** IDN-11

> **Note:** Overrides [0011](0011-polyrepo-e-commerce-learn-org.md) (the repo name `backend-identity` in its
> folder tree and follow-ups — the service repos are `backend-auth` and `backend-users`). Its naming rule
> `backend-<service>` is unchanged.

## Context

Phase 1 is "Auth & Users". It was open whether credentials/tokens and user accounts/profiles live in one
service or two. [0004](0004-microservices-from-phase-1.md) already names one Auth service (login,
registration, password hashing, issuing JWTs) and lists `users_db` next to `auth_db`, and
[0009](0009-jira-team-spaces.md) gives the Identity team `backend/auth` and `backend/users`. Meanwhile the
polyrepo setup ([0011](0011-polyrepo-e-commerce-learn-org.md)) planned a single repo named after the team,
`backend-identity`, which breaks its own rule that repos are named after the service.

## Decision

We will build **two services**, both owned by the Identity team (`IDN`):

| Service | Repo | Owns | Database |
|---|---|---|---|
| **Auth** | `backend-auth` | Credentials, login, registration, password hashing, issuing JWTs | `auth_db` (role `auth_service`) |
| **Users** | `backend-users` | User accounts and profile data | `users_db` (role `users_service`) |

- Repos are named after the **service**, not the team (`backend-<service>`, [0011](0011-polyrepo-e-commerce-learn-org.md)).
  Local paths: `backend/backend-auth/`, `backend/backend-users/`.
- The existing `backend/auth` scaffold moves into `backend-auth`; its NestJS project keeps the name `auth`.
- `backend-users` is created when its work starts.

**Not decided here:**

- How registration spans both services (synchronous call vs event) — decided with the authentication design
  (IDN-12).
- Where roles and permissions live — authorization model (IDN-13).
- When the Users service is built (Phase 1 or with Phase 4 "Customer Profiles") — IDN-15.

## Alternatives considered

- **One Identity service** owning credentials, users and roles — simpler (one repo, one database, no
  service-to-service call on registration), but less microservices practice and a single service that grows
  with every user-related feature.
- **External identity provider** (e.g. Keycloak) — realistic for production, but skips building auth by hand,
  which is part of what this project is for.

## Consequences

- Good: each service has one clear job and its own database; matches [0004](0004-microservices-from-phase-1.md)
  and [0009](0009-jira-team-spaces.md); repo names survive team renames.
- Cost: registration touches two services, so it needs a service-to-service call or an event; two repos to
  maintain for one team.
- Follow-ups: IDN-6 migrates `backend/auth` into `backend-auth`; IDN-15 sets up `backend-users` +
  `users_db`; PLAT-3 (init script) adds `users_db` / `users_service` when Users starts.
