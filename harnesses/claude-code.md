# Claude Code

> *Treat `CLAUDE.md` like code: review it when things go wrong, prune it
> regularly, and test changes by observing whether Claude's behavior actually
> shifts.*
> — Anthropic, [Best Practices for Claude Code](https://www.anthropic.com/engineering/claude-code-best-practices)

Claude Code is Anthropic's coding agent — usable in the **CLI** (the original
surface), inside **IDEs** (VS Code, JetBrains, Zed), through **skills** (an
open standard for packaged expertise), and via the **SDK** (programmatic
access from TypeScript or Python).

The Claude Code worldview leans toward *persistent local pair-programming*.
The agent is a teammate that lives in your repo, reads your files on every
session, runs your commands, and can be extended with skills and slash
commands without changes to the base tool.

---

## Anatomy

### 1. `CLAUDE.md` — the persistent project prompt

Claude reads this file at the start of every conversation. Therefore:

> *Only include things that apply broadly. For each line, ask: "Would
> removing this cause Claude to make mistakes?" If not, cut it.* — Anthropic

`CLAUDE.md` is functionally identical to `AGENTS.md`; in many setups they are
the same file or one symlinks the other. The hygiene is the same:

- **Bash commands** Claude can't guess.
- **Code style rules** that differ from defaults.
- **Testing instructions** and the test runner.
- **Repository etiquette** (branch naming, PR conventions).
- **Architectural decisions** specific to your project.
- **Environment quirks** and required env vars.

What to **exclude**: anything Claude can figure out from the code, standard
language conventions, file-by-file descriptions, advice like *"write clean
code"*.

If Claude keeps doing something you don't want, the file is too long and the
rule is getting lost. Cut. Move it earlier. Add `IMPORTANT:` or `YOU MUST:`.
Test by observation, not faith.

### 2. The `CLAUDE.md` hierarchy

Claude reads `CLAUDE.md` from multiple locations, with imports:

| Path | Scope |
| --- | --- |
| `~/.claude/CLAUDE.md` | All sessions, machine-wide. Personal defaults. |
| `./CLAUDE.md` | Project root. Checked in. Shared with the team. |
| `./CLAUDE.local.md` | Project, gitignored. Personal overrides. |
| `parent/CLAUDE.md` + `parent/sub/CLAUDE.md` | Monorepos. Both pulled in. |
| `child/CLAUDE.md` | Loaded on demand when working in that directory. |

Imports use `@path/to/file`. Treat them like header includes — small,
purposeful, traceable.

### 3. Skills

Skills are how knowledge that applies *sometimes* lives outside the system
prompt. A skill is a directory containing `SKILL.md` and optional bundled
files. The frontmatter (`name`, `description`) is pre-loaded into the system
prompt; the body is read on demand; bundled files are read deeper-on-demand.
Three levels of progressive disclosure.

