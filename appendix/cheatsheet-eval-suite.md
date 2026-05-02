# Cheatsheet — Eval suite

Three levels. Different costs. Different cadences. All necessary.

---

## The pyramid

| Level | What | Cost | Cadence | Verdict by |
| --- | --- | --- | --- | --- |
| **1** | Assertions on output. | $ | Every change. | Code. |
| **2** | Rubric scoring on samples. | $$ | Weekly / on prompt change. | Human or LLM judge. |
| **3** | A/B test on real users. | $$$ | After Level 1 + 2 pass. | Real metrics. |

Cost rises with cadence-inverse. Cheap things run always. Expensive things
run rarely.

---

## Level 1 — Assertions

Examples for any agent:

- Output is valid JSON / matches schema.
- Output contains expected token / value.
- Tool was called exactly once. Or: at least once. Or: not at all.
- Tool was called with arguments matching a pattern.
- The agent did not call a forbidden tool.
- Refusal text appeared / did not appear.
- Latency was under a budget.
- Token usage was under a budget.

Build hundreds of these. They are cheap. They catch the dumb stuff.

---

## Level 2 — Rubric

When assertions can't reach: tone, helpfulness, factual accuracy,
faithfulness to a source.

Rubric template:

```
Task: <what the agent was asked to do>
Reference: <expected behavior, links, hold-out facts>

Score each (0/1):
- Did the agent address the actual question?
- Was the response factually consistent with the reference?
- Was the tone appropriate?
- Did the agent ask appropriate clarifying questions?
- Did the agent stay within scope?
- Were tool calls justified?

Comments: <anything that doesn't fit>
```

Run weekly on a sample of 30–100 traces. Track the trend.

**LLM judges** are cheap and partly true. **Human judges** are expensive
and true. Use both; sample the LLM judge against humans monthly to detect
drift.

---

## Level 3 — A/B

The final arbiter. Real users, real metrics, real money.

Reserve for:

- Model upgrades.
- Major prompt rewrites.
- Tool roster overhauls.
- New sub-agent architectures.
- Anything Level 1 + 2 cannot judge.

Don't run A/B tests on tweaks. Most changes do not deserve Level 3, and
that is correct.

---

## The trace viewer (the real eye)

Build, do not buy something inadequate.

**Required:**

- One-click from any failure → full trace.
- Trace shows: prompt, tool calls + arguments, tool results, model
  responses, timings, token counts.
- Search by user, time, prompt content, tool used, error type.
- Diff two traces side-by-side.
- Replay (re-run with the same inputs).

**Latency budget:** under ten seconds from "hmm" to "I see the prompt." If
slower, fix the viewer first.

> *Remove ALL friction from looking at data.*
> — Hamel Husain

---

## What "done" looks like

- Level 1 runs on every PR. Failures block merge.
- Level 2 runs on every prompt or model change. Trends are visible.
- Level 3 runs on the changes that earn it.
- Looking at a trace takes under ten seconds.
- The team rolls back regressions and forward improvements without
  arguing.
