# Definition of Ready / Definition of Done

Why: [ADR 0010](../adr/0010-record-decisions-as-adrs.md). Statuses: [`jira.md`](jira.md#workflow).

## Definition of Ready

A ticket may move to `Ready For Development` or into a sprint when it:

- Has **Background + Why** — anyone can tell what problem it solves.
- Has **testable acceptance criteria**.
- Has its **dependencies known and linked** ("blocks"), and isn't blocked by unfinished work in the same
  sprint unless that's planned.
- Is **small enough to finish within one sprint** (otherwise split it).
- Has the **open decisions it depends on made** (or it's explicitly a spike to make them).

## Definition of Done

A ticket may move to `Done` when:

- All acceptance criteria are **met and verified** — run it, not just "it compiles".
- It's **reviewed** (`/code-review` or the user) and **merged** with the ticket key in the commit / PR title.
- **Docs are updated** — see [`CLAUDE.md`](../CLAUDE.md) → Write triggers (ADR if a decision was made).
- **No secrets committed**; nothing left that only works on one machine.
- **The user confirmed.**
