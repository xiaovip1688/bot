# Bybit 自动交易 Agent 可行性验证报告

- **报告时间**：2026-03-04 10:26 (Asia/Dubai)
- **项目目录**：`/Users/admin/.openclaw/workspace/bybit-auto-agent`
- **运行环境**：本机 Python 3.14 + pybit v5
- **交易模式**：Bybit Mainnet（实盘）
- **交易标的**：BTCUSDT / ETHUSDT / MNTUSDT / SOLUSDT（USDT 永续）

---

## 1) 验证目标

本次验证目标是确认：

1. Agent 能否在本机稳定运行并循环执行策略；
2. 能否基于 K 线 + 情绪因子自动生成交易信号；
3. 能否在实盘环境完成下单及风控动作（开仓/反手/加仓/止盈止损）；
4. 能否通过 Telegram 实时通知运行状态和异常；
5. 遇到真实交易报错时，能否定位并修复。

---

## 2) 系统如何运转（运行机制）

### 2.1 主循环
- 固定周期（`LOOP_SECONDS=30`）执行一次 `run_once()`。
- 每轮流程：
  1. 拉取统一账户 USDT 权益（equity）；
  2. 计算当日收益率（用于日内熔断）；
  3. 拉取情绪指标（Fear & Greed）；
  4. 针对每个 symbol：拉 K 线 -> 计算指标 -> 生成信号 -> 执行交易动作；
  5. 发送 Telegram 通知（启动、开平仓、异常、风控触发）。

### 2.2 信号与决策
- 周期：15m K线；
- 指标：EMA(20/50)、RSI(14)、ATR(14)；
- 情绪过滤：Fear & Greed 指数；
- 决策动作：
  - 无仓位：根据信号开仓（LONG/SHORT）；
  - 有仓位：允许反手（reverse）；
  - 同向延续：允许加仓（add-position）；
  - 自动设置 ATR 动态止盈止损（TP/SL）。

### 2.3 风控框架
- 最大杠杆：5x；
- 单笔风险：约 1% equity（通过 ATR 距离估算头寸）；
- 总暴露上限：约 35%（按 notional 估算）；
- 日亏损熔断：-4%；
- 连续亏损熔断：4 次（框架已留，后续可按成交回报强化）。

---

## 3) 实施过程回顾

### 阶段 A：初版搭建
- 创建项目骨架：`bot.py / requirements.txt / .env.example / README.md`；
- 安装依赖，接入 Bybit Unified Trading API；
- 增加 Telegram 通知模块；
- 增加后台运行与日志输出（`agent.log` + `agent.pid`）。

### 阶段 B：从 DRY_RUN 到实盘
- 先以 `DRY_RUN=true` 验证逻辑流；
- 切换 `DRY_RUN=false` 后进入实盘执行。

### 阶段 C：线上报错处置
- 你反馈“日志有不少报错”，随后进行日志排查与定向修复。

---

## 4) 遇到的问题与处理

## 问题 1：Qty invalid (ErrCode: 10001) 大量出现

**现象**
- ETH/MNT/SOL 下单频繁报：`Qty invalid`。

**根因**
- 下单数量包含不符合交易所规则的小数位，未按每个合约的 `qtyStep/minOrderQty/maxOrderQty` 归一化。

**修复**
1. 动态拉取每个合约 `instruments_info`；
2. 下单前将数量按 `qtyStep` 向下取整；
3. 小于 `minOrderQty` 直接跳过；
4. 大于 `maxOrderQty` 截断后再按 step 取整；
5. 字符串化 qty，去掉多余尾零，避免格式问题。

**结果**
- 消除了由数量精度导致的核心报错来源。

---

## 问题 2：Bybit API ReadTimeout

**现象**
- 偶发 `HTTPSConnectionPool ... Read timed out (10s)`。

**根因**
- 网络链路波动/接口瞬时延迟，10s 超时偏紧。

**修复**
1. API 客户端 timeout 从 10s 提升到 20s；
2. 主循环针对 `ReadTimeout` 单独捕获并降级为 warning，避免刷 fatal traceback。

**结果**
- 稳定性提升，循环不中断。

---

## 问题 3：日志可观测性不足

**现象**
- 仅启动日志，不利于确认循环健康度。

**修复**
- 增加每轮关键信息日志：`equity / dailyPnL / sentiment`。

**结果**
- 现在可在日志中直接观察策略循环是否正常推进。

---

## 5) 验证结论（可行性）

**结论：可行，且已进入实盘运行。**

本次验证证明：
1. Agent 可在本机稳定后台运行；
2. 可完成多标的自动决策与实盘下单链路；
3. 异常可通过日志与 Telegram 感知；
4. 核心实盘报错（Qty invalid）已定位并修复；
5. 当前系统具备继续迭代优化的基础。

---

## 6) 当前已知限制与建议

### 已知限制
1. “连续亏损统计”目前主要依赖框架状态，尚未完整接入成交回报来精确归因每笔盈亏；
2. “同一根 K 线重复触发”在高波动情况下仍可能发生；
3. 情绪因子来源单一（Fear & Greed），抗噪声能力有限。

### 下一步建议（优先级）
1. **高优先**：加“同 K 线下单锁”（每品种每根 K 线最多开仓/反手一次）；
2. **高优先**：接入成交与已实现盈亏回报，真实统计连续亏损；
3. **中优先**：增加最大回撤（MDD）熔断；
4. **中优先**：增加黑天鹅保护（单根大波动时暂停入场）；
5. **中优先**：增加策略参数化配置（便于快速调参和回滚）。

---

## 7) 运维说明（你可直接使用）

### 查看运行日志
```bash
tail -f /Users/admin/.openclaw/workspace/bybit-auto-agent/agent.log
```

### 查看进程
```bash
ps -p $(cat /Users/admin/.openclaw/workspace/bybit-auto-agent/agent.pid) -o pid,comm,args
```

### 重启
```bash
kill $(cat /Users/admin/.openclaw/workspace/bybit-auto-agent/agent.pid)
source /Users/admin/.openclaw/workspace/bybit-auto-agent/.venv/bin/activate
nohup python -u /Users/admin/.openclaw/workspace/bybit-auto-agent/bot.py > /Users/admin/.openclaw/workspace/bybit-auto-agent/agent.log 2>&1 &
echo $! > /Users/admin/.openclaw/workspace/bybit-auto-agent/agent.pid
```

---

## 8) 安全说明（必须）

- 你在会话中提供过 API key/secret 明文；
- 从安全角度，建议尽快轮换（rotate）密钥；
- API 权限保持最小化：仅 `Read + Trade`，禁止 `Withdraw`；
- 本地 `.env` 已通过 `.gitignore` 排除，避免误提交。

---

## 9) 变更记录（本次）

- `feat`: 初版 agent 脚手架 + 风控 + Telegram 通知
- `chore`: 运行时文件忽略 + 日志改善
- `fix`: qty/price 归一化 + timeout 强化 + 主循环观测日志

（对应 commit 已写入仓库历史）
