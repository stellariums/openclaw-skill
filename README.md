# OpenClaw Agent Skill 🦞

[![Release](https://img.shields.io/github/v/release/stellariums/openclaw-skill)](https://github.com/stellariums/openclaw-skill/releases)
[![License](https://img.shields.io/github/license/stellariums/openclaw-skill)](LICENSE)
[![Stars](https://img.shields.io/github/stars/stellariums/openclaw-skill?style=social)](https://github.com/stellariums/openclaw-skill/stargazers)

[中文文档](README_CN.md) | English

A production-ready **Agent Skill** that turns Codex, Claude Code, Antigravity, or other AI assistants into a focused OpenClaw operator.

> Install once, then ask your assistant to diagnose, configure, secure, and maintain a local OpenClaw deployment with much better context.

## Why This Skill

- **Operationally focused** — tuned for real OpenClaw maintenance, not generic Q&A
- **Broad coverage** — channels, model providers, Gateway ops, multi-agent routing, secrets, sandboxing, and security
- **Practical outputs** — optimized for step-by-step diagnosis, config edits, recovery, and verification

## Best For

- Running or upgrading a self-hosted OpenClaw instance
- Adding new channels, providers, or agent routing rules
- Troubleshooting startup failures, auth errors, broken bindings, and unhealthy Gateway behavior
- Checking upgrade-era behavior changes such as Control UI proxy issues, workspace plugin trust, and short-lived pairing tokens
- Giving AI assistants a reusable OpenClaw knowledge pack instead of re-explaining docs each time

## Example Prompts

- `Help me diagnose my local OpenClaw setup`
- `Set up a Telegram bot for OpenClaw`
- `Why is my Gateway not responding?`
- `Why did Control UI stop connecting after I put it behind a reverse proxy?`
- `Why did my workspace plugin stop auto-loading after the upgrade?`
- `Why do my /pair or QR setup codes expire so quickly now?`
- `Lock down my OpenClaw security configuration`
- `Add a second agent for work with isolated workspace`

### Synced Release Coverage

This skill currently reflects the key stable OpenClaw changes through 2026-03-13, especially:

- `v2026.3.12`: dashboard-v2 Control UI refresh, shared fast-mode toggle across `/fast`/TUI/Control UI/ACP, provider-plugin architecture for Ollama/vLLM/SGLang, `sessions_yield`, Slack `channelData.slack.blocks`, short-lived pairing bootstrap tokens, disabled implicit workspace plugin auto-load
- `v2026.3.11`: browser-origin validation hardening, `openclaw doctor --fix` cron migration, Ollama Local / Cloud + Local onboarding, Gemini `gemini-embedding-2-preview` memory indexing, Discord `autoArchiveDuration`, ACP `sessions_spawn.resumeSessionId`
- `v2026.3.8`: `backup create/verify`, `talk.silenceTimeoutMs`, Brave `llm-context`, `openclaw acp --provenance`, `gateway.remote.token`
- `v2026.3.7`: ContextEngine plugin slot, persistent ACP channel/topic bindings, Telegram topic-level routing, SecretRef support for `gateway.auth.token`, `messages.tts.openai.baseUrl`
- `v2026.3.2`: `openclaw config validate`, broader SecretRef coverage, first-class `pdf` tool, inline `sessions_spawn` attachments
- The stable release sequence skips `v2026.3.10` and `v2026.3.9`.

It also covers the most important February 2026 additions:

- `v2026.2.26`: first-class `openclaw secrets` workflow and `openclaw agents bindings|bind|unbind`
- `v2026.2.25`: `agents.defaults.heartbeat.directPolicy` and the reverted default direct-heartbeat behavior
- `v2026.2.6`: xAI / Grok, Voyage AI, `claude-opus-4-6`, and `openai-codex/gpt-5.3-codex`
- `v2026.2.2`: `agents.defaults.subagents.thinking`

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

## Quick Install

If you already know which assistant you use, start here:

- `Codex`: `git clone https://github.com/stellariums/openclaw-skill.git ~/.codex/skills/openclaw`
- `Claude Code`: clone the repo, then create an `openclaw` subagent with `/agents`
- `Antigravity`: `cp -r openclaw-skill ~/.gemini/antigravity/skills/openclaw`

### Verify Installation

After installing, try one of these prompts:

- `Help me diagnose my local OpenClaw setup`
- `Check whether my OpenClaw configuration has any problems`
- `Use the openclaw skill to troubleshoot Gateway startup`

If the assistant starts using `SKILL.md`, the installation worked.

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

### For Codex

The fastest option is to clone the repo directly into your Codex skills directory:

```bash
# macOS / Linux
git clone https://github.com/stellariums/openclaw-skill.git ~/.codex/skills/openclaw
```

```powershell
# Windows PowerShell
git clone https://github.com/stellariums/openclaw-skill.git "$env:USERPROFILE\.codex\skills\openclaw"
```

Restart Codex after installing so the new skill is loaded.

If you downloaded a ZIP instead, extract it and copy the folder to `~/.codex/skills/openclaw` or `%USERPROFILE%\.codex\skills\openclaw`.

### For Claude Code

Based on Anthropic's current docs, Claude Code does not expose a native `skills/` directory. The closest built-in equivalent is a personal subagent in `~/.claude/agents/`.

The easiest option is to run `/agents` inside Claude Code and create a user-level `openclaw` subagent that points to this repository.

If you prefer files, a practical setup is:

```bash
# macOS / Linux
git clone https://github.com/stellariums/openclaw-skill.git ~/.claude/openclaw-skill
mkdir -p ~/.claude/agents
cat > ~/.claude/agents/openclaw.md <<'EOF'
---
name: openclaw
description: Use the OpenClaw skill repository to diagnose, configure, and troubleshoot OpenClaw.
---

Use `~/.claude/openclaw-skill/SKILL.md` as the primary workflow.
Open only the needed files under `~/.claude/openclaw-skill/references/`.
EOF
```

On Windows, clone the repo to `%USERPROFILE%\.claude\openclaw-skill`, then create `%USERPROFILE%\.claude\agents\openclaw.md` with the same content.

After that, you can ask Claude Code to use the `openclaw` subagent for OpenClaw-related work.

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
| "Help me upgrade OpenClaw" | Runs `openclaw backup create`, `openclaw update` (or `npm install -g openclaw@latest`), `openclaw config validate`, `openclaw doctor --fix`, restarts Gateway, verifies |
| "Set up a Telegram bot" | Walks through bot creation, token setup, config, and verification |
| "Gateway is not responding" | Runs diagnostic command ladder: status → logs → doctor → channels probe |
| "Control UI stopped connecting behind my reverse proxy" | Checks browser-origin validation, proxy headers, and trusted-proxy config instead of weakening auth |
| "My workspace plugin stopped loading after upgrade" | Verifies `v2026.3.12` plugin trust behavior and switches to an explicit trust/enable path |
| "Pairing or QR setup codes keep expiring" | Re-runs `/pair` or `openclaw qr setup`, explains short-lived bootstrap tokens, and avoids reusing shared credentials |
| "Lock down my OpenClaw security" | Runs security audit, applies hardened baseline, fixes permissions |
| "Add a second agent for work" | Creates agent, sets up workspace, configures bindings, restarts |
| "EADDRINUSE error" | Identifies port conflict, runs `openclaw gateway --force` or changes port |

## Key Commands Quick Reference

```bash
# Most Used
openclaw status                    # Overall status
openclaw gateway status            # Gateway daemon status
openclaw channels status --probe   # Channel health
openclaw config validate           # Validate config before start/restart
openclaw doctor                    # Diagnose issues
openclaw security audit            # Check security posture
openclaw backup create             # Create local backup before risky changes
openclaw --version                 # Check installed version (+ git hash when available)

# Must Check After Upgrades
openclaw gateway status --deep     # Deep scan including system services
openclaw doctor --fix              # Apply safe fixes and migrations
openclaw backup verify <archive>   # Verify backup manifest + payload
openclaw security audit --deep     # Probe live gateway security posture
openclaw agents bindings           # Show resolved agent bindings

# Common Setup Commands
openclaw configure                 # Interactive wizard
openclaw config get <path>         # Read config value
openclaw config set <path> <value> # Set config value
openclaw gateway install           # Install as system service
openclaw gateway start/stop/restart
openclaw channels add              # Add channel (wizard)
openclaw channels login            # WhatsApp QR pairing
openclaw channels list             # Show configured channels
openclaw models set <model>        # Set default model
openclaw models status --probe     # Check auth status
openclaw models auth add           # Add provider auth interactively
openclaw secrets reload            # Reload secret refs
openclaw security audit --fix      # Auto-fix issues
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
