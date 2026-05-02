# 09 — Memory and amnesia

> *LLMs are a bit like a coworker with anterograde amnesia — they don't
> consolidate or build long-running knowledge or expertise once training is
> over and all they have is short-term memory (context window). It's hard to
> build relationships (see: 50 First Dates) or do work (see: Memento) with
> this condition.*
> — Andrej Karpathy

The agent forgets. Every session starts naked. Every restart, every
compaction, every sub-agent boundary, every long-running task that overflows
the window — *gone*.

This is not a bug to be fixed. It is a property to be designed around.

---

## The harness remembers

The agent forgets. The harness remembers. Memory is engineering, not magic.

There are five places memory lives in a real harness. Use them on purpose.

| Surface | What it stores | Lifetime |
| --- | --- | --- |
| **Project files** (`AGENTS.md`, `CLAUDE.md`, skills) | Conventions, rules, team knowledge. | Until you change them. |
| **The codebase itself** | The "true" state of the system. | Until refactored. |
| **Session scratch** (notes, plans, todo files) | Within-task working state. | This session. |
| **External memory** (DBs, vector stores, files) | Long-running facts the model can retrieve. | As long as you keep it. |
| **The git log + PR history** | Decisions, rationales, prior art. | Forever, if you write it down. |

If you find yourself re-explaining the same thing to the agent across sessions,
one of these surfaces is missing or wrong.

---

## System prompt learning

Karpathy proposes a paradigm we don't have a great name for yet:

> *We're missing (at least one) major paradigm for LLM learning. ... possibly
> it has a name — system prompt learning? Pretraining is for knowledge.
> Finetuning (SL/RL) is for habitual behavior. Both involve a change in
> parameters but a lot of human learning feels more like a change in system
> prompt.*

The practical version: when the agent makes a mistake and you fix it, do not
just fix it. *Write down what you learned* — into the `AGENTS.md`, into a
skill, into a checklist file the agent reads on relevant tasks. The next
session inherits the lesson.

This is the Hashimoto rule with a memory surface attached.

---

## Just-in-time, not just-in-case

The temptation is to load every fact the agent might ever need. Resist it.
Anthropic's guidance is firm:

> *Just-in-time context: Rather than pre-loading all relevant data, agents
> retrieve context dynamically, when it's needed.*
> — [Effective context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

Build retrieval. Trust retrieval. Document where things live. Let the agent
ask.

Pre-loading is comfortable. Just-in-time is correct.

---

## Skills and progressive disclosure

The cleanest mechanism for long-tail knowledge available today is **Agent
Skills** — an open standard ([Anthropic, Dec 2025](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills))
with three levels of progressive disclosure:

1. **Metadata.** A name and description. Pre-loaded into the system prompt.
   Tells the agent *that the skill exists* without spending tokens on its
   contents.
2. **Body.** A `SKILL.md` file. Loaded when the agent decides the skill is
   relevant. Holds the actual instructions.
3. **Bundled files.** Scripts, reference data, templates. Loaded only when
   referenced from the SKILL.md.

The result: a vast library of expertise can sit on disk while only a few
hundred tokens of metadata sit in context. Skills are how you build a
*compounding* knowledge surface without bloating the prompt.

If a piece of knowledge applies sometimes — write a skill. If it applies
always — put it in `AGENTS.md`. If it applies *never*, delete it.

---

## Compaction

Long sessions overflow. The harness must summarize.

A good compaction step:

- Preserves the **task spec** verbatim.
- Preserves the **active plan** verbatim.
- Replaces resolved sub-steps with one-line summaries.
- Drops tool outputs whose content has been incorporated into a summary.
- Keeps recent error messages (the agent is mid-correction).

Bad compaction loses the thread. Good compaction loses the noise.

---

**Next:** [10 — Trust boundaries](10-trust-boundaries.md)
