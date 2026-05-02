# 07 — Long-horizon task with checkpoints

How a six-hour autonomous run doesn't go off the rails.

---

## The premise

The Codex team reports single runs of *six hours or more* on a single task.
This is not impossible. It is engineered.

The shape of a long-horizon task is not a long prompt. It is a **plan, a
loop, and a chain of checkpoints**.

---

## The plan-first discipline

Before the agent starts:

1. Human writes the spec.
2. Agent reads the spec, reads the relevant code, asks clarifying questions
   if any.
3. Agent produces an explicit plan as a file (`PLAN.md`):
   - The list of phases.
   - The success criterion for each phase.
   - The expected commits / artifacts at each phase.
4. Human reviews the plan. Approves, edits, or rejects.

A plan that fits on one screen is good. A plan that requires scrolling is
probably either too coarse or hiding ambiguity.

This is the same shape as Claude Code's *Plan Mode* — separate exploring
from doing.

---

## Phases as commits

Each phase ends in a commit. Each commit is a checkpoint.

Why commits as the unit:

- Resumable. If the agent crashes, you can `git diff HEAD~1` and know
  exactly where it was.
- Reviewable. A human can read a commit; they cannot read six hours of
  conversation.
- Bisectable. If something broke at phase 4, `git bisect` finds it.

Each phase commit follows a strict format:

```
<phase number>: <one-line description>

<bullet list of what changed>

Verifies:
- <test or check that confirms this phase>
```

The "Verifies" line is a contract: this commit asserts a verifiable
property of the system. The agent writes it; the human and the next agent
can check it.

---

## The inner loop, per phase

```
read PLAN.md
identify the next unchecked phase
do the work for that phase
run the phase's verifier
if pass: mark complete in PLAN.md, commit, continue
if fail: diagnose, fix, re-run; bound retries
if budget exhausted: write CHECKPOINT.md and pause
```

The agent operates one phase at a time. The plan is not re-derived; it is
executed.

---

## Garbage collection between phases

Long runs accumulate cruft: stale scratch files, abandoned approaches,
half-baked branches. Between phases, run a GC step:

- Delete any temporary files created during this phase.
- Compact the conversation: the original spec, the plan, the latest
  checkpoint, the most recent error messages — keep these. Drop the rest.
- Update `PLAN.md` to reflect actual progress.

Without GC, six-hour runs slow, then leak, then crash.

---

## Checkpoint files

The agent writes these by convention. The harness reads them on resume.

`CHECKPOINT.md`:

```
Phase: 4 — implement payment refund flow
Status: paused; awaiting human review of refund_service.ts
Next steps:
  1. Run: pnpm test --filter payments
  2. If green: mark phase 4 complete, proceed to phase 5
  3. If red: errors are in spec/refund.spec.ts; investigate
Open questions:
  - Should refunds be recorded in the audit log? (defaulted to yes)
```

A checkpoint is the harness's memory of where the agent is. It survives
crashes, restarts, and human interruptions.

---

## Where humans sit on long runs

A six-hour autonomous run still has human gates. They are just rare and
high-altitude:

- **Plan approval.** Before the run starts.
- **Phase boundary nudges.** "I notice phase 3 is taking longer than
  expected; the agent says X. Approve continuing or pause."
- **Irreversible action approval.** Same as always.
- **End-of-run review.** A summary, the diff, the eval suite output.

If the human is being interrupted more than every 30–60 minutes, the
altitude is too low or the harness is too weak.

---

## Failure modes specific to long runs

- **Drift.** The agent slowly stops resembling the plan. Mitigation: every
  N phases, re-read the plan and assert alignment.
- **Cruft accumulation.** Without GC, the loop slows.
- **Loop cycles.** The agent fixes A, breaks B, fixes B, breaks A. Bound
  retries; on cycle detection, write a checkpoint and pause.
- **Test exhaustion.** The agent passes the tests by changing the tests.
  Strict invariant: the original tests do not change unless explicitly
  approved.
- **Context ossification.** The conversation is so long the model can't
  reason fresh. Compact, hard.

---

## When *not* to attempt a long run

- The plan is unclear. (Fix the plan. Don't run.)
- The eval suite is weak. (Strengthen first.)
- The task involves new external systems. (Build tools first.)
- Stakes are high and verification is post-hoc. (Reduce stakes or move
  verification earlier.)

A failed six-hour run wastes more than thirty failed five-minute runs. Long
horizons amplify everything — including the harness.
