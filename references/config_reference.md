# OpenClaw Configuration Reference

Complete field-by-field reference for `~/.openclaw/openclaw.json`. This covers the most important configuration sections.

## Table of Contents

- [Config File](#config-file)
- [Gateway Section](#gateway-section)
- [Channels Section](#channels-section)
- [Agent Defaults](#agent-defaults)
- [Tools Section](#tools-section)
- [Session Section](#session-section)
- [Multi-Agent Routing](#multi-agent-routing)
- [Secrets Section](#secrets-section)
- [Environment Section](#environment-section)
- [Browser Section](#browser-section)
- [Plugins Section](#plugins-section)
- [Hooks Section](#hooks-section)
- [Cron Section](#cron-section)
- [Config Includes](#config-includes)

## Config File

Path: `~/.openclaw/openclaw.json` (JSON5 format)

Override via `OPENCLAW_CONFIG_PATH` env var.

Practical config path order:
1. `OPENCLAW_CONFIG_PATH`
2. `~/.openclaw/openclaw.json`
3. Platform-specific bootstrap / onboarding fallbacks (if the primary file has not been created yet)

## Gateway Section

```json5
{
  gateway: {
    mode: "local",               // "local" (run gateway) | "remote" (connect to remote)
    port: 18789,                 // Single multiplexed port for WS + HTTP
    bind: "loopback",           // "auto" | "loopback" (default) | "lan" | "tailnet" | "custom"
    auth: {
      mode: "token",            // "none" | "token" | "password" | "trusted-proxy"
      token: "your-token",
      // password: "your-password",
      allowTailscale: true,     // Allow Tailscale Serve identity headers
      rateLimit: {
        maxAttempts: 10,
        windowMs: 60000,
        lockoutMs: 300000,
        exemptLoopback: true,   // Default true
      },
    },
    tailscale: {
      mode: "off",              // "off" | "serve" | "funnel"
      resetOnExit: false,
    },
    controlUi: {
      enabled: true,
      basePath: "/openclaw",
    },
    remote: {                   // For mode: "remote"
      token: "oc_remote_xxx",     // Remote-mode gateway token (can also be SecretRef-backed)
    },
    auth: {
      mode: "token",              // Explicit when both token and password are configured
      token: "${OPENCLAW_GATEWAY_TOKEN}",
    },
  },
  talk: {
    silenceTimeoutMs: 1200,        // Added in v2026.3.8; unset = platform default pause window
  },
      url: "ws://gateway.tailnet:18789",
      transport: "ssh",         // "ssh" | "direct"
      token: "your-token",
    },
    trustedProxies: [],         // IP addresses of trusted reverse proxies
    reload: {
      mode: "hybrid",          // "off" | "hot" | "restart" | "hybrid"
    },
  },
}
```

**Port precedence** (highest to lowest):
1. `--port` CLI flag
2. `OPENCLAW_GATEWAY_PORT` env var
3. `gateway.port` in config
4. Default: `18789`

**Auth notes:**
- Non-loopback binds REQUIRE auth
- `"trusted-proxy"`: delegate to identity-aware reverse proxy
- Rate limiter blocks per client IP, returns `429 + Retry-After`

## Channels Section

### Per-Channel Config

```json5
{
  channels: {
    whatsapp: {
      enabled: true,
      dmPolicy: "pairing",      // "pairing" | "allowlist" | "open" | "disabled"
      allowFrom: ["+15551234567"],
      groupPolicy: "allowlist",  // "open" | "allowlist" | "disabled"
      groupAllowFrom: ["+15551234567"],
      groups: {
        "*": { requireMention: true },
        "123@g.us": { requireMention: false },
      },
      selfChatMode: true,       // For personal number use
      textChunkLimit: 4096,
      sendReadReceipts: true,
    },
    telegram: {
      enabled: true,
      botToken: "123:abc",
      dmPolicy: "pairing",
      allowFrom: ["tg:123"],
      streaming: "off",         // "off" | "partial" | "block" | "progress"
      proxy: "socks5://user:pass@host:1080",
    },
    discord: {
      enabled: true,
      token: "bot-token",
      dmPolicy: "pairing",
      guilds: {
        SERVER_ID: {
          requireMention: true,
          users: ["USER_ID"],
        },
      },
    },
    slack: {
      enabled: true,
      mode: "socket",           // "socket" | "http"
      appToken: "xapp-...",
      botToken: "xoxb-...",
      signingSecret: "...",     // For HTTP mode
    },
  },
}
```

### Multi-Account (All Channels)

```json5
{
  channels: {
    telegram: {
      accounts: {
        personal: { botToken: "...", dmPolicy: "pairing" },
        work: { botToken: "...", dmPolicy: "allowlist", allowFrom: ["tg:456"] },
      },
    },
  },
}
```

## Agent Defaults

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: {
        primary: "anthropic/claude-sonnet-4-5",
        fallbacks: ["openai/gpt-5.2"],
      },
      imageModel: {
        primary: "openai/dall-e-3",
      },
      models: {                 // Model catalog + allowlist for /model
        "anthropic/claude-sonnet-4-5": { alias: "Sonnet" },
        "openai/gpt-5.2": { alias: "GPT" },
      },
      imageMaxDimensionPx: 1200,  // Image downscaling (reduces vision-token usage)
      heartbeat: {
        every: "30m",
        directPolicy: "allow",  // "allow" | "block" (v2026.2.25; default reverted to allow)
      },
      subagents: {
        thinking: "medium",     // Default subagent thinking level (v2026.2.2)
      },
      compaction: { ... },
      contextPruning: { ... },
      sandbox: {
        mode: "off",             // "off" | "non-main" | "all"
        scope: "session",        // "session" | "agent" | "shared"
        workspaceAccess: "none", // "none" | "ro" | "rw"
      },
    },
  },
}
```

## Tools Section

```json5
{
  tools: {
    profile: "full",            // "minimal" | "coding" | "messaging" | "full"
    allow: [],                  // Explicit tool allow (if non-empty, everything else blocked)
    deny: ["browser", "canvas"], // Block specific tools
    byProvider: {               // Per-model tool restrictions
      "google-antigravity": { profile: "minimal" },
    },
    elevated: {
      enabled: true,
      allowFrom: {
        whatsapp: ["+15555550123"],
      },
    },
    exec: {
      security: "allow",       // "allow" | "ask" | "deny"
      ask: "risky",            // "always" | "risky" | "never"
      backgroundMs: 10000,
      timeoutSec: 1800,
    },
    fs: {
      workspaceOnly: false,    // Restrict to workspace directory
    },
    web: {
      search: {
        provider: "brave",
        apiKey: "${BRAVE_API_KEY}",
        brave: { mode: "llm-context" },
        // Perplexity-style localization filters are provider-specific
      },
    },
  },
}
```

### Tool Groups

| Group | Tools Included |
|---|---|
| `group:runtime` | exec, process, bash |
| `group:fs` | read, write, edit, apply_patch |
| `group:sessions` | sessions_list, sessions_history, sessions_send, sessions_spawn, session_status |
| `group:memory` | memory_search, memory_get |
| `group:web` | web_search, web_fetch |
| `group:ui` | browser, canvas |
| `group:automation` | cron, gateway |
| `group:messaging` | message |
| `group:nodes` | nodes |

## Session Section

```json5
{
  session: {
    // sessions_spawn inline attachments are controlled under tools.sessions_spawn.attachments in newer releases
    dmScope: "main",           // "main" | "per-channel-peer" | "per-account-channel-peer"
    mainKey: "main",           // Key for the main session
    historyLimit: 100,
  },
}
```

## Multi-Agent Routing

```json5
{
  agents: {
    list: [
    defaults: {
      pdfModel: "anthropic/claude-sonnet-4-5",
      pdfMaxBytesMb: 32,
      pdfMaxPages: 50,
    },
      { id: "main", workspace: "~/.openclaw/workspace" },
      { id: "work", workspace: "~/.openclaw/workspace-work",
        model: { primary: "anthropic/claude-opus-4-6" },
        tools: { profile: "coding" },
      },
    ],
  },
  bindings: [
    { agentId: "work", match: { channel: "telegram", accountId: "work" } },
    { agentId: "main", match: { channel: "whatsapp" } },
  ],
}
```

### Binding Match Fields

- `channel` — channel name
- `accountId` — channel account ID
- `peer.kind` — `"direct"` or `"group"`
- `peer.id` — specific sender/group ID

Operational note:
- In newer releases, account-scoped bindings can also be managed through `openclaw agents bindings`, `openclaw agents bind`, and `openclaw agents unbind` instead of hand-editing this block.

## Secrets Section

See [references/secrets.md](secrets.md) for full details.

## Environment Section

```json5
{
  env: {
    OPENROUTER_API_KEY: "sk-or-...",
    OPENROUTER_HTTP_REFERER: "https://your-app.example",
    OPENROUTER_X_TITLE: "OpenClaw Gateway",
    vars: { GROQ_API_KEY: "gsk-..." },
    shellEnv: {
      enabled: true,           // Import shell env
      timeoutMs: 15000,
    },
  },
}
```

- Use OpenRouter attribution headers when the upstream provider expects app identification in addition to the API key.

## Browser Section

```json5
{
  browser: {
    executablePath: "/usr/bin/google-chrome",
    relayBindHost: "127.0.0.1",   // Explicit bind host for WSL2 / cross-namespace relay use
    // ... CDP config
  },
}
```

## Plugins Section

```json5
{
  plugins: {
    allow: ["backups", "memory-lancedb-pro"],  // Explicit allowlist
    slots: {
      contextEngine: "lossless-claw",
    },
    entries: {
      "lossless-claw": {
        hooks: { allowPromptInjection: false },
      },
    },
  },
}
```

## Hooks Section

```json5
{
  hooks: {
    gmail: { ... },
    // Webhook integrations
  },
}
```

## Cron Section

```json5
{
  cron: {
    jobs: [
      { schedule: "0 9 * * *", message: "Good morning!", channel: "whatsapp" },
    ],
  },
}
```

## Config Includes

Split config across files using `$include`:

```json5
{
  $include: ["./channels.json", "./agents.json"],
  gateway: { ... },
}
```

Env var substitution works inside `$include` files.
