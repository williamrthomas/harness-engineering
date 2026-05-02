# Codex

> *0 lines of manually-written code. Hundreds of internal users.
> ~1,500 PRs. Three engineers, then seven. 3.5 PRs per engineer per day.*
> — [OpenAI Codex team](https://openai.com/index/harness-engineering/)

Codex is OpenAI's coding agent — usable in the **app** (chat with worktree
isolation and cloud execution), the **CLI** (`codex` running locally against
your shell, your tools, your git), and the **SDK** (programmatic access from
TypeScript, Python, or anywhere else).

The Codex team coined "harness engineering" by living inside this constraint:
*the agent writes every line. Humans only steer.* Their primary-source post is
the most important single document on this surface; everything below is a
distillation.

---

## The five conviction lines

Before tactics, the worldview. These are quoted verbatim from
[Lopopolo's post](https://openai.com/index/harness-engineering/):

1. **"Humans steer. Agents execute."**
2. **"No manually-written code."**
3. **"Give Codex a map, not a 1,000-page instruction manual."**
4. **"Too much guidance becomes non-guidance."**
5. **"By enforcing invariants, not micromanaging implementations, we let
   agents ship fast."**

Read them. Read them again. They are the spine of every Codex pattern.

---

## Anatomy

A Codex harness has six layers. Each layer is independently improvable.

### 1. The repository

The repo is *the* primary instruction surface. More than the prompt. More
than chat history. The agent reads files; therefore the files are the agent's
mind.

A Codex-friendly repo:

- Has an **`AGENTS.md`** at the root that names what the project is, the
  package manager, the test command, the deploy command, and *where to look*
  for more.
- Sub-`AGENTS.md` files at directory boundaries when conventions differ.
  *Closest file wins.*
- A small set of **skills** (well-named, scoped, with one example each)
  rather than one giant prompt.
- Layout that makes the right thing easy: tests next to source, types in one
  place, generated code clearly marked.

> *We tried the "one big `AGENTS.md`" approach. It failed in predictable
> ways.* — Lopopolo

### 2. The map (`AGENTS.md`)

The map points. It does not narrate.

```
# AGENTS.md

This is the Foo service. It is a TypeScript Node.js HTTP API
backed by Postgres.

## Where things live
- `src/api/` — HTTP routes (one file per resource)
- `src/db/` — schema + migrations (timestamp-prefixed)
- `src/services/` — business logic, no I/O
- `tests/` — vitest, mirrors `src/`

## Commands
- install: `pnpm install`
- dev: `pnpm dev`
- test: `pnpm test`
- typecheck: `pnpm typecheck`
- lint+fix: `pnpm fix`

## Invariants (CI enforces these; do not bypass)
- All public functions have explicit return types.
- Money values are `Decimal`, never `float`.
- Migrations are reversible — every `up` has a `down`.
- No new env var without a default + entry in `env.schema.ts`.

## When stuck
- Look at `docs/architecture.md` for the high-level picture.
- Look at `docs/skills/` for task playbooks.
- Run `pnpm test --filter <name>` to verify a slice fast.
```

What this map does *not* contain: framework tutorials, language style guides,
file-by-file descriptions, or anything Codex can read for itself.

### 3. The skills

Skills carry knowledge that applies *sometimes*. A skill is a directory with
a `SKILL.md` — short metadata up top, instructions in the body, optional
bundled scripts. Skills are loaded on demand, so they don't tax the system
prompt.

A useful set for Codex:

- `bug-reproduction/` — how to spin up a clean worktree, capture logs,
  bisect.
- `database-migration/` — how to write reversible migrations and verify them.
- `feature-flag/` — when to add one, how to gate, how to retire.
- `release/` — the deploy checklist.
- `incident/` — runbook for production triage.

The Codex team builds skills the same way they build code: incrementally,
each one earning its slot.

### 4. The tools

Codex is given the tools a human engineer would use: `gh`, the package
manager, the test runner, the linter, the database CLI, the deploy CLI. Plus
custom skills for the things the team built.

A few high-leverage Codex tool patterns:

- **Per-worktree app.** Make the app bootable per git worktree, so the agent
  can launch one instance per change without colliding with other agents or
  with you.
- **Chrome DevTools Protocol wired into the runtime.** The agent can take
  screenshots, snapshot the DOM, navigate, and reason about UI behavior
  directly. No human acting as eyes.
- **Local observability stack, ephemeral per worktree.** Logs queried via
  LogQL, metrics via PromQL. Now prompts like *"ensure service startup
  completes in under 800ms"* become tractable.
- **Agent-driven review.** Codex reviews its own changes locally, requests
  additional agent reviews (local and cloud), responds to feedback, and
  loops until all reviewers are satisfied. *The Ralph Wiggum Loop.*

### 5. The loop

> *Single Codex runs work on a single task for upwards of six hours, often
> while the humans are sleeping.* — Lopopolo

Long autonomous runs are only safe inside a strong loop. The Codex loop:

```
plan → act → run tests → run typecheck → run lint
     → self-review → request peer agent review
     → fix → repeat until green → open PR
```

The humans do not sit in this loop. They sit *outside* it, watching the PR
queue and approving the irreversible.

### 6. The layer rule

Push every constraint to the leftmost layer that catches it.

> *Types → Config → Repo → Service → Runtime → UI*

A bug caught by the typechecker costs nothing. A bug caught in production
costs a war room. The whole point of Codex's harness is to bias every
constraint left until production has nothing left to catch.

---

## App, CLI, SDK

### App (Codex on chatgpt.com / native apps)

- Best for **bounded, self-contained tasks**: a feature spec, a bug report,
  a refactor with a clear before/after.
- Use **per-worktree isolation** to parallelize. Different agents work on
  different branches without collision.
- Approve PRs, not keystrokes. The agent runs free until it produces a
  reviewable diff.

### CLI (`codex`)

- Best for **inside-the-loop work**: hands on the keyboard, agent at the
  prompt, you steering between turns.
- Live in the same shell, the same git, the same env. Powerful and dangerous —
  trust boundaries are weaker than the cloud surface.
- Sandboxes and approval modes exist. Use them.

### SDK

- Best for **embedding Codex inside other systems**: CI bots, ticket
  triagers, documentation generators, internal QA agents.
- A long-lived SDK script *is* a custom harness. Apply the same doctrine —
  invariants, sensors, loops, evals — to the script as you would to a repo.
- Treat each SDK call as a discrete `act → observe` step. Don't try to
  recreate the chat UI. Build the loop you actually need.

---

## Idioms unique to Codex

- **Map, not manual.** Repeated everywhere because it is repeated everywhere.
- **Worktree-per-task.** The unit of agent state is a worktree, not a session.
- **Agent-to-agent review.** Push human review right; let agents catch the
  cheap stuff.
- **Make everything legible.** Logs, metrics, UI snapshots, traces — pipe
  them all into something the agent can query.
- **Garbage collection between iterations.** A long loop accumulates cruft;
  prune it explicitly.

---

## Where Codex shines, where to be careful

**Shines:**
- Greenfield projects. The harness is malleable; the agent shapes it.
- Long-horizon tasks behind strong CI.
- Anything where invariants can be expressed as code.

**Be careful:**
- Brittle legacy codebases with no test suite. Build the harness first.
- Tasks where the right answer is genuinely ambiguous; the agent will pick
  *an* answer with confidence.
- Long-running cloud agents without egress filtering. The trifecta is real.

---

## Further reading

- [Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/) — Ryan Lopopolo, OpenAI. *The* primary source.
- [AGENTS.md spec](https://agents.md) — Open standard, used by Codex and many others.
- See [Patterns](../patterns/) for cross-cutting techniques (context shaping,
  tool design, sub-agents) that apply directly to Codex.
