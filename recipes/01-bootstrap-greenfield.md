# 01 — Bootstrap a greenfield repo

Day-one harness for a project the agent will mostly write.

---

## The goal

A repo where, on the first commit, the agent already has:

- A small, accurate `AGENTS.md` (and/or `CLAUDE.md`).
- The right tools available with the right names.
- Linters, typecheckers, and tests wired into CI.
- An eval suite stub.
- A clear gate for irreversible actions.

You only get to do this once per project. Do it right.

---

## The sequence

### Step 1 — Pick your stack with intent

Pick technologies the model knows well *and* that have strong static analysis.
The grain of the language matters.

- **TypeScript with strict mode** is friendlier than untyped Python for agents.
- **Rust** is friendlier than C — the compiler is a sensor.
- **A monorepo with one package manager** is friendlier than five repos.

The model is the same; the *feedback the harness can deliver* differs by an
order of magnitude.

### Step 2 — `/init`, then prune

Use the agent itself to scan the empty repo and produce the first
`AGENTS.md`. Then *cut it in half*. Most generated `AGENTS.md` files are
twice as long as they should be.

What stays:

- **Project identity.** One paragraph.
- **Where things live.** One section.
- **Commands.** install / dev / test / typecheck / lint / deploy.
- **Invariants.** 3–7 lines of must-be-true.
- **When stuck.** One paragraph pointing at deeper docs.

What goes: anything the agent can read from `package.json`, the file tree,
or its training.

### Step 3 — Wire the sensors

Before the agent writes feature code, set up the sensors:

- Strict typechecking.
- Linter with auto-fix on save and in CI.
- Formatter (no debate, no config).
- Pre-commit hook that runs lint + typecheck.
- Test runner with fast filtering.
- A `Makefile` (or `justfile`) that names every command.

Each of these is a feedback signal the agent will use a hundred times. Spend
the morning on them.

### Step 4 — Pick the autonomy altitude

Decide *up front* where the slider sits:

- Keystroke approval (rare; only for the first hour, learning the harness).
- Allowlist common commands (the default).
- Sandbox + auto-mode classifier (when ready).
- PR-only gate (when CI is strong).

Write the choice into `AGENTS.md` so future-you, future-teammates, and the
agent itself know.

### Step 5 — Seed the eval suite

Even three Level-1 evals beat zero:

- *"Generate the README scaffold from the project description; assert
  it contains <project name>, install, and dev commands."*
- *"Add a hello-world endpoint; assert it responds 200 and matches a
  schema."*
- *"Write a failing test for an unimplemented function; assert the test
  fails for the right reason."*

Run them on every change. They will save you sooner than you expect.

### Step 6 — First skill

Pick the first skill. Don't pick all of them. A useful first skill on most
projects:

- `release/` — how we cut a release. Branching, version bumps, changelog,
  tag, deploy.

It is well-scoped, mechanical, and a textbook example of the *applies
sometimes* knowledge that should not bloat `AGENTS.md`.

### Step 7 — Commit and let the agent take a turn

Hand the agent a small starter ticket. Watch what it does. Watch where it
struggles. Each struggle is a ticket back to the harness.

This is the [Hashimoto rule](../doctrine/01-agent-equals-model-plus-harness.md)
in action. The harness compounds from here.

---

## Anti-checklist (avoid)

- **Do not** copy a 5,000-line `AGENTS.md` from another project. It will be
  mostly wrong and the wrong lines will be invisible.
- **Do not** install every linter rule on day one. Start strict but
  tractable; tighten as the agent demonstrates competence.
- **Do not** wire CI to a slow integration test suite before you have a
  fast one. Round-trip time *is* the harness.
- **Do not** skip the eval stub. Even three tests are enough to catch the
  worst regressions.
