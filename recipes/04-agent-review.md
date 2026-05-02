# 04 — Multi-agent code review

The Ralph Wiggum Loop, dressed up. Two agents, one PR, until both are
satisfied.

---

## The shape

```
       ┌──────────────────────────┐
       │  Author agent            │
       │  (writes code, opens PR) │
       └────────────┬─────────────┘
                    │ PR
                    ▼
       ┌──────────────────────────┐
       │  Reviewer agent          │
       │  (reads, runs tests,     │
       │   leaves comments)       │
       └────────────┬─────────────┘
                    │ comments
                    ▼
       ┌──────────────────────────┐
       │  Author agent            │
       │  (responds, fixes,       │
       │   pushes new commits)    │
       └────────────┬─────────────┘
                    │
                    ▼
              loop until ✅
                    │
                    ▼
              human merges
```

The loop runs until the reviewer agent has no more comments. Then a human
takes a final pass — usually fast, because the diff is already
self-consistent.

---

## Why two agents

One agent reviewing its own code is the same agent. It will overlook the
same things. Two prompts with different *roles* see different things.

- The **author** is rewarded for shipping.
- The **reviewer** is rewarded for blocking what is not yet right.

These two goals naturally diverge, which is the whole point.

---

## The role prompts

### Author

- *Goal:* implement the spec, with tests, with passing CI.
- *Tools:* full set — read, write, run tests, open PR, push commits.
- *Stop condition:* CI green, the reviewer has no remaining blocking
  comments.

### Reviewer

- *Goal:* find things wrong with this PR — correctness, security, style,
  performance, test coverage. Block until acceptable.
- *Tools:* read-only on the codebase. Can run tests. *Cannot* push code.
- *Style:* one comment per issue. Concrete. Cite the line. Suggest a fix
  when possible.
- *Stop condition:* nothing remains to flag at the agreed bar.

The asymmetry of tools is load-bearing. The reviewer cannot fix anything,
so it has nothing to do *except* find issues.

---

## What the reviewer looks for

Tune over time. Reasonable starting list:

- **Correctness against spec.** Does the diff actually implement the
  ticket?
- **Tests.** Is the new behavior tested? Are the tests meaningful?
- **Edge cases.** Empty inputs, very large inputs, concurrent calls, error
  paths.
- **Security.** Injection, authz, secret handling, untrusted input.
- **Performance.** Obvious O(n²) when O(n) was easy.
- **Style.** Consistency with the codebase.
- **Documentation.** Are public APIs documented? Is `AGENTS.md` updated if
  the change affects how the agent should work?
- **Migration.** If the change touches the schema, is a `down` present?

Encode this list as the reviewer's prompt. Update it from your incident
postmortems and your PR retrospectives.

---

## The handoff

The harness wires the loop:

1. Author opens PR with a clear description.
2. Reviewer is invoked on the PR.
3. Reviewer leaves comments inline.
4. Author reads comments, addresses each one, pushes new commits, replies.
5. Reviewer re-checks. If clean, approves. Otherwise, more comments.
6. Loop until clean.

CI runs throughout, blocking on its own gate.

---

## Where humans sit

- **Spec writing.** The ticket. The constraints. The acceptance criteria.
- **Final review.** A spot-check after the agents are done, focused on
  things humans see uniquely well: product judgment, naming, emergent
  design.
- **Bar tuning.** When the reviewer is too strict or too lenient, edit its
  prompt.
- **Irreversible approval.** Merges to main, deploys to prod.

The OpenAI team explicitly pushes review *right* — toward humans only at
the end, with agent-to-agent review absorbing the rest. That is the
direction of travel.

---

## Failure modes

- **Reviewer drift toward niceness.** Agents tend to converge. If the
  reviewer keeps approving with no comments, tighten its prompt and add a
  hold-out test that *should* be flagged.
- **Author capitulation.** The author "fixes" by deleting the feature. Add
  an invariant: the original tests must still pass.
- **Infinite loops.** Bound the loop. Three rounds, then a human looks. If
  three rounds didn't converge, a human is genuinely useful.
- **Reviewer writes code via comments.** Long suggested-fix blocks become
  the change. That's fine until it isn't; if the reviewer is doing the
  author's job, the role prompts have collapsed.

---

## A meta-rule

This recipe scales. You can have a *security* reviewer, a *perf* reviewer,
a *style* reviewer, each with a tighter scope. Each is an agent with a
specialized prompt. The author addresses each in turn. Costs grow linearly;
catch rate grows nonlinearly.

Most teams never need more than two roles. Some need five. Two is the right
default.
