# 0006. Two frontends: Angular CRM, low-priority storefront

- **Status:** Accepted (revises the earlier "customer frontend not planned")
- **Date:** 2026-09-25
- **Owner (team):** future CRM and Storefront teams

## Context

The backend should serve frontends through the gateway. Originally only a CRM was planned; on 2026-09-25
the user added a customer storefront as a second, low-priority frontend.

## Decision

- **CRM** (`frontend/crm/`) — Angular, for internal operators. A frontend only; **no separate CRM backend**;
  it consumes the API through the gateway.
  - **Do not start the CRM until Categories, Inventory/Products and Customer Profiles are complete.** Those
    phases establish repeatable patterns; Customer Profiles maps directly to CRM concepts.
- **Customer storefront** (`frontend/storefront/`) — planned, **very low priority**, after the CRM.
  Framework not decided yet.
- The API stays frontend-friendly: clean response shapes, proper error codes, pagination-ready endpoints.

## Alternatives considered

- **A CRM-specific backend** — rejected; the CRM is just another client of the gateway.

## Consequences

- Good: one API for all clients.
- Follow-up: if a frontend needs data shaped specifically for its screens, that's a BFF owned by that
  frontend team, not a change to the shared gateway.
