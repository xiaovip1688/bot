# Skill 机制最佳实践指南

> 基于 OpenClaw Skills 机制深度分析
> 版本: OpenClaw 2026.3.2
> 分析时间: 2026-03-04

---

## 概述

Skill 是 OpenClaw 扩展能力的主要机制。 通过 SKILL.md 文件定义, Skill 可以：
1. 注入上下文，指导 agent 如何使用工具
2. 自动加载, 根据环境自动激活
3. 轻量级, 每个 skill 只增加 ~97 字符上下文

---

## 核心概念

### SKILL.md 文件结构

```markdown
---
name: skill-name
description: "简短描述 skill 的用途"
metadata:
  {
    "openclaw":
      {
        "requires": { "bins": ["required-cli"], "env": ["API_KEY"] },
        "primaryEnv": "API_KEY",
        "emoji": "🛠️"
      },
  }
---

# 详细使用说明
...
```

### 关键 Frontmatter 字段

| 字段 | 必填 | 说明 |
|------|------|------|
| `name` | ✅ | Skill 唯一标识符 |
| `description` | ✅ | 简短描述（用于 skill 列表和 agent 决策） |
| `metadata.openclaw.requires.bins` | ❌ | 风格依赖检查的 CLI 列表 |
| `metadata.openclaw.requires.env` | ❌ | 风格依赖的环境变量列表 |
| `metadata.openclaw.requires.config` | ❌ | 风格依赖的配置项列表 |
| `metadata.openclaw.primaryEnv` | ❌ | 主要 API Key 对应的环境变量名 |
| `metadata.openclaw.emoji` | ❌ | 在 UI 中显示的 emoji |
| `metadata.openclaw.always` | ❌ | 设为 true 则跳过所有门控检查 |

| `metadata.openclaw.os` | ❌ | 限制操作系统 (`darwin`, `linux`, `win32`) |

---

## Skill 类型分析

### 1. CLI Wrapper Skill

**定义**: 包装外部 CLI 工具的 skill

**示例**: byreal-cli

```markdown
---
name: byreal-cli
description: "Byreal DEX (Solana) all-in-one CLI: query pools/tokens/TVL, analyze pool APR & risk, open/close/claim CLMM positions, token swap, wallet & balance management. Use when user mentions Byreal, LP, liquidity, pools, DeFi positions, token swap, or Solana DEX operations."
metadata:
  {
    "openclaw":
      {
        "requires": { "bins": ["byreal-cli"] },
        "primaryEnv": "SOLANA_RPC_URL",
      },
  }
---

# Byreal LP Management

## Installation

```bash
which byreal-cli && byreal-cli --version

npm install -g https://github.com/byreal-git/byreal-cli/releases/latest/download/byreal-cli.tgz
```

## Commands

```bash
# Get pool list
byreal-cli pools list --sort-field apr24h --page-size 10

# Open position
byreal-cli positions open --pool <addr> --price-lower <p> --price-upper <p> --amount-usd 100 --confirm
```

## Hard Constraints

1. **`-o json` only for parsing** — when showing results to the user, **omit it** and let the CLI's built-in tables/charts render directly. Never fetch JSON then re-draw charts yourself.
2. **Never truncate on-chain data** — always display the FULL string for: transaction signatures (txid), mint addresses, pool addresses, NFT addresses, wallet addresses. Never use `xxx...yyy` abbreviation.
3. **Never display private keys** - use keypair paths only
4. **Preview first** with `--dry-run`, then `--confirm`
```

**适用场景**:
- ✅ 包装现有的 CLI 工具
- ✅ 工具有清晰的命令行接口
- ✅ 输出格式化良好（表格/JSON）
- ✅ 鰃用频率较低

---

### 2. API Integration Skill

**定义**: 调用外部 API 的 skill

**示例**: weather skill

```markdown
---
name: weather
description: "Get current weather and forecasts via wttr.in or Open-Meteo. Use when: user asks about weather, temperature, or forecasts for any location. NOT for: historical weather data, severe weather alerts, or detailed meteorological analysis."
metadata:
  {
    "openclaw":
      {
        "requires": { "env": ["OPEN_METEO_API_KEY"] },
        "emoji": "🌤️",
      },
  }
---

# Weather Information

## Commands

```bash
# Get current weather
curl "https://api.open-meteo.com/v1/forecast?latitude=<lat>&longitude=<lon>&current_weather=true"

