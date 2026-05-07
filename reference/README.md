# Reference

The configuration manual.

The doctrine teaches why. The harness chapters teach shape. The reference teaches every knob.

This layer is for the operator who wants to know what `permissions.defaultMode` accepts, where Codex looks for `AGENTS.md`, which env var a Pi extension reads. Not feature catalogs (those live in vendor docs and rot fast). Configuration mechanics: file paths, schemas, precedence, gotchas.

## Files

- [claude-code.md](claude-code.md). Anthropic Claude Code. `~/.claude/`, `settings.json`, hooks, skills, MCP.
- [codex.md](codex.md). OpenAI Codex CLI. `~/.codex/config.toml`, profiles, sandboxing, approvals.
- [pi.md](pi.md). Mario Zechner's Pi coding agent. `~/.pi/agent/`, packages, extensions, themes, providers.
- [agents-md.md](agents-md.md). The cross-vendor `AGENTS.md` format. One file, many readers.

## Dating Policy

Configuration drifts. Each reference file carries a dated header:

```
Verified YYYY-MM-DD against <harness> <version>
```

Re-verify before quoting. The vendor doc is canonical. This repo lags by design, so the reader sees what was true on a known date rather than what the model guessed today.

## What Belongs Here

- File and directory layout.
- Full config schemas (TOML, JSON, frontmatter).
- Precedence rules (which config wins).
- CLI flag highlights that change harness behavior.
- Hook, skill, command, extension structure.
- Provider resolution and credential paths.
- Gotchas observed in the field.

## What Does Not

- Feature announcements. Link to the changelog.
- Pricing. Link to the vendor.
- Tutorials. The recipes/ directory holds workflows.
- Opinions. The doctrine/ directory holds those.

A reference is a map, not a sermon. Read it when you need to know exactly where a knob lives.
