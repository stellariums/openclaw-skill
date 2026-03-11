# OpenClaw Multi-Agent Routing Reference

## Table of Contents

- [Overview](#overview)
- [What Is One Agent](#what-is-one-agent)
- [CLI Commands](#cli-commands)
- [Quick Start](#quick-start)
- [Routing Rules](#routing-rules)
- [Bindings](#bindings)
- [ACP Topic + Channel Bindings](#acp-topic--channel-bindings)
- [Per-Agent Configuration](#per-agent-configuration)
- [Examples](#examples)

## Overview

Multi-agent routing lets one Gateway serve multiple isolated agent personalities, each with its own workspace, sessions, model config, and channel bindings.

## What Is One Agent

Each agent has:
- **Workspace**: files, AGENTS.md, SOUL.md, USER.md, persona rules
- **State directory** (`agentDir`): auth profiles, model registry, per-agent config at `~/.openclaw/agents/<agentId>/agent/`
- **Session store**: chat history + routing state at `~/.openclaw/agents/<agentId>/sessions`
- **Skills**: per-agent skills in `agentDir/skills/` or shared from `~/.openclaw/skills`

## CLI Commands

```bash
openclaw agents add <id>            # Create new agent
openclaw agents list                # List agents
openclaw agents list --bindings     # Show agent-channel bindings
openclaw agents bindings            # Show resolved account-scoped bindings
openclaw agents bind                # Add/update a binding via CLI
openclaw agents unbind              # Remove a binding via CLI
openclaw agents delete <id>         # Remove agent
```

## Quick Start

1. Create agent workspaces:

```bash
openclaw agents add coding
openclaw agents add social
```

2. Create channel accounts (one bot per agent for Discord/Telegram):

```bash
openclaw channels login --channel whatsapp --account work
openclaw channels add --channel telegram --account social --token $TOKEN
```

3. Add agents, accounts, and bindings to config:

```json5
{
  agents: {
    list: [
      {
        id: "coding",
        workspace: "~/.openclaw/workspace-coding",
      },
      {
        id: "social",
        workspace: "~/.openclaw/workspace-social",
      },
    ],
  },
  bindings: [
    {
      agentId: "coding",
      match: { channel: "telegram", accountId: "coding" },
    },
    {
      agentId: "social",
      match: { channel: "telegram", accountId: "social" },
    },
  ],
}
```

4. Restart and verify:

Topic-aware routing note:
- Newer releases can persist ACP bindings for Discord channels and Telegram topics across restarts.
- Telegram forum topics and DM topics can also route to dedicated `agentId` targets.


```bash
openclaw gateway restart
openclaw agents list --bindings
openclaw channels status --probe
```

## Routing Rules

Messages pick an agent via bindings (matched first → wins):

- **channel** — match channel name
- **accountId** — match channel account
- **peer.kind** — `"direct"` (DM) or `"group"`
- **peer.id** — specific sender/group ID
- **topic / thread context** — Telegram topics and ACP-bound thread targets can resolve to agent-specific routes in newer releases
- **Default**: unmatched messages → `agents.defaults` (single-agent fallback)

## Bindings

```json5
{
  bindings: [
    // All Telegram "alerts" account → alert agent
    { agentId: "alerts", match: { channel: "telegram", accountId: "alerts" } },
    // Specific WhatsApp DM → personal agent
    {
      agentId: "alex",
      match: {
        channel: "whatsapp",
        peer: { kind: "direct", id: "+15551230001" },
      },
    },
    // Specific WhatsApp group → family agent
    {
      agentId: "family",
      match: {
        channel: "whatsapp",
        peer: { kind: "group", id: "group-jid@g.us" },
      },
    },
  ],
}
```

## ACP Topic + Channel Bindings

Recent releases make ACP bindings durable across restarts for supported targets such as Discord channels and Telegram topics.

Operational guidance:
- Treat ACP binding storage as persistent configuration, not ephemeral session state.
- In Telegram ACP flows, `--thread here` and `--thread auto` are the important thread-binding modes.
- When one forum topic should map to a different agent, prefer a topic-level binding or topic-level `agentId` override instead of splitting the whole account.
- In `v2026.2.26`, `openclaw agents bindings|bind|unbind` became the preferred operational surface for account-scoped route changes; use it when a user wants to repair or migrate live bindings without editing JSON by hand.

If routing breaks after a restart, re-check binding persistence first before assuming the agent runtime lost context.

## Per-Agent Configuration

Each agent can override:
- Model (primary + fallbacks)
- Tools (profile, allow, deny)
- Sandbox settings
- Group chat mention patterns
- Heartbeat settings
- Subagent thinking level

```json5
{
  agents: {
    list: [{
      id: "support",
      workspace: "~/.openclaw/workspace-support",
      model: { primary: "anthropic/claude-sonnet-4-5" },
      tools: {
        profile: "messaging",
        allow: ["slack"],
      },
      groupChat: {
        mentionPatterns: ["@support"],
      },
      heartbeat: {
        directPolicy: "block",   // "allow" | "block"
      },
      subagents: {
        thinking: "medium",      // Default subagent thinking level
      },
    }],
  },
}
```

## Examples

### WhatsApp Daily Chat + Telegram Deep Work

```json5
{
  agents: {
    list: [
      { id: "daily", workspace: "~/.openclaw/workspace-daily" },
      { id: "work", workspace: "~/.openclaw/workspace-work" },
    ],
  },
  bindings: [
    { agentId: "daily", match: { channel: "whatsapp" } },
    { agentId: "work", match: { channel: "telegram" } },
  ],
}
```

### Same Channel, Specific Peer Gets Different Model

```json5
{
  agents: {
    list: [
      { id: "main" },
      { id: "opus", model: { primary: "anthropic/claude-opus-4-6" } },
    ],
  },
  bindings: [
    {
      agentId: "opus",
      match: { channel: "whatsapp", peer: { kind: "direct", id: "+1555VIP" } },
    },
  ],
}
```

### Multiple Gateways on One Host

Each needs unique port, config, and state:

```bash
OPENCLAW_CONFIG_PATH=~/.openclaw/a.json OPENCLAW_STATE_DIR=~/.openclaw-a openclaw gateway --port 19001
OPENCLAW_CONFIG_PATH=~/.openclaw/b.json OPENCLAW_STATE_DIR=~/.openclaw-b openclaw gateway --port 19002
```
