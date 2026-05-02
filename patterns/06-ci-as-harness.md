# 06 — CI as harness

> *We made the app bootable per git worktree, so Codex could launch and
> drive one instance per change.*
> — [OpenAI](https://openai.com/index/harness-engineering/)

CI is not a checkpoint. It is *part of the agent's harness*. The pipeline is
how the harness talks back at the largest scale you have.

---

## CI as the outer loop

Every loop the agent runs locally — read, edit, test — has a counterpart at
the team scale: open a PR, run CI, get reviewed, merge.

The local loop is fast and intimate. The CI loop is slow and public. Both
are *the same shape*. Both deserve the same engineering.

A CI pipeline that talks to the agent well:

- **Names the failure precisely.** File. Line. Rule. Suggested fix.
- **Returns logs the agent can grep.** Not opaque colored output meant for
  human eyes.
- **Caches aggressively.** A 30-minute pipeline is a 30-minute round trip.
  Halve it and the agent ships twice as much.
- **Posts back as a structured comment** the agent can read on its next
  turn.

If the agent has to interpret a CI failure by squinting at a webpage, the
pipeline is a sensor only for humans. Make it a sensor for the agent.

---

## The layer rule applied to CI

> *Types → Config → Repo → Service → Runtime → UI*

Push every constraint to the leftmost layer that catches it. CI lives at
*Service* and *Runtime*. Use it for what nothing earlier can catch:

- Integration tests across services.
- End-to-end browser tests.
- Migration up/down round trips.
- Performance budgets ("p95 latency < 200ms").
- Eval suite execution.
- Deployment smoke tests.

Don't use CI for what a typechecker, linter, or pre-commit hook would catch
locally. Local catches are seconds; CI catches are minutes; production
catches are war rooms.

---

## Per-worktree isolation

The single highest-leverage CI move for agentic teams: make the application
**bootable per git worktree**. Every PR — and every agent — runs in its own
isolated environment, with its own database, its own logs, its own metrics.

Why it matters:

- Multiple agents in flight simultaneously without collision.
- The agent can launch, drive, and verify the app for *its* PR.
- Logs and metrics are scoped to *this* change.
- Debugging is local-feeling even when running in cloud.

Build this once. It pays for itself within a quarter.

---

## Observability the agent can read

The Codex team wires LogQL and PromQL directly into the agent runtime. The
agent can ask:

- "What was the p95 latency on `/api/foo` over the last 5 minutes?"
- "Which spans took more than 200ms?"
- "What did the service log when it returned 500?"

Now prompts like *"ensure service startup completes in under 800ms"* or
*"no span in these four critical user journeys exceeds two seconds"* become
tractable.

The same principle applies in miniature to local dev: pipe `tail -f`,
`jq`-friendly logs, structured timing into something the agent can grep.

---

## Agent-to-agent review

> *We've pushed almost all review effort towards being handled
> agent-to-agent.* — OpenAI

A second agent reviews the first agent's PR before a human looks at it. The
reviewer agent:

- Has a *different* role prompt — focused on correctness, security, style.
- Can call the same tools and run the same tests.
- Posts comments on the PR that the original agent reads and responds to.

The Ralph Wiggum Loop, again: the dumbest thing that could possibly work,
in a loop, with feedback. Two agents, one PR, until both are satisfied.

This pushes human review *right*. Humans catch the genuinely novel; agents
catch everything else.

---

## CI as a gate, not a guess

Treat CI as the place where invariants get the last word. If the suite is
green, the change is mergeable. If it is red, the change is not. No "the
test is flaky, just retry." Flakes are bugs. Fix the flake; do not approve
around it.

The agent will respect a deterministic gate. It will exploit a fuzzy one.
