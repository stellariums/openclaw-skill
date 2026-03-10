---
name: openclaw
description: Comprehensive guide for installing, configuring, operating, and troubleshooting OpenClaw — a self-hosted, multi-channel AI agent gateway. Use when the user asks about OpenClaw setup, configuration, channel management (WhatsApp/Telegram/Discord/Slack/iMessage/etc.), model provider setup, Gateway operations, multi-agent routing, security hardening, troubleshooting, or any maintenance task related to their local OpenClaw installation. Also use when encountering errors from `openclaw` CLI commands or the Gateway daemon.
---

# OpenClaw Maintenance Skill

OpenClaw is a self-hosted, open-source (MIT) gateway that routes AI agents across WhatsApp, Telegram, Discord, Slack, iMessage, Signal, and 15+ other channels simultaneously. It runs on macOS, Linux, or Windows.

## Release Focus (v2026.3.8)

Prioritize these new surfaces when the user is updating, troubleshooting, or documenting a recent OpenClaw setup:

- openclaw backup create / openclaw backup verify for local state archives before risky changes; --only-config and --no-include-workspace are now first-class.
- gateway.remote.token for remote mode setup on macOS; preserve existing non-plaintext values unless the user explicitly wants to replace them.
- talk.silenceTimeoutMs is the top-level Talk auto-send silence window; when unset, platform defaults still apply.
- TUI now infers the active agent from the current workspace when launched inside a configured agent workspace; explicit agent: session targets still win.
- tools.web.search.brave.mode: "llm-context" is opt-in and lets web_search use Brave's LLM Context endpoint with extracted grounding snippets.
- openclaw --version may now include a short git commit hash; do not treat the decorated version string as a mismatch by itself.
- openclaw acp --provenance off|meta|meta+receipt controls ACP ingress provenance retention and receipt injection.

## Release Focus (v2026.3.7)

Also keep these v2026.3.7 changes in mind when the user is configuring routing, plugins, web search, auth, or containerized deployments:

- ContextEngine plugins now have a first-class lifecycle and can change context assembly / compaction behavior without modifying core runtime code; check plugin config first when context behavior changes unexpectedly.
- ACP Discord channel bindings and Telegram topic bindings are now durable across restarts; Telegram ACP thread binding supports --thread here|auto.
- Telegram forum topics and DM topics can route to dedicated agentId targets with isolated sessions.
- configure / onboard now exposes full web-search provider selection and SecretRef ref-mode; Perplexity search also supports language / region / time filters.
- gateway.auth.token now supports SecretRef; prefer secret-backed auth for non-loopback or remotely exposed gateways.
- messages.tts.openai.baseUrl can target OpenAI-compatible TTS endpoints when the user is not using the default OpenAI base URL.
- OPENCLAW_EXTENSIONS can preinstall bundled extension npm dependencies into Docker / Podman images for faster and more reproducible startup.

## Release Focus (v2026.3.2)

The nearest earlier stable release before v2026.3.7 is v2026.3.2 (released 2026-03-03); v2026.3.6, v2026.3.5, v2026.3.4, and v2026.3.3 do not exist on the GitHub releases page.

Keep these v2026.3.2 additions in mind when the user is validating config, working with secrets, or handling PDFs and media-heavy sessions:

- `openclaw config validate` was added to validate config before gateway startup or restart, including detailed invalid-key paths and optional `--json` output.
- SecretRef coverage expanded broadly across user-supplied credential surfaces; unresolved refs now fail fast on active surfaces and degrade to diagnostics on inactive ones.
- A first-class `pdf` tool was added, with native Anthropic / Google PDF support plus extraction fallback for other models and configurable PDF limits.
- `sessions_spawn` supports inline attachments in subagent runtime, which matters when a workflow needs to hand files directly to a spawned session.
- New Telegram installs default to partial streaming, so preview behavior is expected unless operators explicitly disable it.

## Reference Files

