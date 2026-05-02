# 01 — Agent = Model + Harness

> *The term harness has emerged as a shorthand to mean everything in an AI
> agent except the model itself — Agent = Model + Harness.*
> — Birgitta Böckeler, [Harness engineering for coding agent users](https://martinfowler.com/articles/harness-engineering.html)

The model is the engine. The harness is the rest of the car.

The model is the part you do not own. You rent it from a frontier lab. It will
be replaced next quarter by something cheaper, faster, and less polite. Build
nothing on the assumption that today's model is the model you will use in six
months.

The **harness** is the part you do own. It is:

- The system prompt and the project files the model reads on startup.
- The tools you let it call.
- The loop it runs in.
- The signals it gets back when it is wrong.
- The humans, gates, and approvals along the way.
- The memory across sessions.
- The CI, the linters, the typecheckers, the tests.
- The repo layout that makes the right thing easy and the wrong thing hard.

The harness is where engineering happens. The model is where magic happens.
Do not confuse the two.

---

## The Hashimoto rule

The single most important sentence in this book belongs to Mitchell Hashimoto:

> *Anytime you find an agent makes a mistake, you take the time to engineer a
> solution such that the agent never makes that mistake again.*
> — [My AI Adoption Journey](https://mitchellh.com/writing/my-ai-adoption-journey)

That is the entire job. Every chapter that follows is a way of doing it.

A mistake is not a failure of the model. It is a failure of the harness to
prevent the mistake. Re-prompting is a tax. Engineering the harness is an
investment. Always pay the investment. Never let yourself pay the tax twice.

---

## Why this framing wins

Three reasons the *Agent = Model + Harness* framing is load-bearing:

1. **It separates rented from owned.** The model gets better on its own.
   The harness only gets better if you build it.
2. **It makes the work legible.** "Make the agent better" is a wish.
   "Add a sensor that catches X" is a ticket.
3. **It compounds.** A harness that catches one class of mistake catches it
   forever, across every future model, for every future task.

---

## The corollary

If a teammate's harness is better than yours, their agent is better than yours.
The model is the same. The leverage is in the surrounding code.

---

**Next:** [02 — Humans steer, agents execute](02-humans-steer-agents-execute.md)
