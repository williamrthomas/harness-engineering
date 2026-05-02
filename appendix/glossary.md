# Glossary

Vocabulary, defined once.

---

**`AGENTS.md`** — The cross-vendor open-standard project file for
agent-facing instructions. Closest file wins. See
[agents.md](https://agents.md).

**Action-Selector** — Security pattern: the agent selects from a fixed set
of actions; tool outputs never flow back to the model.

**Agent** — A system where a model uses tools in a loop to accomplish a
goal. Equivalently: *Model + Harness*.

**Agents Rule of Two** — Hold at most two of: private data, untrusted
content, external communication.

**Anterograde amnesia** — Karpathy's metaphor for LLM memory: short-term
context only; no consolidation; every session starts blank.

**Autonomy slider** — The dial from "agent suggests, human approves
everything" to "agent runs free, reports at the end."

**Bloat** — Context that pays a turn cost without paying its way in
behavior.

**`CLAUDE.md`** — Anthropic's name for the project file. Functionally
identical to `AGENTS.md`.

**Compaction** — Deliberate replacement of chronicled detail with curated
summary, mid-session.

**Computational control** — A deterministic check: linter, typechecker,
test, schema validator. Same input → same verdict.

**Context engineering** — The set of strategies for curating and
maintaining the optimal set of tokens during inference.

**Context-Minimization** — Security pattern: the agent sees only the
context strictly necessary for the current decision.

**Code-Then-Execute** — Security pattern: the agent writes a small
program in a constrained DSL; the program is executed deterministically.

**Dual LLM** — Security pattern: a privileged LLM handles trusted content
and tools; a quarantined LLM handles untrusted content with no tool
access.

**Eval** — A measurement of agent quality. Three levels: assertions,
rubric, A/B.

**Feedback** — A signal *after* the action that helps the agent
self-correct. *Sensor*.

**Feedforward** — Steering *before* the action that increases the
probability of a good first attempt. *Guide*.

**Garbage collection (loop GC)** — Periodic pruning of accumulated cruft
in long-running agent loops.

**Guide** — Anything that anticipates and shapes agent behavior before it
acts. (Böckeler.)

**Harness** — Everything in an agent except the model itself.

**Hashimoto rule** — *Anytime you find an agent makes a mistake, you take
the time to engineer a solution such that the agent never makes that
mistake again.*

**Inferential control** — A probabilistic check: LLM-as-judge, model-graded
eval, classifier-based filter. Same input → mostly the same verdict.

**Injection (prompt injection)** — A property of LLMs: instructions
appearing in *data* are treated like instructions. Not a vulnerability to
patch; a property to architect around.

**Invariant** — A short, machine-checkable property that must always hold.

**Just-in-time retrieval** — Pulling context only when needed, not in
advance.

**Layer rule** — *Types → Config → Repo → Service → Runtime → UI.* Push
every constraint to the leftmost layer that catches it.

**Lethal trifecta** — All three of: private data + untrusted content +
external communication. Avoid.

**LLM Map-Reduce** — Security pattern: a coordinator dispatches sub-agents
over untrusted content; results are validated, then aggregated.

**Loop** — *Act → observe → adjust → act,* automated. The unit of
agentic work.

**Map (vs. manual)** — A short, navigational project file that points at
where things live, not what every line does.

**Memory surface** — A place memory lives across sessions: project files,
codebase, scratch files, external stores, git history.

**Plan-Then-Execute** — Security pattern: the agent commits to a plan
before any chance of exposure to untrusted content.

**Plan Mode** — Claude Code's explicit two-phase workflow: explore, then
plan, then code.

**Progressive disclosure** — Three-level loading: metadata always, body on
demand, bundled files on reference. The Skill anatomy.

**Ralph Wiggum Loop** — *Do the dumbest thing that could possibly work, in
a loop, with feedback.* (OpenAI Codex team.)

**Sensor** — Anything that observes after the agent acts and helps it
self-correct. (Böckeler.)

**Skill** — A directory containing a `SKILL.md` (with frontmatter) and
optional bundled files. Open standard, December 2025.

**Steering loop** — The cybernetic shape of a working harness: guide
narrows, sensor catches, agent retries inside a smaller cone.

**Sub-agent** — A child agent invocation in its own context window,
returning a distilled result.

**System prompt learning** — Karpathy's hypothesized paradigm: durable
behavior change via written-down lessons, not parameter updates.

**Trifecta** — See *lethal trifecta*.

**Worktree-per-task** — Per-task isolated environment (filesystem,
database, observability), enabling parallel agents without collision.
