# 05 — Eval loops

> *No eval, no feature.*
> — folk wisdom in applied AI, well-supported by [Hamel Husain](https://hamel.dev/blog/posts/evals/)

Eval loops are how a harness *knows* it is improving. Without them, every
change is a vibe.

---

## The three levels (Husain's framework)

### Level 1 — Unit tests

Assertions. Deterministic. Cheap. Run on every change.

Examples for an agent:

- Output is valid JSON.
- Output contains the user's question repeated back.
- The agent called `tool_X` exactly once.
- The agent did *not* call `tool_dangerous`.
- A regex over the response (no PII, no slurs, no internal terms leaked).

Build hundreds of these. They are cheap. They catch the dumb stuff.

### Level 2 — Model & human eval

Where assertions can't reach: tone, helpfulness, factual accuracy on open
questions, faithfulness to a long source.

Two flavors:

- **Human review.** Random sample of traces, rated by a person against a
  rubric. Slow, expensive, true.
- **LLM-as-judge.** A model rates a trace against a rubric. Fast, cheap,
  partly true. Good rubrics generalize; bad rubrics drift.

Run on a *cadence*: weekly, after meaningful prompt changes, after model
upgrades. Track the trend.

### Level 3 — A/B tests

Real users, real metrics, real money. Slowest, most expensive, most
truthful.

Reserve for changes that survived levels 1 and 2. Most changes do not get
to level 3, and that is correct.

---

## The cadence inverts the cost

| Level | Cost | Cadence |
| --- | --- | --- |
| 1 | $ | Every change. |
| 2 | $$ | Weekly / on prompt change / on model change. |
| 3 | $$$ | Significant product changes only. |

Cheap things run often. Expensive things run rarely. This is not a
constraint; it is the *point*.

---

## Look at your data

> *Remove ALL friction from looking at data.*
> — Husain

> *You are doing it wrong if you aren't looking at lots of data.*
> — Husain

Build the trace viewer. Build the search. Build the filter. Build the link
that takes you from a metric anomaly to the exact prompt and response in
under five clicks. The single highest-leverage action in any AI project is
*reducing the seconds-from-curiosity-to-trace*.

If the trace viewer is bad, fix it before you fix the prompt. The viewer is
the eye; the prompt is the hand. Bad eyes, bad hands.

---

## What to eval (coding-agent edition)

A useful starter suite for any harness shipping coding agents:

- **Repo bootstrap.** Given an `AGENTS.md`, can the agent set up a fresh
  repo with the right conventions?
- **Bug fix.** Seed a known bug; does the agent locate, fix, and test it?
  (Hold-out test verifies.)
- **Feature add.** A small, well-spec'd feature. Hold-out test verifies.
- **Refactor.** Rename a concept across N files; does the test suite still
  pass?
- **Tool use.** Solve a task using *only* the tools provided; no shell
  fallback.
- **Refusal.** Refuse a manifestly bad request. Don't refuse a good one.
- **Trust boundary.** A tool result containing prompt injection — does the
  agent follow it? (It should not.)

Each task gets a deterministic verifier (Level 1) or a curated rubric
(Level 2). Versions track over time. Regressions are visible.

---

## Eval the harness, not just the model

The most common mistake is treating evals as model evaluation. They are not;
they are *system* evaluation.

When you change the system prompt, run the suite. When you add a tool, run
the suite. When you tighten the linter, run the suite. When the model
provider ships a new minor version, run the suite.

If a change moves the numbers, you have learned something. If it doesn't,
roll it back. The harness compounds without bloating because every line in
it justifies itself against the suite.

---

## The eval pyramid is the harness pyramid

Level 1 evals are *Computational controls* in
[Böckeler's vocabulary](../doctrine/04-guides-and-sensors.md). Level 2 evals
are *Inferential controls*. Level 3 is the world telling you the truth.

Build the pyramid in order. Level 1 first. Level 2 when assertions don't
reach. Level 3 only when you are sure enough to spend it.
