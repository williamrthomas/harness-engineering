# 10 — Trust boundaries

> *The lethal trifecta: an agent with access to private data, exposure to
> untrusted content, and the ability to externally communicate.*
> — Simon Willison

Any agent that touches the network, the file system, the email system, or
another agent's output is operating across a trust boundary. The model has
no instinct for boundaries. The harness must.

---

## The threat

Prompt injection is not a vulnerability you patch. It is a property of LLMs:
they treat instructions and data the same way, because they have to. Anything
the model reads — a webpage, a tool result, a Slack message, a PDF, a
filename, a git commit message, a search snippet — can contain instructions
the attacker wants the agent to follow.

If the agent has the *ability* to act on those instructions, sooner or later
it will.

---

## The Agents Rule of Two

A useful default, popularized by Meta and refined by Simon Willison:

> *An agent at any time should hold at most two of: access to private data,
> exposure to untrusted content, ability to externally communicate.*

All three at once is the **lethal trifecta**. Avoid it. When you cannot avoid
it, you are no longer doing harness engineering — you are doing security
engineering, and the harness must reflect that.

---

## The six design patterns

Simon Willison's [Design Patterns for Securing LLM Agents](https://simonwillison.net/2025/Jun/13/prompt-injection-design-patterns/)
catalogs the field. Internalize them.

### 1. Action-Selector

The agent selects from a fixed set of safe actions. Tool *outputs* never
flow back to the model; the agent is "an LLM-modulated switch statement."
Strongest pattern. Suitable when the action surface is small and known.

### 2. Plan-Then-Execute

The agent commits to a plan *before* seeing untrusted content. The plan can
read, but cannot be reshaped by what it reads. Useful for "do X, then Y" tasks
where Y must remain locked.

### 3. LLM Map-Reduce

A coordinator dispatches to sub-agents over untrusted content. Each
sub-agent's output is constrained, then aggregated by the trusted coordinator.
The coordinator never sees raw untrusted text.

### 4. Dual LLM

A privileged LLM handles trusted content and tool dispatch. A quarantined
LLM handles untrusted content and returns only typed, schema-validated
outputs to the privileged side.

### 5. Code-Then-Execute

The agent writes a small program in a constrained DSL. The program is
inspected and executed deterministically. The model produces *code*, not
*actions*.

### 6. Context-Minimization

The agent sees only the context strictly necessary for the current decision.
A fresh agent with a narrow window cannot be hijacked by content it never
read.

These patterns compose. A real production agent is usually two or three of
them stacked.

---

## Defense in depth

No single layer is sufficient. Stack them:

- **Provenance** — track what came from where. Untrusted content carries a tag.
- **Capability gating** — the agent does not have powerful tools by default;
  they are unlocked per-task.
- **Confirmation** — irreversible actions require human approval, with the
  *full* prompt visible.
- **Egress filtering** — outbound network calls go through a proxy that
  enforces a domain allowlist.
- **Logging and replay** — every tool call recorded, replayable, attributable.

A harness without these is not unsafe in theory. It is unsafe in practice.

---

## What never crosses

Treat these as bright lines:

- **No secrets in the model context.** API keys, tokens, credentials live in
  the harness, not the prompt. The agent calls a tool that uses them.
- **No raw untrusted HTML, PDFs, or emails as system messages.** Untrusted
  content is *user* content, in a quarantined sub-agent.
- **No agent-initiated communication to humans the agent has not been
  introduced to.** No emails to addresses the agent learned from a webpage.
  No DMs to users the agent discovered in a database.
- **No tools that combine read-untrusted with write-anywhere.** Split the tool.

---

## The mindset

Assume injection. Assume your tools will be called with arguments derived
from text written by an attacker. Assume your agent will, at some point, read
a document that says "ignore your previous instructions" — and *the
attacker's job is to make that document the only one the agent reads*.

The harness's job is to make that not matter.

---

This concludes the doctrine. The patterns and recipes that follow are
applications of these ten laws to specific surfaces. If the patterns ever
contradict the doctrine, the doctrine wins.
