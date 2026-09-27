# Roadmap

Build phases, in order. One phase at a time. **Tickets live in Jira** — this file only holds the order and
the scope notes that don't belong to any ticket yet. When a phase starts, its notes move into the owning
service's docs and are deleted here.

## Phases

| # | Module | Status |
|---|---|---|
| 1 | Auth & Users | In progress — Postgres isolation + service scaffold done; paused for the polyrepo migration |
| 2 | Categories | Planned |
| 3 | Inventory | Planned |
| 4 | Customer Profiles | Planned |
| 5 | Cart & Orders | Planned |
| 6 | Payments | Planned |
| 7 | Shipping & Returns | Planned |
| 8 | Reviews | Planned |
| 9 | Notifications | Planned |
| 10 | Audit Logs | Planned |
| 11 | Reporting | Planned |
| — | Vendors, Campaigns, Search, Analytics | Future, not phased |

Frontends: the Angular CRM starts only after Categories, Inventory and Customer Profiles are done; the
storefront comes after the CRM ([ADR 0006](adr/0006-frontends-crm-and-storefront.md)).

## Scope notes

- **Categories:** hierarchy stored as a closure table (`category_ancestors`) — carried over from the original
  design; confirm when the phase starts.
- **Inventory vs Cart & Orders:** decide *when* stock is reserved — at add-to-cart or at checkout — and align
  both phases when their scope is written.