# Get forecast
curl "https://api.open-meteo.com/v1/forecast?latitude=<lat>&longitude=<lon>&hourly=temperature_2m"
```

## Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `latitude` | Yes | Location latitude |
| `longitude` | Yes | Location longitude |
| `hourly` | No | Forecast hours (default: 24) |

## Error Handling

- Handle rate limits gracefully
- Provide user-friendly error messages
- Fall back to alternative data sources if primary fails
```

**适用场景**:
- ✅ 蟥询类操作
- ✅ 数据获取
- ✅ 需要认证的 API

---

### 3. Workflow Skill

**定义**: 执行多步骤工作流的 skill

**示例**: healthcheck skill

```markdown
---
name: healthcheck
description: "Host security hardening and risk-tolerance configuration for OpenClaw deployments. Use when a user asks for security audits, firewall/SSH/update hardening, risk posture, exposure review, or version status checks."
metadata:
  {
    "openclaw":
      {
        "requires": { "bins": ["openclaw"] },
        "emoji": "🔒",
      },
  }
---

# Security Health Check

## Workflow

1. **Version Check** → 检查 OpenClaw 版本
2. **Network Audit** → 检查开放端口
3. **SSH Hardening** → 检查 SSH 配置
4. **Update Status** → 检查系统更新
5. **Risk Assessment** → 综合风险评分

## Commands

```bash
# Version check
openclaw --version

# Network audit
sudo netstat -tulpn | grep LISTEN

# SSH hardening
sudo grep -E "^(PermitRootLogin|PasswordAuthentication|PubkeyAuthentication)" /etc/ssh/sshd_config

# Update status
sudo apt update -s
```

## Report Format

```markdown
# Security Health Check Report

## OpenClaw Version
- Current: 2026.3.2
- Status: ✅ Up to date

## Network Exposure
- SSH: 22/tcp (open)
- Gateway: 7654/tcp (open)
- Recommendation: ⚠️ Consider changing SSH port

...
```
```

**适用场景**:
- ✅ 多步骤流程
- ✅ 需要生成报告
- ✅ 系统检查/审计

---

## 最佳实践

### 1. SKILL.md 编写

#### ✅ 像这样做

```markdown
---
name: my-skill
description: "Clear, concise description of what this skill does"
metadata:
  {
    "openclaw":
      {
        "requires": { "bins": ["required-cli"] },
        "emoji": "🎯"
      },
  }
---

# Skill Title

Brief introduction to what this skill does.

## Installation

```bash
# Check if already installed
which required-cli

# Install if not
npm install -g required-cli
```

## Commands

### Query Data

```bash
required-cli query --param value
```

### Execute Action

```bash
required-cli execute --param value --confirm
```

## Hard Constraints

1. **Constraint 1** - Explanation of why this is important
2. **Constraint 2** - Explanation of the consequence

## Error Handling

- Common errors and how to resolve them
- Fallback strategies
```

#### ❌ 不要这样做

```markdown
---
name: my-skill
description: "This skill does many things including querying data, executing actions, managing state, handling errors, and more"
---

This skill can do everything you need!
```

**问题**:
- 描述太长太泛
- 没有结构化说明
- 缺少硬约束
- 缺少错误处理

---

### 2. 依赖声明

#### ✅ 像这样做

```markdown
metadata:
  {
    "openclaw":
      {
        "requires": {
          "bins": ["node", "npm"],
          "env": ["NODE_ENV", "API_KEY"],
          "config": ["browser.enabled"]
        },
        "primaryEnv": "API_KEY"
      },
  }
```

#### ❌ 不要这样做

```markdown
metadata:
  {
    "openclaw":
      {
        "requires": {
          "bins": ["node", "npm", "yarn", "pnpm"],  // 太多可选工具
          "env": ["API_KEY", "SECRET_KEY", "TOKEN"]  // 没有主次之分
        }
      },
  }
```

---

### 3. 硬约束定义

#### ✅ 像这样做

```markdown
## Hard Constraints

1. **`-o json` only for parsing** — when showing results to the user, **omit it** and let the CLI's built-in tables/charts render directly. Never fetch JSON then re-draw charts yourself.
   - Reason: CLI already provides formatted output
   - Impact: Saves tokens and provides better UX

2. **Never truncate on-chain data** — always display the FULL string for: transaction signatures (txid), mint addresses, pool addresses, Never use `xxx...yyy` abbreviation.
   - Reason: Users need to verify data on-chain
   - Impact: Enables blockchain verification
```

