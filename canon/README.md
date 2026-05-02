# Canon

An annotated reading list. Forty-odd sources, each earning its slot. For
every source: *what it teaches*, *why it matters*, *one line to remember*.

The canon is organized by tier. Tier 1 is required reading; you cannot
practice harness engineering without these. Tier 2 is the deep bench —
sharpens the doctrine, opens new moves, settles arguments. Tier 3 is the
context — the long-arc thinking that makes the rest legible.

---

## Tier 1 — Required reading

### [Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/)
**Ryan Lopopolo, OpenAI · 2026-02-11**

The post that named the field. A team shipped a real product with zero
hand-written code over five months. Documents the experiment, the lessons,
and the principles.

- **Teaches:** Map vs manual. Invariants vs micromanagement. Per-worktree
  isolation. Agent-driven observability. The Ralph Wiggum Loop. Push every
  constraint left.
- **Why it matters:** The most concrete primary source on what production
  harness engineering looks like at scale.
- **Remember:** *"Humans steer. Agents execute."*

### [Harness engineering for coding agent users](https://martinfowler.com/articles/harness-engineering.html)
**Birgitta Böckeler, Thoughtworks · 2026-04-02**

The taxonomy paper. Distinguishes *guides* from *sensors*, *computational*
from *inferential* controls, three flavors of harness (maintainability,
architecture, behavior). Cybernetics-flavored framing.

- **Teaches:** Feedforward vs feedback. The steering loop. Where to put
  controls. Ashby's Law applied to LLM agents.
- **Why it matters:** Gives you a language to *describe* what your harness
  does. Crucial when the harness becomes complex.
- **Remember:** *"Agent = Model + Harness."*

### [My AI Adoption Journey](https://mitchellh.com/writing/my-ai-adoption-journey)
**Mitchell Hashimoto · 2025**

The personal narrative that contains the single most important sentence in
the field.

- **Teaches:** The harness mindset. The discipline of engineering out
  mistakes. Drop the chatbot. Build feedback loops.
- **Why it matters:** Rare to find a primary source that is both this
  honest and this load-bearing.
- **Remember:** *"Anytime you find an agent makes a mistake, you take the
  time to engineer a solution such that the agent never makes that mistake
  again."*

### [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
**Anthropic · 2025**

The canonical statement on context as a scarce resource. Just-in-time
retrieval. Hybrid retrieval. Compaction. Sub-agent architectures.

- **Teaches:** Token economics. The four levers (prune, compress, retrieve
  JIT, externalize). Why "smallest set of high-signal tokens" beats "more
  context."
- **Why it matters:** Every harness eventually wins or loses on context
  discipline.
- **Remember:** *"Find the smallest set of high-signal tokens that maximize
  the likelihood of the desired outcome."*

### [Writing effective tools for AI agents — using AI agents](https://www.anthropic.com/engineering/writing-tools-for-agents)
**Anthropic · 2025**

The five tool-design principles. Test tools with agents. Iterate.

- **Teaches:** Tools as contracts with non-deterministic callers.
  Namespacing. Returning meaningful context. Tool descriptions are prompts.
- **Why it matters:** Tool design is where most harnesses leak performance.
- **Remember:** *"Tools are a new kind of software which reflects a contract
  between deterministic systems and non-deterministic agents."*

### [Best Practices for Claude Code](https://www.anthropic.com/engineering/claude-code-best-practices)
**Anthropic · 2025**

The canonical operations doc for Claude Code. CLAUDE.md hygiene, permission
modes, slash commands, the explore-plan-code workflow, hooks.

- **Teaches:** How to live with a coding agent day to day. What to put in
  the prompt and what not to.
- **Why it matters:** Most directly applicable doc to "I am using a coding
  agent today and want to be better at it."
- **Remember:** *"Treat CLAUDE.md like code: review it when things go wrong,
  prune it regularly, and test changes by observing whether Claude's
  behavior actually shifts."*

