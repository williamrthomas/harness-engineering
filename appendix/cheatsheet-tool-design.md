# Cheatsheet — Tool design

The five rules and a checklist for shipping good tools.

---

## The five rules (verbatim worth memorizing)

1. **Choose the right tools to implement** (and not to implement).
2. **Namespace tools** to define clear boundaries.
3. **Return meaningful context** from tools back to agents.
4. **Optimize tool responses for token efficiency.**
5. **Prompt-engineer tool descriptions and specs.**

— [Anthropic, Writing effective tools for AI agents](https://www.anthropic.com/engineering/writing-tools-for-agents)

---

## The pre-ship checklist

Before adding a tool to the harness, every box must be true.

### Necessity

- [ ] Does this tool solve a *task* the agent attempts often?
- [ ] Could the agent solve the task with existing tools? If yes, don't add.
- [ ] Is this tool's job different in *kind* from the next-closest tool?

### Naming

- [ ] Is the name a verb-shaped, task-shaped phrase (e.g. `find_user`,
      not `users.list`)?
- [ ] Does it sit in a namespace with related tools (`gh.*`, `db.*`,
      `fs.*`)?
- [ ] Is it impossible to confuse with a sibling at a glance?

### Inputs

- [ ] Are arguments typed? (Strings, ints, dates — never "any".)
- [ ] Are required vs optional fields obvious?
- [ ] Are defaults sensible?
- [ ] Will the agent fail loudly on bad input, not silently?

### Outputs

- [ ] Does the tool return what the agent will plausibly need *next*
      (ids, links, snippets, hints)?
- [ ] Is output token-efficient (truncated, paginated, summarized when
      large)?
- [ ] Does output indicate when it has been truncated, with a way to
      retrieve more?
- [ ] Is the schema stable across calls (same shape, same field names)?

### Errors

- [ ] Do error messages name the field, the value, and the fix?
- [ ] Do errors point to the right adjacent tool when the agent picked
      wrong?
- [ ] Are errors short enough that the agent does not waste context
      reading them?

### Description

- [ ] Does the description state *when to use* the tool?
- [ ] Does it state *what to use instead* in adjacent situations?
- [ ] Does it include one canonical example call?
- [ ] Is it short enough to scan?

### Verification

- [ ] Have you given this tool to an agent on a real task and watched it
      use it?
- [ ] Did the agent pick the right tool? Pass the right arguments?
- [ ] Did the agent need the output, or ignore it?
- [ ] If anything went wrong, did you fix the *tool*, not the prompt?

---

## Common smells

- **The tool is a thin wrapper over one API call.** It probably belongs
  inside another tool.
- **The tool returns `{success: true}`.** Wasted round trip.
- **The tool description is a single sentence copied from a docstring.**
  Rewrite as a prompt fragment.
- **You have ten tools with `users` in the name.** Collapse or namespace.
- **The agent calls the tool ten times in a row.** Granularity is too fine.

---

## The meta-rule

Test every tool with an agent on a real task. Watch where it struggles. Fix
the description, the schema, or the return value. Loop. The tool gets
better at being used by the kind of caller it actually has — which is not a
human reading the docs.
