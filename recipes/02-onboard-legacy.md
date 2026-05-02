# 02 — Onboard an agent to a legacy codebase

A 100k-LOC repo with no tests, three frameworks, one dead Slack channel
named `#wiki`, and a deploy process that lives in someone's head.

---

## The principle

You cannot put the codebase into the prompt. You cannot put the codebase
into the agent's head. You can only build a **map** the agent can navigate.

This is the longest-pole fix. Treat it as a project, not a task.

---

## Phase 1 — Survey

Spend a day with the agent itself doing the survey. Pair-program your
discovery.

Ask the agent:

- "Read the top-level directory and describe what you see."
- "Which directories are dead code? How can you tell?"
- "Where are the tests, if any? What runner?"
- "How does the app start? What's the entry point?"
- "What database? What framework? What deploy?"
- "What conventions are used in `src/foo/`? Are they consistent across the
  repo?"

Capture the answers in a `docs/survey.md`. Many will be wrong. Correct them.
The corrections themselves are knowledge.

## Phase 2 — Build the map

From the survey, write a single `AGENTS.md` (and/or `CLAUDE.md`):

```
# AGENTS.md

## Identity
This repo is the Foo monolith. ~120k lines. Started 2018.
Stack: Rails 6 + React + Postgres + Sidekiq.

## Where things live
- `app/` — Rails app code (controllers, models, views).
- `frontend/` — React, ejected webpack config.
- `lib/` — shared utilities. Beware: `lib/legacy/` is a graveyard.
- `spec/` — RSpec tests. Run with `bundle exec rspec`.
- `script/` — operational scripts. Read before invoking.

## Commands
install: `bundle install && cd frontend && yarn`
dev:     `bin/dev`
test:    `bundle exec rspec` (Ruby), `cd frontend && yarn test`
deploy:  see `docs/deploy.md` (DO NOT run blindly).

## Conventions worth knowing
- New Ruby code: avoid `concerns/`, prefer service objects in `app/services/`.
- Frontend: no class components in new code; functional + hooks only.
- Database: migrations are reviewed by @alice; don't auto-merge.

## Invariants
- `bin/lint` must pass.
- `rspec --tag critical` must pass.
- No new env var without an entry in `.env.example`.

## When stuck
- `docs/architecture.md` — the picture.
- `docs/decisions/` — why we did things the way we did.
- `docs/runbook.md` — what to do when prod is sad.
```

Notice what's *missing*: framework tutorials, file-by-file descriptions,
historical commentary. Save those for skills or external docs.

## Phase 3 — Wire the cheapest sensors first

The agent needs feedback. Without a test suite, build the cheapest sensors
that exist:

- The linter — even one rule is better than none.
- The typechecker — Sorbet, ruby-lsp, mypy, even partial.
- A smoke test — does the app boot and return 200 on `/`?
- A "fixture run" — does this URL render without 500ing?

Don't wait for full test coverage. Build the loop with whatever signal you
have, then expand.

## Phase 4 — Skills for the rituals

Legacy codebases are full of rituals. Encode them as skills.

- `add-endpoint/` — a checklist for adding a new HTTP endpoint, in *this
  codebase's* conventions.
- `migration/` — how to write, test, and merge a migration here.
- `feature-flag/` — the in-house flag system, with examples.
- `incident/` — runbook for the on-call.

The agent does not need to learn these rituals from scratch every session.
The harness teaches them once.

## Phase 5 — Cordon the danger zones

Some parts of every legacy codebase should be touched only by the human, or
never. Mark them.

```
# AGENTS.md

## Touch with care
- `app/services/billing/` — money. Always pair-review.
- `lib/legacy/` — deprecated; do not extend.
- `db/schema.rb` — generated; don't edit directly.
```

The agent will respect a clearly drawn line. It will *exploit* a fuzzy one.

## Phase 6 — Take the first turn

Hand the agent a small, well-spec'd ticket. Watch where it struggles. Each
struggle is a hole in the map. Patch it. Compound.

---

## A note on test coverage

A legacy codebase without tests is a harness without sensors. The single
highest-leverage long-term investment is **building a test suite, with the
agent's help, around the most-changed files first**.

This is not a "dev productivity" project. It is the harness becoming able to
catch the agent's mistakes. Without it, every change is a vibe — including
the agent's.
