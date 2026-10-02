# 0013. Platform tools follow the user's company stack

- **Status:** Accepted
- **Date:** 2026-09-28
- **Owner (team):** Platform
- **Scope:** global
- **Jira:** PLAT-36

> **Note:** Overrides [0004](0004-microservices-from-phase-1.md) (the "No Consul / service registry" point: Consul
> is used, together with Nomad). Settles the tool choice in PLAT-27 (gateway) and PLAT-35 (logging).

## Context

The project is for learning backend work *and* for preparing for the user's job. The user's company runs
Gravitee, Elastic, Grafana, Nomad, Consul and Vault. The user uses some of them daily (for example Gravitee's
request/response logs) without knowing how they fit together.

## Decision

When the project needs a platform tool, it **prefers the one the user's company uses**, so skills carry over.
Decided so far:

| Area | Tool | Replaces / fills |
|---|---|---|
| API gateway | **Gravitee APIM** (Community Edition) | The custom NestJS gateway planned in PLAT-4 |
| Logs + tracing | **Elastic Stack**: Filebeat → Elasticsearch → Kibana, plus **APM** for traces (services send them with OpenTelemetry) | `docker logs` per container |
| Metrics, dashboards, alerts | **Grafana** (OSS), metrics most likely from **Prometheus** (confirm what the user's company uses when P4 starts) | Nothing yet |
| Runtime across machines | **HashiCorp Nomad** | Compose, outside local dev |
| Service discovery + health checks | **HashiCorp Consul** | Compose's built-in DNS, outside local dev |
| Secrets | **HashiCorp Vault**, arriving **with Nomad** (P6). Nomad jobs fetch secrets from Vault, so services contain no Vault code | Git-ignored `.env` files, then Docker secret files (PLAT-32) |

**Nomad + Consul (decided 2026-09-30)** only pay off across several machines. The project gets a second, real
machine: the user's **MSI laptop** (Linux, joined to the Mac over the VPN), next to VMs on the Mac. Unplugging it
is a real "server died" test. Oracle Always Free was checked and rejected as the main home (limits halved in
June 2026, idle VMs reclaimed); cheap on-demand cloud VMs (e.g. with Terraform) stay an option for later.
They are **not** used until the project has enough to run on them. **Docker Compose stays for local dev.** When
to start: [`roadmap.md`](../roadmap.md) → Platform, P6–P7.

**Vault timing (decided 2026-09-30):** not before Nomad. Without Nomad, every service would need its own Vault
login code (repeated per repo under zero shared code, and thrown away later). Secret files (PLAT-32) come first.
Dynamic Postgres users (Vault creates short-lived DB users) are a stretch goal, not part of the plan.

Only self-hosted free editions are used. How each tool is set up (repo, components, versions, what runs
locally) is decided when its roadmap step starts.

## Alternatives considered

- **Pick each tool on its merits** (e.g. Grafana Loki instead of Elastic): reasonable, but loses the direct
  carry-over to the user's job.
- **Hand-written gateway first**: teaches what a gateway does inside; declined because the user is new to
  gateway concepts and wants to learn them through the tool used at work.

## Consequences

- Good: skills carry over to the user's job; Gravitee stores its analytics in Elasticsearch; Nomad registers
  services in Consul, so the tools fit together.
- Cost: two CPU architectures (Mac = ARM, MSI = x86), so Docker images must be built for both
  (`linux/arm64` + `linux/amd64`).
- Cost: more infrastructure than the features need. Elasticsearch and Gravitee are Java-based and
  memory-hungry: the dev Mac has 16 GB, Docker is limited to 7.8 GB, so only what the current work needs runs
  at once.
- Cost: time. The user has ~5–6 hours a week; infrastructure work competes with the shop's features.
- Follow-ups: the order is in [`roadmap.md`](../roadmap.md) → Platform. Tickets are opened when each step is
  about to start ([0014](0014-tickets-just-in-time.md)).