| Reference | Coverage |
|---|---|
| [channels.md](references/channels.md) | Per-channel setup (WhatsApp, Telegram, Discord, etc.) |
| [channel_troubleshooting.md](references/channel_troubleshooting.md) | Per-channel failure signatures and walkthroughs |
| [tools.md](references/tools.md) | Tools inventory (profiles, groups, all built-in tools) |
| [exec.md](references/exec.md) | Exec tool: parameters, config, PATH, security, process tool |
| [exec_approvals.md](references/exec_approvals.md) | Exec approvals: allowlists, safe bins, approval flow |
| [browser.md](references/browser.md) | Browser tool: profiles, CDP, relay, `relayBindHost`, SSRF, Control API |
| [web_tools.md](references/web_tools.md) | Web tools: provider selection, Brave LLM Context, Perplexity filters, search providers |
| [lobster.md](references/lobster.md) | Lobster: typed workflow runtime with approvals |
| [llm_task.md](references/llm_task.md) | LLM Task: JSON-only LLM step for structured output |
| [openprose.md](references/openprose.md) | OpenProse: multi-agent program runtime |
| [plugins.md](references/plugins.md) | Plugins: official list, context engines, hook policy, manifest, CLI, authoring |
| [skills.md](references/skills.md) | Skills: locations, config, ClawHub, watcher, token impact |
| [providers.md](references/providers.md) | Model provider setup |
| [multi_agent.md](references/multi_agent.md) | Multi-agent routing, ACP bindings, topic-based agent routing |
| [nodes.md](references/nodes.md) | Nodes (iOS/Android/macOS/headless) |
| [security.md](references/security.md) | Security hardening |
| [secrets.md](references/secrets.md) | Secrets management (SecretRef, vault, gateway auth) |
| [sandboxing.md](references/sandboxing.md) | Sandboxing (Docker isolation, container extension deps) |
| [config_reference.md](references/config_reference.md) | Full config field reference |
| [gateway_ops.md](references/gateway_ops.md) | Gateway operations, backups, restarts, update safety |
| [remote_access.md](references/remote_access.md) | Remote access, SSH, Tailscale, web dashboard, remote gateway auth |

## Quick Reference

### Key Paths

| Path | Purpose |
|---|---|
| `~/.openclaw/openclaw.json` | Main config (JSON5) |
| `~/.openclaw/.env` | Global env fallback |
| `~/.openclaw/workspace` | Default agent workspace |
| `~/.openclaw/agents/<id>/` | Per-agent state + sessions |
| `OPENCLAW_CONFIG_PATH` | Override config location |
| `OPENCLAW_STATE_DIR` | Override state directory |
| `OPENCLAW_HOME` | Override home directory |

### Essential Commands

```
openclaw status                    # Overall status
openclaw gateway status            # Gateway daemon status
openclaw gateway status --deep     # Deep scan including system services
openclaw config validate           # Validate config before start/restart
openclaw doctor                    # Diagnose config/service issues
openclaw doctor --fix              # Auto-fix safe issues
openclaw backup create             # Create local state backup
openclaw backup verify <archive>   # Verify backup manifest + payload
openclaw logs --follow             # Tail gateway logs
openclaw channels status --probe   # Channel health check
openclaw security audit            # Security posture check
openclaw security audit --fix      # Auto-fix security issues
openclaw --version                 # Version (+ short git hash when available)
```

### Default Gateway

- Bind: `127.0.0.1:18789` (loopback)
- Dashboard: `http://127.0.0.1:18789/`
- Protocol: WebSocket (JSON text frames)

## Core Workflow

### Diagnosing Issues

Always follow this command ladder:

1. `openclaw status` — quick overview
2. `openclaw gateway status` — daemon running? RPC probe ok?
3. `openclaw logs --follow` — watch for errors
4. `openclaw doctor` — config/service diagnostics
4.5. `openclaw config validate` — validate config and SecretRef-backed values before restart
5. `openclaw channels status --probe` — per-channel health

Before uninstalling, reinstalling, resetting a channel, or replacing auth/config values, create a backup first:

```bash
openclaw backup create
openclaw backup create --only-config
openclaw backup verify <archive>
```

### Starting / Restarting Gateway

```bash
# Foreground with verbose logging
openclaw gateway --port 18789 --verbose

# Force-kill existing listener then start
openclaw gateway --force

# Service management (launchd on macOS, systemd on Linux)
openclaw gateway install
openclaw gateway start
openclaw gateway stop
openclaw gateway restart
```

If a restart fails after config edits, verify config validity before retrying and prefer:

```bash
openclaw gateway status --deep
openclaw doctor
```

v2026.3.8 improves restart recovery for invalid config, launchd restarts, and timeout-driven shutdowns, so these two commands are now the fastest confirmation path.

### Configuration

Edit config via any method:

```bash
# Interactive wizard
openclaw onboard                    # Full setup
openclaw configure                  # Config wizard

# CLI one-liners
openclaw config get <path>          # Read value
openclaw config set <path> <value>  # Set value (JSON5 or raw string)
openclaw config unset <path>        # Remove value

# Newly relevant settings in v2026.3.8
openclaw config set gateway.remote.token <token>
openclaw config set talk.silenceTimeoutMs 1200
openclaw config set tools.web.search.brave.mode llm-context
openclaw config set gateway.auth.token <token-or-secretref>
openclaw config set messages.tts.openai.baseUrl https://api.example.com/v1

# Direct edit
# Edit ~/.openclaw/openclaw.json (JSON5 format)
# Gateway hot-reloads on save (if gateway.reload.mode != "off")
```

Minimal config example:

```json5
{
  agents: { defaults: { workspace: "~/.openclaw/workspace" } },
  channels: { whatsapp: { allowFrom: ["+15555550123"] } },
}
```