#### ❌ 不要这样做

```markdown
## Constraints

- Be careful with data
- Don't show too much
- Handle errors properly
```

**问题**:
- 太模糊
- 没有说明原因和影响
- 不够具体

---

### 4. 错误处理

#### ✅ 像这样做

```markdown
## Error Handling

### Insufficient Balance

**Error**: `Insufficient Balance`

**Cause**: Wallet does not have enough tokens for the operation

**Solution**:
1. Check wallet balance: `byreal-cli wallet balance`
2. Swap tokens if needed: `byreal-cli swap execute`
3. Retry the operation

### Transaction Failed

**Error**: `Transaction failed: signature invalid`

**Cause**: Keypair may be corrupted or network issues

**Solution**:
1. Verify keypair: `cat ~/.config/solana/keypair.json`
2. Check network: `solana cluster-version`
3. Regenerate keypair if needed
```

#### ❌ 不要这样做

```markdown
## Errors

- If something goes wrong, try again
- Check your configuration
- Contact support
```

---

### 5. 示例命令

#### ✅ 像这样做

```markdown
## Commands

### List Pools

```bash
# List top 10 pools by TVL
byreal-cli pools list --sort-field tvl --page-size 10

# List pools by APR
byreal-cli pools list --sort-field apr24h --page-size 10

# Filter by category
byreal-cli pools list --category 1  # stable pools
```

### Open Position

```bash
# Preview position open (no execution)
byreal-cli positions open \
  --pool DF5SshvX3XTKcvJNxi384FYvfeog7ih9kb89hGbaQBpA \
  --price-lower 28.16 \
  --price-upper 34.33 \
  --amount-usd 100 \
  --dry-run

# Execute position open
byreal-cli positions open \
  --pool DF5SshvX3XTKcvJNxi384FYvfeog7ih9kb89hGbaQBpA \
  --price-lower 28.16 \
  --price-upper 34.33 \
  --amount-usd 100 \
  --confirm
```
```

#### ❌ 不要这样做

```markdown
## Commands

```bash
byreal-cli pools list
byreal-cli positions open
```
```

**问题**:
- 缺少参数说明
- 没有常见用例
- 缺少预览步骤

---

## Skill 发现与加载

### 加载优先级

```
工作区 Skills (<workspace>/skills)
    ↓ 覆盖
托管 Skills (~/.openclaw/skills)
    ↓ 覆盖
内置 Skills (npm 包内)
```

### 检查 Skills

```bash
# 列出所有 Skills
openclaw skills list

# 列出符合条件的 Skills
openclaw skills list --eligible

# 检查特定 Skill
openclaw skills info byreal-cli

# 检查所有依赖
openclaw skills check
```

---

## Skill 调试

### 常见问题

#### 1. Skill 未被识别

**症状**: `openclaw skills list` 不显示新 skill

**检查**:
```bash
# 检查文件位置
ls ~/.openclaw/skills/my-skill/SKILL.md

# 检查 frontmatter 格式
head -20 ~/.openclaw/skills/my-skill/SKILL.md

# 检查依赖
openclaw skills info my-skill
```

**解决**:
- 确保 SKILL.md 在正确的位置
- 检查 YAML frontmatter 格式
- 验证依赖是否满足

#### 2. Skill 不符合条件

**症状**: `openclaw skills list --eligible` 不显示 skill

**检查**:
```bash
# 检查依赖
which required-cli
echo $REQUIRED_ENV

# 检查配置
cat ~/.openclaw/openclaw.json | grep skill-name
```

**解决**:
- 安装缺失的 CLI
- 设置环境变量
- 更新配置文件

#### 3. Skill 执行失败

**症状**: Agent 调用 skill 时失败

**检查**:
```bash
# 手动执行命令
required-cli command --param value

# 检查环境
env | grep REQUIRED

# 检查权限
ls -la ~/.config/required/
```

**解决**:
- 验证命令可执行
- 检查环境变量
- 验证文件权限

---

## 高级模式

### 1. 组合多个 Skills

```python
# 1. 使用 weather skill 获取天气
weather_data = get_weather(location)

# 2. 使用 calendar skill 检查日程
events = check_calendar(date)

# 3. 使用 notification skill 发送提醒
if weather_data["will_rain"]:
    send_notification(f"Rain expected at {location}")
```

