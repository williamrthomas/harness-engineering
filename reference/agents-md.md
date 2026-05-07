# AGENTS.md: Cross-Vendor Reference

Verified 2026-05-07 against https://agents.md.

One file. Many readers. The shared instruction document for coding agents.

## What It Is

A Markdown file at the root (or any ancestor directory) of a project that gives an AI coding agent the project specific guidance it needs: how to run tests, what conventions matter, what to avoid, where the architecture lives. The format is intentionally plain prose. There is no required schema.

## Who Reads It

As of this writing, AGENTS.md is read by:

- OpenAI Codex CLI
- Cursor
- Aider
- Jules
- Amp
- Factory
- GitHub Copilot
- Windsurf
- Pi (via the `agent/AGENTS.md` and project `AGENTS.md` walk)

Claude Code reads `CLAUDE.md` natively. Codex can fall back to `CLAUDE.md` via `project_doc_fallback_filenames`. Many projects symlink or duplicate.

## Resolution Rules

The general pattern across harnesses:

1. Walk from repo root toward the current working directory.
2. Concatenate every `AGENTS.md` found, separated by blank lines.
3. Closer directories appear later, effectively overriding earlier guidance on conflict.
4. Many harnesses also load a global `~/.<harness>/AGENTS.md`.

Codex specific layering:

- Global: `~/.codex/AGENTS.override.md` if non empty, else `~/.codex/AGENTS.md`.
- Project: per directory, `AGENTS.override.md` first, then `AGENTS.md`, then `project_doc_fallback_filenames` (e.g., `CLAUDE.md`).
- Total bytes capped by `project_doc_max_bytes` (32 KiB default).

Pi specific layering:

- Global: `~/.pi/agent/AGENTS.md` (or `$PI_CODING_AGENT_DIR/AGENTS.md`).
- Project: walk from cwd to repo root, all `AGENTS.md` files concatenated.

## What to Put Inside

Useful sections, in rough order:

1. **Project orientation**. One paragraph: what the repo is, what it produces.
2. **Run commands**. Test, lint, build, dev server. Exact commands.
3. **Conventions**. Naming, file layout, language style.
4. **Architecture pointers**. Where to look first. Which files are load bearing.
5. **Dangerous areas**. Code the agent must not edit without explicit approval.
6. **Definition of done**. What a complete change looks like (tests pass, types pass, changelog updated).
7. **Out of scope**. Things this repo does not do.

Keep it short. The file consumes context tokens on every turn.

## What to Leave Out

- Secrets. The file is read by the model and may be logged.
- Volatile information. Pin tickets and PR numbers go stale.
- Marketing prose. The agent does not need a pitch.
- Long code samples. Link to source files instead.

## Override Files

Where supported (Codex, some forks), `AGENTS.override.md` is the per machine personal layer. Useful when you want to tell the agent something that shouldn't ship to the team:

> "Use my fork of internal-cli at /Users/me/code/internal-cli when running tests."

Treat overrides as gitignored.

## Common Patterns

### Minimal project AGENTS.md

```markdown
# Agent Instructions

This is a Next.js + Supabase app for healthcare data training.

## Run

- Install: `pnpm install`
- Dev: `pnpm dev`
- Test: `pnpm test`
- Lint: `pnpm lint`

## Conventions

- TypeScript strict.
- Tailwind for styles.
- Server components by default.

## Definition of done

- Tests pass.
- Types pass.
- No new ESLint warnings.

## Do not touch

- `supabase/migrations/` without explicit approval.
- `infra/terraform/` without an Ops review.
```

### Layered AGENTS.md in a monorepo

Place a root `AGENTS.md` with global conventions. Place per package `AGENTS.md` in `packages/<name>/` with package specific instructions. Harnesses concatenate them automatically when the agent works inside a package.

### CLAUDE.md interop

If the team also uses Claude Code, the simplest path is one of:

1. Symlink `CLAUDE.md` to `AGENTS.md`.
2. Duplicate content (keep them in sync via a script).
3. Configure Codex `project_doc_fallback_filenames = ["CLAUDE.md"]` and write only `CLAUDE.md`.

Symlinks are the cleanest in practice.

## Gotchas

- Byte caps truncate silently. Watch deep monorepos where concatenation exceeds the harness limit.
- Conflicting instructions across nested files create confusion. The closest file wins, but the agent sees the contradiction. Resolve in writing.
- AGENTS.md is not a permission system. It is guidance. Enforce real rules in the harness sandbox/approvals layer.
- A bloated AGENTS.md eats tokens on every turn. Treat brevity as a feature.

## Vendor changelog

Re-verify against https://agents.md and each harness's project doc handling before relying on edge case behavior.
