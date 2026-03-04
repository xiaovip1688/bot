# byreal-cli 内部机制分析

> 分析时间: 2026-03-04
> 版本: 0.2.1

---

## 概述

byreal-cli 是一个 Node.js CLI 工具，用于与 Byreal DEX (Solana) 交互。本文档分析其内部机制、与 OpenClaw exec 工具的关系、以及数据流。

---

## 架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                         OpenClaw Agent                          │
│  ┌─────────────┐                                                │
│  │  SKILL.md   │ (注入使用说明)                                  │
│  └──────┬──────┘                                                │
│         ↓                                                       │
│  ┌──────▼──────┐                                                │
│  │  exec 工具  │ ← Agent 调用 byreal-cli                            │
│  └──────┬──────┘                                                │
│         ↓                                                       │
│  ┌──────────────────▼─────────────────────┐                              │
│  │  byreal-cli (Node.js)                                        │
│  └──────────────────┬─────────────────────┘                              │
│         ↓                     │                     ↓                             │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐    │
│  │  RPC Client    │  │  Wallet Manager │  │  API Client    │    │
│  └───────────────┘  └───────────────┘  └───────────────┘    │
│         ↓                     │                     ↓                             │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐    │
│  │  Solana Network │  │  Local Config  │  │  Byreal API   │    │
│  └───────────────┘  └───────────────┘  └───────────────┘    │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 组件分析

### 1. OpenClaw Agent

**角色**: 用户交互界面 + 决策中心

**职责**:
- 掄收用户请求
- 读取 SKILL.md 获取使用说明
- 决定调用哪个 CLI 命令
- 格式化结果给用户

**Token 消耗**: 每个 skill ~97 字符

---

### 2. SKILL.md

**角色**: 硬编码的知识库

**位置**: `~/.agents/skills/byreal-cli/SKILL.md`

**职责**:
- 提供 CLI 命令示例
- 说明参数和约束
- 定义错误处理规则

**示例**:
```markdown
---
name: byreal-cli
description: "Byreal DEX CLI for LP management"
---

## Open Position
byreal-cli positions open --pool <addr> --amount-usd 100 --confirm

## Hard Constraints
1. Never truncate on-chain data (txid, addresses)
2. Preview first with --dry-run
3. Large amounts (>$1000) require confirmation
```

---

### 3. exec 工具

**角色**: 通用 shell 执行器

**职责**:
- 启动子进程 (byreal-cli)
- 传递命令参数
- 捕获 stdout/stderr
- 超时控制
- 环境变量传递

**不负责**:
- ❌ 錾上操作
- ❌ 交易签名
- ❌ RPC 调用
- ❌ 业务逻辑

**实现**:
```typescript
// exec 工具伪代码
async function exec(options: ExecOptions): Promise<ExecResult> {
  // 1. 启动子进程
  const child = spawn(options.command, {
    cwd: options.workdir,
    env: options.env,
    timeout: options.timeout,
  });
  
  // 2. 捕获输出
  let stdout = '';
  let stderr = '';
  child.stdout.on('data', (data) => stdout += data);
  child.stderr.on('data', (data) => stderr += data);
  
  // 3. 等待完成
  const result = await child.promise;
  
  // 4. 返回结果
  return {
    stdout,
    stderr,
    exitCode: result.exitCode,
  };
}
```

---

### 4. byreal-cli

**角色**: Solana 交易执行器

**位置**: `/Users/gm/.nvm/versions/node/v25.6.1/bin/byreal-cli`

**技术栈**: Node.js + TypeScript

**核心模块**:

| 模块 | 职责 |
|------|------|
| **Wallet Manager** | 读取/存储私钥, 签名交易 |
| **RPC Client** | 连接 Solana 网络 |
| **Transaction Builder** | 构建交易指令 |
| **API Client** | 调用 Byreal API 获取池子数据 |
| **CLI Parser** | 解析命令行参数 |
| **Output Formatter** | 格式化表格/JSON 输出 |

**配置文件**: `~/.config/byreal/config.json`
```json
{
  "keypair": "~/.config/solana/keypair.json",
  "rpcUrl": "https://api.mainnet-beta.solana.com",
  "byrealApiUrl": "https://api.byreal.xyz"
}
```

---

## 调用链详解

### 用户请求: 开仓 HYPE/USDC

```
用户: "开仓HYPE池子 $50"
    ↓
Agent 读取 SKILL.md
    ↓
Agent 决定使用: byreal-cli positions open --pool DF5S... --amount-usd 50 --confirm
    ↓
Agent 调用 exec 工具
    ↓
exec(command="byreal-cli positions open --pool DF5s... --amount-usd 50 --confirm")
    ↓
exec 启动 byreal-cli 子进程
    ↓
byreal-cli 执行内部流程:
    ↓
[详细内部流程见下文]
    ↓
结果返回给 Agent
    ↓
Agent 格式化给用户
```

### byreal-cli 内部流程

