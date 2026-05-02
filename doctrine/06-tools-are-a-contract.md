# 06 — Tools are a contract

> *Tools are a new kind of software which reflects a contract between
> deterministic systems and non-deterministic agents.*
> — Anthropic, [Writing effective tools for AI agents](https://www.anthropic.com/engineering/writing-tools-for-agents)

A function is a contract between two pieces of code. A tool is a contract
between code and a *probabilistic caller* who has read your description, may
or may not have understood it, and is allowed to be wrong.

Design accordingly.

---

## The five principles

Anthropic's tool-design rules are the closest thing the field has to a settled
canon. Memorize them.

1. **Choose the right tools to implement.** Few, high-leverage tools beat
   many shallow ones. A tool that wraps a single API call is usually a smell;
   a tool that completes a *task* is usually correct.
2. **Namespace your tools.** `gh.create_issue`, `gh.list_prs`, `db.query`.
   The model is smarter when names group naturally.
3. **Return meaningful context.** A tool that returns `{success: true}` is
   wasted tokens. Return what the agent will plausibly need next: ids, links,
   the relevant snippet, the next step.
4. **Optimize for token efficiency.** Truncate intelligently. Paginate.
   Summarize. Hide the haystack; expose the needles.
5. **Prompt-engineer the tool description.** The description is a prompt that
   runs every time the tool appears. Make it short, specific, with one
   example.

---

## The tool description is a prompt

This is the principle most teams miss. The tool description is not
documentation for humans. It is **a system prompt fragment that the model
reads to decide whether and how to call the tool**.

Bad:

> *get_user: Gets a user.*

Better:

> *get_user(user_id: string) → User. Returns the user record by id. Use this
> when you have a numeric id. For email lookups, use `find_user_by_email`.*

The good description does three things: it tells the agent **when** to use
the tool, **what it gets back**, and **what to use instead** when the
situation differs. That last sentence is a guide and a sensor in one.

---

## Few > many

Every tool you add steals attention from every other tool. Past roughly a
dozen tools, the model starts confusing them, calling the wrong one, or
hallucinating arguments. Reasons to be ruthless:

- Tools that wrap single API calls usually belong inside another tool.
- Read-only and write tools should not share names.
- "Convenience" tools that combine common patterns are usually right; "all
  the endpoints of our API as tools" is usually wrong.

When in doubt, **ship fewer tools that do more**. The model is better at
reasoning over a small, well-named, task-shaped surface than over a large,
flat, API-shaped one.

---

## Token discipline at the boundary

Tools that return paginated, ranked, or trimmed output are kinder to the
context window than tools that return the truth. The agent does not need
the entire log. It needs the relevant lines, with timestamps, and a hint
about how to ask for more.

A useful pattern:

```
{
  "results": [...top 20 sorted by relevance...],
  "total": 14823,
  "next_cursor": "...",
  "hint": "Pass cursor to retrieve more, or call with filter='error' to narrow."
}
```

The `hint` field is the tool teaching the agent how to use itself.

---

## The error message is a tool

When a tool fails, the error string *is* the tool's output. Treat it as
output. Bad errors waste a turn. Good errors teach.

Bad:

> *Error: invalid input.*

Good:

> *Error: `user_id` must be a positive integer; got `"42"`. Try
> `get_user(42)` without the quotes, or use `find_user_by_email("...")` if
> you only have an email.*

This is the same principle as Böckeler's *sensors*: signals optimized for
LLM consumption ([Böckeler](https://martinfowler.com/articles/harness-engineering.html)).

---

## Test your tools with agents

Anthropic's piece is explicit:

> *Collaborate with agents like Claude Code to automatically increase the
> performance of your tools.*

You write the tool. The agent uses it on a real task. You watch where it
struggles. You fix the description, the schema, or the return value. You
loop. The tool gets better at being used by the kind of caller it actually has.

Do not skip this loop. A tool that looks clean to a human can be a maze to a
model.

---

**Next:** [07 — The eval is the product](07-the-eval-is-the-product.md)
