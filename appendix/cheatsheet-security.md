# Cheatsheet — Security

The lethal trifecta. The Rule of Two. The six design patterns. Defense in
depth.

---

## The lethal trifecta

> An agent at any time should hold at most **two** of:

1. **Access to private data.**
2. **Exposure to untrusted content.**
3. **Ability to externally communicate.**

All three at once = lethal trifecta. Avoid by design.

---

## The six design patterns

From [Simon Willison](https://simonwillison.net/2025/Jun/13/prompt-injection-design-patterns/).
Compose at least two.

### 1. Action-Selector
Agent picks from a fixed action set. Tool outputs **never** flow back to
the model. *"LLM-modulated switch statement."*

### 2. Plan-Then-Execute
Agent commits to a plan *before* seeing untrusted content. Tool outputs
can affect *content*, not the *choice* of subsequent actions.

### 3. LLM Map-Reduce
Coordinator dispatches sub-agents over untrusted content. Each
sub-agent's output is constrained and validated; coordinator aggregates.
Coordinator never sees raw untrusted text.

### 4. Dual LLM
Privileged LLM handles trusted content + tools. Quarantined LLM handles
untrusted content; returns only typed, schema-validated output.

### 5. Code-Then-Execute
Agent writes a small program in a constrained DSL. Program is inspected,
then executed deterministically. Model produces *code*, not *actions*.

### 6. Context-Minimization
Agent sees only context strictly necessary. A fresh agent with a narrow
window cannot be hijacked by content it never read.

---

## Defense in depth

| Layer | What it does |
| --- | --- |
| **Provenance tagging** | Every context piece tagged trusted/untrusted. |
| **Capability gating** | Powerful tools unlock per task; small default surface. |
| **Confirmation** | Irreversible actions require human approval, with full context visible. |
| **Egress filtering** | Outbound network goes through a domain-allowlist proxy. |
| **Logging + replay** | Every tool call recorded, replayable, attributable. |
| **Sandboxing** | OS-level isolation for filesystem and network. |

Stack at least three.

---

## Bright lines

Treat as non-negotiable.

- ❌ **No secrets in the model context.** Keys live in the harness. The
  agent calls a tool that uses them.
- ❌ **No raw untrusted documents in the system prompt.** Untrusted content
  is *user* content, in a quarantined sub-agent.
- ❌ **No agent-initiated communication to humans the agent has not been
  introduced to.** No emails to addresses the agent learned from a
  webpage.
- ❌ **No tools that combine read-untrusted with write-anywhere.** Split
  the tool.

---

## The mindset

- Assume injection. Always.
- Architect, don't patch.
- Out-architect, don't out-prompt.
- Treat *"ignore your previous instructions"* as a string the model will
  see — and must not act on.

---

## The reflex when designing a tool

Three questions:

1. **Does this tool read untrusted content?** If yes, what stops that
   content from steering the agent?
2. **Does this tool write somewhere with consequences?** If yes, what
   confirmation gates the write?
3. **Could this tool combine read-untrusted with write-anywhere via the
   agent's planning?** If yes, split the tool.
