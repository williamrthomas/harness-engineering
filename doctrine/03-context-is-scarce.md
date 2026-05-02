# 03 — Context is a scarce resource

> *Context is a scarce resource.*
> — Anthropic, [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

> *Find the smallest set of high-signal tokens that maximize the likelihood
> of the desired outcome.*
> — Anthropic, ibid.

The window is finite. Attention inside the window is finite. Quality degrades
long before the limit. Treat tokens like money.

---

## The two failure modes

**Starvation.** The model lacks the file, the type, the constraint, the
example, the convention. It hallucinates a plausible answer, or asks. Cost:
a wrong answer or a wasted round trip.

**Bloat.** The model has the entire repo, ten thousand lines of logs, six
old chat turns, and two contradictory specs. It loses the thread, ignores
your instruction, picks up a stale convention. Cost: a wrong answer that
*looks* informed.

Bloat is more common, more expensive, and harder to detect. Good harnesses
are biased toward starvation, with strong recovery — *just-in-time* retrieval
when the agent needs more.

---

## The four levers

Every context decision is one of these four levers:

1. **Prune.** Remove what is not pulling weight. Old turns, generic
   boilerplate, stale rules, copy-pasted documentation.
2. **Compress.** Replace ten lines with one. Replace a transcript with a
   summary. Use [compaction](../patterns/04-memory-and-compaction.md) when a
   session grows long.
3. **Retrieve just-in-time.** Don't pre-load. Let the agent ask. Tools that
   read files, search, and grep beat pre-stuffed context for any non-trivial
   project.
4. **Externalize.** Push state out of the window into the file system,
   structured notes, or sub-agents that report back distilled findings.

Anthropic's effective-context piece collapses these into one slogan worth
memorizing: *find the smallest set of high-signal tokens.*

---

## The map, not the manual

OpenAI's framing for Codex is identical in spirit:

> *Give Codex a map, not a 1,000-page instruction manual.*
> — [Lopopolo, OpenAI](https://openai.com/index/harness-engineering/)

A map is small. A map points. A map says "the database lives over here, the
auth lives over there, here is how they talk." A manual lists every API.

Maps survive refactors. Manuals don't. Maps respect the budget. Manuals don't.

---

## Token economics, in practice

The agent has roughly three context buckets:

| Bucket | What lives there | Default policy |
| --- | --- | --- |
| **System / project** | `AGENTS.md`, `CLAUDE.md`, role prompts, broad rules. | Short. Re-read every session. Prune ruthlessly. |
| **Tools** | Tool descriptions and schemas. | Few, namespaced, well-named. |
| **Working** | The current conversation, retrieved files, scratch notes. | Lazy. Pull only when needed. Compact when full. |

The system bucket is *non-renewable*. Every line you add to `AGENTS.md` or
`CLAUDE.md` is paid for on every turn, forever. Spend it on rules that
genuinely change behavior. Cut everything else.

---

## The diagnostic

When the agent ignores a rule you wrote, the rule is not too weak. The file
is too long.

> *If Claude keeps doing something you don't want despite having a rule
> against it, the file is probably too long and the rule is getting lost.*
> — [Best Practices for Claude Code](https://www.anthropic.com/engineering/claude-code-best-practices)

Cut the file. Move the rule earlier. Add emphasis. Then test.

---

**Next:** [04 — Guides and sensors](04-guides-and-sensors.md)
