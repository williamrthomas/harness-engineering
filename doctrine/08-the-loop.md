# 08 — The loop

> *The Ralph Wiggum Loop: do the dumbest thing that could possibly work,
> in a loop, with feedback.*
> — paraphrased from the OpenAI Codex team

The single biggest unlock in agentic engineering is **the loop**. Not the
clever prompt. Not the giant context. The loop.

A loop is: *act → observe → adjust → act again,* automated, with the agent
reading its own feedback. Every successful agentic system is a loop dressed
up with extra steps. Every failure mode is a broken loop.

---

## Why loops compound

A single agent call is a coin flip with weighted odds. Some percentage of
the time it succeeds, the rest it fails. You can either:

- **Improve the coin** (better model, better prompt, better tools).
- **Flip it more** (let the agent retry against feedback).

Improving the coin has diminishing returns. Flipping it more has compounding
returns — *if* the feedback teaches the agent something.

A loop with good sensors does not just retry. It retries in a smaller cone
each time. The error message says what was wrong; the next attempt avoids it;
the loop converges. This is the same machinery as a [Newton iteration](https://en.wikipedia.org/wiki/Newton%27s_method) or a [PID controller](https://en.wikipedia.org/wiki/PID_controller),
just applied to text.

---

## The minimum viable loop

```
while not done:
    plan_or_continue()
    run_tools()
    read_results()
    if hard_invariant_violated: break
    if soft_invariant_violated: emit_correction_message()
    if success_criterion_met: done = True
```

Five lines of pseudocode. Every coding-agent harness — Codex, Claude Code,
Pi, your homemade SDK script — is a variation on this skeleton.

The interesting design decisions are:

- **What counts as success?** (the eval)
- **What signals does the agent see between turns?** (the sensors)
- **When does the loop break for a human?** (the gate)
- **When does the loop break for safety?** (the invariant)

Get those four right and the model becomes almost incidental.

---

## The garbage collection metaphor

A long-running agent loop accumulates cruft: stale plans, abandoned files,
half-complete features, dead code. Treat the loop like a runtime. Add a
garbage collection step.

OpenAI's Codex team makes this explicit: between iterations, the harness
prunes scratch files, summarizes long traces, and resets state that has no
business surviving the next turn. Without GC, the loop slows, then leaks,
then crashes.

Your equivalents:

- Periodic compaction of the conversation.
- A "scratch" directory cleaned on every commit.
- Hard limits on transient files.
- A summarization step after every N tool calls.

---

## The autonomy slider

Karpathy's framing: *every agentic UX has a slider from "model suggests, human
approves every step" to "model runs free, reports back at the end."*
([Karpathy on Software 3.0](https://www.latent.space/p/s3))

The slider's right setting depends on:

- **Reversibility.** Irreversible actions slide left. Local edits slide right.
- **Cost.** Expensive actions slide left.
- **Verifiability.** Things easy to test after the fact slide right.
- **Trust.** Track record raises the slider.

Bad harnesses pin the slider to one position. Good harnesses make the
position task-dependent and visible.

---

## Stop heroics

The temptation, when a loop fails, is to write a heroic prompt that
single-shots the answer. Resist it. Heroics do not compound. Loops do.

If a loop fails:

- Find the missing sensor.
- Find the missing guide.
- Find the missing tool.
- Find the bad invariant.
- Then run the loop again.

Each fix makes every future loop stronger. The heroic prompt makes one task
work, once.

---

**Next:** [09 — Memory and amnesia](09-memory-and-amnesia.md)
