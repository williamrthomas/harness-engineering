# 05 — Invariants over instructions

> *By enforcing invariants, not micromanaging implementations, we let agents
> ship fast.*
> — Ryan Lopopolo, [Harness engineering](https://openai.com/index/harness-engineering/)

> *Too much guidance becomes non-guidance.*
> — ibid.

An invariant is something that must always be true. An instruction is
something the agent should do. The two collapse beginners and separate experts.

---

## What an invariant looks like

- "All public functions have type signatures." (Enforced by the typechecker.)
- "No file exceeds 500 lines." (Enforced by a lint rule.)
- "Every endpoint has a contract test." (Enforced by CI.)
- "All money values are `Decimal`, never `float`." (Enforced by a custom check.)
- "Migrations are reversible." (Enforced by a CI gate that runs `down` then `up`.)

Notice three properties:

1. The invariant is **short**. One line.
2. The invariant is **checkable**. A program can decide.
3. The invariant **does not say how**. It says *what must be true at the end*.

An invariant cuts the agent's solution space. It does not narrow the path
inside the cut.

## What an instruction looks like

- "Use `useState` for component state."
- "Loop with `for...of` instead of `forEach`."
- "Always import lodash as `_`."
- "Write tests using `describe` and `it`, not `test`."

These can be useful. They can also be noise. Two failure modes:

**Inflation.** A team writes ten such instructions, then thirty, then a
hundred. The `AGENTS.md` becomes a wiki. The model loses the thread. Rules
get ignored at random.

**Calcification.** The team upgrades the framework. The instruction is now
wrong. The agent obeys it anyway. The harness has become a museum.

> *Too much guidance becomes non-guidance.*

Every instruction must justify its line. If you can replace it with an
invariant, replace it.

---

## The conversion

Walk every rule in your `AGENTS.md` or `CLAUDE.md` and ask:

> *Can a program decide whether this rule was followed?*

If yes, move the rule out of the prompt and into a check. The prompt loses a
line. The harness gains a sensor. The agent gains a tighter steering loop.

| Instruction (before) | Invariant (after) |
| --- | --- |
| "Always handle errors." | `eslint-plugin-promise` rule + `no-throw-literal`. |
| "Don't use `any`." | `tsconfig: noImplicitAny: true`. |
| "Write a test for every endpoint." | CI step: count endpoints, count tests, fail if mismatch. |
| "Format with Prettier." | Pre-commit hook + CI check. |
| "All migrations have a `down`." | Lint script over `migrations/`. |

The `AGENTS.md` shrinks. Behavior tightens. Drift becomes impossible.

---

## When instructions are right

Three cases keep an instruction in the prompt:

1. **Genuinely fuzzy.** "Prefer concise variable names over abbreviations" —
   no checker exists.
2. **Contextual style.** "When writing user-facing copy, prefer plain language" —
   a model judges this better than a regex.
3. **Onboarding to a quirk.** "We deploy on Mondays only." — a fact about the
   world, not a rule about code.

Even then, keep them short. Prefer one line over five. Prefer concrete
examples over abstract advice.

---

## The corollary for humans

This rule applies to your code reviews of the agent, too. If you find
yourself leaving the same comment on three PRs, do not leave it a fourth time.
Encode it. Either as a guide (in the prompt) or, better, as a sensor (in the
linter, in CI, in a test).

The harness is how your team's expertise outlives your team's attention.

---

**Next:** [06 — Tools are a contract](06-tools-are-a-contract.md)