```python
# 1. 解析命令行
const args = parseArgs(process.argv);
// args = {
//   pool: "DF5sshvX3XTKcvJNxi384FYvfeog7ih9kb89hGbaQBpA",
//   amountUsd: 50,
//   confirm: true
// }

# 2. 读取配置
const config = loadConfig("~/.config/byreal/config.json");
// config = {
//   keypair: "~/.config/solana/keypair.json",
//   rpcUrl: "https://api.mainnet-beta.solana.com",
// }

# 3. 加载钱包
const keypair = loadKeypair(config.keypair);
// keypair = { publicKey: "...", privateKey: "..." }

# 4. 连接 RPC
const connection = new Connection(config.rpcUrl);

# 5. 获取池子信息
const poolInfo = await fetchPoolInfo(args.pool);
// poolInfo = {
//   baseMint: "98sMhvDwXj1RQi5c5Mndm3vPe9cBqPrbLaufMXFNMh5g", // HYPE
//   quoteMint: "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v", // USDC
//   currentPrice: 31.46,
// }

# 6. 计算仓位
const position = calculatePosition({
  priceLower: 28.16,
  priceUpper: 34.33,
  amountUsd: 50,
  currentPrice: 31.46,
});
// position = {
//   baseAmount: 0.711, // HYPE
//   quoteAmount: 28.03, // USDC
// }

# 7. 构建交易
const tx = new Transaction();
tx.add(openPositionInstruction({
  pool: args.pool,
  owner: keypair.publicKey,
  positionBump: calculatePositionBump(position),
  ...positionParams
}));

# 8. 签名交易
tx.sign(keypair);

# 9. 发送交易
const sig = await connection.sendRawTransaction(tx.serialize());

# 10. 等待确认
const confirmation = await connection.confirmTransaction(sig);

# 11. 返回结果
return {
  signature: sig,
  nftAddress: deriveNftAddress(sig),
  confirmed: true,
  pool: args.pool,
  position: position,
};
```

---

## 关键点

### 1. exec 是通用工具

| 特点 | 说明 |
|------|------|
| **通用性** | 可以执行任何 shell 命令, 不限于 byreal-cli |
| **隔离性** | 子进程执行, 不影响主进程 |
| **超时控制** | 可配置 timeout |
| **环境传递** | 传递 API keys 等环境变量 |

### 2. byreal-cli 是专用工具

| 特点 | 说明 |
|------|------|
| **专业性** | 只处理 Byreal/Solana 操作 |
| **链上能力** | 直接与 Solana 网络交互 |
| **钱包管理** | 本地私钥管理, 签名交易 |
| **独立使用** | 可以在终端直接使用, 不依赖 agent |

### 3. SKILL.md 是桥梁

| 特点 | 说明 |
|------|------|
| **知识注入** | 教 agent 如何使用 byreal-cli |
| **约束定义** | 定义参数规则和错误处理 |
| **版本兼容** | 可随项目更新 |

### 4. 链上操作在 byreal-cli 内

| 操作 | 说明 |
|------|------|
| **签名** | 使用本地私钥签名交易 |
| **RPC 调用** | 直接调用 Solana RPC |
| **交易构建** | 构建正确的指令格式 |
| **确认等待** | 等待网络确认 |

---

## 为什么这样设计？

### 关注点分离

| 组件 | 关注点 |
|------|--------|
| **OpenClaw Agent** | 用户交互, 决策 |
| **exec 工具** | 进程管理, 输入输出 |
| **byreal-cli** | 链上操作, 交易签名 |
| **SKILL.md** | 使用说明, 约束定义 |

### 好处

| 好处 | 说明 |
|------|------|
| **模块化** | 每个组件职责单一 |
| **可测试** | byreal-cli 可独立测试 |
| **可扩展** | 新增 skill 只需创建 SKILL.md |
| **安全** | 私钥在 byreal-cli 内, agent 不接触 |

---

## 安全考虑

### 私钥管理

```
┌─────────────────────────────────────────────────────────────┐
│                    私钥存储位置                           │
│  ~/.config/solana/keypair.json                              │
│  (byreal-cli 读取, Agent 不接触)                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                    交易签名流程                           │
│  1. byreal-cli 读取私钥                                    │
│  2. 在内存中签名交易                                       │
│  3. 私钥不离开进程                                         │
│  4. 签名后发送交易                                         │
└─────────────────────────────────────────────────────────────┘
```

### 权限控制

| 权限 | 说明 |
|------|------|
| **exec** | 可配置 elevated 权限 |
| **byreal-cli** | 需要文件系统权限读取私钥 |
| **Agent** | 不直接接触私钥 |

---

## 性能分析

### Token 消耗

| 组件 | Token 消耗 |
|------|------------|
| **SKILL.md 注入** | ~97 字符 (一次性) |
| **exec 调用** | ~50 字符 (每次) |
| **结果处理** | ~100-500 字符 (取决于输出) |

### 延迟分析

| 操作 | 延迟 |
|------|------|
| **exec 启动** | ~100ms |
| **byreal-cli 执行** | ~2-5s (取决于 RPC) |
| **交易确认** | ~400ms - 30s (Solana 网络延迟) |
| **总延迟** | ~3-35s |

---

## 总结

```
┌──────────────────────────────────────────────────────────────┐
│                     职责划分                                │
│  exec: 通用执行器 (手)                                     │
│  byreal-cli: 专业工具 (工具)                               │
│  SKILL.md: 使用说明 (说明书)                               │
│  Agent: 决策中心 (大脑)                                    │
└──────────────────────────────────────────────────────────────┘
```

**核心原则**:
1. **分离关注点** - 每个组件只做一件事
2. **最小知识** - agent 不需要知道链上细节
3. **安全隔离** - 私钥不暴露给 agent
4. **可组合性** - exec 可用于任何 CLI

---

*最后更新: 2026-03-04 17:52 SGT*