### [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
**Anthropic · 2025 (open standard, December 2025)**

The launch and standardization of Agent Skills. Three-level progressive
disclosure. The shape of reusable agent expertise.

- **Teaches:** How long-tail knowledge lives outside the system prompt
  without being inert.
- **Why it matters:** Skills are the cleanest mechanism we have for
  knowledge that *applies sometimes*.
- **Remember:** *Metadata pre-loads; body loads on demand; bundled files
  load on reference. Three levels.*

### [Your AI Product Needs Evals](https://hamel.dev/blog/posts/evals/)
**Hamel Husain · 2024**

The evals post. Three levels — unit / model+human / A/B. Cost inverts with
cadence. Look at the data.

- **Teaches:** What an eval suite is for, how to build one, how to maintain
  it, how to make data inspection effortless.
- **Why it matters:** The single highest-leverage skill in applied AI, and
  the most under-practiced.
- **Remember:** *"Remove ALL friction from looking at data."*

### [Design Patterns for Securing LLM Agents against Prompt Injections](https://simonwillison.net/2025/Jun/13/prompt-injection-design-patterns/)
**Simon Willison · 2025**

The catalog. Six design patterns: Action-Selector, Plan-Then-Execute,
Map-Reduce, Dual LLM, Code-Then-Execute, Context-Minimization. Plus the
lethal trifecta and the Agents Rule of Two.

- **Teaches:** How to architect agents that survive contact with the
  internet.
- **Why it matters:** Injection is a property of LLMs, not a vulnerability.
  Architect, don't patch.
- **Remember:** *Lethal trifecta: private data + untrusted content +
  external communication.*

### [AGENTS.md](https://agents.md)
**Agentic AI Foundation (Linux Foundation) · open standard**

The cross-vendor specification. Closest file wins. Used by Codex, Cursor,
Aider, Jules, Amp, Factory, and many others.

- **Teaches:** A common, predictable place for agent-facing instructions.
  Keeps READMEs for humans, AGENTS.md for agents.
- **Why it matters:** Standards reduce the cognitive cost of working across
  agents.
- **Remember:** *Closest AGENTS.md wins. Hierarchy is by directory.*

### [Pi — Minimal terminal coding harness](https://pi.dev)
**Mario Zechner · ongoing**

The opposite-pole harness. Primitives, not features. Self-modifying. Four
modes (Interactive, Print/JSON, RPC, SDK). Explicit list of non-features.

- **Teaches:** What a harness looks like when it refuses to dictate
  workflow. The discipline of small primitives + sharp extension points.
- **Why it matters:** Pi makes the "Agent = Model + Harness" decomposition
  literal. You bring the harness.
- **Remember:** *"Change the harness, not your workflow."*

### [Andrej Karpathy on Software 3.0](https://www.latent.space/p/s3)
**swyx (Latent Space), with Andrej Karpathy · 2025**

The framework that makes the field legible. Software 1.0 / 2.0 / 3.0. LLMs
as utilities, fabs, OSes. Anterograde amnesia. System prompt learning.
Iron Man Suits, not AGI demos. Build for agents.

- **Teaches:** The macro picture. Why current limits exist. Where they
  bend.
- **Why it matters:** When you don't know what to build next, this is the
  source that re-orients.
- **Remember:** *"Demo is `works.any()`. Product is `works.all()`."*

---

## Tier 2 — Sharpening the practice

### [Karpathy: Software 2.0](https://karpathy.medium.com/software-2-0-a64152b37c35)
**Andrej Karpathy · 2017**

The original essay. Reads as prophetic eight years later.

- **Remember:** Code that learns from data is a different substance than
  code that runs on it.

### [Karpathy: Anterograde amnesia (Twitter)](https://twitter.com/karpathy/status/1869566048461066383)
**Andrej Karpathy**

The metaphor that makes memory engineering feel inevitable.

- **Remember:** *"50 First Dates. Memento. The agent forgets."*

