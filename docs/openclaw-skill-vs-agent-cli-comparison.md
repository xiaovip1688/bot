# OpenClaw: Skill 机制 vs Agent CLI 机制对比分析

> 分析时间: 2026-03-04
> 版本: OpenClaw 2026.3.2

---

## 概述

OpenClaw 提供了两种主要的工具/能力扩展机制：

1. **Skill 机制** - 通过 SKILL.md 定义，注入上下文指导 agent 使用工具
2. **Agent CLI 机制** - 通过 ACP Harness 或 Sub-agent 运行独立的编码 agent

---

## 📊 快速对比

| 维度 | Skill 机制 | Agent CLI (ACP Harness) | Sub-agent |
|------|------------|-------------------------|-----------|
| **本质** | 上下文注入 | 独立 agent 进程 | 独立 OpenClaw 实例 |
| **Token 消耗** | 低 (~97 chars/skill) | 中 (最小上下文) | 高 (完整上下文) |
| **延迟** | 无 (即时) | 高 (启动+执行) | 中 (启动) |
| **隔离性** | 无 | 完全隔离 | 会话隔离 |
| **适用场景** | 调用 CLI/API | 代码重构/复杂任务 | 并行研究/后台任务 |
| **错误恢复** | 手动处理 | Agent 自主处理 | Agent 自主处理 |
| **复杂度** | 低 | 高 | 中 |

---

## 🔧 Skill 机制详解

### 工作原理

```
用户请求 → Agent 读取 SKILL.md → Agent 使用 exec 调用 CLI → 返回结果
```

### 核心

1. **SKILL.md 文件** - 包含 YAML frontmatter + 使用说明
2. **自动门控** - 检查 bins/env/config 依赖
3. **上下文注入** - 将使用指南注入 agent 提示词

### 示例

```markdown
---
name: byreal-cli
description: "Byreal DEX CLI for LP management"
metadata:
  {
    "openclaw":
      {
        "requires": { "bins": ["byreal-cli"], "env": ["SOLANA_RPC"] },
      },
  }
---

# Byreal LP Management

## Commands
byreal-cli pools list
byreal-cli positions open --pool <addr> --amount-usd 100
```

### 优势 ✅

| 优势 | 说明 |
|------|------|
| **轻量级** | 每个 skill 只增加 ~97 字符上下文 |
| **即时响应** | 无需启动额外进程 |
| **标准化** | AgentSkills 兼容格式，可共享 |
| **可发现** | `openclaw skills list` 查看所有可用技能 |
| **自动依赖检查** | 门控机制自动检查环境 |
| **版本控制** | 可随项目一起管理 |
| **热重载** | 修改后自动刷新 |

### 劣势 ❌

| 劣势 | 说明 |
|------|------|
| **无隔离** | 在主 agent 上下文中执行 |
| **需要主 agent 智商** | agent 需要理解说明并正确执行 |
| **错误处理依赖 agent** | 无自主错误恢复能力 |
| **状态管理复杂** | 需要手动处理复杂状态 |

### 适用场景

- ✅ 调用外部 CLI 工具 (byreal-cli, gemini-cli)
- ✅ 执行 API 请求
- ✅ 简单的工作流编排
- ✅ 需要快速响应的任务
- ✅ 低频使用的工具

---

## 🤖 Agent CLI 机制详解

### ACP Harness (外部编码 agent)

```
用户请求 → sessions_spawn(runtime="acp") → 启动 Codex/Claude Code/Gemini → 独立执行 → 返回结果
```

#### 工作流程

```python
# Agent 调用
sessions_spawn(
    runtime="acp",
    agentId="codex",
    task="Refactor the auth module to use JWT",
    thread=True,  # 持久会话
    mode="session"
)
```

#### 优势 ✅

| 优势 | 说明 |
|------|------|
| **完全隔离** | 独立进程，不影响主 agent |
| **自主决策** | agent 可以自主迭代和修复 |
| **专业能力** | 使用专门的编码 agent (Claude Code, Codex) |
| **持久会话** | thread 模式支持多轮交互 |
| **复杂任务** | 适合大规模重构、多文件修改 |

#### 劣势 ❌

| 劣势 | 说明 |
|------|------|
| **高延迟** | 需要启动独立进程 |
| **高 Token** | 虽然主 agent 上下文小，但外部 agent 有自己的消耗 |
| **配置复杂** | 需要配置 acp.allowedAgents 等 |
| **调试困难** | 独立进程难以调试 |

### Sub-agent (内部子 agent)

```
用户请求 → sessions_spawn(runtime="subagent") → 启动新 OpenClaw 实例 → 执行任务 → 返回结果
```

#### 工作流程

```python
# Agent 调用
sessions_spawn(
    runtime="subagent",
    task="Research all Bybit V5 API endpoints",
    model="zai/glm-5",  # 可以用便宜模型
    sandbox="require"
)
```

#### 优势 ✅

| 优势 | 说明 |
|------|------|
| **并行执行** | 不阻塞主 agent |
| **灵活配置** | 可指定不同模型、thinking 模式 |
| **沙箱隔离** | 可选沙箱环境执行 |
| **结果推送** | 自动推送结果到请求频道 |

#### 劣势 ❌

