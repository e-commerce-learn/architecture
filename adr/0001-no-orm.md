# 0001. Raw SQL only, no ORM

- **Status:** Accepted
- **Date:** before 2026-08 (carried over from the original project)
- **Owner (team):** all backend teams

## Context

This is a learning project whose main goal is SQL practice and understanding backend architecture.
An ORM hides exactly the part that is meant to be learned.

## Decision

All database access uses **hand-written raw SQL prepared statements**. No ORM (TypeORM, Prisma,
Sequelize, MikroORM, …) and no query builder that replaces writing SQL.

## Alternatives considered

- **An ORM** — faster to start, but hides the SQL; rejected because SQL practice is the point.

## Consequences

- Good: full control and visibility of every query; real SQL skills.
- Cost: more boilerplate (row → DTO mapping, see [0007](0007-naming-snake-case-db-camel-case-api.md)); no automatic migrations.
- **Rule:** never suggest an ORM, regardless of complexity.
