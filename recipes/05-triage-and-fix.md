# 05 — Triage and fix loop

Bug report → reproduction → fix → PR. The agent drives the whole arc.

---

## Why this loop matters

Most "feature" work in real teams is bug-fix work in disguise. A harness
that handles triage and fix well saves more time than one that handles new
features well.

---

## The full loop

```
1. Bug report arrives.
2. Triage agent reads it.
3. Triage agent reproduces (or reports "cannot repro").
4. Triage agent locates the cause.
5. Fix agent writes a regression test that fails.
6. Fix agent fixes; the regression test passes.
7. CI runs the full suite.
8. PR is opened with: report → repro → cause → fix → test.
9. Reviewer agent (recipe 04) signs off.
10. Human merges.
```

Each step is small. Each step has a clear handoff. The whole arc is
auditable.

---

## Step 1 — Bug report → structured issue

Many bug reports are bad. The first job is to fix the report.

A triage agent is given the raw report and asked to produce a structured
issue:

```
- Steps to reproduce
- Expected
- Actual
- Environment
- Severity hypothesis
- Open questions for the reporter
```

If "open questions" is non-empty, reply to the reporter and pause. Don't
guess.

## Step 2 — Reproduction

The reproduction step is the most important. If the agent cannot reproduce
deterministically, the fix is hopeless.

Set the agent up with:

- A clean worktree.
- A reproducible environment (Docker, devcontainer, per-worktree DB).
- The ability to run the app, hit endpoints, take logs.

Output: a script (`repro.sh`, a failing test, or a one-liner) that
*reliably* triggers the bug. Commit it as a draft regression test, marked
`xfail` or `skip` for now.

If the agent can't reproduce after a few attempts, escalate to a human with
its findings. Don't loop forever.

## Step 3 — Locate the cause

With reproduction in hand:

- Bisect the commit history if the bug is recent.
- Add temporary logging.
- Use the observability stack.
- Read the code.

Output: a one-paragraph hypothesis pointing at a specific file / function /
line, with evidence.

## Step 4 — Regression test

Promote the reproduction to a real test that *fails for the right reason*.
A test that fails by accident is not a regression test.

This step is not optional. The fix without the test is a wish.

## Step 5 — Fix

Now, and only now, write the fix. Constraints:

- Make the regression test pass.
- Do not modify the test.
- Do not modify other tests unless the bug genuinely changes their
  expected behavior — and if so, document why.
- Touch only the files needed.

## Step 6 — CI gate

Run the full suite. The fix passes everything.

## Step 7 — PR with a clean narrative

The PR description writes itself, because the harness has produced every
piece:

```
## Bug
<original report, cleaned up>

## Reproduction
<the script / test / one-liner>

## Cause
<the one-paragraph hypothesis, now confirmed>

## Fix
<what changed, why, and what we considered>

## Regression test
<link to the new test>
```

A reviewer can audit each section against the next. The agent did not just
"fix it" — it produced an auditable arc.

---

## What humans do

- Triage the genuinely ambiguous reports.
- Make the call when reproduction fails.
- Approve the merge.
- Update `AGENTS.md` or a skill if the fix revealed a class of mistake.

The last one is the harness compounding. *"This bug happened because the
agent didn't know X."* Don't fix the symptom and walk away. Write down what
you learned so the next session has it.

---

## Anti-patterns

- **Skip reproduction.** Always reproducible by definition; skip it and you
  are guessing for the rest of the loop.
- **Edit the test to make it pass.** Hard rule: never. The test is the
  oracle.
- **One commit that does everything.** Separate "regression test (failing)"
  from "fix (now passing)". The bisect-ability matters.
- **Close the bug without writing down the lesson.** The next bug in this
  class will arrive within a week.
