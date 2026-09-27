# 0007. `snake_case` in the DB, `camelCase` in the API

- **Status:** Accepted
- **Date:** before 2026-08 (carried over from the original project)
- **Owner (team):** all backend teams

## Context

SQL convention is `snake_case`; JavaScript/JSON convention is `camelCase`. With raw SQL
([0001](0001-no-orm.md)) there is no ORM to translate automatically.

## Decision

DB columns use `snake_case`. DTO fields and API response properties use `camelCase`. Mapping happens in the
service layer.

## Consequences

- Good: each side follows its own idiom; clients never see DB internals.
- Cost: explicit row → DTO mapping code in every service.
- **Rule:** never return raw DB column names to the client.
