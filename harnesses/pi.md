# Pi

> *Pi is a minimal terminal coding harness. Adapt Pi to your workflows, not
> the other way around.*
> — [pi.dev](https://pi.dev)

> *Primitives, not features.*
> — ibid.

Pi is the third harness, and the most opinionated about its own minimalism.
Where Codex aims for autonomy and Claude Code aims for companionship, **Pi
aims for hackability**. The defaults are sparse. The extension surface is
wide. The slogan is exact: *change the harness, not your workflow.*

Pi is the right harness when you want to *build the harness* — when the
out-of-the-box feature set of any other agent feels like a cage, however
gilded.

---

## The four modes

Pi exposes the same agent through four very different interfaces. Choose by
the work, not by the habit.

| Mode | What it is | Best for |
| --- | --- | --- |
| **Interactive** | The full terminal UI (TUI). | Hands-on coding, exploration, pair work. |
| **Print / JSON** | `pi -p "query"` for scripts; `--mode json` for event streams. | Shell pipelines, cron jobs, glue code. |
| **RPC** | JSON protocol over stdin/stdout. | Non-Node integrations, embedding into other tools. |
| **SDK** | Embed Pi as a library. | Building your own agent product on top of Pi. |

A single Pi installation moves between all four without ceremony. Treat them
as one harness with four mouths.

---

## Extensibility primitives

Pi exposes five primitives, deliberately small and deliberately composable.

- **Extensions.** TypeScript modules with access to tools, commands,
  keyboard shortcuts, events, and the full TUI.
- **Skills.** Capability packages with instructions and tools, loaded on
  demand. Same progressive-disclosure shape as the open standard.
- **Prompt templates.** Reusable prompts as Markdown files. Type `/name` to
  expand.
- **Themes.** Cosmetic customization.
- **Packages.** Bundles of any of the above, distributed via `npm` or
  `git`. Install with `pi install npm:@foo/pi-tools` or
  `pi install git:github.com/badlogic/pi-doom`.

The shape repeats: a *small core* + *strong extension hooks* + *a package
ecosystem*. Build it, share it, install it.

---

## Explicit non-features

Pi is unusually direct about what it does **not** ship. This list is the
single best clue to its philosophy:

- **No sub-agents.** Spawn Pi instances via `tmux`, or build your own with an
  extension, or install a package that does it your way.
- **No MCP.** Build CLI tools with READMEs (skills), or build an extension
  that adds MCP support if you need it.
- **No plan mode.** Write plans to files, or build it with extensions.
- **No permission popups.** Run in a container, or build your own
  confirmation flow inline with your environment and security needs.
- **No built-in to-dos.** Use a `TODO.md` file, or build your own.
- **No background bash.** Use `tmux`. Full observability, direct interaction.

Read this list and pick a side. Either Pi's minimalism makes you happy or it
makes you nervous. Both reactions are reasonable. Both predict whether Pi is
the right harness for the job.

---

## Self-modification

The most distinctive Pi idiom:

> *If you need a command, tool, provider, workflow, or UI tweak, just ask Pi
> to build it. It will customize itself on the fly. Have Pi manipulate
> itself in place, hit `/reload`, and keep going.*

This makes Pi the only harness in this book that *expects to be rewritten by
the agent inside it.* The implication: Pi sessions tend to drift toward your
particular shape over time. The harness becomes increasingly yours.

If you build something useful, package it. The 50+ existing Pi packages are a
reasonable starting library; many were built this way.

---

## Provider stance

> *15+ providers, hundreds of models.*

Pi is provider-agnostic. Anthropic, OpenAI, Google, Azure, Bedrock, Mistral,
Groq, Cerebras, xAI, Hugging Face, OpenRouter, Ollama, and others. Switch
mid-session with `/model` or `Ctrl+L`. Cycle favorites with `Ctrl+P`.

This is a feature, not a flex. Different models suit different turns of the
loop: a fast cheap model for bulk edits, a strong reasoner for plans, a local
model for sensitive code. A harness that pins you to one provider misses
those turns.

---

## A note on Pi vs Pi Labs

Two different products share a syllable. Don't confuse them.

- **Pi (pi.dev)** — the minimal terminal coding harness described above.
- **Pi Labs (withpi.ai)** — a *scoring* product for evaluating LLM outputs.
  Small encoder model, deterministic, sub-100ms, 20+ scoring dimensions.
  Useful as a Level 2 eval signal alongside or instead of LLM-as-judge.

Both are good. They are not the same thing. This chapter is about pi.dev.

---

## Idioms unique to Pi

- **Modes by intent.** Use Interactive for live work, Print/JSON for
  scripting, RPC for embedding, SDK for product-building.
- **Build it, don't request it.** Missing a feature? Build the extension.
- **Packages as memes.** Skills and extensions are the unit of sharing.
  Publish to npm or push a repo.
- **`tmux` as the orchestrator.** Pi expects you to bring your own
  parallelism. `tmux` panes, `tmux send-keys`, `tmux capture-pane` are the
  right vocabulary.
- **Plain files as state.** No special to-do system. No magic plan mode.
  Just files. The agent reads them, writes them, follows them.

---

## Where Pi shines, where to be careful

**Shines:**
- Building your own agent product. The SDK and RPC modes are clean.
- Workflows that don't fit any other agent's mold. Customize, don't fight.
- Multi-provider, model-shopping setups.

**Be careful:**
- The minimalism is real. If you don't enjoy building primitives, you'll feel
  it. Pick Codex or Claude Code instead.
- "Run in a container" and "no permission popups" together require *you* to
  build the safety. Don't skip it.
- Self-modifying sessions are powerful and easy to lose track of. Use git.

---

## Further reading

- [pi.dev](https://pi.dev) — overview, modes, primitives.
- [pi-labs (withpi.ai)](https://withpi.ai) — different product, useful for evals.
- See [Patterns](../patterns/) for sub-agent, eval, and tool-design techniques
  that you will, by Pi's design, build for yourself.
