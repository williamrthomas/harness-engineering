# 02 — Humans steer, agents execute

> *Humans steer. Agents execute.*
> — Ryan Lopopolo, [Harness engineering](https://openai.com/index/harness-engineering/)

Three words. Memorize them.

Everything that goes wrong in an agentic project, eventually traces back to
inverting this sentence. A human starts executing — typing code, running
commands, fighting with formatters — while the agent sits idle. Or an agent
starts steering — choosing what the project should be, what to prioritize,
what counts as done — while the human nods along.

Both are failure modes. Both are common. Both are reversible.

---

## What "steer" means

To steer is to:

- **Specify intent.** State what success looks like, in writing, before work begins.
- **Design the environment.** Choose the repo layout, the tools, the gates, the loop.
- **Set the invariants.** Decide what must be true at every commit. Encode them.
- **Build the feedback.** Wire up the linters, the tests, the eval suite, the dashboards.
- **Review the output.** Read the diff. Watch the trace. Spot-check the data.
- **Approve the irreversible.** Money, deletes, deploys, communications.

To steer is *not* to babysit each tool call. If you find yourself approving
every keystroke, your harness is too weak. Strengthen the gates so you can
approve at the right altitude.

---

## What "execute" means

To execute is to:

- Read the codebase.
- Run the tools.
- Write the code, the tests, the docs, the migrations.
- Compile, lint, typecheck, run.
- Iterate against feedback.
- Report back with a diff, a trace, and a summary.

The agent should be doing the boring, well-specified, verifiable work.
Anything boring, well-specified, and verifiable that *you* are doing is a sign
the harness has not yet absorbed it.

---

## The OpenAI experiment

The Codex team at OpenAI shipped a real product with **0 lines of
manually-written code** for five months. Application logic, tests, CI,
documentation, observability — all written by Codex. The team's job was
"to design environments, specify intent, and build feedback loops that allow
Codex agents to do reliable work" ([Lopopolo](https://openai.com/index/harness-engineering/)).

You do not have to commit to zero hand-written code. But you should know that
it is possible, and you should know what it costs: a real harness.

---

## When to take the wheel

Steer harder, not by writing code yourself, but by tightening the harness:

- The agent keeps making the same mistake → add a guide or a sensor.
- You cannot tell whether output is good → build an eval.
- A change feels risky → add a gate, not a meeting.
- You spent the day reviewing trivial diffs → raise the gate, not the diff count.

If you find yourself reaching for the keyboard to "just fix it real quick,"
ask: *which mistake am I refusing to engineer out?*

---

**Next:** [03 — Context is a scarce resource](03-context-is-scarce.md)
