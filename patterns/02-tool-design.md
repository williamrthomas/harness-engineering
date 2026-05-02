# 02 — Tool design

> *Tools are a new kind of software which reflects a contract between
> deterministic systems and non-deterministic agents.*
> — Anthropic, [Writing effective tools for AI agents](https://www.anthropic.com/engineering/writing-tools-for-agents)

Tool design is where the harness wins or loses most of its time. Bad tools
make every loop slower and every error more confusing. Good tools make the
agent feel three IQ points smarter than the model alone.

---

## The five rules (verbatim worth memorizing)

1. **Choose the right tools to implement.**
2. **Namespace tools to define clear boundaries.**
3. **Return meaningful context.**
4. **Optimize for token efficiency.**
5. **Prompt-engineer the description.**

Every tool you ship should pass all five.

---

## Task-shaped, not API-shaped

The most common tool-design mistake: shipping one tool per API endpoint.

If your service has 40 REST endpoints, you should not ship 40 tools. You
should ship 5–10 tools that each accomplish a *task* — possibly using
multiple endpoints internally. The model is much better at choosing among 8
verbs than 40.

| API-shaped (avoid) | Task-shaped (prefer) |
| --- | --- |
| `users.list`, `users.get`, `users.search`, `users.update`, `users.delete`, `users.activate`, `users.deactivate` | `find_user(query)`, `update_user(id, changes)`, `set_user_status(id, status)` |
| `db.query` (raw SQL) | `read_user_orders(user_id)`, `read_recent_signups(days)` |

The wrong axis cuts the surface by *resource*. The right axis cuts it by
*intent*.

---

## Namespacing

When two tools share a noun, prefix.

```
gh.list_prs
gh.create_issue
gh.merge_pr

db.read_user
db.write_user

fs.read_file
fs.write_file
fs.search
```

The namespace is a hint to the agent about *which world it is acting in*.
Cross-namespace mistakes (writing to `db` when you meant `fs`) become
unlikely.

---

## Return meaningful context

A tool that returns `{success: true}` has wasted the round trip.

A useful return:

- The thing the agent asked for.
- Identifiers it will plausibly use next (`user_id`, `pr_number`, `cursor`).
- A small hint about what to do with it (`"To list comments, call ..."`).
- A truncation marker if you trimmed (`"Showing 20 of 1483; pass cursor=..."`).

Tool returns are *the agent's eyes*. Make them informative.

---

## Token efficiency at the boundary

The agent's context is your tool's downstream cost. Therefore:

- **Truncate intelligently.** Top N by relevance, not first N by accident.
- **Paginate.** Always. Hide the haystack; expose the needles.
- **Summarize.** A 500-line log file → a 20-line summary + a "show full log"
  hint.
- **Compress structure.** Remove fields the agent does not use.

If your tool returns more than a few hundred tokens by default, you are
probably wrong.

---

## The description is a prompt

The description runs every time the tool appears in the system prompt. It
is, functionally, a tiny prompt that ships with the tool.

A good description:

- States *when to use* the tool, in one sentence.
- States *what it returns*, structurally.
- States *what to use instead* in adjacent situations.
- Includes one canonical example call.

A bad description is a single sentence copied from a docstring.

```yaml
# Bad
get_user:
  description: Returns a user.

# Good
find_user:
  description: |
    Find a single user by id, email, or username. Use this whenever you have
    any of those three. For ambiguous queries (partial name, full-text
    search), prefer search_users. Returns {id, email, username, status,
    last_seen, plan} or null.
  example: find_user(query="alice@example.com")
```

---

## Errors are output

A tool that fails returns an error string. That string *is* the next prompt.

Bad:

```
Error: invalid input
```

Good:

```
Error: `user_id` must be a positive integer; got "42" (string).
Try: find_user(42)  -- without quotes
Or:  find_user(query="42@example.com")  -- if you have the email
```

Anthropic calls this *signals optimized for LLM consumption*. Every error
message is an opportunity to teach the agent something it can use *this turn*.

---

## Test tools with agents

The fastest way to find a bad tool is to give it to an agent with a real
task and watch.

The agent's struggles tell you everything:

- It calls the wrong tool → name or description is misleading.
- It passes wrong arguments → schema is unclear or types are sloppy.
- It calls the tool 10 times to do one thing → tool is too granular.
- It can't find the tool when it should → description doesn't match the
  natural phrasing of the task.
- It looks at the output, then re-calls → return value is missing context.

Loop this. Each pass makes the tool stronger for every future caller.
