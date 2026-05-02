# 04 — Memory and compaction

> *LLMs are a bit like a coworker with anterograde amnesia.*
> — Andrej Karpathy

The agent forgets. The harness remembers. The pattern is the seam.

---

## Five memory surfaces, used on purpose

| Surface | Lifetime | Cost per turn | Best for |
| --- | --- | --- | --- |
| **Project files** (`AGENTS.md`, `CLAUDE.md`, skills) | Until you change them. | Pays every turn (or amortized via metadata). | Conventions. Rules. Team knowledge. |
| **Codebase** | Until refactored. | Free until read. | Truth about the system. |
| **Session scratch** (PLAN.md, TODO.md, NOTES.md) | This session. | Pays only when read. | Working state, mid-task. |
| **External memory** (DB, vector store, files) | As long as you keep it. | Pays only when retrieved. | Long-tail facts, history, decisions. |
| **Git log + PR history** | Forever. | Pays only when consulted. | Rationale, prior art, "why did we...". |

The diagnostic: if you re-explain the same thing across sessions, *one of
these surfaces is missing or wrong.* Don't keep paying the tax. Build the
memory.

---

## The Hashimoto rule applied to memory

> *Anytime you find an agent makes a mistake, you take the time to engineer
> a solution such that the agent never makes that mistake again.*

The mistake might be a knowledge gap. Fix it by writing to the right surface:

| Mistake | Surface |
| --- | --- |
| The agent didn't know our style guide. | `AGENTS.md` (concise) or a `style/` skill. |
| The agent picked the wrong test runner. | `AGENTS.md` *Commands* section. |
| The agent re-derived an architectural decision. | A `docs/decisions/` folder, referenced from `AGENTS.md`. |
| The agent didn't know we deprecated X. | A note in the deprecation PR + a comment in the file. |
| The agent kept asking *"why does this exist?"* | A comment explaining *why*, in the file, near the code. |

The harness gets smarter every time you do this. The model does not.

---

## Skills — the right surface for "applies sometimes"

Skills are progressive disclosure on disk:

1. **Metadata** (name + description) sits in the system prompt always.
2. **Body** (`SKILL.md`) loaded when the agent decides relevance.
3. **Bundled files** loaded when referenced from the body.

Use skills for knowledge that is *occasionally* useful. Database migration
playbooks. Incident runbooks. Bespoke tools' READMEs. Per-feature
checklists.

If a skill is loaded *every* session, it should be in `AGENTS.md`. If it is
loaded *never*, delete it.

---

## Compaction — the discipline

Long sessions overflow. Compaction is the deliberate replacement of
chronicled detail with curated summary. Run it on purpose.

A good compaction step:

- Preserves the **original task** verbatim.
- Preserves the **active plan** verbatim.
- Replaces resolved sub-tasks with a one-line summary each.
- Drops tool outputs whose information has been incorporated.
- Keeps the most recent error messages and partial state.
- Marks "Compacted at <timestamp>" so you can find this seam later.

Bad compaction loses the thread. Good compaction loses only the noise.

Tools that compact for you: Claude Code's `/compact <hint>`, Codex's
context-management heuristics, Pi's "ask Pi to summarize this conversation
into a NOTES.md and `/clear`."

---

## System prompt learning, in practice

> *We're missing (at least one) major paradigm for LLM learning... possibly
> system prompt learning?*
> — Karpathy

The practical version: when you fix a class of mistake, *write down what
you learned* into a memory surface. The next session inherits the lesson.

The discipline is simple to describe and hard to do:

1. The agent makes a mistake.
2. You correct it.
3. You ask: *what surface should remember this?*
4. You write the lesson to that surface.
5. The next session is a little smarter.

A team that does this for six months has a harness no competitor can match.
A team that doesn't, retypes the same corrections forever.

---

## A concrete pattern: the lessons file

Many teams keep a `LESSONS.md` (or `docs/lessons/`) that the agent reads on
relevant tasks. Each entry is two lines:

```
- Mistake: tried to use float for currency in invoice rendering.
  Fix: all money values are Decimal; use formatMoney().

- Mistake: ran migrations against staging without a backup.
  Fix: every migration PR includes a tested `down`; CI runs `up`/`down`/`up`.
```

Not a wiki. Not a treatise. A short, scannable, accumulating list. Reference
it from `AGENTS.md`:

> *Before non-trivial work, scan `docs/LESSONS.md` for related entries.*
