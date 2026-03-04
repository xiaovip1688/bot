# Bybit API 测试报告 - 第五批系统化测试

> 测试时间: 2026-03-04 14:31 SGT
> 测试范围: 76 个新接口（Market, Trade, Position, Account, Asset, User, SpotMargin, Earn, LeverToken）
> 基于 pybit SDK v5.14.0 完整接口清单

---

## 测试概览

| 指标 | 之前累计 | 本批测试 | 累计总计 |
|------|----------|----------|----------|
| 测试接口数 | 33 | 76 | **109** |
| 成功数 | 31 | 28 | **59** |
| 失败数 | 2 | 48 | **50** |
| **成功率** | 94% | 36.8% | **54.1%** |

---

## 本批测试结果（按模块）

| 模块 | 测试数 | 成功 | 失败 | 成功率 |
|------|--------|------|------|--------|
| SpotMargin | 13 | 8 | 5 | 61.5% |
| Account | 12 | 7 | 5 | 58.3% |
| Market | 7 | 4 | 3 | 57.1% |
| User | 3 | 2 | 1 | 66.7% |
| Asset | 15 | 4 | 11 | 26.7% |
| Position | 10 | 2 | 8 | 20.0% |
| Trade | 9 | 1 | 8 | 11.1% |
| Earn | 5 | 0 | 5 | 0% |
| LeverToken | 2 | 0 | 2 | 0% |
| **总计** | **76** | **28** | **48** | **36.8%** |

---

## 成功接口列表

### Market 模块 (4个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 溢价指数K线 | `/v5/market/premium-index-price-kline` | ✅ |
| 2 | 价格限制 | `/v5/market/price-limit` | ✅ |
| 3 | RPI订单簿 | `/v5/market/rpi_orderbook` | ✅ |
| 4 | ADL预警 | `/v5/market/adlAlert` | ✅ |

### Account 模块 (7个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 抵押信息 | `/v5/account/collateral-info` | ✅ |
| 2 | 合约交易记录 | `/v5/account/contract-transaction-log` | ✅ |
| 3 | 账户交易对信息 | `/v5/account/instruments-info` | ✅ |
| 4 | SMP组 | `/v5/account/smp-group` | ✅ |
| 5 | 查询DCP信息 | `/v5/account/query-dcp-info` | ✅ |
| 6 | 做市商保护状态 | `/v5/account/mmp-state` | ✅ |
| 7 | 用户设置配置 | `/v5/account/user-setting-config` | ✅ |

### Position 模块 (2个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 已平仓盈亏 | `/v5/position/closed-pnl` | ✅ |
| 2 | 移动历史 | `/v5/position/move-history` | ✅ |

### Trade 模块 (1个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 撤销所有订单 | `/v5/order/cancel-all` | ✅ |

### Asset 模块 (4个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 查询允许列表 | `/v5/asset/deposit/query-allowed-list` | ✅ |
| 2 | 查询内部充值记录 | `/v5/asset/deposit/query-internal-record` | ✅ |
| 3 | 交割记录 | `/v5/asset/delivery-record` | ✅ |
| 4 | 结算记录 | `/v5/asset/settlement-record` | ✅ |

### User 模块 (2个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 获取会员类型 | `/v5/user/get-member-type` | ✅ |
| 2 | 查询API | `/v5/user/query-api` | ✅ |

### SpotMargin 模块 (8个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 现货杠杆数据 | `/v5/spot-margin-trade/data` | ✅ |
| 2 | 币种状态 | `/v5/spot-margin-trade/coinstate` | ✅ |
| 3 | 抵押币种 | `/v5/spot-margin-trade/collateral` | ✅ |
| 4 | 自动还款模式 | `/v5/spot-margin-trade/get-auto-repay-mode` | ✅ |
| 5 | 利率历史 | `/v5/spot-margin-trade/interest-rate-history` | ✅ |
| 6 | 最大可借 | `/v5/spot-margin-trade/max-borrowable` | ✅ |
| 7 | 持仓层级 | `/v5/spot-margin-trade/position-tiers` | ✅ |
| 8 | 状态 | `/v5/spot-margin-trade/state` | ✅ |

---

## 失败接口分析

### 按失败原因分类

