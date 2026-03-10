# OpenClaw Agent Skill 🦞

[中文文档](README_CN.md) | English

A comprehensive **Agent Skill** for installing, configuring, operating, and troubleshooting [OpenClaw](https://github.com/openclaw/openclaw) — a self-hosted, multi-channel AI agent gateway.

## What is This?

This is an Agent Skill designed for AI coding assistants (like Claude with Antigravity). Once installed, the AI assistant gains deep knowledge of OpenClaw and can help you with:

- 🔧 **Installation & Updates** — Install, upgrade, or migrate OpenClaw
- ⚙️ **Configuration** — Edit `openclaw.json`, set up models, manage secrets
- 📡 **Channel Management** — Set up WhatsApp, Telegram, Discord, Slack, iMessage, and 15+ other channels
- 🚀 **Gateway Operations** — Start, stop, restart, health check, remote access
- 🤖 **Multi-Agent Routing** — Configure multiple agents with isolated workspaces and sessions
- 🔒 **Security Hardening** — Audit, lock down access, manage tokens and secrets
- 🔍 **Troubleshooting** — Diagnose and fix common errors from CLI and Gateway

### Recently Synced Release Changes

This skill now reflects the key stable OpenClaw changes from early March 2026, especially:

- `v2026.3.8`: `backup create/verify`, `talk.silenceTimeoutMs`, Brave `llm-context`, `openclaw acp --provenance`, `gateway.remote.token`.
- `v2026.3.7`: ContextEngine plugin slot, persistent ACP channel/topic bindings, Telegram topic-level routing, SecretRef support for `gateway.auth.token`, `messages.tts.openai.baseUrl`.
- `v2026.3.2`: `openclaw config validate`, broader SecretRef coverage, first-class `pdf` tool, inline `sessions_spawn` attachments.

## Skill Structure

```
OpenClaw-Skill/
├── SKILL.md                     # Main entry (core workflows, commands, troubleshooting)
└── references/
    ├── channels.md              # 20+ channel setup guides (WhatsApp, Telegram, Discord, etc.)
    ├── gateway_ops.md           # Gateway architecture, service management, remote access
    ├── multi_agent.md           # Multi-agent routing, bindings, per-agent config
    ├── providers.md             # 20+ model providers (Anthropic, OpenAI, Ollama, etc.)
    └── security.md              # Auth, access control, hardening baseline, incident response
```

**Total: ~1,400 lines** of structured reference covering all core OpenClaw functionality.

## Installation

### Download This Skill

Choose whichever is easiest:

1. Clone the repository:

```bash
git clone https://github.com/stellariums/openclaw-skill.git
```

2. Download a ZIP archive from GitHub:

- Open `https://github.com/stellariums/openclaw-skill`
- Click `Code` → `Download ZIP`
- Extract it locally

### For Antigravity (Claude)

Copy the downloaded folder to your Antigravity skills directory. If needed, rename the folder to `openclaw`:

```bash
cp -r openclaw-skill ~/.gemini/antigravity/skills/openclaw
```

On Windows, you can also copy the folder manually to `%USERPROFILE%\.gemini\antigravity\skills\openclaw`.

The skill will be automatically detected and triggered when you ask about OpenClaw-related tasks.

### For Other AI Assistants

You can download this repository the same way, then reuse `SKILL.md` and `references/` in any assistant that supports skill or knowledge injection.

## Usage Examples

Once installed, just ask naturally:

| What You Say | What the AI Does |
|---|---|
| "Help me upgrade OpenClaw" | Runs `npm install -g openclaw@latest`, `openclaw doctor`, restarts Gateway, verifies |
| "Set up a Telegram bot" | Walks through bot creation, token setup, config, and verification |
| "Gateway is not responding" | Runs diagnostic command ladder: status → logs → doctor → channels probe |
| "Lock down my OpenClaw security" | Runs security audit, applies hardened baseline, fixes permissions |
| "Add a second agent for work" | Creates agent, sets up workspace, configures bindings, restarts |
| "EADDRINUSE error" | Identifies port conflict, runs `openclaw gateway --force` or changes port |

## Key Commands Quick Reference

```bash
# Status & Health
openclaw status                    # Overall status
openclaw gateway status            # Gateway daemon status
openclaw config validate           # Validate config before start/restart
openclaw doctor                    # Diagnose issues
openclaw channels status --probe   # Channel health

# Gateway Management
openclaw gateway install           # Install as system service
openclaw gateway start/stop/restart
openclaw backup create             # Create local backup before risky changes
openclaw backup verify <archive>   # Verify backup manifest + payload

# Configuration
openclaw config get <path>         # Read config value
openclaw config set <path> <value> # Set config value
openclaw configure                 # Interactive wizard

# Security
openclaw security audit            # Check security posture
openclaw security audit --fix      # Auto-fix issues
openclaw secrets reload            # Reload secret refs

# Channels
openclaw channels add              # Add channel (wizard)
openclaw channels login            # WhatsApp QR pairing
openclaw channels list             # Show configured channels

# Models
openclaw models set <model>        # Set default model
openclaw models status --probe     # Check auth status
```

## Documentation Source

This skill is built from the official [OpenClaw Documentation](https://docs.openclaw.ai/), covering:

- [Install](https://docs.openclaw.ai/install)
- [Gateway Architecture](https://docs.openclaw.ai/concepts/architecture)
- [Configuration](https://docs.openclaw.ai/gateway/configuration)
- [Channels](https://docs.openclaw.ai/channels)
- [Model Providers](https://docs.openclaw.ai/providers)
- [Tools](https://docs.openclaw.ai/tools)
- [Multi-Agent Routing](https://docs.openclaw.ai/concepts/multi-agent)
- [Security](https://docs.openclaw.ai/gateway/security)
- [Troubleshooting](https://docs.openclaw.ai/gateway/troubleshooting)
- [CLI Reference](https://docs.openclaw.ai/cli)

## License

This skill is provided as-is for use with AI assistants. OpenClaw itself is [MIT licensed](https://github.com/openclaw/openclaw/blob/main/LICENSE).

## Contributing

Issues and PRs welcome! If OpenClaw releases new features or changes, feel free to update the reference files accordingly.