> *Open standard, December 2025. Cross-platform portability.*
> — [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

Skills become reusable across teams, repos, even agents. A well-built skill
library is a serious knowledge asset.

### 4. Slash commands

Slash commands are the keyboard shortcuts of Claude Code. Built-ins worth
knowing:

| Command | Use |
| --- | --- |
| `/init` | Analyzes the codebase to seed `CLAUDE.md`. Always your starting move. |
| `/clear` | Reset context between unrelated tasks. Use it. |
| `/compact <hint>` | Compact mid-session, optionally focused. |
| `/permissions` | Manage tool/domain allowlists. |
| `/hooks` | Inspect hook configuration. |
| `/btw` | Aside that doesn't pollute context. |
| `/rewind` | Restore previous state, or summarize from a chosen turn. |
| `/continue` / `/resume` | Pick up where you left off. |
| `/<skill-name>` | Invoke a skill explicitly. |

Custom slash commands are just skills wired to a name. If you're typing the
same multi-step prompt twice, make it a slash command.

### 5. Plan Mode and the explore-plan-code workflow

> *Letting Claude jump straight to coding can produce code that solves the
> wrong problem.* — Anthropic

The recommended default is the four-phase loop:

1. **Explore** — Plan Mode on. Claude reads files, asks questions, makes no
   changes.
2. **Plan** — Ask for an explicit implementation plan. Read it.
3. **Code** — Plan Mode off. Execute against the plan.
4. **Verify** — Tests, typecheck, lint, manual spot-check.

Plan Mode is the autonomy slider made into a button. Use it for anything you
haven't done before, and for anything risky or ambiguous.

### 6. Permission modes

Claude Code has three ways to handle the *"is it safe to do this?"* question:

- **Auto mode** — a classifier model decides. Blocks scope escalation,
  unknown infrastructure, hostile-content-driven actions. Best when you trust
  the direction but don't want to click through every step.
- **Permission allowlists** — explicit list of tools and commands that don't
  need approval. `npm run lint`, `git commit`, `pnpm test`. Boring,
  reversible, frequent.
- **Sandboxing** — OS-level isolation for filesystem and network. Lets the
  agent move fast inside a defined cage.

Pick deliberately. After ten approvals you are not reviewing — you are
clicking through. That is the failure mode the harness must prevent.

### 7. Hooks

Hooks are deterministic side-effects bound to lifecycle events: before a
tool call, after a file write, on session start. Use them as **sensors**:
auto-format on write, run typecheck after a code change, log every tool call
to a trace file.

---

## App / IDE / CLI / SDK

### CLI (`claude`)

- The original surface. Live in the terminal, the agent in the prompt.
- Strong loop ergonomics: explore-plan-code feels native here.
- Pair with `tmux` when you need parallelism (one Claude per pane).

### IDE integrations

- VS Code, JetBrains, Zed. Claude reads the open file, the cursor, the
  selection — context is implicit.
- Best for tight inner-loop work: rename, refactor inside a function, write a
  test for the thing under the cursor.
- Less appropriate for "go do this autonomously for an hour" — for that, drop
  to the CLI or use a task runner.

### Skills

- Open standard (December 2025). Portable across agents that adopt it.
- Treat the skill library as a versioned product. Review changes. Test them.
- Watch the metadata cost: only `name` and `description` sit in the prompt by
  default, but with hundreds of skills, that adds up.

### SDK

- Programmatic Claude. Build your own harness: ticket triagers, daily
  briefings, eval runners, doc generators.
- Each SDK script is a small bespoke harness. Apply the doctrine: invariants,
  sensors, loops, evals.
- Think of the SDK as the language for building your *own* coding agent on
  top of Claude — not as an autocomplete API.

---

## Idioms unique to Claude Code

- **`/init` first, always.** Seed the `CLAUDE.md` from the codebase before
  hand-editing it.
- **Plan Mode for anything novel.** The discipline of writing the plan
  before writing the code catches half the bugs at zero cost.
- **Skills for "applies sometimes" knowledge.** Don't bloat `CLAUDE.md`.
- **`/clear` between tasks.** Context drift is a tax. Pay it on purpose.
- **Hooks as sensors.** Format-on-write, typecheck-after-edit. Make the loop
  tighten itself.
- **Treat `CLAUDE.md` like code.** Review, prune, test, commit.

---

## Where Claude Code shines, where to be careful

**Shines:**
- Long-running projects with a stable repo. The skills library compounds.
- Pair-programming workflows where you want a teammate, not a worker.
- Cross-tool workflows where skills bridge bespoke internal tooling.

**Be careful:**
- Auto-mode + remote execution + untrusted content = the trifecta.
  ([See doctrine 10.](../doctrine/10-trust-boundaries.md))
- Skill sprawl. A skill library that nobody curates is a graveyard with a
  search bar.
- Letting `CLAUDE.md` become a wiki. Every line costs every session forever.

---

## Further reading

- [Best Practices for Claude Code](https://www.anthropic.com/engineering/claude-code-best-practices) — Anthropic. The canonical doc.
- [Effective context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Anthropic.
- [Writing effective tools for AI agents](https://www.anthropic.com/engineering/writing-tools-for-agents) — Anthropic.
- [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) — Anthropic.
- [AGENTS.md spec](https://agents.md) — interoperable with `CLAUDE.md`.
- [reference/claude-code.md](../reference/claude-code.md) — the configuration manual: every knob in `~/.claude/settings.json`, hooks, skills, sandbox.
- [reference/agents-md.md](../reference/agents-md.md) — cross-vendor file format.
