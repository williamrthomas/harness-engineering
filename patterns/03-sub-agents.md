# 03 — Sub-agents

> *Sub-agent architectures: a coordinator dispatches, sub-agents do the
> reading, the coordinator aggregates.*

A sub-agent is a child agent invocation that runs in its own context window
and returns a distilled result. Two reasons to use one. Both are load-bearing.

---

## Reason 1: protect context

The parent agent has a long, expensive task. Reading 50 files would blow the
window. So the parent spawns a child:

> *Search the repo for all places that touch the auth token. Read them.
> Return a 1-paragraph summary of how tokens flow through the system.*

The child uses 30,000 tokens. Returns 200. The parent's window is
preserved for *thinking about* what the child found.

This is the most common, lowest-risk use of sub-agents. Whenever you find
the parent agent about to read a lot, ask: *can a child do this and report?*

---

## Reason 2: isolate trust

The parent agent must process untrusted content — a webpage, a PDF, an
email, an arbitrary user comment. Letting that content into the parent's
context is the [lethal trifecta](../doctrine/10-trust-boundaries.md) waiting
to happen.

Solution: a quarantined sub-agent reads the untrusted content and returns
*structured, validated* output. The parent never sees the raw text.

This is the **Map-Reduce** and **Dual LLM** patterns from
[Simon Willison's catalog](https://simonwillison.net/2025/Jun/13/prompt-injection-design-patterns/).
A coordinator dispatches; sub-agents handle untrusted content; results are
schema-validated before they touch the trusted side.

---

## Anatomy of a good sub-agent call

Every sub-agent invocation should specify:

1. **A specific objective.** "Find X and return Y." Not "go figure stuff out."
2. **Output shape.** Schema, length, format. Constrain the surface.
3. **Tool budget.** What can the child call? Usually less than the parent.
4. **Context.** Pass *only* what the child needs. The parent's window is not
   a free buffet.
5. **A timeout / step budget.** Children loop too. Bound them.

Without these, sub-agents drift, return prose-soup, or recursively spawn
their own children until the system grinds.

---

## Anti-patterns

**Sub-agents as ego.** Don't spawn a child just to feel like a manager. If
the parent could do the work in 500 tokens, the child is overhead.

**Recursive descent.** A child that spawns grandchildren that spawn
great-grandchildren. Each layer is a context boundary, a trust boundary, and
a step budget. Two levels is usually plenty.

**Children seeing parent secrets.** Pass the *task*, not the *whole context*.
Parent context contains keys, tokens, and prior decisions the child does not
need.

**No output schema.** A child returning free-form prose is a parent reading
a child's diary. Constrain the output.

---

## When *not* to use sub-agents

- The parent's window has plenty of room.
- The work is short and benefits from the parent's existing context.
- Latency matters and the round-trip cost is dominant.
- The child would only call one tool. Just call the tool.

Sub-agents are a hammer for two specific nails: *context preservation* and
*trust isolation*. Use them for those, not as a vibes-based architecture.

---

## A note on parallelism

When you have multiple independent reads — "summarize each of these 10 files"
— parallelize the children. Most harnesses (Codex, Claude Code, Pi) support
fan-out. The wall-clock saving is large; the context saving is the same as
sequential.

When the children's outputs feed each other, you've moved from fan-out to a
chain. Chains compound errors. Eval them harder.
