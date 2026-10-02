# pr-description — a Claude Code skill

Type `write the PR description` and get the description a reviewer wishes every PR had: why, what changed, how to test, what could break. Written from the real diff, in your repo's own PR template.

Free and MIT-licensed. One skill from the [DevTeam AI Kit](https://devteam-ai-kit.netlify.app/?ref=github).

## What it does

- Reads the **actual diff and commits**, not just the file list.
- Fills **your PR template** if the repo has one (`.github/pull_request_template.md`, GitLab templates…).
- Groups changes by intent (feature, fix, refactor, migration), not by file.
- Calls out what a reviewer must notice: migrations, new env vars, new dependencies, breaking changes, feature flags.
- Lists what is **missing** (tests, docs) instead of hiding it.
- Follows your title convention (Conventional Commits by default).
- Never opens or edits a PR without your confirmation.

## Example

A 9-line endpoint was added to a test repo. Prompt: `write the PR description`. Shortened output:

```markdown
**Title:** feat(orders): add listOrders handler with items and total

## Why
SHOP-42: clients need to list orders, optionally filtered by status,
with each order's line items and an aggregate total.

## How to test
1. Wire the handler to a route, then seed a few orders with different statuses.
2. `GET /orders?status=paid`
3. Expected: `200` with `{ orders: [...], total }`, only `paid` orders.

## Risk & rollout
- Migrations: none
- Breaking changes: none (additive export)
- Rollback: revert the commit

## Notes for reviewer
- **Blocker: SQL injection.** The orders query interpolates `req.query.status`
  directly into the SQL string.
- **N+1 queries.** Items are fetched in a loop, one query per order.
- **No tests.** Nothing in the diff covers the new behaviour.
```

The ticket number came from the branch name (`feat/SHOP-42-list-orders`).

## Install

```bash
# for one project (commit it so your team gets it too)
mkdir -p .claude/skills
cp -r skills/pr-description .claude/skills/

# or for you, in every project
mkdir -p ~/.claude/skills
cp -r skills/pr-description ~/.claude/skills/
```

Then, in Claude Code, on your feature branch:

```
> write the PR description
```

Requires [Claude Code](https://claude.com/claude-code) and `git`. The GitHub CLI `gh` is optional (used only if you ask it to open the PR).

## Optional team configuration

Create `.claude/team-kit.md` to set your conventions:

```markdown
## Language
English

## Conventions
- Base branch: `main`
- PR title: Conventional Commits (`feat(scope): ...`)
- Ticket prefix: `ABC-`
```

## Customize it

The skill is one Markdown file: `skills/pr-description/SKILL.md`. Edit the default template, add sections your team needs.

## Want the other 10?

The full [DevTeam AI Kit](https://devteam-ai-kit.netlify.app/?ref=github) covers the rest of a dev team's workflow, all sharing the same `team-kit.md` configuration:

`code-review` · `ticket-to-spec` · `task-breakdown` · `test-writer` · `adr-writer` · `incident-postmortem` · `release-notes` · `one-on-one-prep` · `sprint-planning` · `interview-kit`

## Licence

MIT. Not affiliated with or endorsed by Anthropic.
