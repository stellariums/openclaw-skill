# OpenClaw Agent Skill 🦞

[![Release](https://img.shields.io/github/v/release/stellariums/openclaw-skill)](https://github.com/stellariums/openclaw-skill/releases)
[![License](https://img.shields.io/github/license/stellariums/openclaw-skill)](LICENSE)
[![Stars](https://img.shields.io/github/stars/stellariums/openclaw-skill?style=social)](https://github.com/stellariums/openclaw-skill/stargazers)

[English](README.md) | 中文文档

一个偏生产可用的 **Agent Skill**，可以把 Codex、Claude Code、Antigravity 或其他 AI 助手，变成更懂 OpenClaw 的运维助手。

> 安装一次后，你就可以直接让助手用更完整的上下文，去诊断、配置、加固和维护本地 OpenClaw 部署。

## 为什么值得装

- **偏运维实战** — 重点不是泛泛问答，而是 OpenClaw 的真实维护场景
- **覆盖面完整** — 包括频道、模型提供商、Gateway 运维、多 Agent 路由、Secrets、Sandbox 与安全
- **输出更可执行** — 更适合分步骤排查、修改配置、恢复服务和做结果验证

## 适合什么场景

- 运行、升级或迁移自托管 OpenClaw
- 新增频道、模型提供商或 Agent 路由规则
- 排查启动失败、鉴权错误、绑定失效、Gateway 不健康等问题
- 给 AI 助手装一套可复用的 OpenClaw 知识包，而不是每次都重讲文档

## 可以直接怎么问

- `Help me diagnose my local OpenClaw setup`
- `检查我的 OpenClaw 配置是否有问题`
- `Set up a Telegram bot for OpenClaw`
- `Why is my Gateway not responding?`
- `加固我的 OpenClaw 安全配置`

### 同步的版本范围

目前这套 Skill 已覆盖 2026 年 3 月上旬的关键稳定版本变更，重点包括：

- `v2026.3.8`：`backup create/verify`、`talk.silenceTimeoutMs`、Brave `llm-context`、`acp --provenance`、`gateway.remote.token`
- `v2026.3.7`：ContextEngine 插件槽、ACP 持久化频道/话题绑定、Telegram topic 级路由、`gateway.auth.token` 的 SecretRef 支持、`messages.tts.openai.baseUrl`
- `v2026.3.2`：`openclaw config validate`、更广泛的 SecretRef 覆盖、一等公民 `pdf` 工具、`sessions_spawn` 内联附件

## Skill 结构

```
OpenClaw-Skill/
├── SKILL.md                     # 主入口（核心工作流、命令速查、故障签名表）
└── references/
    ├── channels.md              # 20+ 频道配置指南（WhatsApp、Telegram、Discord 等）
    ├── gateway_ops.md           # Gateway 架构、服务管理、远程访问
    ├── multi_agent.md           # 多 Agent 路由、Bindings、Agent 配置
    ├── providers.md             # 20+ 模型提供商（Anthropic、OpenAI、Ollama 等）
    └── security.md              # 认证、访问控制、加固基线、事件响应
```

**共计约 1,400 行**结构化参考文档，覆盖 OpenClaw 所有核心功能。

## 快速安装

如果你已经知道自己在用哪个助手，可以直接看这里：

- `Codex`：`git clone https://github.com/stellariums/openclaw-skill.git ~/.codex/skills/openclaw`
- `Claude Code`：先克隆仓库，再通过 `/agents` 创建 `openclaw` subagent
- `Antigravity`：`cp -r openclaw-skill ~/.gemini/antigravity/skills/openclaw`

### 安装后验证

安装完成后，可以直接试这些提示词：

- `Help me diagnose my local OpenClaw setup`
- `检查我的 OpenClaw 配置是否有问题`
- `Use the openclaw skill to troubleshoot Gateway startup`

如果助手已经开始按 `SKILL.md` 的流程工作，说明安装成功。

## 安装方法

### 下载这个 Skill

你可以任选一种方式获取：

1. 直接克隆仓库：

```bash
git clone https://github.com/stellariums/openclaw-skill.git
```

2. 从 GitHub 下载 ZIP：

- 打开 `https://github.com/stellariums/openclaw-skill`
- 点击 `Code` → `Download ZIP`
- 解压到本地

### Codex 用户

最方便的方式，是直接把仓库克隆到 Codex 的 skills 目录：

```bash
# macOS / Linux
git clone https://github.com/stellariums/openclaw-skill.git ~/.codex/skills/openclaw
```

```powershell
# Windows PowerShell
git clone https://github.com/stellariums/openclaw-skill.git "$env:USERPROFILE\.codex\skills\openclaw"
```

安装后重启 Codex，让新 skill 被加载。

如果你下载的是 ZIP，也可以解压后手动复制到 `~/.codex/skills/openclaw` 或 `%USERPROFILE%\.codex\skills\openclaw`。

### Claude Code 用户

根据 Anthropic 当前文档，Claude Code 没有公开的原生 `skills/` 目录；最接近的内置方式是用户级 subagent，也就是 `~/.claude/agents/`。

最方便的方式，是直接在 Claude Code 里运行 `/agents`，创建一个用户级 `openclaw` subagent，并让它使用这个仓库。

如果你更偏好文件方式，也可以这样安装：

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

在 Windows 上，可以把仓库克隆到 `%USERPROFILE%\.claude\openclaw-skill`，然后按同样内容创建 `%USERPROFILE%\.claude\agents\openclaw.md`。

完成后，你可以直接在 Claude Code 里让它使用 `openclaw` subagent 来处理 OpenClaw 相关任务。

### Antigravity（Claude）用户

将下载后的文件夹复制到 Antigravity 的 skills 目录；如果目录名不同，建议改成 `openclaw`：

```bash
cp -r openclaw-skill ~/.gemini/antigravity/skills/openclaw
```

在 Windows 上，也可以手动复制到 `%USERPROFILE%\.gemini\antigravity\skills\openclaw`。

安装后，当你提到 OpenClaw 相关任务时，Skill 会自动触发。

### 其他 AI 助手

也可以用同样的下载方式获取本仓库，然后复用 `SKILL.md` 和 `references/`，适配任何支持 Skill 或知识注入的 AI 助手。

## 使用示例

安装后，自然语言提问即可：

| 你说的话 | AI 的操作 |
|---|---|
| "帮我升级 OpenClaw" | 执行 `npm install -g openclaw@latest`、`openclaw doctor`、重启 Gateway、验证状态 |
| "配置一个 Telegram Bot" | 引导创建 Bot、设置 Token、写入配置、验证连接 |
| "Gateway 没有响应" | 运行诊断命令梯子：status → logs → doctor → channels probe |
| "加固 OpenClaw 安全配置" | 运行安全审计、应用加固基线、修复权限 |
| "添加第二个 Agent 用于工作" | 创建 Agent、设置工作区、配置 Bindings、重启 |
| "EADDRINUSE 错误" | 识别端口冲突，执行 `openclaw gateway --force` 或更换端口 |

## 常用命令速查

```bash
# 状态与健康检查
openclaw status                    # 总体状态
openclaw gateway status            # Gateway 守护进程状态
openclaw config validate           # 启动/重启前校验配置
openclaw doctor                    # 诊断问题
openclaw channels status --probe   # 频道健康检查

# Gateway 管理
openclaw gateway install           # 安装为系统服务
openclaw gateway start/stop/restart
openclaw backup create             # 变更前创建本地备份
openclaw backup verify <归档>      # 校验备份

# 配置管理
openclaw config get <路径>          # 读取配置值
openclaw config set <路径> <值>     # 设置配置值
openclaw configure                 # 交互式向导

# 安全
openclaw security audit            # 检查安全状况
openclaw security audit --fix      # 自动修复问题
openclaw secrets reload            # 重新加载密钥引用

# 频道
openclaw channels add              # 添加频道（向导模式）
openclaw channels login            # WhatsApp QR 配对
openclaw channels list             # 显示已配置频道

# 模型
openclaw models set <模型>          # 设置默认模型
openclaw models status --probe     # 检查认证状态
```

## 文档来源

本 Skill 基于 [OpenClaw 官方文档](https://docs.openclaw.ai/) 构建，涵盖：

- [安装](https://docs.openclaw.ai/install)
- [Gateway 架构](https://docs.openclaw.ai/concepts/architecture)
- [配置](https://docs.openclaw.ai/gateway/configuration)
- [频道](https://docs.openclaw.ai/channels)
- [模型提供商](https://docs.openclaw.ai/providers)
- [工具](https://docs.openclaw.ai/tools)
- [多 Agent 路由](https://docs.openclaw.ai/concepts/multi-agent)
- [安全](https://docs.openclaw.ai/gateway/security)
- [故障排查](https://docs.openclaw.ai/gateway/troubleshooting)
- [CLI 参考](https://docs.openclaw.ai/cli)

## 许可证

本 Skill 免费提供给 AI 助手使用。OpenClaw 本身采用 [MIT 许可证](https://github.com/openclaw/openclaw/blob/main/LICENSE)。

## 贡献

欢迎 Issue 和 PR！如果 OpenClaw 发布了新功能或变更，请随时更新 references 文件。