| 劣势 | 说明 |
|------|------|
| **高 Token** | 每个 sub-agent 有完整上下文 |
| **成本累积** | 多个 sub-agent 会累积成本 |
| **协调复杂** | 需要设计任务分配和结果合并 |

---

## 🎯 选择指南

### 使用 Skill 机制的场景

```
1. 调用明确的 CLI 工具
   - byreal-cli pools list
   - gemini-cli search "topic"
   
2. 执行简单的 API 调用
   - curl https://api.example.com/data
   
3. 需要即时响应
   - 用户等待的交互场景
   
4. 低频使用的工具
   - 偶尔需要的功能
   
5. 上下文敏感的任务
   - 需要主 agent 上下文的操作
```

### 使用 ACP Harness 的场景

```
1. 大规模代码重构
   - "用 Claude Code 重构认证模块"
   
2. 复杂的多文件修改
   - 跨多个文件的架构调整
   
3. 需要专业编码能力
   - 复杂算法实现
   - 性能优化
   
4. 需要 agent 自主迭代
   - 允许 agent 多次尝试修复
```

### 使用 Sub-agent 的场景

```
1. 并行研究任务
   - 同时研究多个主题
   
2. 长时间后台任务
   - 大规模 API 测试
   
3. 需要隔离执行
   - 不确定安全性的操作
   
4. 成本敏感场景
   - 用便宜模型处理简单任务
```

---

## 💡 最佳实践

### Skill 设计原则

```markdown
1. 单一职责 - 每个 skill 只做一件事
2. 清晰约束 - 列出所有 bins/env/config 依赖
3. 提供示例 - 包含常见用例的命令
4. 错误处理 - 说明常见错误和解决方案
5. 版本标注 - 说明兼容的工具版本
```

### Agent CLI 使用原则

```markdown
1. 明确任务 - 提供清晰的任务描述
2. 适当隔离 - 评估是否需要隔离
3. 成本控制 - 选择合适的模型
4. 超时设置 - 设置合理的超时时间
5. 结果验证 - 检查返回结果
```

---

## 📈 性能与成本分析

### Token 消耗对比 (假设 10 个 skill, 1 次 agent CLI 调用)

| 机制 | Token 消耗 | 说明 |
|------|------------|------|
| **10 Skills** | ~1000 tokens | 10 × 97 chars ≈ 250 tokens (一次性) |
| **ACP Harness** | ~5000+ tokens | 外部 agent 上下文 (独立计算) |
| **Sub-agent** | ~10000+ tokens | 完整 OpenClaw 上下文 |

### 延迟对比

| 机制 | 延迟 | 说明 |
|------|------|------|
| **Skill** | < 1s | exec 调用延迟 |
| **ACP Harness** | 10-60s | 启动 + 执行 + 返回 |
| **Sub-agent** | 5-30s | 启动 + 执行 + 返回 |

---

## 🔄 混合使用场景

### 场景 1: Skill + Sub-agent

```python
# 1. 用 Skill 快速获取信息
byreal-cli pools list --sort-field apr24h

# 2. 用 Sub-agent 并行分析多个池子
for pool in pools:
    sessions_spawn(
        runtime="subagent",
        task=f"Analyze pool {pool}",
        model="cheap-model"
    )
```

### 场景 2: Skill + ACP Harness

```python
# 1. 用 Skill 获取数据
byreal-cli wallet balance

# 2. 用 ACP Harness 生成复杂交易脚本
sessions_spawn(
    runtime="acp",
    agentId="claude",
    task="Generate a rebalancing script based on wallet data"
)
```

---

## 📋 决策流程图

```
                     ┌─────────────────┐
                     │ 需要扩展能力？ │
                     └────────┬────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
     ┌────────▼────────┐             ┌───────▼───────┐
     │ 是否需要隔离？ │             │ 是否编码任务？│
     └────────┬────────┘             └───────┬───────┘
              │                               │
       ┌──────┴──────┐                 ┌─────┴─────┐
       │             │                 │           │
   ┌───▼───┐    ┌────▼────┐      ┌────▼────┐  ┌───▼───┐
   │  No   │    │   Yes   │      │   Yes   │  │  No   │
   └───┬───┘    └────┬────┘      └────┬────┘  └───┬───┘
       │             │                │           │
  ┌────▼────┐   ┌────▼────┐     ┌────▼────┐ ┌────▼────┐
  │  Skill  │   │Sub-agent│     │   ACP   │ │  Skill  │
  │ Mechanism│   │         │     │ Harness │ │ Mechanism│
  └─────────┘   └─────────┘     └─────────┘ └─────────┘
```

---

## 🎓 总结

| 机制 | 核心优势 | 核心劣势 | 最佳场景 |
|------|----------|----------|----------|
| **Skill** | 轻量、即时、标准化 | 无隔离、依赖主 agent | CLI 调用、API 请求 |
| **ACP Harness** | 隔离、自主、专业 | 高延迟、复杂配置 | 大规模重构、复杂编码 |
| **Sub-agent** | 并行、隔离、灵活 | 高 Token、成本累积 | 研究任务、后台作业 |

**黄金法则**：
- 简单任务用 Skill
- 复杂编码用 ACP Harness  
- 并行研究用 Sub-agent

---

*最后更新: 2026-03-04 17:31 SGT*
