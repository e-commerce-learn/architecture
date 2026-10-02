# 0016. Camunda for long-running business workflows, where they're needed

- **Status:** Accepted
- **Date:** 2026-10-02
- **Owner (team):** Platform (runs Camunda); each service team owns its own processes
- **Scope:** global
- **Jira:** PLAT-36

## Context

Some e-commerce flows span several services and take minutes to days, e.g. an order: reserve stock →
take payment → ship → notify, and undo earlier steps when a later one fails (release stock if payment fails).
Some need a person to act (approve a return, approve a vendor). Writing that as chains of service calls and
queue messages spreads the flow over many repos, where nobody can see where a given order is stuck.

Workflow engines such as **Camunda** solve this: the flow is drawn as a **BPMN** diagram, the engine runs it,
remembers each instance's state, handles timers, retries and human tasks, and shows every running instance
in a UI. The user wants the project to use Camunda where real-world systems would.

## Decision

We use **Camunda 8** (Self-Managed) **only where a flow needs it**, i.e. when at least one of these holds:

- it spans **several services** and needs **compensation** (undoing earlier steps) when one fails;
- it **waits** for something: a timer ("cancel if unpaid after 30 minutes"), an external event (payment
  callback, delivery update);
- it has a **human step** (approve, review);
- the business needs to **see** where each instance is.

Simple request/response or CRUD work never goes through Camunda.

Expected first uses, decided when each phase starts:

| Phase | Flow |
|---|---|
| 5 Cart & Orders / 6 Payments | Order fulfilment: reserve stock → payment (with timeout) → confirm or compensate |
| 7 Shipping & Returns | Returns and refunds, with an approval step |
| Vendors (future) | Vendor onboarding with an admin approval step |

Services take part through **job workers** (a small piece of code in the service that picks up "do step X"
jobs from Camunda), so each service keeps owning its own logic and data (ADR 0004).

**Licence:** Camunda 8 Self-Managed is under the Camunda License 1.0: **free for development and testing**,
an enterprise licence is needed for production. Fine for this learning project; if anything is ever run as
a real production system, revisit. (Camunda 7 Community Edition reached end of life in October 2025, so it's
not an option.)

## Alternatives considered

- **Choreography only** (services react to each other's RabbitMQ events): no central engine, but the flow
  is invisible and compensation logic is scattered.
- **Hand-written orchestrator service**: full control, but rebuilds what an engine already does.
- **Temporal** or other engines: valid, but the user chose Camunda (BPMN, widely used in enterprises).

## Consequences

- Good: long-running flows are visible and testable as diagrams; learning BPMN and orchestration vs
  choreography.
- Cost: another heavy component (the engine, plus a search database for its web UIs) on a 16 GB Mac, so it
  runs only while that work is being done. Job-worker code in each service that takes part.
- Follow-ups: [`roadmap.md`](../roadmap.md) → scope notes for phases 5–7. Tickets when those phases start
  ([0014](0014-tickets-just-in-time.md)).