Release-specific config example:

```json5
{
  gateway: {
    remote: {
      token: "oc_remote_xxx",
    },
  },
  talk: {
    silenceTimeoutMs: 1200,
  },
  tools: {
    web: {
      search: {
        brave: {
          mode: "llm-context",
        },
      },
    },
  },
}
```

Notes:

- If `gateway.remote.token` is currently a non-plaintext/secret-backed value, do not overwrite it unless the user explicitly wants to rotate or flatten it.
- Leave `talk.silenceTimeoutMs` unset when the user wants each platform's default silence window.
- Use Brave `llm-context` mode only when the user wants extracted grounding snippets rather than standard search result formatting.
- `gateway.auth.token` can be a SecretRef-backed value; prefer that over plaintext when the gateway is exposed beyond localhost.
- If the user is unsure which web-search backend to choose, prefer `openclaw configure` because v2026.3.7 surfaces provider selection and SecretRef-aware setup in the wizard.
- Set `messages.tts.openai.baseUrl` only for OpenAI-compatible TTS providers; leave it unset for the default OpenAI endpoint.
- SecretRef support is broad enough that credential-bearing fields should be assumed to accept secret-backed values unless docs say otherwise; unresolved active refs should be treated as blockers.
- For PDF-heavy workflows, prefer the built-in `pdf` tool and tune `agents.defaults.pdfModel`, `pdfMaxBytesMb`, or `pdfMaxPages` before reaching for custom extraction scripts.

### Channel Setup

For detailed per-channel setup, see [references/channels.md](references/channels.md).
For per-channel troubleshooting (failure signatures, setup walkthroughs), see [references/channel_troubleshooting.md](references/channel_troubleshooting.md).
For plugins adding new channels (Matrix, Nostr, MS Teams, etc.), see [references/plugins.md](references/plugins.md).

Quick channel add:

```bash
# Interactive wizard
openclaw channels add

# Non-interactive
openclaw channels add --channel telegram --account default --name "My Bot" --token $BOT_TOKEN
openclaw channels login --channel whatsapp     # QR pairing for WhatsApp
openclaw channels status --probe               # Verify
```
Routing notes from v2026.3.7:

- ACP bindings for Discord channels and Telegram topics now persist across restarts, so restart-related routing regressions should be diagnosed as binding/config issues first, not assumed to be transient state loss.
- In Telegram forum groups or DM topics, dedicated topic-level agent routing is now possible; use it when one chat space needs isolated agent state per topic.
- For Telegram ACP spawning, `--thread here` and `--thread auto` are the newly relevant thread-binding modes.


### Model Provider Setup

For detailed provider setup, see [references/providers.md](references/providers.md).

```bash
# Set default model
openclaw models set anthropic/claude-sonnet-4-5

# List available models
openclaw models list --all

# Check auth/token status
openclaw models status --probe

# Add auth interactively
openclaw models auth add
```

Config example:

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-sonnet-4-5",
        fallbacks: ["openai/gpt-5.2"],
      },
    },
  },
}
```

### Multi-Agent Routing

For detailed multi-agent config, see [references/multi_agent.md](references/multi_agent.md).

```bash
openclaw agents add <id>                # Create agent
openclaw agents list --bindings         # Show agent-channel bindings
openclaw agents delete <id>             # Remove agent
```

If the user launches the TUI from inside a configured agent workspace, v2026.3.8 now infers that agent automatically. Keep using explicit `agent:` session targets when the user needs to override workspace-based inference.

v2026.3.7 also makes ACP thread/channel bindings durable across restarts and adds topic-level agent routing for Telegram forum or DM topics. When a user reports that one topic should map to a different agent, treat topic bindings and per-topic agent overrides as first-class options.

When debugging plugin-driven context behavior, check whether a ContextEngine plugin is configured before assuming core compaction is at fault.

### ACP / Provenance

Use provenance controls when ACP-origin context or traceability matters:

```bash
openclaw acp --provenance off
openclaw acp --provenance meta
openclaw acp --provenance meta+receipt
```

- `off`: do not retain ACP ingress provenance.
- `meta`: retain provenance metadata such as session trace IDs.
- `meta+receipt`: retain metadata and inject a visible receipt into agent-visible context.

### Nodes (iOS / Android / macOS / Headless)

For detailed node setup, see [references/nodes.md](references/nodes.md).

```bash
openclaw nodes status                   # List connected nodes
openclaw nodes describe --node <id>     # Node capabilities
openclaw devices list                   # Pending device approvals
openclaw devices approve <requestId>    # Approve a device
openclaw node run --host <host> --port 18789  # Start headless node host
```

### Security

For detailed security hardening, see [references/security.md](references/security.md).
For secrets management (SecretRef, vault integration), see [references/secrets.md](references/secrets.md).
For sandboxing (Docker isolation for tools), see [references/sandboxing.md](references/sandboxing.md).
For full config field reference, see [references/config_reference.md](references/config_reference.md).
For remote access (SSH, Tailscale, VPN), see [references/remote_access.md](references/remote_access.md).

```bash
openclaw security audit                 # Check posture
openclaw security audit --deep          # Live gateway probe
openclaw security audit --fix           # Auto-fix safe issues
openclaw secrets reload                 # Re-resolve secret refs
openclaw secrets audit                  # Scan for plaintext leaks

