# Codex CLI: Configuration Reference

Verified 2026-05-07 against OpenAI Codex CLI (developers.openai.com/codex).

Primary sources:
- Config basic: https://developers.openai.com/codex/config-basic
- Config advanced: https://developers.openai.com/codex/config-advanced
- Config reference (full): https://developers.openai.com/codex/config-reference
- AGENTS.md guide: https://developers.openai.com/codex/guides/agents-md
- Sandboxing: https://developers.openai.com/codex/concepts/sandboxing
- Approvals & security: https://developers.openai.com/codex/agent-approvals-security
- CLI reference: https://developers.openai.com/codex/cli/reference
- Skills: https://developers.openai.com/codex/skills

## File Layout

```
~/.codex/
  config.toml              # user config
  AGENTS.md                # global agent instructions
  AGENTS.override.md       # global override (wins over AGENTS.md)
  auth.json                # credentials
  log/
  history/
```

Project scope:

```
<repo>/
  .codex/config.toml       # project config (closest dir wins, walking root to cwd)
  AGENTS.md                # project agent instructions (any dir on the walk)
  AGENTS.override.md       # project override
```

System scope (Unix only):

```
/etc/codex/config.toml
```

`CODEX_HOME` overrides `~/.codex`. Useful for sandboxes and CI.

## Precedence

Highest wins:

1. CLI flags and `--config key=value`.
2. Profile selected via `--profile <name>` or top level `profile = "name"`.
3. Project `.codex/config.toml`. The closest directory between repo root and cwd wins. Project configs only load from trusted projects.
4. User `~/.codex/config.toml`.
5. System `/etc/codex/config.toml`.
6. Built in defaults.

Trust a project the first time Codex prompts; the trust list lives in user config.

## config.toml: Common Schema

```toml
# Top level defaults
model = "gpt-5.4"
model_provider = "openai"
model_reasoning_effort = "medium"   # minimal | low | medium | high
model_reasoning_summary = "auto"    # auto | concise | detailed
approval_policy = "on-request"      # untrusted | on-request | never
sandbox_mode = "workspace-write"    # read-only | workspace-write | danger-full-access
profile = "default"                 # name of a [profiles.X] block to use as default

# Project doc loading
project_doc_max_bytes = 32768
project_doc_fallback_filenames = ["CLAUDE.md", "GEMINI.md"]

# Approvals
approvals_reviewer = "auto_review"  # route approvals through a reviewer agent

[approval_policy.granular]
sandbox_approval = "on-request"
rules = "on-request"
mcp_elicitations = "on-request"
request_permissions = "on-request"
skill_approval = "on-request"

# Sandbox tuning for workspace-write mode
[sandbox_workspace_write]
network_access = false
writable_roots = ["./"]
exclude_tmpdir_env_var = false
exclude_slash_tmp = false

# Model providers
[model_providers.openai]
name = "OpenAI"
base_url = "https://api.openai.com/v1"
env_key = "OPENAI_API_KEY"
wire_api = "responses"

[model_providers.local-llama]
name = "Local Llama"
base_url = "http://localhost:8080/v1"
env_key = "LOCAL_KEY"
wire_api = "chat"

# MCP servers
[mcp_servers.github]
command = "npx"
args = ["-y", "@modelcontextprotocol/server-github"]
env = { GITHUB_TOKEN = "${GITHUB_TOKEN}" }

# Profiles override any of the above
[profiles.deep-review]
model = "gpt-5-pro"
model_reasoning_effort = "high"
approval_policy = "never"
sandbox_mode = "read-only"

[profiles.yolo]
approval_policy = "never"
sandbox_mode = "danger-full-access"
```

### Sandbox Modes

- `read-only`. Agent reads anywhere allowed, writes nothing. Network blocked unless `network_access = true` in mode block.
- `workspace-write`. Writes inside `writable_roots` (default cwd). Reads outside allowed. Network off by default.
- `danger-full-access`. No sandbox. Equivalent to running scripts as yourself.

### Approval Policies

- `untrusted`. Approve every command and edit.
- `on-request`. Approve when the model asks. The auto preset.
- `never`. Headless. The agent runs without prompts. Pair with `read-only` for CI safety, or `danger-full-access` for trusted automation.

`approvals_reviewer = "auto_review"` routes pending approvals through a secondary agent that decides yes or no. Useful for CI with a second opinion model.

The `[approval_policy.granular]` block lets you set different policies per category (sandbox escapes vs MCP elicitations vs skill activation).

## CLI Flag Highlights

```
codex                                # interactive, default config
codex --profile deep-review          # use named profile
codex --sandbox workspace-write \
      --ask-for-approval on-request  # the auto preset
codex --sandbox danger-full-access   # full access
codex --dangerously-bypass-approvals-and-sandbox   # alias: --yolo
codex exec "task description"        # non interactive
codex --config model="gpt-5-pro" --config approval_policy="never"
codex resume <session-id>
codex --cd /path/to/project          # change root
```

`--config key=value` accepts dotted paths. Repeatable. Strings, ints, bools, JSON.

`CODEX_HOME=/tmp/sandbox-home codex` runs against an isolated config tree. Test new configs without touching `~/.codex`.

## AGENTS.md Hierarchy

Global pass:

1. `~/.codex/AGENTS.override.md` if non empty, else
2. `~/.codex/AGENTS.md`.

Project pass, walking repo root toward cwd, then for each directory:

1. `AGENTS.override.md` (if present, replaces lower priority files in that dir).
2. `AGENTS.md`.
3. Files listed in `project_doc_fallback_filenames` (e.g., `CLAUDE.md`).

All non empty files concatenate, separated by blank lines. Closer directories appear later, so they effectively override earlier instructions when guidance conflicts. Total prepended bytes capped at `project_doc_max_bytes` (32 KiB default).

`AGENTS.override.md` is the escape hatch. Use it to pin guidance without touching the team file.

## Skills

`~/.codex/skills/<name>/SKILL.md` and `.codex/skills/<name>/SKILL.md` follow the Agent Skills spec (same as Claude Code, same as Pi). See `agents-md.md` for the shared frontmatter.

Approval gate: `[approval_policy.granular].skill_approval` controls whether activating a skill needs user consent.

## MCP Servers

Defined under `[mcp_servers.<name>]` in any config.toml. Same shape across user, project, and profiles. Profiles can add or replace servers per workflow.

## Providers

`[model_providers.<name>]` blocks. Switch with `model_provider = "name"`. `wire_api` accepts `"responses"` (OpenAI Responses API) or `"chat"` (Chat Completions). Set `env_key` to the env var holding the API key. Custom `base_url` enables local models, gateways, or proxies.

## Trust List

Project configs load only from trusted projects. The trust list is stored in user config when you accept the trust prompt. Inspect with `codex projects` (subject to version).

## Gotchas

- A project `.codex/config.toml` is ignored until you trust the project. The harness will warn once.
- `--yolo` overrides everything including granular approvals. Use only inside ephemeral sandboxes.
- `project_doc_max_bytes` truncates silently. Watch for missed AGENTS.md content in deep trees.
- Profiles are not stacked; selecting one replaces top level keys it specifies, but does not merge nested tables fully. Test with `codex --print-config`.
- The `auto_review` approvals reviewer charges tokens. Disable for cost-sensitive runs.
- `sandbox_workspace_write.network_access = true` is the easiest way to let the agent run `npm install` in workspace mode.

## Vendor changelog

Re-verify against https://developers.openai.com/codex/changelog before quoting this file.
