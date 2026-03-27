---
name: agentguard
description: "AgentGuard 安全引擎 — 拦截危险操作、审计所有行为、保护敏感数据。所有命令/文件/网络操作通过 ag_* 工具经规则引擎审核后执行。"
homepage: https://www.agentguard.site
user-invocable: true
command-dispatch: tool
command-tool: ag_status
command-arg-mode: raw
metadata: { "openclaw": { "emoji": "🛡️", "requires": { "bins": ["agentguard"] }, "primaryEnv": "AGENTGUARD_DAEMON_PORT", "install": [{ "id": "curl-install", "kind": "download", "url": "https://www.agentguard.site/download/install.sh", "label": "Install AgentGuard (curl)" }], "os": ["darwin", "linux"] } }
---

# AgentGuard 安全引擎

你现在集成了 **AgentGuard 安全引擎**。所有 Agent 操作必须经过安全审核，你需要使用 `ag_*` 系列工具替代原生操作。

## 安全架构

AgentGuard 通过 **四层防护** 确保 Agent 无法绕过安全监管：

1. **Gateway 工具封锁 (L1)** — 安装时自动在 `openclaw.json` 中注入 `tools.deny`，在 Gateway 层禁用原生 `exec`/`write`/`edit`/`apply_patch`/`process` 工具。Agent **物理上无法调用**被封锁的原生工具。
2. **规则引擎 (L2)** — 所有 `ag_*` 工具调用经命令黑白名单、文件路径控制、域名白名单审核
3. **脱敏引擎 (L3)** — 自动过滤 API Key、Token、SSH Key 等 15 类敏感信息（输入+输出双向脱敏）
4. **审计日志 (L4)** — 所有操作记录到本地 SQLite，可在 Dashboard 查看，不可篡改

> ⚠️ 原生危险工具已在 Gateway 层被封锁，你只能使用下方 `ag_*` 工具执行操作。

## 安全工具

### 操作类 (替代原生工具)

| ag_* 工具 | 替代原生工具 | 安全能力 |
|-----------|-------------|---------|
| `ag_execute_command` | `exec` / `process` | 命令黑白名单 + 危险命令拦截 + 输出脱敏 |
| `ag_read_file` | `read` | 敏感路径拦截 (.ssh/, keychain, 浏览器数据) + 内容脱敏 |
| `ag_write_file` | `write` / `edit` / `apply_patch` | 路径访问控制 + 写入内容脱敏检查 |
| `ag_list_directory` | `read` (目录) | 目录访问控制 |
| `ag_http_request` | `browser` / 网络工具 | 域名白名单 + 数据外泄防护 |

### 检查类

| ag_* 工具 | 用途 |
|-----------|------|
| `ag_skill_check` | 检查 Skill/插件是否在安全白名单中 |
| `ag_status` | 查看引擎状态：运行模式 / 审计统计 / Panic 状态 |

### 控制类

| ag_* 工具 | 用途 |
|-----------|------|
| `ag_panic` | 🚨 紧急暂停 — 立即拒绝所有后续操作 |
| `ag_resume` | 恢复正常运行 |

## 使用规则

1. **必须使用 `ag_*` 工具**执行所有命令、文件和网络操作。不得使用 `exec`、`read`、`write`、`apply_patch`、`process` 等原生工具绕过安全检查。
2. 当 `ag_*` 工具返回 `🚫 拦截` 信息时，**不要尝试绕过**，向用户说明操作被安全策略拦截及原因。
3. 当返回 `⏳ 等待审批` 时，告知用户正在等待审批，请在 Dashboard 中操作。
4. 如果 AgentGuard daemon 未运行（连接失败），提示用户：
   - 启动命令: `agentguard daemon start`
   - 安装命令: `curl -fsSL https://www.agentguard.site/download/install.sh | sh`
5. 可以随时使用 `ag_status` 查看当前安全状态。
6. 发现可疑行为或用户要求时，使用 `ag_panic` 紧急暂停。

## 安全模式

AgentGuard 有三种运行模式：

- **enforce (强制拦截)** — 违反规则的操作直接拒绝
- **supervised (监督审批)** — 可疑操作暂停等待用户审批
- **permissive (宽松放行)** — 仅记录审计日志，不拦截

## Dashboard

所有操作的审计日志可在本地 Dashboard 查看：**http://127.0.0.1:19821**

Dashboard 提供：实时操作时间线 / 审计统计图表 / 规则配置 / 一键 Panic