When enabling non-loopback or remote gateway auth, prefer a SecretRef-backed `gateway.auth.token` over plaintext config.
```

### Update / Uninstall

```bash
# Update
openclaw backup create
npm install -g openclaw@latest
openclaw --version
openclaw doctor          # Run after update to apply migrations

# Uninstall
openclaw uninstall
```

## Tools Reference

For detailed per-tool documentation, see [references/tools.md](references/tools.md).

For specific tools, see:
- [references/exec.md](references/exec.md) — Exec tool deep-dive
- [references/exec_approvals.md](references/exec_approvals.md) — Exec approvals and allowlists
- [references/browser.md](references/browser.md) — Browser automation deep-dive
- [references/web_tools.md](references/web_tools.md) — Web search/fetch with multiple providers
- [references/lobster.md](references/lobster.md) — Lobster workflow runtime
- [references/llm_task.md](references/llm_task.md) — LLM Task for structured JSON output
- [references/openprose.md](references/openprose.md) — OpenProse multi-agent programs
- [references/plugins.md](references/plugins.md) — Plugin system (install, author, distribute)
- [references/skills.md](references/skills.md) — Skills system (load, config, ClawHub)

Prefer the built-in `pdf` tool for PDF inspection and analysis tasks; it became a first-class tool in v2026.3.2 and is usually a better default than ad-hoc OCR or text extraction workflows inside OpenClaw.

**Tool profiles**: `minimal`, `coding`, `messaging`, `full` (default).

**Tool groups** (for allow/deny):
- `group:runtime` — exec, bash, process
- `group:fs` — read, write, edit, apply_patch
- `group:sessions` — sessions_list/history/send/spawn, session_status
- `group:memory` — memory_search, memory_get
- `group:web` — web_search, web_fetch
- `group:ui` — browser, canvas
- `group:automation` — cron, gateway
- `group:messaging` — message
- `group:nodes` — nodes

## Common Failure Signatures

| Error | Cause | Fix |
|---|---|---|
| `refusing to bind gateway ... without auth` | Non-loopback bind without token | Set `gateway.auth.token` or `gateway.auth.password` |
| `another gateway instance is already listening` / `EADDRINUSE` | Port conflict | `openclaw gateway --force` or change port |
| `Gateway start blocked: set gateway.mode=local` | Local mode not enabled | Set `gateway.mode="local"` |
| `unauthorized` / reconnect loop | Token/password mismatch | Check `OPENCLAW_GATEWAY_TOKEN` or config auth |
| `backup verify` fails / manifest mismatch | Corrupt or partial archive | Recreate with `openclaw backup create`, then re-run `openclaw backup verify <archive>` before destructive work |
| Remote token loads but app cannot use it directly | `gateway.remote.token` is secret-backed or stored in a non-plaintext shape | Preserve existing value unless replacement is intended; if the app needs a raw token, explicitly set a direct token value |
| `config validate` fails / unresolved SecretRef | Config key is invalid or a required secret ref cannot resolve | Run `openclaw config validate` and `openclaw secrets audit` before restart, then fix the reported path or secret source |
| ACP topic/channel routing disappears after restart | Binding was never persisted or was rebound incorrectly | Re-check ACP binding storage and re-bind the current Discord channel / Telegram topic before debugging runtime routing |
| `device identity required` | Missing device auth | Ensure client completes connect.challenge flow |
| No replies from bot | Pairing/allowlist/mention gating | Check `openclaw pairing list`, DM policy, mention patterns |

## Environment Variables

| Variable | Purpose |
|---|---|
| `OPENCLAW_GATEWAY_TOKEN` | Gateway auth token |
| `OPENCLAW_GATEWAY_PASSWORD` | Gateway auth password |
| `OPENCLAW_GATEWAY_PORT` | Override gateway port |
| `OPENCLAW_CONFIG_PATH` | Override config file path |
| `OPENCLAW_STATE_DIR` | Override state directory |
| `OPENCLAW_HOME` | Override home directory |
| `OPENCLAW_LOAD_SHELL_ENV` | Import shell env (set to `1`) |
| `BRAVE_API_KEY` | For web_search tool |
| `OPENCLAW_EXTENSIONS` | Preinstall bundled extension npm dependencies in Docker / Podman images |
