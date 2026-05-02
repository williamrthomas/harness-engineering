# 07 — The eval is the product

> *Unsuccessful products almost always share a common root cause: a failure
> to create robust evaluation systems.*
> — Hamel Husain, [Your AI Product Needs Evals](https://hamel.dev/blog/posts/evals/)

> *Demo is `works.any()`. Product is `works.all()`.*
> — Andrej Karpathy

You do not have a product until you have an eval. Before that, you have a
demo and an opinion.

---

## Why this is hard

LLM outputs are non-deterministic. Two reasonable-looking outputs may differ
in subtle, important ways. Output that looks correct often is not. Output
that looks broken often works fine. Your aesthetic judgment is unreliable
across a thousand cases.

The whole point of an eval is to replace aesthetic judgment with **a process
that scales**.

---

## The three levels

Hamel Husain's framework is the cleanest in the field. Adopt it.

### Level 1 — Unit tests for LLMs

Assertions. Deterministic. Cheap. Fast.

- "Output is valid JSON."
- "Output contains the user's name."
- "Tool was called with the correct arguments."
- "Refusal text does not appear in a non-refusal scenario."

> *I often run Level 1 evals on every code change.*
> — Husain

These are not cute. They are load-bearing. Modern AI products run hundreds of
them on every PR.

### Level 2 — Model & human eval

Where assertions can't reach: tone, helpfulness, factual accuracy on open
questions, faithfulness to a long source. Run a sample of traces past a model
judge, a human reviewer, or both. Cadence: weekly, or after meaningful prompt
or model changes.

### Level 3 — A/B testing

The final arbiter. Real users, real metrics, real money. Slowest, most
expensive, most truthful. Reserve for changes that survived levels 1 and 2.

> *The cost of Level 3 > Level 2 > Level 1. The cadence is the inverse.*

---

## Look at your data

> *Remove ALL friction from looking at data.*
> — Husain

> *You are doing it wrong if you aren't looking at lots of data.*
> — Husain

Build the dashboards. Build the search. Build the filters. Build the "click
this trace, see the prompt, the tool calls, the response, the timing" link.
The single highest-leverage action in any AI project is reducing the
seconds-from-curiosity-to-trace.

If looking at a real production trace takes more than ten seconds, fix that
before you fix the model.

---

## What to eval

For coding agents, useful first-pass eval suites include:

- **Repo bootstrap:** can the agent set up a fresh repo with the right
  conventions from your `AGENTS.md`?
- **Bug fix:** seed a known bug; does the agent locate, fix, and test it?
- **Feature add:** small, verifiable feature with a hidden hold-out test.
- **Refactor:** rename a concept across N files; does the test suite still pass?
- **Tool use:** can the agent solve the task using *only* the tools provided,
  without falling back to shell?

Each comes with a deterministic verifier (Level 1) or a curated rubric
(Level 2). Versions track over time.

---

## The corollary

The harness is *also* eval-able. When you change the system prompt, the
`AGENTS.md`, the tool roster, run the suite. If the numbers move, you have
learned something. If they don't, you have learned that this change does not
matter.

This is how the harness compounds without bloating: every addition justifies
itself against the suite. Things that don't move the numbers come back out.

---

**Next:** [08 — The loop](08-the-loop.md)
