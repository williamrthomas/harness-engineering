# 04 — Guides and sensors

> *Guides anticipate the agent's behaviour and steer it before it acts.
> Sensors observe after the agent acts and help it self-correct.*
> — Birgitta Böckeler, [Harness engineering for coding agent users](https://martinfowler.com/articles/harness-engineering.html)

The harness has two halves. Most teams build one and wonder why it isn't enough.

---

## Guides (feedforward)

A guide acts *before* the agent acts. It increases the probability of a good
result on the first attempt. Examples:

- A short `AGENTS.md` that names the package manager and forbids `any`.
- A repo skeleton that puts tests next to source, so the agent puts new
  tests in the right place by default.
- A tool that *only* accepts validated inputs, so the agent cannot pass garbage.
- A code template that handles the boring 80% so the agent only writes the
  novel 20%.
- A prompt example that shows, not tells, what good output looks like.

Guides are cheap to add and silent when they work. They reduce the *rate* of
mistakes but never go to zero.

## Sensors (feedback)

A sensor acts *after* the agent acts. It reports back. Examples:

- A linter that says "this file uses `var`, prefer `const`."
- A typechecker that says "no overload matches this call."
- A unit test that says "expected 200, got 500, here is the body."
- A custom error message that says, in plain English, *what to do next*.
- A health check that says "the service started but `/healthz` returned 503."

Sensors are how the agent learns inside a session. The signal must be **for
the agent**, not for a tired human at 11pm. Optimize error text for LLM
consumption: name the file, name the line, name the rule, name the fix.

---

## Why both

Guides without sensors: the agent obeys until it doesn't. The first deviation
goes uncaught. Subtle drift accumulates.

Sensors without guides: the agent thrashes. Every mistake is a round trip.
Token cost explodes. Wall-clock time explodes.

Together they form a **steering loop**: the guide narrows the action space,
the sensor catches the deviation, the agent retries inside a smaller cone.
This is the [cybernetic governor](https://en.wikipedia.org/wiki/Centrifugal_governor)
applied to code generation.

---

## Computational vs inferential

A second axis, also from Böckeler:

- **Computational controls** are deterministic. Linters, typecheckers, tests,
  schema validators. Same input → same verdict.
- **Inferential controls** are probabilistic. LLM-as-judge, model-graded
  evals, classifier-based safety filters. Same input → mostly the same verdict.

Prefer computational where possible. They are cheaper, faster, and they don't
hallucinate. Use inferential controls where the criterion is itself fuzzy
("is this docstring helpful?", "does this UI feel modern?"). Never use
inferential where computational will do.

---

## Where they live

The same control can live at different times. Pick the earliest one that
catches the mistake reliably.

| Stage | Examples | Cost of catching here |
| --- | --- | --- |
| **Type / schema** | TypeScript, Pydantic, JSON Schema, Protobuf. | Almost free. |
| **Config** | Linters, formatters, pre-commit hooks. | Cheap. |
| **Repo** | Conventions, file layout, templates, `AGENTS.md`. | Cheap, with care. |
| **Service** | Unit tests, contract tests. | Moderate. |
| **Runtime** | Integration tests, eval suite, smoke tests. | Higher. |
| **UI / human** | Code review, manual QA. | Most expensive. |

OpenAI lists this as the **layer rule**: *Types → Config → Repo → Service →
Runtime → UI* ([Lopopolo](https://openai.com/index/harness-engineering/)).
Push every control as far left as it will go.

---

## The reflex

Whenever the agent makes a mistake, ask:

1. Could a guide have prevented it? (Add it.)
2. If the agent makes this mistake again, what sensor will catch it cheaply?
   (Build it.)
3. At what layer should this live? (Push it left.)

Three questions. Every time. This is the harness compounding.

---

**Next:** [05 — Invariants over instructions](05-invariants-over-instructions.md)
