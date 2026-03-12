---
name: openclaw
description: Comprehensive guide for installing, configuring, operating, and troubleshooting OpenClaw — a self-hosted, multi-channel AI agent gateway. Use when the user asks about OpenClaw setup, configuration, channel management (WhatsApp/Telegram/Discord/Slack/iMessage/etc.), model provider setup, Gateway operations, multi-agent routing, security hardening, troubleshooting, or any maintenance task related to their local OpenClaw installation. Also use when encountering errors from `openclaw` CLI commands or the Gateway daemon.
---

# OpenClaw Maintenance Skill

OpenClaw is a self-hosted, open-source (MIT) gateway that routes AI agents across WhatsApp, Telegram, Discord, Slack, iMessage, Signal, and 15+ other channels simultaneously. It runs on macOS, Linux, or Windows.

## Release Focus (v2026.3.11)

Prioritize these newer surfaces when the user is updating or troubleshooting a current OpenClaw deployment:

- `v2026.3.11` is the next stable release after `v2026.3.8`; `v2026.3.10` and `v2026.3.9` do not exist on the GitHub releases page.
- Gateway/WebSocket now enforces browser origin validation for browser-originated connections even in trusted-proxy mode; if a reverse-proxied Control UI suddenly stops connecting after upgrade, verify the browser origin and proxy setup instead of trying to bypass auth.
- `openclaw doctor --fix` now migrates legacy cron storage and legacy notify/webhook delivery metadata; after upgrading older cron-heavy installs, run it before assuming cron delivery is broken.
- Onboarding now has a first-class Ollama flow with `Local` and `Cloud + Local` modes and avoids unnecessary local pulls for cloud-only choices; prefer the wizard when the user wants the easiest local-model setup.
- OpenCode Go is now a first-class provider during onboarding; treat OpenCode Zen and OpenCode Go as one setup flow with one shared key unless the user explicitly needs provider-level runtime differences.
- `memorySearch.extraPaths` can now opt into multimodal image/audio indexing with `google/gemini-embedding-2-preview`, configurable embedding dimensions, and automatic reindexing when dimensions change.
- Discord auto-created threads can now set `autoArchiveDuration`, so thread retention no longer has to stay at the default one hour.
- `sessions_spawn` with `runtime: "acp"` can now reuse an existing ACPX/Codex conversation via `resumeSessionId` instead of always starting a fresh session.

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

## Release Focus (v2026.2.26)

Keep these late-February changes in mind when the user is dealing with secrets, ACP thread sessions, or agent routing operations:

- `openclaw secrets` is now a first-class workflow: `audit`, `configure`, `apply`, and `reload` should be treated as the standard path for migrating away from plaintext secrets.
- ACP thread-bound agents are first-class runtimes now; when a thread-scoped ACP flow misbehaves, debug ACP runtime lifecycle and thread dispatch instead of assuming it is just a transient reply bug.
- `openclaw agents bindings`, `openclaw agents bind`, and `openclaw agents unbind` exist for account-scoped route management; prefer these commands over hand-editing bindings when the user wants to adjust live channel/account routing.
- `openai-codex` transport is WebSocket-first by default with SSE fallback, so transport changes around Codex models are expected in late-February installs.

## Release Focus (v2026.2.25)

Keep these mid-February operational changes in mind when diagnosing heartbeats, onboarding expectations, or DM delivery:

- `agents.defaults.heartbeat.directPolicy` replaces the older heartbeat DM toggle, and the default direct-policy behavior reverted to `allow`.
- To preserve older DM-blocked heartbeat behavior from `v2026.2.24`, explicitly set `agents.defaults.heartbeat.directPolicy: "block"` or a per-agent override.
- OpenClaw onboarding docs now treat the product as personal-by-default; shared or multi-user deployments should be assumed to need explicit hardening, auth, and approval review.

## Release Focus (v2026.2.6 / v2026.2.2)

The first February stable releases added a few provider and agent surfaces that are still relevant in current maintenance work:

- Anthropic `claude-opus-4-6` and OpenAI Codex `openai-codex/gpt-5.3-codex` gained forward-compat support in `v2026.2.6`.
- xAI / Grok model-provider support landed in `v2026.2.6`.
- Voyage AI support landed as a native memory provider in `v2026.2.6`; treat it as an embeddings / memory surface rather than a primary chat model.
- `agents.defaults.subagents.thinking` (and per-agent `subagents.thinking`) landed in `v2026.2.2`, so subagent thinking level can now be configured instead of inferred ad hoc.

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
openclaw agents bindings           # Show resolved agent bindings
openclaw agents bind               # Add/update an agent binding
openclaw agents unbind             # Remove an agent binding
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

If the deployment was upgraded from an older cron setup and scheduled jobs stop notifying correctly, run:

```bash
openclaw doctor --fix
```

before assuming the current cron config is wrong.

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
- For Discord installs that auto-create threads, `autoArchiveDuration` is now a first-class retention control; use it when threads should stay open for longer than the old one-hour default.


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

Provider notes from `v2026.3.11`:

- Prefer `openclaw onboard` when the user wants Ollama because the wizard now distinguishes `Local` vs `Cloud + Local` flows and skips unnecessary local model pulls.
- Treat OpenCode Zen and OpenCode Go as a shared onboarding/auth setup, even though runtime provider routing is now split.
- For memory indexing over file trees, `google/gemini-embedding-2-preview` is now the notable Gemini memory-search model; changing configured output dimensions should be expected to trigger reindexing.

### Multi-Agent Routing

For detailed multi-agent config, see [references/multi_agent.md](references/multi_agent.md).

```bash
openclaw agents add <id>                # Create agent
openclaw agents list --bindings         # Show agent-channel bindings
openclaw agents bindings                # Show resolved account-scoped bindings
openclaw agents bind                    # Add/update account-scoped bindings
openclaw agents unbind                  # Remove account-scoped bindings
openclaw agents delete <id>             # Remove agent
```

If the user launches the TUI from inside a configured agent workspace, v2026.3.8 now infers that agent automatically. Keep using explicit `agent:` session targets when the user needs to override workspace-based inference.

v2026.3.7 also makes ACP thread/channel bindings durable across restarts and adds topic-level agent routing for Telegram forum or DM topics. When a user reports that one topic should map to a different agent, treat topic bindings and per-topic agent overrides as first-class options.

In `v2026.2.26`, binding management also became a first-class CLI workflow via `openclaw agents bindings|bind|unbind`. Prefer that route when the user wants to promote a channel-only route into an account-scoped route, clean up stale bindings, or avoid hand-editing config during a live routing incident.

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
```

When enabling non-loopback or remote gateway auth, prefer a SecretRef-backed `gateway.auth.token` over plaintext config.

`v2026.3.11` also hardens browser-origin checks for browser-based Gateway connections. When the Control UI is served through a reverse proxy or trusted-proxy setup, treat unexpected browser connection failures as an origin-validation or proxy-header issue first.

### Update / Uninstall

```bash
# Update
openclaw backup create
npm install -g openclaw@latest
openclaw --version
openclaw doctor --fix    # Run after update to apply safe migrations, especially cron metadata

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
| Browser Control UI reloads but browser-originated Gateway calls fail | `v2026.3.11` origin validation rejects the current browser origin or proxy path | Verify reverse-proxy/browser origin setup and trusted-proxy configuration; do not work around it by weakening auth |
| Cron jobs stopped announcing or webhook delivery broke right after upgrade | Legacy cron storage or notify/webhook metadata needs migration | Run `openclaw doctor --fix`, then re-check cron config and delivery target |
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
