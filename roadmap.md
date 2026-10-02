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

## Platform

Infrastructure steps, in order ([ADR 0013](adr/0013-platform-mirrors-company-stack.md)). Candidates are the user's company tools, to be decided once the user understands them. Each gets its Jira
tickets only when it's about to start ([ADR 0014](adr/0014-tickets-just-in-time.md)). Interleave with the
feature phases above rather than doing all of them first.

| # | Step | Status | Notes |
|---|---|---|---|
| P1 | Postgres in `infra-postgres`, rebuilt from scratch | In progress — PLAT-12 | Then IDN-16: `backend-auth` connects to it |
| P2 | Gravitee in front of `backend-auth` | Planned — setup decision PLAT-27 | Routing `/auth/*`, JWT policy (needs IDN-12), rate limits, analytics |
| P3 | Structured logs + request ID, then Elastic | Planned — PLAT-28, PLAT-35 | JSON logs to stdout; Filebeat → Elasticsearch → Kibana; APM traces via OpenTelemetry. Cap Elasticsearch's memory |
| P4 | Grafana dashboards + alerts | Planned — **start when** P3 is done and 2+ services run behind Gravitee | Metrics store most likely Prometheus (ask what the user's company uses). Each NestJS service exposes `/metrics`. First dashboard: requests/s, error rate, p95 latency, memory, DB connections; first alert: error rate > 5% for 5 min. Later also Gravitee, Consul, Nomad metrics. Private behind the VPN once deployed (ADR 0015) |
| P5 | Secrets as Docker secret files | Planned — PLAT-32; **start when** convenient after P1 (small) | Password moves from an env var to a git-ignored file (`POSTGRES_PASSWORD_FILE`); no longer visible in `docker inspect`. Vault comes later, with Nomad (P6) |
| P6 | Nomad + Consul + Vault warm-up on the Mac (dev mode) | Planned — **start when** Phase 1 (Auth & Users) is done, P2–P3 are done, and at least 2 services build Docker images in CI (`ghcr.io`) and have a `/health` endpoint | `nomad agent -dev` + `consul agent -dev` + `vault server -dev`: job files, deployments, canaries, auto-rollback, services registered in Consul, Gravitee finding them, a Nomad job reading `backend-auth`'s DB password from Vault (template), no Vault code in the service. Compose stays for daily dev. Ask at work what developers do with Nomad there |
| P7 | Nomad + Consul + Vault cluster: Mac VMs + MSI laptop | Planned — **start when** P6 is done and a 3rd service exists (e.g. Categories, Phase 2) | MSI: install Linux (Ubuntu Server), Docker, Nomad + Consul clients; join over Tailscale/WireGuard (ADR 0015). Images multi-arch (Mac ARM, MSI x86). Test: unplug the MSI, watch services move. All service secrets come from Vault (private behind the VPN). Stretch: Vault dynamic Postgres users (touches the connection code from IDN-16). Open: MSI's RAM/CPU, wipe vs dual-boot Windows. Later option: on-demand cloud VMs with Terraform |
| P8 | Deployment: private network behind a VPN, with certificates | Planned — [ADR 0015](adr/0015-private-infrastructure-behind-vpn.md) | Only the gateway + frontends public. Decide: VPN product (ask what the user's company uses), certificate source, mutual TLS |

## Scope notes

- **Categories:** hierarchy stored as a closure table (`category_ancestors`) — carried over from the original
  design; confirm when the phase starts.
- **Camunda ([ADR 0016](adr/0016-camunda-for-long-running-workflows.md)):** when phases 5–7 start, check which flows need it —
  order fulfilment (reserve stock → payment with timeout → confirm or compensate), returns/refunds with
  approval, later vendor onboarding. Decide orchestration (Camunda) vs plain events per flow.
- **Inventory vs Cart & Orders:** decide *when* stock is reserved — at add-to-cart or at checkout — and align
  both phases when their scope is written.
