# Pi: Configuration Reference

Verified 2026-05-07 against Pi coding agent (Mario Zechner, pi.dev).

Primary sources:
- Home: https://pi.dev
- Usage: https://pi.dev/docs/latest/usage
- Skills: https://pi.dev/docs/latest/skills
- Extensions: https://pi.dev/docs/latest/extensions
- Themes: https://pi.dev/docs/latest/themes
- Providers: https://pi.dev/docs/latest/providers
- Packages: https://pi.dev/packages

## File Layout

User scope, default `~/.pi/agent/`, override with `PI_CODING_AGENT_DIR`:

```
~/.pi/
  agent/
    AGENTS.md            # global agent instructions
    SYSTEM.md            # replace or append default system prompt
    skills/<name>/SKILL.md
    extensions/<name>/index.ts
    themes/<name>.json
    prompts/
  auth.json              # credentials
  models.json            # provider and model registry
```

Project scope:

```
<repo>/
  AGENTS.md              # project agent instructions, walked from root toward cwd
  SYSTEM.md              # project system prompt addition
  .agents/skills/        # cross tool skills shared with Claude/Codex
```

Cross tool skills also resolve from `~/.agents/skills/`. The `.agents/` convention is intentional: same directory works for any harness that follows the Agent Skills spec.

## Precedence and Resolution

`PI_CODING_AGENT_DIR` overrides `~/.pi/agent/`. Project AGENTS.md files concatenate from root toward cwd, closer wins on conflict. SYSTEM.md replaces or appends the harness default depending on extension declarations (see Extensions: `resources_discover`).

## Skills

Same Agent Skills frontmatter as Claude Code and Codex:

```yaml
---
name: deploy-check
description: Run deployment preflight. Use before pushing to production.
license: MIT
compatibility: "claude-code, codex, pi"
metadata:
  owner: platform-team
allowed-tools: ["Bash", "Read"]
disable-model-invocation: false
---
```

Required: `name` (≤64 chars, lowercase a-z, digits, hyphens, must match parent directory name), `description` (≤1024 chars, drives autonomous invocation).

Optional:
- `license`. SPDX identifier.
- `compatibility`. Free text, ≤500 chars.
- `metadata`. Free form object.
- `allowed-tools`. Experimental, restricts which tools the skill may call.
- `disable-model-invocation`. If true, skill runs only on explicit `/name` call.

Resolution order: `~/.pi/agent/skills/`, then `~/.agents/skills/`, then `.agents/skills/` walked from cwd to repo root.

## Extensions

TypeScript modules in `~/.pi/agent/extensions/<name>/index.ts`. Import the typed API:

```ts
import { ExtensionAPI } from "@mariozechner/pi-coding-agent";

export default function activate(pi: ExtensionAPI) {
  pi.on("session_start", (ctx) => {
    ctx.ui.setStatus("ready");
  });

  pi.registerTool({
    name: "lint",
    description: "Run repo linter",
    handler: async (input, ctx) => {
      const out = await ctx.shell("npm run lint");
      return { ok: out.exitCode === 0, output: out.stdout };
    }
  });

  pi.registerCommand({
    name: "/deploy",
    handler: async (args, ctx) => { /* ... */ }
  });

  pi.registerShortcut({ key: "ctrl+l", action: "clear" });
  pi.registerFlag({ name: "--strict", description: "Strict mode" });
}
```

### Events

- `session_start`. Once per session.
- `tool_call`. Before/after a tool invocation; payload includes name, input, output.
- `before_provider_request`. Mutate or veto outbound model calls.
- `resources_discover`. Return additional resources Pi should load: `{ skillPaths, promptPaths, themePaths }`.

### Context object

`ctx.ui` exposes `notify`, `confirm`, `setStatus`, `setWidget`. `ctx.shell(cmd)` runs shell with the harness sandbox. `ctx.fs` reads/writes within sandbox roots.

## Themes

JSON files in `~/.pi/agent/themes/<name>.json`. Use the schema URL for editor validation:

```json
{
  "$schema": "https://raw.githubusercontent.com/badlogic/pi-mono/main/packages/coding-agent/src/modes/interactive/theme/theme-schema.json",
  "name": "deep-ocean",
  "colors": {
    "core": { /* 11 mandatory tokens */ },
    "thinking": { /* 6 tokens */ },
    "bashMode": { /* 1 token */ },
    "syntax": { /* ... */ },
    "markdown": { /* ... */ },
    "toolDiff": { /* ... */ }
  }
}
```

Fifty one mandatory color tokens across categories: Core UI (11), thinking (6), bashMode (1), syntax, markdown, tool diff, plus a few ancillary groups. The schema enforces all required keys; rely on it.

## Providers

`models.json` declares providers and the models each one exposes. `auth.json` stores keys.

### Resolution order for credentials

1. CLI `--api-key <key>`.
2. `auth.json`.
3. Environment variables.
4. `models.json` provider entry.

### Common env vars

- `ANTHROPIC_API_KEY`
- `OPENAI_API_KEY`
- `GEMINI_API_KEY`
- `OPENROUTER_API_KEY`
- `AI_GATEWAY_API_KEY`
- `HF_TOKEN`
- `FIREWORKS_API_KEY`

Custom providers register through `models.json` with `baseUrl`, `apiKeyEnv`, `wireFormat`. Local models work the same way as long as they speak the declared wire format.

## Packages

```
pi install npm:<package>
pi install git:<repo-url>
```

Packages can ship skills, extensions, themes, prompts, or any combination. Installation drops the bundle into the appropriate `~/.pi/agent/` subdirectory and registers it.

Inspect: `pi list`, `pi info <package>`. Remove: `pi uninstall <package>`.

## Prompts

`~/.pi/agent/prompts/<name>.md`. Reusable prompt fragments referenced by skills, extensions, or commands. Path can be returned from `resources_discover` to load custom prompt directories.

## SYSTEM.md

Replaces or appends the default system prompt. Project `SYSTEM.md` stacks on user `SYSTEM.md`. Use to inject org wide guardrails or project specific behavior. Extensions can also push additional system text via `resources_discover`.

## Gotchas

- The skill `name` must match the parent directory exactly. Mismatches silently skip registration.
- Theme files missing any mandatory token fail to load. Use the schema URL in your editor.
- Extensions run in the harness process. A misbehaving extension can block events. Test in an isolated `PI_CODING_AGENT_DIR`.
- Provider env var names are case sensitive and exact. `Anthropic_API_Key` does not work.
- Cross tool skill directories (`.agents/skills/`) are picked up by other harnesses too. A skill placed there will appear in Claude Code and Codex if those harnesses scan that path.
- `disable-model-invocation: true` is the right setting for high stakes skills you only want triggered explicitly.

## Vendor changelog

Re-verify against https://pi.dev/docs/latest/changelog (or the published release notes) before quoting this file.
