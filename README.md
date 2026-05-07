# Harness Engineering

> *Agent = Model + Harness.*
> The model is rented. The harness is yours.

A cookbook for the people who steer agents — distilled from primary sources,
sharpened against the canonical voices in the field, and written to be re-read
for years.

This repository covers the three harnesses that matter most today —
[**Codex**](harnesses/codex.md), [**Claude Code**](harnesses/claude-code.md),
[**Pi**](harnesses/pi.md) — across **app, CLI, and SDK**. Below the surface
they share the same physics; above it, each has its own grain. Learn the
physics first. Then the grain.

---

## The premise

The model is the engine. Everything that surrounds it — the prompts, the
files it reads, the tools it can call, the loops it runs in, the signals it
gets back, the humans it answers to — is the **harness**.

Models keep getting better. Harnesses are how *you* get better.

> *Anytime you find an agent makes a mistake, you take the time to engineer a
> solution such that the agent never makes that mistake again.*
> — Mitchell Hashimoto, [My AI Adoption Journey](https://mitchellh.com/writing/my-ai-adoption-journey)

> *Humans steer. Agents execute.*
> — Ryan Lopopolo, OpenAI, [Harness engineering](https://openai.com/index/harness-engineering/)

---

## How to read this book

Read it in order once. After that, read it out of order forever.

| Part | What it gives you |
| --- | --- |
| **[Doctrine](doctrine/)** | The universal laws. Context, tools, loops, evals, control. The physics that hold across every harness. |
| **[Harnesses](harnesses/)** | Anatomy and idioms for Codex, Claude Code, and Pi — across app, CLI, and SDK. |
| **[Patterns](patterns/)** | Reusable moves: context shaping, tool design, memory, sub-agents, eval loops, CI as harness, human-in-the-loop, security. |
| **[Recipes](recipes/)** | Copy-pasteable workflows. Greenfield repos, legacy codebases, multi-agent crews, eval pipelines. |
| **[Reference](reference/)** | The configuration manual. Every knob in `~/.claude/`, `~/.codex/`, `~/.pi/`. Schemas, paths, precedence. Dated. |
| **[Anti-patterns](anti-patterns.md)** | The mistakes that waste tokens, time, and trust. |
| **[Canon](canon/)** | An annotated reading list. The ~40 sources behind every claim in this book. |
| **[Appendix](appendix/)** | Cheatsheets, config schemas, the primary-source index. |

---

## The thirteen laws

Memorize these. The rest of the book is commentary.

1. **Agent = Model + Harness.** Improve the harness.
2. **Humans steer. Agents execute.** Never invert it.
3. **Context is a scarce resource.** Spend it like money.
4. **Find the smallest set of high-signal tokens.** Then stop.
5. **Give a map, not a manual.** Pointers beat instructions.
6. **Enforce invariants. Do not micromanage implementations.**
7. **Guides anticipate. Sensors correct.** Use both.
8. **Tools are a contract with a non-deterministic caller.** Design accordingly.
9. **No eval, no feature.** What you cannot measure, you cannot ship.
10. **Look at your data.** Remove every gram of friction between you and a real trace.
11. **Loops compound. Heroics don't.** Build the loop.
12. **Trust nothing untrusted reaches the model.** Assume injection.
13. **The harness is code.** Review it. Prune it. Test it. Commit it.

---

## A note on voice

Every line in this book is meant to earn its place. If a sentence does not
sharpen your thinking or change what you do tomorrow, treat it as a bug and
delete it. We borrow the cadence of Sun Tzu on purpose: the field is moving
fast, and aphorisms survive better than paragraphs.

When we quote, we quote primary sources. When we point, we point at the canon.
When we hedge, we say so.

> *Demo is `works.any()`. Product is `works.all()`.*
> — Andrej Karpathy

Build the harness for the second one.

---

## License

MIT. Steal freely. Send patches.
