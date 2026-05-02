# 07 — Human in the loop

> *After the tenth approval you're not really reviewing anymore, you're just
> clicking through.*
> — Anthropic, [Best Practices for Claude Code](https://www.anthropic.com/engineering/claude-code-best-practices)

Human approval is the most expensive resource in the system. Spend it on
purpose.

---

## The autonomy slider

Every agentic UX has a slider:

- **Left.** Model suggests; human approves every step.
- **Right.** Model runs free; human reviews at the end.

The slider's *right setting* depends on:

| Factor | Slides slider... |
| --- | --- |
| Reversibility | Right (irreversible → left). |
| Cost of action | Right (expensive → left). |
| Verifiability after the fact | Right (hard to verify → left). |
| Track record on this kind of task | Right (failures → left). |
| Trust boundary involvement | Left. |

Bad harnesses pin the slider. Good harnesses make it task-dependent and
visible.

---

## The four altitudes of approval

Pick deliberately. Most teams default to the wrong one.

### Altitude 1 — every keystroke

Approving every tool call. Maximum control, maximum tax.

**Use when:** First time doing this kind of task. Onboarding. Demo to a
skeptical stakeholder.

**Avoid when:** You actually want the agent to do work. After ten
approvals, the human is no longer reviewing.

### Altitude 2 — every irreversible action

Approve writes to production, deploys, money movements, communications,
permanent deletes. Everything else runs free.

**Use as the default.** This is where most production agents should sit.

### Altitude 3 — every PR

Agent runs end-to-end inside a worktree, opens a PR, you review the diff.
Used by the Codex team for production work.

**Use when:** Strong CI, strong invariants, reversible local actions, the
diff is small enough to read.

### Altitude 4 — periodic spot-check

Agent runs continuously; human reviews a sample of traces weekly.

**Use when:** High volume, low individual stakes (triage, classification,
bulk transformation), and you trust the eval suite.

---

## What never delegates

Some things are always the human's job. Encode them as gates the agent cannot
auto-approve:

- **Money.** Real or simulated production money. Always confirm.
- **Communications to humans.** Email, Slack, customer messages.
- **Public actions.** Posts, releases, announcements.
- **Permanent deletes.** Especially of customer data.
- **Permission changes.** Granting access, rotating keys, changing roles.
- **Anything cross-trust-boundary.** Egress to a new domain, ingest of an
  unknown source.

The harness should make these technically impossible to auto-approve. Not
"the agent shouldn't"; *"the agent can't"*.

---

## Allowlists, classifiers, sandboxes

Three tools for adjusting the slider without losing safety:

- **Allowlists.** Specific commands and tools auto-approved by name.
  `git commit`, `npm test`, `gh pr create`. Boring, reversible, frequent.
- **Classifier-based auto-mode.** A model judges each action; routine ones
  proceed, suspicious ones (scope escalation, untrusted-influenced
  arguments, novel infrastructure) prompt.
- **Sandboxing.** OS-level isolation. The agent runs free *inside the cage*;
  the cage handles the rest.

Most production setups stack two: allowlists for the boring stuff, sandbox
for the unboring stuff.

---

## The diagnostic

If you are clicking *Approve* without reading, raise the altitude. The
approvals at your current level have stopped meaning anything; they are
training you to ignore them. That is the worst possible state.

Either:

- Move to a higher altitude (PRs instead of keystrokes), or
- Tighten the harness so fewer approvals are needed at this altitude
  (more allowlists, more sandboxing, stronger invariants).

Approval should be rare *and* meaningful. Frequent meaningless approvals are
a tax on the human and a false sense of safety on the system.
