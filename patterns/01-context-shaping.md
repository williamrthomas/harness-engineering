# 01 — Context shaping

> *Find the smallest set of high-signal tokens that maximize the likelihood
> of the desired outcome.*
> — Anthropic

Context shaping is the art of putting the right tokens into the window in
the right order. Not the most. The right.

---

## The hierarchy

Context lives in layers. From most permanent to most transient:

1. **System prompt** — the agent's identity. Rarely changes. Must be
   ruthlessly short.
2. **Project files** (`AGENTS.md`, `CLAUDE.md`) — the project's identity.
   Changes per project. Must earn every line.
3. **Skills metadata** — the menu of optional knowledge. Pre-loaded, but
   small per skill.
4. **Active skill bodies** — loaded on demand for relevant tasks.
5. **Tool descriptions** — the contract surface. Few, namespaced, examples.
6. **Retrieved files / search results** — fetched just-in-time.
7. **Working scratch** — plans, notes, todos. Compacted aggressively.
8. **Conversation history** — the running session.

Each layer is paid for on every turn (1–5) or on the turn it lives in (6–8).
A token in layer 1 is the most expensive token in your system.

---

## The four moves

### Prune

Walk the system prompt and the project files weekly. For each line ask the
Claude Code question:

> *Would removing this cause the agent to make mistakes?*

If no, cut. Half of an `AGENTS.md` file in the wild is content the model
can derive from the codebase, the package manager, or its training.

### Compress

Replace prose with structure. Replace structure with examples. Replace
examples with the smallest example that still teaches.

> "All public functions in this codebase use TypeScript explicit return
> types, and we want this to be enforced consistently across all files in
> the src directory and below."

becomes

> "All public functions: explicit return types. (Enforced by tsconfig.)"

You lost no information. You gained nine lines back.

### Retrieve just-in-time

Don't pre-load. Trust the agent to ask. Provide:

- A grep tool.
- A file-read tool.
- A search tool over docs.
- A clear hint in the project file: *"For X, look at Y."*

Pre-loading "everything the agent might need" is the most common form of
bloat. The agent only needs 2% of it on any given turn.

### Externalize

Push state out of the window. Common targets:

- **Files.** A `PLAN.md` the agent reads, edits, and re-reads. Compacts
  trivially.
- **Sub-agents.** A child agent does heavy reading; returns one paragraph.
- **A vector store.** Long-tail facts retrieved by similarity.
- **A database.** Structured project memory. Queryable.

The window is for thinking. Everything else lives elsewhere.

---

## The diagnostics

Three signals that context is mis-shaped:

1. **The agent ignores a rule you wrote.** The file is too long; the rule
   got buried. Cut the file or move the rule earlier.
2. **The agent invents conventions.** A guide is missing. Write it once,
   short, in the project file or a skill.
3. **The agent re-derives the same fact every session.** A memory surface
   is missing. Add it to `AGENTS.md`, a skill, or external memory.

Treat each signal as a ticket, not a complaint.

---

## The compaction discipline

Long sessions overflow. When they do, compact deliberately:

- Preserve the original task and the active plan **verbatim**.
- Replace each completed subtask with one line.
- Drop tool outputs whose information has been summarized.
- Keep the most recent error messages — the agent is mid-correction.

Bad compaction loses the thread. Good compaction loses only the noise.