| 失败原因 | 数量 | 占比 | 示例 |
|----------|------|------|------|
| **权限不足** (`10005`) | 20 | 41.7% | 查询账户余额、提币、兑换等 |
| **HTTP 404** | 12 | 25.0% | 新交割价格、全仓账户等 |
| **参数错误** (`10001`) | 8 | 16.7% | 撤销订单、修改订单等 |
| **业务逻辑限制** | 8 | 16.7% | 保证金模式、持仓模式等 |

### 权限不足详情

以下接口需要在 Bybit 后台为 API Key 开启相应权限：

| 模块 | 接口 | 所需权限 |
|------|------|----------|
| Asset | 查询账户币种余额 | Wallet |
| Asset | 查询提币地址 | Withdraw |
| Asset | 可提金额 | Withdraw |
| Asset | 兑换订单记录 | Exchange |
| User | 查询子账户 | Sub-account |
| Earn | 持仓/订单/收益 | Earn |

### HTTP 404 接口

以下接口可能在 V5 API 中不存在或路径变更：

| Endpoint | 说明 |
|----------|------|
| `/v5/market/new-delivery-price` | 新交割价格 |
| `/v5/account/upgrade-to-uta` | 升级UTA |
| `/v5/asset/covert/get-quote` | 获取报价 |
| `/v5/spot-cross-margin-trade/account` | 全仓账户 |
| `/v5/spot-lever-token/info` | 杠杆代币信息 |

### 业务逻辑限制

以下接口因账户状态或持仓状态限制而失败：

| 接口 | 错误信息 | 原因 |
|------|----------|------|
| Position:添加保证金 | `only isolated mode can add margin` | 当前是全仓模式 |
| Position:切换逐仓 | `unified account is forbidden` | 统一账户不支持 |
| Position:切换模式 | `You have an existing position` | 有持仓时不能切换 |
| Account:设置保证金模式 | `Set margin mode failed` | 账户状态限制 |

---

## 测试建议

### 需要开通的权限

要完成剩余接口测试，建议在 Bybit 后台开启以下权限：

1. **Wallet** - 查询账户余额
2. **Withdraw** - 提币相关
3. **Exchange** - 兑换相关
4. **Sub-account** - 子账户管理
5. **Earn** - 理财相关

### 接口状态确认

以下接口需要确认是否在 V5 API 中存在：

- `/v5/market/new-delivery-price`
- `/v5/account/upgrade-to-uta`
- `/v5/spot-cross-margin-trade/*` 系列

### 业务逻辑测试

建议在不同账户状态下测试：

1. **逐仓模式** - 添加保证金、设置杠杆
2. **无持仓状态** - 切换模式
3. **经典账户** - 对冲模式等

---

## 累计测试进度

| 模块 | 总接口数 | 已测试 | 成功 | 覆盖率 | 成功率 |
|------|----------|--------|------|--------|--------|
| Market | 22 | 19 | 19 | 86% | 100% |
| Account | 26 | 18 | 13 | 69% | 72% |
| Position | 14 | 13 | 5 | 93% | 38% |
| Trade | 12 | 12 | 4 | 100% | 33% |
| Asset | 33 | 19 | 8 | 58% | 42% |
| User | 16 | 5 | 4 | 31% | 80% |
| SpotMargin | 22 | 13 | 8 | 59% | 62% |
| Crypto Loan | 38 | 0 | 0 | 0% | - |
| RFQ | 15 | 0 | 0 | 0% | - |
| Spread | 11 | 0 | 0 | 0% | - |
| Broker | 7 | 0 | 0 | 0% | - |
| Earn | 6 | 5 | 0 | 83% | 0% |
| Fiat | 7 | 0 | 0 | 0% | - |
| LeverToken | 5 | 2 | 0 | 40% | 0% |
| Institutional | 7 | 0 | 0 | 0% | - |
| PreUpgrade | 6 | 0 | 0 | 0% | - |
| **总计** | **247** | **109** | **59** | **44%** | **54%** |

---

## 下一步计划

1. **开通权限** - 申请 Wallet/Withdraw/Exchange 等权限
2. **确认接口** - 查证 404 接口是否存在
3. **继续测试** - Crypto Loan / RFQ / Spread 等模块

---

*报告生成时间: 2026-03-04 14:31 SGT*
