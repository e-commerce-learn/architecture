# Git workflow

Applies to every repo in the org.

## Repo settings (checklist for each new repo)

- [ ] Created in the `e-commerce-learn` org, **public**
- [ ] Named with its prefix: `architecture`, `infra-<tool>`, `backend-<service>`, `frontend-<app>`
      ([ADR 0011](../adr/0011-polyrepo-e-commerce-learn-org.md))
- [ ] Settings → General → Pull Requests: **only "Allow squash merging"** ticked, default commit message
      **"Pull request title"**; merge commits and rebase merging off
- [ ] **"Automatically delete head branches"** on
- [ ] Cloned to `~/Desktop/Code/e-commerce-learn/<group>/<repo>` (prefix kept in the folder name)
- [ ] Row added to [`services.md`](../services.md) in a matching PR in `architecture`

## Branches

`feature/<KEY>` from `main`, e.g. `feature/PLAT-11`. Sub-tasks can share their parent ticket's branch.
Never commit directly to `main`.

## Commits and PR titles

`Type(KEY-N): message` — e.g. `Docs(PLAT-10): add ADR 0011 polyrepo in e-commerce-learn org`,
`Chore(PLAT-2): move postgres to infra/compose.yaml`.

| Type | For |
|---|---|
| `Feat` | New behaviour |
| `Fix` | Bug fix |
| `Docs` | Documentation only |
| `Chore` | Tooling, config, infra, moves |
| `Refactor` | Code change with no behaviour change |
| `Test` | Tests only |

`Docs` and `Chore` are in use. `Feat`, `Fix`, `Refactor` and `Test` are proposed — confirm or change before first use.

Because merges are squash-only with "Pull request title" as the message, **the PR title becomes the commit
on `main`** — write it in this format.

## Flow

1. Move the ticket to `In Progress`; create `feature/<KEY>`.
2. Commit as you go (only the user decides when to commit — Claude never commits unasked).
3. Review: `/code-review` (or the user).
4. Push, open a PR into `main`; move the ticket to `In Review`.
5. Squash merge; the branch is deleted automatically.
6. The user confirms → `Done`.

## Never commit

`.env` files or any real credential — only `.env.example` with placeholder values.
