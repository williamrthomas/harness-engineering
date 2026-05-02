# Cheatsheet — `AGENTS.md` / `CLAUDE.md`

The project file is the single most-read prompt in your harness. Treat it
like code.

---

## The shape

```markdown
# <Project name>

## Identity
<One paragraph. What this is. Stack. Status.>

## Where things live
<Directory map. One line per top-level directory.>

## Commands
- install: <cmd>
- dev: <cmd>
- test: <cmd>
- typecheck: <cmd>
- lint+fix: <cmd>
- deploy: <cmd>  (or: see docs/deploy.md)

## Invariants
<3–7 lines. Things that must always be true. Each enforced by a check.>

## Conventions worth knowing
<Short. Things that differ from defaults. Things that are easy to miss.>

## When stuck
<Pointers to deeper docs. One paragraph.>
```

That is the whole template. If your file looks much longer, prune it.

---

## Include checklist

✅ Bash commands the agent could not guess.
✅ Code style rules that *differ* from language/framework defaults.
✅ The test runner. The deploy command. The lint command.
✅ Repository etiquette (branch naming, PR conventions).
✅ Architectural decisions specific to this project.
✅ Environment quirks. Required env vars (with examples).
✅ Common gotchas. Non-obvious behaviors.
✅ The **closest-file-wins** sub-files in deeper directories when conventions differ.

---

## Exclude checklist

❌ Anything the agent can read from `package.json`, `pyproject.toml`, etc.
❌ Standard language/framework conventions the agent already knows.
❌ Detailed API documentation. (Link to docs instead.)
❌ Information that changes more often than the file gets reviewed.
❌ Long explanations or tutorials.
❌ File-by-file descriptions of the codebase.
❌ Self-evident advice ("write clean code", "be helpful").

---

## Hierarchy

The closest file to the working directory wins. Use this:

| Path | Scope |
| --- | --- |
| `~/.claude/CLAUDE.md` (or equivalent) | Personal, all projects. |
| `./AGENTS.md` or `./CLAUDE.md` | Project root. Checked in. |
| `./AGENTS.local.md` or `./CLAUDE.local.md` | Project, gitignored. Personal overrides. |
| `parent/CLAUDE.md` + `parent/sub/CLAUDE.md` | Monorepo. Both pulled in. |
| `child/CLAUDE.md` | Loaded on demand when working in that directory. |

---

## Imports

Use them for things that legitimately belong elsewhere but the agent should
load on demand:

```
See @docs/architecture.md for the picture.
See @docs/decisions/ for ADRs.
Personal overrides: @~/.claude/my-project-instructions.md
```

Imports cost what their content costs when loaded. Don't import giant
files casually.

---

## The diagnostic

If the agent ignores a rule you wrote, the rule is not too weak. The file
is too long.

> *If Claude keeps doing something you don't want despite having a rule
> against it, the file is probably too long and the rule is getting lost.*
> — Anthropic

Cut. Move the rule earlier. Add `IMPORTANT:` or `YOU MUST:`. Test by
observation.

---

## The reflex

Whenever you find yourself adding a line, ask:

> *Could a program decide whether this rule was followed?*

If yes, move the rule out of the prompt and into a check (lint, type,
test, CI). The prompt loses a line. The harness gains a sensor.

> *Too much guidance becomes non-guidance.*
> — OpenAI
