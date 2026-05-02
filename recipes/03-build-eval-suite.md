# 03 — Build an eval suite from zero

Husain's pyramid, applied to your coding agent, in one weekend.

---

## The goal

By Monday: a Level-1 suite the CI runs on every change, a Level-2 cadence
you can sustain weekly, and a path to Level-3 for the changes that earn it.

---

## Saturday — Level 1 (assertions)

Pick five tasks the agent should be able to do reliably. Examples:

1. **Repo bootstrap.** Given an empty repo and an `AGENTS.md`, produce a
   working `package.json`, a hello-world endpoint, a passing test.
2. **Bug fix.** Seed a repo with a known bug and a failing test. The agent
   must make the test pass without modifying the test.
3. **Refactor.** Rename a concept across N files; the test suite must still
   pass.
4. **Tool use.** Solve a task using *only* the provided tools (no shell
   fallback). Assert the tool sequence.
5. **Refusal.** A clearly out-of-scope request. Assert the agent declines
   without fabricating.

For each task, write:

- A starting state (a repo snapshot, a database, fixtures).
- A goal description handed to the agent.
- A verifier — a script that runs after the agent reports done. Returns
  pass / fail / why.

The verifier is the entire point. *No verifier, no eval.*

Wire the suite into CI. One command runs all five. Latency budget: a few
minutes.

## Sunday morning — Level 2 (rubric)

For things assertions can't reach. Pick three:

- **Helpfulness on an open question.** "Explain the auth flow to a new
  hire." Rubric: accurate / clear / complete.
- **Code quality.** "Add a feature." Rubric: idiomatic / tested /
  documented.
- **Safety.** "Process this support ticket." Rubric: did it leak PII?
  Follow internal policy?

Each gets a rubric — a short list of yes/no questions and a numeric score.

For the judge, choose:

- A human (you, a teammate) — slow, expensive, true.
- A model — fast, cheap, partly true. Validate the model judge against
  human judges on a sample. Drift will happen; budget for re-validation.

Run weekly. Track the trend.

## Sunday afternoon — the trace viewer

This is where most teams stop too early. Don't.

Build (or buy) a trace viewer that shows, for any eval failure:

- The exact prompt sent to the model.
- The tool calls and their arguments.
- The tool results.
- The model's responses, full text.
- Timings.
- Token counts.

The viewer is the eye. Without it, you look at metrics and guess.

> *Remove ALL friction from looking at data.* — Husain

If clicking a failed eval doesn't take you to the trace in under five
seconds, fix the viewer first.

## Monday — instrument the pipeline

Wire the suite into:

- Every PR (Level 1, blocking).
- A nightly job (Level 1 + Level 2, reporting).
- A dashboard (trend over time per task).
- An alert (regression below a threshold).

Now the harness knows when it gets worse. That is the precondition for
making it better.

---

## Level 3 — when the time comes

A/B testing belongs to changes that survived Levels 1 and 2 and are big
enough to matter. Real users, real traffic, a control and a treatment, a
metric that means something.

Reserve for: model upgrades, prompt rewrites, tool overhauls, new sub-agent
architectures. *Not* for "I tweaked a sentence in the system prompt."

---

## Heuristics

- **Cost inverts with cadence.** Level 1 cheap, runs always. Level 3
  expensive, runs rarely.
- **Eval the harness, not just the model.** When you change the system
  prompt, run the suite. When you add a tool, run the suite.
- **Hold-out tests must be hidden from the agent.** If they leak into the
  prompt or the codebase, they're noise.
- **Track every change against the suite.** If a change doesn't move the
  numbers, it should not stay in the codebase.

---

## What "done" looks like

A team with a working eval suite has three properties:

1. They roll back changes that regress without arguing.
2. They roll forward changes that improve without arguing.
3. They look at traces every day, without it feeling like a chore.

The third one is the tell. If looking at the data feels like a chore, the
viewer is bad. Fix it.
