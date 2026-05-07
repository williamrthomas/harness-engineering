# Claude Code: Configuration Reference

Verified 2026-05-07 against Claude Code (Anthropic, current docs at docs.claude.com/claude-code).

Primary sources:
- Settings: https://docs.claude.com/en/docs/claude-code/settings
- Hooks reference: https://code.claude.com/docs/en/hooks
- Hooks guide: https://code.claude.com/docs/en/hooks-guide
- Slash commands: https://code.claude.com/docs/en/agent-sdk/slash-commands
- Settings schema (community gist): https://gist.github.com/xdannyrobertsx/0a395c59b1ef09508e52522289bd5bf6

## File Layout

User scope, machine wide:

```
~/.claude/
  settings.json          # primary user config
  agents/                # named subagent prompts
  commands/              # legacy slash commands (prefer skills)
  skills/                # SKILL.md packages
~/.claude.json           # global MCP server registry
```

Project scope, committed:

```
<repo>/
  .claude/
    settings.json        # team shared
    agents/
    skills/<name>/SKILL.md
    commands/            # legacy
  .mcp.json              # project MCP servers
  CLAUDE.md              # project memory loaded into context
```

Project scope, gitignored:

```
.claude/settings.local.json
```

Plugin hooks travel inside the plugin directory at `hooks/hooks.json`.

## Precedence

Local project settings override shared project settings. Project overrides user. CLI flags trump all. Memory files (`CLAUDE.md`) merge in conversation context, not in JSON config space.

## settings.json: Full Schema

```json
{
  "apiKeyHelper": "/path/to/script",
  "awsCredentialExport": "/path/to/aws-cred-script",
  "awsAuthRefresh": "/path/to/aws-refresh",
  "cleanupPeriodDays": 30,
  "env": { "FOO": "bar" },
  "includeCoAuthoredBy": true,
  "model": "claude-sonnet-4-5",
  "outputStyle": "default",
  "spinnerTipsEnabled": true,
  "alwaysThinkingEnabled": false,
  "skipWebFetchPreflight": false,

  "permissions": {
    "allow": ["Bash(git:*)", "Read"],
    "deny": ["Bash(rm -rf:*)"],
    "ask":  ["WebFetch"],
    "defaultMode": "default",
    "disableBypassPermissionsMode": false,
    "additionalDirectories": ["/abs/path/to/extra"]
  },

  "enableAllProjectMcpServers": false,
  "enabledMcpjsonServers": ["server-a"],
  "disabledMcpjsonServers": ["server-b"],

  "hooks": { "PreToolUse": [ /* see Hooks */ ] },
  "disableAllHooks": false,

  "statusLine": {
    "type": "command",
    "command": "/path/to/statusline.sh",
    "padding": 1
  },

  "enabledPlugins": ["plugin-id"],
  "extraKnownMarketplaces": [
    { "name": "internal", "source": { "url": "https://..." } }
  ],
  "skippedMarketplaces": [],
  "skippedPlugins": [],

  "forceLoginMethod": "claudeai",
  "forceLoginOrgUUID": "00000000-0000-0000-0000-000000000000",
  "otelHeadersHelper": "/path/to/script",

  "sandbox": {
    "network": { "allowUnixSockets": true, "allowLocalBinding": false },
    "filesystem": {
      "read":  { "denyOnly": ["/etc/secrets"] },
      "write": { "allowOnly": ["./"], "includeDefaults": true, "denyWithinAllow": [".git"] },
      "ignoreViolations": false
    },
    "unsandboxedCommands": ["docker"],
    "enableWeakerNestedSandbox": false
  }
}
```

### Permissions

`defaultMode` accepts:

- `"default"`. Ask before sensitive actions.
- `"acceptEdits"`. Auto approve file edits.
- `"plan"`. Plan only, no execution.
- `"bypassPermissions"`. Yolo. Disable with `disableBypassPermissionsMode: true` to prevent the user from enabling it.

Match strings follow the `Tool(arg-pattern)` form: `Bash(git:*)`, `Read`, `WebFetch(domain:example.com)`. The deny list wins over allow.

### Sandbox

The sandbox block is evaluated when Claude Code launches a sandboxed session. Filesystem `write.allowOnly` is the workspace whitelist; `denyWithinAllow` carves holes (e.g., protect `.git`). Network `allowLocalBinding` lets the agent serve on localhost.

## Hooks

Defined under `hooks` in any settings.json. Structure:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/check.sh",
            "timeout": 5000,
            "if": "tool_input.command matches '^git'"
          }
        ]
      }
    ]
  }
}
```

### Events

- `SessionStart`. Once per session.
- `Setup`. After session bootstrap, before first prompt.
- `UserPromptSubmit`. User pressed enter.
- `UserPromptExpansion`. After macro expansion.
- `PreToolUse`. Before any tool call. Veto here.
- `PostToolUse`. After successful tool.
- `PostToolUseFailure`. After tool error.
- `PostToolBatch`. After a batched tool group.
- `Notification`. Sidechannel UI events.
- `Stop`. Session end.

### Hook entry fields

- `type`: `"command"` (only supported type).
- `command`: shell command. Receives JSON on stdin.
- `timeout`: milliseconds.
- `if`: optional CEL-like condition. Requires Claude Code v2.1.85+.

A hook script that exits non zero blocks the action (for `PreToolUse`). Stdout becomes a notice in the session.

Inspect runtime hooks with `/hooks` inside the session.

## Skills

Preferred over legacy `commands/`. One directory per skill:

```
.claude/skills/code-review/
  SKILL.md
  scripts/...
  references/...
```

`SKILL.md` requires YAML frontmatter:

```yaml
---
name: code-review
description: Reviews a diff for security and clarity. Use when the user asks to review code.
---
```

Required keys: `name` (lowercase, hyphens, must match directory), `description` (when to invoke). Optional: `license`, `compatibility`, `metadata`, `allowed-tools`, `disable-model-invocation`. The body is the prompt.

Invocation: `/code-review` or autonomous when description matches user intent.

## Commands (legacy)

`.claude/commands/<name>.md` with optional frontmatter. Plain prompt body. Skills supersede this. New work should use skills.

## MCP Servers

User scope: `~/.claude.json`. Project scope: `.mcp.json`.

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "${GITHUB_TOKEN}" }
    }
  }
}
```

Settings keys `enableAllProjectMcpServers`, `enabledMcpjsonServers`, `disabledMcpjsonServers` gate which entries actually load. Default policy denies project MCP unless explicitly trusted.

## Plugins and Marketplaces

Plugins are bundles of commands, skills, hooks, MCP servers. Sources: `url`, `github`, `git`, `npm`, `file`, `directory`. Declare in `extraKnownMarketplaces`, opt in via `enabledPlugins`.

## Memory (CLAUDE.md)

Not config in the JSON sense. Loaded as conversation prefix. Walks from cwd to repo root. User memory at `~/.claude/CLAUDE.md`. Use `#` to add to memory mid session.

## Gotchas

- `settings.local.json` is gitignored by default. Personal overrides go there.
- `permissions.deny` cannot be overridden by `permissions.allow` for the same pattern. Deny wins.
- Hook `command` runs in your shell with your env. A bad hook bricks the session. Test outside Claude first.
- `bypassPermissions` mode disables the deny list too. Treat as YOLO.
- `model` here is a default. `--model` flag and `/model` slash override.
- Skills with malformed frontmatter silently fail to register. Run with verbose logging when debugging.

## Vendor changelog

Re-verify against https://docs.claude.com/en/docs/claude-code/release-notes before quoting this file in production.
