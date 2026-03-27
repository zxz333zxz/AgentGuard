# 🛡️ AgentGuard

> AI Agent Security Engine — Block dangerous operations, audit all behaviors, protect sensitive data.

**Built for OpenClaw · Also supports Cursor / Windsurf / Claude**

---

## What is AgentGuard?

AgentGuard is a security layer for AI agents. It intercepts dangerous commands, prevents data leaks, and maintains a tamper-proof audit log of everything your agent does.

### Four-Layer Protection

| Layer | Name | Description |
|-------|------|-------------|
| L1 | **Gateway Lockdown** | Blocks native `exec`/`write`/`edit` tools at the gateway level — agents physically cannot bypass |
| L2 | **Rules Engine** | Command blacklists, file path controls, domain whitelists |
| L3 | **Redaction Engine** | Auto-filters API keys, tokens, SSH keys and 15+ other sensitive data types (input + output) |
| L4 | **Audit Log** | All operations logged to local SQLite — viewable in Dashboard, immutable |

---

## Supported Platforms

| Platform | Status |
|----------|--------|
| [OpenClaw](https://openclaw.ai) | ✅ Full support |
| [Cursor](https://cursor.com) | ✅ Supported |
| [Windsurf](https://codeium.com/windsurf) | ✅ Supported |
| [Claude](https://claude.ai) | ✅ Supported |

---

## Installation

```bash
# Install AgentGuard daemon
curl -fsSL https://www.agentguard.site/download/install.sh | sh

# Start daemon
agentguard daemon start
```

---

## Security Tools (ag_* API)

### Operations (replaces native tools)

| Tool | Replaces | Security |
|------|----------|----------|
| `ag_execute_command` | `exec` / `process` | Command blacklist + dangerous command interception + output redaction |
| `ag_read_file` | `read` | Sensitive path blocking (.ssh/, keychain, browser data) + content redaction |
| `ag_write_file` | `write` / `edit` | Path access control + write content inspection |
| `ag_list_directory` | `read` (directory) | Directory access control |
| `ag_http_request` | network tools | Domain whitelist + data exfiltration prevention |

### Inspection

| Tool | Purpose |
|------|---------|
| `ag_skill_check` | Check if a skill/plugin is in the security whitelist |
| `ag_status` | View engine status: mode / audit stats / panic state |

### Control

| Tool | Purpose |
|------|---------|
| `ag_panic` | 🚨 Emergency stop — immediately reject all subsequent operations |
| `ag_resume` | Resume normal operation |

---

## Security Modes

- **enforce** — Violations are blocked immediately
- **supervised** — Suspicious operations pause and await user approval
- **permissive** — Audit log only, no blocking

---

## Dashboard

View the full audit log at: **http://127.0.0.1:19821**

Features: real-time operation timeline · audit charts · rule configuration · one-click panic

---

## Links

- 🌐 Website: [agentguard.site](https://www.agentguard.site)
- 📦 Skills: [skills/agentguard/SKILL.md](./skills/agentguard/SKILL.md)

---

## 中文说明

AgentGuard 是一个 AI Agent 安全引擎，通过四层防护机制确保 Agent 操作安全可控：

- **L1 网关封锁** — 在 Gateway 层物理禁用危险原生工具
- **L2 规则引擎** — 命令黑白名单、路径控制、域名白名单
- **L3 脱敏引擎** — 自动过滤 API Key、Token 等 15 类敏感信息
- **L4 审计日志** — 所有操作记录本地 SQLite，不可篡改

支持平台：OpenClaw · Cursor · Windsurf · Claude
