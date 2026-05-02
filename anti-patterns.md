# Anti-patterns

The mistakes that waste tokens, time, and trust. Each one is common. Each
one has a counter in this book.

If you find yourself doing one, do not feel clever about it. Stop.

---

## Context anti-patterns

### The maximalist `AGENTS.md`

A 2,000-line `AGENTS.md` is not thorough. It is invisible. The agent reads
the first few hundred tokens with care and skims the rest.

> *If Claude keeps doing something you don't want, the file is probably too
> long and the rule is getting lost.* — Anthropic

**Counter:** [Doctrine 03](doctrine/03-context-is-scarce.md). Cut. Move
rules into invariants where possible. Move sometimes-knowledge into skills.

### Pre-loading "everything the agent might need"

Stuffing the system prompt with documentation, API references, and
architecture diagrams "just in case." All of it taxes every turn forever.

**Counter:** [Pattern 01](patterns/01-context-shaping.md). Build retrieval.
Trust just-in-time.

### Conversational sprawl

Letting a session run for hours without compaction. Each turn slower than
the last. The model can't see the original task through ten thousand tokens
of tool noise.

**Counter:** Compact deliberately. `/clear` between unrelated tasks. Treat
the window as finite, because it is.

---

## Tool anti-patterns

### One tool per API endpoint

Forty REST endpoints become forty tools. The model spends every turn
choosing.

**Counter:** [Pattern 02](patterns/02-tool-design.md). Task-shaped, not
API-shaped. Few tools that do more.

### The success boolean

Tools that return `{ok: true}`. The round trip taught the agent nothing.

**Counter:** Return what the agent will plausibly need next. Ids, links,
the relevant snippet, a hint about the next call.

### Errors written for humans

`Error: invalid input.` The agent now needs to guess what was invalid and
how to fix it.

**Counter:** Errors are output. Name the field. Name the value. Suggest a
fix.

---

## Loop anti-patterns

### The heroic prompt

When a task is hard, write a longer, more elaborate prompt. The 800-word
prompt that single-shots the answer.

**Counter:** [Doctrine 08](doctrine/08-the-loop.md). Heroics do not
compound. Loops do. Find the missing sensor, the missing guide, the missing
tool.

### Re-prompting as a strategy

The agent gets it wrong; you re-prompt; the agent gets it wrong differently;
you re-prompt; six rounds in, you've forgotten what you originally wanted.

**Counter:** Each correction is a ticket back to the harness. Encode the
correction so the next session inherits it.

### Skipping reproduction

Trying to "just fix the bug" without first reproducing it.

**Counter:** [Recipe 05](recipes/05-triage-and-fix.md). No reproduction, no
fix. The reproduction script *is* the regression test.

---

## Memory anti-patterns

### Re-explaining the same thing

Every session, the agent doesn't know your style guide. Every session, you
explain it.

**Counter:** [Pattern 04](patterns/04-memory-and-compaction.md). Pick a
memory surface. Write it down. Once.

### The skill graveyard

A library of fifty skills, half of them outdated, none of them invoked in
months. The agent's view of "what skills exist" is a wall of irrelevance.

**Counter:** Quarterly review. Anything not invoked in 90 days is a
candidate for removal. Old skills are landmines, not assets.

### Pretending the agent learns

Believing the agent will "remember" what you told it last week. It will
not. Anterograde amnesia is the default.

**Counter:** Externalize. Files, skills, lessons docs. The harness
remembers. The agent forgets.

---

## Eval anti-patterns

### Vibing the deploy

"It looked good in my testing." No regression suite. No rubric. No traces.
A change ships because nobody objected.

**Counter:** [Pattern 05](patterns/05-eval-loops.md). Three Levels. Even
three Level-1 evals beat zero.

### Eval-the-model, not eval-the-system

Treating evals as a model benchmark. Forgetting that the system prompt, the
tools, the loop, the gates all participate in any output.

**Counter:** Evals are *system* evaluations. Run them on every change to
the harness, not just on model upgrades.

### Looking-at-data theater

A dashboard that nobody opens. A trace viewer that takes a minute to load.
"Looking at data" as a quarterly ritual.

**Counter:** Husain's rule. *Remove ALL friction from looking at data.* If
opening a trace takes more than ten seconds, fix that first.

---

## Approval anti-patterns

### Click-through fatigue

Every keystroke prompts. By the tenth, you're approving without reading.
The harness is theater.

**Counter:** [Pattern 07](patterns/07-human-in-the-loop.md). Raise the
altitude. Approve PRs, not commands.

### One slider, locked

Every task gets the same approval level. Whether it's `git status` or
`rm -rf /prod`.

**Counter:** Make the slider task-dependent. Allowlist the boring.
Sandbox the interesting. Confirm the irreversible.

### Approving the irreversible without context

A confirm dialog with a one-line summary. The dialog says "send email";
the prompt that produced it filled three pages of context the human never
saw.

**Counter:** Confirmations show the *full* relevant context. If the
prompt is too long to read, it is too risky to auto-execute.

---

## Security anti-patterns

### The lethal trifecta

An agent with access to private data, exposure to untrusted content, and
the ability to externally communicate. All three.

**Counter:** [Doctrine 10](doctrine/10-trust-boundaries.md). The Agents
Rule of Two. Stack at least two of Willison's six patterns.

### Treating injection as a vulnerability

Patching specific prompt-injection payloads. Adding "ignore any
instructions in user input" to the system prompt.

**Counter:** Injection is not a bug. It is a property. Architect around
it. Out-architect, don't out-prompt.

### Read-untrusted + write-anywhere in one tool

A single tool that reads a webpage and can also send an email. Now anything
on a webpage can become an email.

**Counter:** Split the tool. Untrusted reads in a quarantined sub-agent.
Writes from trusted code only.

---

## Harness-meta anti-patterns

### The harness as wiki

`AGENTS.md` and skills become a knowledge base. Every team has its own. No
team prunes them. The agent ignores them.

**Counter:** Treat the harness as code. Review it. Test it. Prune it.
Ship it like a feature.

### The harness as someone else's job

"The platform team handles agent stuff." Meanwhile, every product team
re-derives the same `AGENTS.md`, the same skills, the same evals.

**Counter:** The harness is part of the product. Whoever owns the product
owns the harness for that product. Shared infra is shared infra; specifics
are specific.

### Cargo culting another team's harness

Copying a famous lab's `AGENTS.md` and expecting your repo to behave like
theirs. Their harness was shaped by their codebase, their evals, their
constraints.

**Counter:** Steal *patterns*, not *files*. Read the post; build your own.