### [Karpathy: System prompt learning (Twitter)](https://twitter.com/karpathy/status/1921368644069765486)
**Andrej Karpathy · 2025**

A possibly-load-bearing claim about a missing paradigm.

- **Remember:** *"Pretraining is for knowledge. Finetuning is for habitual
  behavior. System prompts may be for something else."*

### [Karpathy: Build for agents (Twitter)](https://twitter.com/karpathy/status/1933299772185514083)
**Andrej Karpathy · 2025**

The exhortation that changed how teams write docs.

- **Remember:** *"BUILD FOR AGENTS."*

### [Simon Willison: weblog](https://simonwillison.net)
**Simon Willison · ongoing**

If a piece of LLM news matters, Simon writes it up clearly within 48 hours.
Subscribe.

- **Remember:** *Read the primary source; trust the careful summarizer.*

### [Anthropic: Agentic Misalignment](https://www.anthropic.com/research/agentic-misalignment)
**Anthropic Alignment**

What happens when agents have goals, tools, and trust they shouldn't.

- **Remember:** *Capability and alignment are not the same axis.*

### [Anthropic Engineering blog](https://www.anthropic.com/engineering)
**Anthropic · ongoing**

The deepest single source on how to engineer with frontier models. Read
everything.

- **Remember:** *Engineering posts beat marketing posts.*

### [OpenAI: Codex documentation](https://platform.openai.com/docs/codex)
**OpenAI**

The official surface. SDK reference, CLI docs, app docs.

- **Remember:** *Read the docs you're going to use.*

### [Hamel Husain: blog](https://hamel.dev)
**Hamel Husain · ongoing**

The applied-AI practitioner blog with the highest signal-to-noise ratio
outside the labs.

- **Remember:** *Look at the data. Build the dashboard. Reduce the
  friction.*

### [Latent Space podcast](https://www.latent.space)
**swyx, Alessio · ongoing**

Long-form interviews with the people shipping. Episodes age fast; the good
ones age into canon.

- **Remember:** *Listen to the people who ship.*

### [Sundar's "AI is the new electricity" / Andrew Ng on AI as utility](https://www.youtube.com/results?search_query=andrew+ng+ai+is+the+new+electricity)
**Andrew Ng · 2017**

Long-arc framing. Compares to context that is decades, not months.

- **Remember:** *Most general-purpose technologies look like toys for a
  decade.*

### [The Bitter Lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html)
**Rich Sutton · 2019**

The 70-year arc of AI: methods that scale beat methods that encode human
insight. The single most cited essay in modern AI.

- **Remember:** *"The biggest lesson... is that general methods that
  leverage computation are ultimately the most effective."*

### [Pieter Abbeel / Sergey Levine: lectures](https://rail.eecs.berkeley.edu)
**RAIL Lab, Berkeley · ongoing**

Robotics-and-RL lens on agency. Useful counterpoint to LLM-only thinking.

- **Remember:** *Embodiment and tools are not so different.*

### [DSPy (Stanford)](https://dspy.ai)
**Omar Khattab et al.**

A framework for systematically optimizing prompts and tool use against
metrics. The serious version of "prompt engineering as compilation."

- **Remember:** *If you can specify the metric, you can optimize the
  prompt.*

### [LangChain documentation, especially LangGraph](https://www.langchain.com)
**LangChain · ongoing**

Even if you don't use it, read the abstractions. Many of the harness moves
in this book are documented as LangGraph patterns.

- **Remember:** *Patterns travel; framework choices don't have to.*

### [The Twelve-Factor App](https://12factor.net)
**Adam Wiggins · 2011**

Pre-AI, but every factor maps onto something a harness needs to handle:
config, dependencies, processes, port binding, logs.

- **Remember:** *Older operational wisdom translates.*

### [Designing Data-Intensive Applications](https://dataintensive.net)
**Martin Kleppmann · 2017**

When the agent reasons about a system, the system's properties matter.
Kleppmann gives you the vocabulary.