### 2. 动态 Skill 加载

```markdown
---
name: adaptive-skill
metadata:
  {
    "openclaw":
      {
        "requires": {
          "anyBins": ["docker", "podman", "kubectl"]
        }
      },
  }
---

# Container Management

This skill works with Docker, Podman, or Kubernetes, whichever is available.
```

### 3. 条件执行

```markdown
## Execution Flow

1. Check if dry-run is requested
2. If dry-run: show preview, exit
3. Check prerequisites (balance, permissions)
4. Execute operation
5. Verify result
6. Handle errors
```

---

## Token 优化

### 每个 Skill 的 Token 成本

```
基础开销: ~195 字符 (当 ≥1 Skills 时)
每个 Skill: ~97 字符 + name + description + location 的长度
```

### 优化策略

1. **精简描述**: 用最少的字描述清楚功能
2. **避免重复**: 不要在多个 skills 中重复相同信息
3. **延迟加载**: 只有需要的 skill 才加载详细信息
4. **使用引用**: 用链接代替完整内容

---

## 安全考虑

### 私钥管理

```markdown
## Security

### Private Keys

- **Never** include private keys in SKILL.md
- **Never** log private keys
- **Always** use environment variables or config files
- **Prefer** keypair files over environment variables

### Configuration

```json
{
  "skills": {
    "entries": {
    "byreal-cli": {
      "enabled": true,
      "apiKey": "SOLANA_RPC_URL",
      "env": {
        "SOLANA_RPC_URL": "https://api.mainnet-beta.solana.com"
      }
    }
  }
}
```
```

### 权限控制

```markdown
## Permissions

### Read Operations
- ✅ Safe to allow without confirmation
- Examples: query pool, check balance

### Write Operations
- ⚠️ Require user confirmation
- Examples: open position, execute swap

### Destructive Operations
- 🔴 Require explicit confirmation
- Examples: close position, withdraw funds
```

---

## 案例研究: byreal-cli

### Skill 设计

| 方面 | 设计 |
|------|------|
| **类型** | CLI Wrapper |
| **依赖** | byreal-cli (CLI) |
| **环境** | SOLANA_RPC_URL (可选) |
| **约束** | 不截断链上数据, 不显示私钥, 先预览后确认 |

### 关键决策

1. **为什么 CLI Wrapper？**
   - byreal-cli 已经是成熟的 CLI 工具
   - 有清晰的命令行接口
   - 输出格式化良好

2. **为什么 Hard Constraints？**
   - 链上数据需要完整显示（用于验证）
   - 私钥敏感（不能暴露给 agent）
   - 交易需要预览（防止误操作）

3. **为什么 Skill 而不是直接调用？**
   - 自动注入使用说明
   - agent 自动学会正确使用
   - 可以热更新 SKILL.md

### 使用流程

```
用户: "开仓 HYPE 池子 $50"
    ↓
Agent: 读取 SKILL.md
    ↓
Agent: 决定命令
byreal-cli positions open --pool <addr> --amount-usd 50 --dry-run
    ↓
Agent: 展示预览给用户
    ↓
用户: 确认
    ↓
Agent: 执行
byreal-cli positions open --pool <addr> --amount-usd 50 --confirm
    ↓
Agent: 格式化结果
```

---

## 总结

### Skill 机制适用场景

| 场景 | 推荐度 | 说明 |
|------|--------|------|
| 包装 CLI 工具 | ⭐️⭐️⭐️⭐️⭐️ | 最佳用例 |
| API 调用 | ⭐️⭐️⭐️⭐️ | 适合查询类操作 |
| 多步骤流程 | ⭐️⭐️⭐️⭐️ | 适合有明确步骤的流程 |
| 复杂决策 | ⭐️⭐️ | 需要考虑 Sub-agent |
| 需要隔离 | ⭐️ | 需要考虑 ACP Harness |

### 关键原则

1. **单一职责** - 每个 skill 只做一件事
2. **清晰约束** - 明确说明限制和原因
3. **完整示例** - 提供常见用例的命令
4. **错误处理** - 说明常见错误和解决方案
5. **安全优先** - 保护敏感信息

6. **用户友好** - 提供清晰的输出和反馈

---

*最后更新: 2026-03-04 17:52 SGT*
