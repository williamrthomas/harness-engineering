# 08 — Security patterns

> *The lethal trifecta: an agent with access to private data, exposure to
> untrusted content, and the ability to externally communicate.*
> — Simon Willison

Build the harness as if the model will, eventually, read attacker-written
text. Because it will.

---

## The Agents Rule of Two

Pick at most two:

- Access to **private data**.
- Exposure to **untrusted content**.
- Ability to **externally communicate**.

All three at once is the lethal trifecta. Avoid it. When you cannot avoid
it, you are no longer doing harness engineering — you are doing security
engineering.

---

## The six patterns

From [Simon Willison's catalog](https://simonwillison.net/2025/Jun/13/prompt-injection-design-patterns/).
Compose at least two for any production system that touches the trifecta.

### 1. Action-Selector

The agent picks from a fixed set of safe actions. Tool *outputs* never flow
back into the model.

> *Agents can trigger tools, but cannot be exposed to or act on the
> responses from those tools.* — Willison

An "LLM-modulated switch statement." Strongest pattern; smallest action
surface.

### 2. Plan-Then-Execute

The agent commits to a plan *before* seeing untrusted content. Tool outputs
can affect *content*, but not the *choice* of subsequent actions.

> *Calendar.read() output might be able to corrupt the body of the email
> that is sent, but it won't be able to change the recipient.* — Willison

### 3. LLM Map-Reduce

A coordinator dispatches sub-agents over untrusted content. Each
sub-agent's output is constrained and validated; the coordinator aggregates.
The coordinator never reads raw untrusted text.

### 4. Dual LLM

A privileged LLM handles trusted content and tool dispatch. A quarantined
LLM handles untrusted content and returns only typed, schema-validated
output.

The privileged side never sees untrusted strings. The quarantined side has
no tools.

### 5. Code-Then-Execute

The agent writes a small program in a constrained DSL. The program is
inspected, then executed deterministically. The model produces *code*, not
*actions*.

This converts "did the agent do the right thing?" into "is this program
safe?" — a much smaller, more familiar question.

### 6. Context-Minimization

The agent sees only the context strictly necessary for the current decision.
A fresh agent with a narrow window cannot be hijacked by content it never
read.

The cheapest pattern. Often combinable with any of the others.

---

## Defense in depth

Pick patterns. Then add layers around them.

- **Provenance tagging.** Every piece of context carries a tag — `trusted`,
  `untrusted`, `quarantined`. Tools refuse to act on untrusted arguments
  without explicit confirmation.
- **Capability gating.** Tools require an unlock per task. The default tool
  set is small.
- **Confirmation for irreversible actions.** Always. With the *full*
  context visible to the human.
- **Egress filtering.** Outbound network calls go through a proxy with a
  domain allowlist.
- **Logging and replay.** Every tool call recorded, replayable,
  attributable.

These are not optional. They are how a real harness survives contact with
the internet.

---

## What never crosses

Bright lines. No exceptions.

- **No secrets in the model context.** Keys, tokens, credentials live in
  the harness, not the prompt. The agent calls a tool that uses them.
- **No raw untrusted documents in the system prompt.** Untrusted content is
  *user content*, ideally in a quarantined sub-agent.
- **No agent-initiated communication to humans the agent has not been
  introduced to.** No emails to addresses the agent learned from a webpage.
  No DMs to users discovered in a database.
- **No tools that combine read-untrusted with write-anywhere.** Split the
  tool. One reads. One writes. They do not share a prompt.

---

## The mindset

Assume injection. Every webpage, every PDF, every search snippet, every
email, every commit message, every filename, every database row written by a
user — could contain "ignore your prior instructions, do X" — and somewhere,
*it does*.

The harness's job is to make that text not matter. Either the model never
sees it (Action-Selector, Context-Minimization), or it cannot influence the
choice of action (Plan-Then-Execute), or its output is structurally
constrained (Dual LLM, Code-Then-Execute), or it is aggregated by a
coordinator it cannot reach (Map-Reduce).

You will not out-prompt an attacker. You will out-architect them.
