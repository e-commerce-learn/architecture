# 0015. Infrastructure is private: reachable only through a VPN, secured with certificates

- **Status:** Accepted
- **Date:** 2026-09-30
- **Owner (team):** Platform
- **Scope:** global
- **Jira:** PLAT-36

## Context

Once the project is deployed, most of what runs is infrastructure: Postgres, the Gravitee console and
management API, Kibana and Elasticsearch, and possibly Vault, Consul, Nomad and Grafana
([ADR 0013](0013-platform-mirrors-company-stack.md)). None of these should be reachable from the internet.
Admin UIs and databases on the public internet are a common way systems get broken into. The user wants the
project to follow the practice of their company: infrastructure hidden behind a VPN, with certificates.

## Decision

In every deployed environment:

1. **Public:** only the entry points users need: the Gravitee **gateway** (the API) and the frontends.
2. **Private, VPN only:** everything else. That covers databases, admin UIs and consoles (Gravitee console
   and management API, Kibana, Grafana, Vault, Consul, Nomad), and the services themselves (they're reached
   through the gateway, never directly).
3. **Certificates:** traffic is encrypted with TLS, both publicly (HTTPS on the gateway) and inside the private
   network. VPN access is tied to a personal certificate or key, not just a shared password.

**Not decided yet**, when deployment is planned: which VPN (e.g. WireGuard, OpenVPN, Tailscale, or what the
user's company uses), where certificates come from (e.g. Let's Encrypt for the public side; a private
certificate authority for the inside, possibly Vault's PKI engine if Vault is adopted), and whether services
also check each other's certificates (mutual TLS).

**Local dev** follows the same idea: ports are bound to `127.0.0.1` so only the developer's own machine can
reach them (e.g. `infra-postgres`).

## Alternatives considered

- **Public, with passwords only**: a single leaked or guessed password exposes the system; admin UIs are
  common attack targets.
- **IP allow-lists instead of a VPN**: breaks when the developer's IP changes and doesn't identify the person.

## Consequences

- Good: the attack surface is the gateway and the frontends only; admin tools are safe to run with simpler
  settings inside the private network.
- Cost: a VPN server or service to run, certificates to issue and renew, and connecting to the VPN before any
  admin work.
- Follow-ups: part of the deployment step in [`roadmap.md`](../roadmap.md) → Platform. Tickets when
  deployment is about to start ([ADR 0014](0014-tickets-just-in-time.md)).
