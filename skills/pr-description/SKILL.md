---
name: pr-description
description: Write a clear pull request title and description from the actual diff and commits - what changed, why, how to test, risks and screenshots checklist - following the repo's PR template if one exists. Use when asked to write, draft or improve a PR/MR description, or before opening a pull request.
---

# PR Description

Write the description a reviewer wishes every PR had: why, what, how to verify, what could break.

## 1. Gather

1. Read `.claude/team-kit.md` if present (language, title convention, ticket prefix, PR template rules).
2. Look for a template: `.github/pull_request_template.md`, `.github/PULL_REQUEST_TEMPLATE/*`, `.gitlab/merge_request_templates/*`, `docs/pull_request_template.md`. If found, **fill that template** instead of the default below.
3. Determine base branch (team-kit, else `main`/`master`) and collect:
    - `git log --oneline <base>..HEAD`
    - `git diff --stat <base>...HEAD`
    - `git diff <base>...HEAD` (read it - do not summarize from the stat alone)
4. Find the ticket: branch name (`feat/ABC-123-...`), commit messages, or ask once. Never invent a ticket ID or link: if none is found, write "Ticket: none found" and ask.

### Large branches

If the stat shows more than ~1,500 changed lines or ~30 files, do not load the whole diff in one go:

1. Start from `git diff --stat` and the commit list: they are the map.
2. Skip noise: lock files, generated code, snapshots, vendored and minified files (e.g. `git diff <base>...HEAD -- . ':(exclude)*.lock' ':(exclude)package-lock.json' ':(exclude)dist/**'`). Mention them in one line ("lock file updated") rather than reading them.
3. Read the rest in groups, one directory or feature area at a time (`git diff <base>...HEAD -- <path>`), and write 2-3 lines of notes per group before moving on.
4. Read in full anything risky regardless of size: migrations, auth and permissions, payment code, public API contracts, config and env handling.
5. Write the description from the notes. Under "Notes for reviewer", say plainly what was only skimmed, and suggest splitting the PR if it mixes unrelated changes.

## 2. Analyse

- Group changes by intent (feature, fix, refactor, tests, config, migrations) - not by file.
- Detect anything a reviewer must notice: migrations, new env vars, new dependencies, changed public API, permission changes, feature flags, breaking changes, deleted code paths.
- Detect what is *missing*: tests for new behaviour, docs for new env vars, translation keys. List them under "Notes for reviewer" rather than hiding them.

## 3. Title

- Follow the team convention (team-kit → `PR title`). Default: Conventional Commits - `feat(export): add CSV export of invoices`.
- ≤ 72 characters, imperative, describes the outcome not the activity ("Add…" not "Working on…").

## 4. Default body

```markdown
## Why
<Problem / ticket link. 1-3 sentences.>

## What changed
- <Grouped by intent, user-visible first>

## How to test
1. <Step-by-step, including seed data / feature flag / URL>
2. Expected: <result>

## Risk & rollout
- Migrations: <none | description, reversible?>
- Config / env vars: <none | NEW_VAR (where to set it)>
- Breaking changes: <none | ...>
- Feature flag: <none | name, default>
- Rollback: <how>

## Screenshots
<Required if UI changed - before/after. Remove section otherwise.>

## Notes for reviewer
- <Where to start reading, trade-offs made, known gaps>
```

Remove sections that are truly empty (except Risk & rollout, where "none" is informative).

## 5. Deliver

- Output title + body in a code block ready to paste.
- If the user asks to open/update the PR: `gh pr create --title "..." --body-file <file>` or `gh pr edit <n> --body-file <file>`. Confirm before running.