- **Remember:** *The agent inherits whatever your data layer can promise.*

### [Site Reliability Engineering (Google book)](https://sre.google/sre-book/table-of-contents/)
**Google SRE · 2016**

SLO thinking and error budgets are how to *measure* whether the harness is
holding the agent to a useful standard.

- **Remember:** *If you don't have an SLO, you have a vibe.*

### [The Pragmatic Programmer](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/)
**Hunt & Thomas, 20th anniversary edition**

DRY, orthogonality, source as truth. All translate cleanly to the
agent-as-collaborator setting.

- **Remember:** *Old craft, new tools.*

### [How to do Research at the MIT AI Lab](https://dspace.mit.edu/handle/1721.1/41487)
**David Chapman et al. · 1988**

Older than most readers. Still the best practical guide to thinking when
the unknown unknowns dominate.

- **Remember:** *Taste develops by doing the thing badly, repeatedly,
  in public.*

---

## Tier 3 — The long arc

### [Sun Tzu, *The Art of War*](https://en.wikisource.org/wiki/The_Art_of_War)
**Sun Tzu · ~5th century BCE, Giles trans.**

The cadence this book borrows from. Aphoristic. Concrete. Unsentimental.

- **Remember:** *"The supreme art of war is to subdue the enemy without
  fighting."* Replace *enemy* with *retry loop* and you have a doctrine.

### [Marcus Aurelius, *Meditations*](https://en.wikisource.org/wiki/The_Meditations_of_the_Emperor_Marcus_Aurelius)
**Marcus Aurelius**

Book-keeping for the mind. Useful when watching agents work.

- **Remember:** *"You have power over your mind — not outside events."*

### [Don Norman, *The Design of Everyday Things*](https://en.wikipedia.org/wiki/The_Design_of_Everyday_Things)
**Donald A. Norman · 1988**

Affordances and signifiers. Tools that hint at how to be used. Maps onto
tool descriptions exactly.

- **Remember:** *Affordances reduce errors more than instructions do.*

### [Christopher Alexander, *A Pattern Language*](https://en.wikipedia.org/wiki/A_Pattern_Language)
**Alexander, Ishikawa, Silverstein · 1977**

Where the term *pattern* comes from. A vocabulary of recurring solutions.
Inspiration for both software design patterns and the patterns chapter of
this book.

- **Remember:** *A pattern names a piece of recurring wisdom; once named,
  it travels.*

### [Norbert Wiener, *Cybernetics*](https://en.wikipedia.org/wiki/Cybernetics_(book))
**Norbert Wiener · 1948**

Feedback systems before computers were universal. Böckeler's *guides and
sensors* are pure Wiener.

- **Remember:** *Control is feedback.*

### [Stewart Brand, *How Buildings Learn*](https://en.wikipedia.org/wiki/How_Buildings_Learn)
**Stewart Brand · 1994**

Pace layers. Things that change at different rates. Maps perfectly onto
*model (fast) vs harness (slower) vs codebase (slower) vs team conventions
(slowest).*

- **Remember:** *The slow constrains the fast.*

### [W. Edwards Deming, *Out of the Crisis*](https://en.wikipedia.org/wiki/Out_of_the_Crisis)
**W. Edwards Deming · 1982**

Process > heroics. Variance > mean. Quality is engineered, not inspected
in.

- **Remember:** *"It is not enough to do your best; you must know what to
  do, and then do your best."*

---

## How to use this canon

Read Tier 1 in order. Re-read it twice a year.

Pick from Tier 2 by the problem at hand. Tool design hard? Read the
Anthropic posts. Eval suite weak? Read Hamel. Security on the table? Read
Simon.

Keep Tier 3 for the times you feel lost. The long-arc thinkers help when
the field's noise is loudest.

When you find a new source worth a slot, *write the annotation in this
shape*: what it teaches, why it matters, one line to remember. The shape
is the discipline.
