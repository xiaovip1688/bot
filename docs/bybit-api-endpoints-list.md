# Bybit V5 API 完整接口清单与测试状态

> 基于 pybit SDK v5.14.0 提取
> 更新时间: 2026-03-04 13:20 SGT
> **总接口数: 247**

---

## 测试覆盖统计

| 状态 | 数量 | 占比 |
|------|------|------|
| ✅ 已测试 | 33 | 13.4% |
| ❌ 未测试 | 214 | 86.6% |
| **总计** | **247** | 100% |

---

## 按模块统计

| 模块 | 总数 | 已测试 | 未测试 | 覆盖率 |
|------|------|--------|--------|--------|
| Market (市场数据) | 22 | 15 | 7 | 68% |
| Account (账户管理) | 26 | 6 | 20 | 23% |
| Position (持仓管理) | 14 | 3 | 11 | 21% |
| Trade (订单管理) | 12 | 3 | 9 | 25% |
| Asset (资产管理) | 33 | 4 | 29 | 12% |
| User (用户管理) | 16 | 2 | 14 | 13% |
| Spot Margin (现货杠杆) | 22 | 0 | 22 | 0% |
| Crypto Loan (加密借贷) | 38 | 0 | 38 | 0% |
| RFQ (询价交易) | 15 | 0 | 15 | 0% |
| Spread (价差交易) | 11 | 0 | 11 | 0% |
| Broker (经纪商) | 7 | 0 | 7 | 0% |
| Earn (理财) | 6 | 0 | 6 | 0% |
| Fiat (法币) | 7 | 0 | 7 | 0% |
| Spot Lever Token (杠杆代币) | 5 | 0 | 5 | 0% |
| Institutional Loan (机构借贷) | 7 | 0 | 7 | 0% |
| Pre Upgrade (升级前) | 6 | 0 | 6 | 0% |
| Execution (执行记录) | 1 | 1 | 0 | 100% |

---

## 完整接口列表

### 1. Market (市场数据) - 22 个接口

| # | Endpoint | 已测试 | 说明 |
|---|----------|--------|------|
| 1 | `/v5/market/time` | ✅ | 服务器时间 |
| 2 | `/v5/market/kline` | ✅ | K线数据 |
| 3 | `/v5/market/mark-price-kline` | ✅ | 标记价格K线 |
| 4 | `/v5/market/index-price-kline` | ✅ | 指数价格K线 |
| 5 | `/v5/market/premium-index-price-kline` | ❌ | 溢价指数K线 |
| 6 | `/v5/market/instruments-info` | ✅ | 交易对信息 |
| 7 | `/v5/market/orderbook` | ✅ | 订单簿 |
| 8 | `/v5/market/tickers` | ✅ | 市场行情 |
| 9 | `/v5/market/funding/history` | ✅ | 资金费率历史 |
| 10 | `/v5/market/recent-trade` | ✅ | 公共成交记录 |
| 11 | `/v5/market/open-interest` | ✅ | 未平仓合约量 |
| 12 | `/v5/market/historical-volatility` | ✅ | 历史波动率 |
| 13 | `/v5/market/insurance` | ✅ | 保险基金 |
| 14 | `/v5/market/risk-limit` | ✅ | 风险限额 |
| 15 | `/v5/market/delivery-price` | ✅ | 交割价格 |
| 16 | `/v5/market/account-ratio` | ✅ | 持仓分布 |
| 17 | `/v5/market/price-limit` | ❌ | 价格限制 |
| 18 | `/v5/market/rpi_orderbook` | ❌ | RPI订单簿 |
| 19 | `/v5/market/new-delivery-price` | ❌ | 新交割价格 |
| 20 | `/v5/market/index-price-components` | ❌ | 指数价格组件 |
| 21 | `/v5/market/adlAlert` | ❌ | ADL预警 |
| 22 | `/v5/market/fee-group-info` | ❌ | 手续费组信息 |

### 2. Account (账户管理) - 26 个接口

| # | Endpoint | 已测试 | 说明 |
|---|----------|--------|------|
| 1 | `/v5/account/info` | ✅ | 账户信息 |
| 2 | `/v5/account/wallet-balance` | ✅ | 钱包余额 |
| 3 | `/v5/account/fee-rate` | ✅ | 手续费率 |
| 4 | `/v5/account/transaction-log` | ✅ | 钱包交易记录 |
| 5 | `/v5/account/borrow-history` | ✅ | 借贷记录 |
| 6 | `/v5/account/set-margin-mode` | ❌ | 设置保证金模式 |
| 7 | `/v5/account/set-hedging-mode` | ❌ | 设置对冲模式 |
| 8 | `/v5/account/set-collateral-switch` | ❌ | 设置抵押开关 |
| 9 | `/v5/account/set-collateral-switch-batch` | ❌ | 批量设置抵押开关 |
| 10 | `/v5/account/collateral-info` | ❌ | 抵押信息 |
| 11 | `/v5/account/contract-transaction-log` | ❌ | 合约交易记录 |
| 12 | `/v5/account/instruments-info` | ❌ | 账户交易对信息 |
| 13 | `/v5/account/mmp-modify` | ❌ | 修改做市商保护 |
| 14 | `/v5/account/mmp-reset` | ❌ | 重置做市商保护 |
| 15 | `/v5/account/mmp-state` | ❌ | 做市商保护状态 |
| 16 | `/v5/account/borrow` | ❌ | 借款 |
| 17 | `/v5/account/repay` | ❌ | 还款 |
| 18 | `/v5/account/quick-repayment` | ❌ | 快速还款 |
| 19 | `/v5/account/no-convert-repay` | ❌ | 无转换还款 |
| 20 | `/v5/account/set-limit-px-action` | ❌ | 设置限价操作 |
| 21 | `/v5/account/smp-group` | ❌ | SMP组 |
| 22 | `/v5/account/query-dcp-info` | ❌ | 查询DCP信息 |
| 23 | `/v5/account/upgrade-to-uta` | ❌ | 升级到UTA |
| 24 | `/v5/account/user-setting-config` | ❌ | 用户设置配置 |
| 25 | `/v5/account/withdrawal` | ❌ | 提币 |
| 26 | `/v5/asset/coin-greeks` | ❌ | 币种Greeks |

### 3. Position (持仓管理) - 14 个接口

| # | Endpoint | 已测试 | 说明 |
|---|----------|--------|------|
| 1 | `/v5/position/list` | ✅ | 持仓列表 |
| 2 | `/v5/position/set-leverage` | ✅ | 设置杠杆 |
| 3 | `/v5/position/trading-stop` | ✅ | 设置止盈止损 |
| 4 | `/v5/position/add-margin` | ❌ | 添加保证金 |
| 5 | `/v5/position/closed-pnl` | ❌ | 已平仓盈亏 |
| 6 | `/v5/position/get-closed-positions` | ❌ | 获取已平仓持仓 |
| 7 | `/v5/position/move-history` | ❌ | 移动历史 |
| 8 | `/v5/position/move-positions` | ❌ | 移动持仓 |
| 9 | `/v5/position/set-auto-add-margin` | ❌ | 设置自动追加保证金 |
| 10 | `/v5/position/set-risk-limit` | ❌ | 设置风险限额 |
| 11 | `/v5/position/set-tpsl-mode` | ❌ | 设置止盈止损模式 |
| 12 | `/v5/position/switch-isolated` | ❌ | 切换逐仓 |
| 13 | `/v5/position/switch-mode` | ❌ | 切换模式 |
| 14 | `/v5/execution/list` | ✅ | 执行历史 |

### 4. Trade (订单管理) - 12 个接口

| # | Endpoint | 已测试 | 说明 |
|---|----------|--------|------|
| 1 | `/v5/order/create` | ✅ | 创建订单 |
| 2 | `/v5/order/realtime` | ✅ | 活动订单 |
| 3 | `/v5/order/history` | ✅ | 订单历史 |
| 4 | `/v5/order/amend` | ❌ | 修改订单 |
| 5 | `/v5/order/amend-batch` | ❌ | 批量修改订单 |
| 6 | `/v5/order/cancel` | ❌ | 撤销订单 |
| 7 | `/v5/order/cancel-all` | ❌ | 撤销所有订单 |
| 8 | `/v5/order/cancel-batch` | ❌ | 批量撤销订单 |
| 9 | `/v5/order/create-batch` | ❌ | 批量创建订单 |
| 10 | `/v5/order/disconnected-cancel-all` | ❌ | 断线撤单 |
| 11 | `/v5/order/pre-check` | ❌ | 订单预检查 |
| 12 | `/v5/order/spot-borrow-check` | ❌ | 现货借贷检查 |

### 5. Asset (资产管理) - 33 个接口

| # | Endpoint | 已测试 | 说明 |
|---|----------|--------|------|
| 1 | `/v5/asset/coin/query-info` | ✅ | 币种信息 |
| 2 | `/v5/asset/deposit/query-record` | ✅ | 存款记录 |
| 3 | `/v5/asset/withdraw/query-record` | ✅ | 提币记录 |
| 4 | `/v5/asset/exchange/query-coin-list` | ❌ | 币种兑换汇率 (权限不足) |
| 5 | `/v5/asset/transfer/inter-transfer` | ❌ | 内部转账 |
| 6 | `/v5/asset/transfer/query-account-coin-balance` | ❌ | 查询账户币种余额 |
| 7 | `/v5/asset/transfer/query-account-coins-balance` | ❌ | 查询账户所有币种余额 |
| 8 | `/v5/asset/transfer/query-asset-info` | ❌ | 查询资产信息 |
| 9 | `/v5/asset/transfer/query-sub-member-list` | ❌ | 查询子账户列表 |
| 10 | `/v5/asset/transfer/query-transfer-coin-list` | ❌ | 查询可转币种列表 |
| 11 | `/v5/asset/transfer/save-transfer-sub-member` | ❌ | 保存转账子账户 |
| 12 | `/v5/asset/transfer/universal-transfer` | ❌ | 通用转账 |
| 13 | `/v5/asset/deposit/deposit-to-account` | ❌ | 存入账户 |
| 14 | `/v5/asset/deposit/query-address` | ❌ | 查询充值地址 |
| 15 | `/v5/asset/deposit/query-allowed-list` | ❌ | 查询允许列表 |
| 16 | `/v5/asset/deposit/query-internal-record` | ❌ | 查询内部充值记录 |
| 17 | `/v5/asset/deposit/query-sub-member-address` | ❌ | 查询子账户地址 |
| 18 | `/v5/asset/withdraw/cancel` | ❌ | 取消提币 |
| 19 | `/v5/asset/withdraw/create` | ❌ | 创建提币 |
| 20 | `/v5/asset/withdraw/query-address` | ❌ | 查询提币地址 |
| 21 | `/v5/asset/withdraw/vasp/list` | ❌ | VASP列表 |
| 22 | `/v5/asset/withdraw/withdrawable-amount` | ❌ | 可提金额 |
| 23 | `/v5/asset/delivery-record` | ❌ | 交割记录 |
| 24 | `/v5/asset/settlement-record` | ❌ | 结算记录 |
| 25 | `/v5/asset/exchange/convert-execute` | ❌ | 兑换执行 |
| 26 | `/v5/asset/exchange/convert-result-query` | ❌ | 兑换结果查询 |
| 27 | `/v5/asset/exchange/order-record` | ❌ | 兑换订单记录 |
| 28 | `/v5/asset/exchange/query-convert-history` | ❌ | 兑换历史 |
| 29 | `/v5/asset/exchange/quote-apply` | ❌ | 报价申请 |
| 30 | `/v5/asset/covert/get-quote` | ❌ | 获取报价 |
| 31 | `/v5/asset/covert/small-balance-execute` | ❌ | 小额余额执行 |
| 32 | `/v5/asset/covert/small-balance-history` | ❌ | 小额余额历史 |
| 33 | `/v5/asset/covert/small-balance-list` | ❌ | 小额余额列表 |

### 6. User (用户管理) - 16 个接口

| # | Endpoint | 已测试 | 说明 |
|---|----------|--------|------|
| 1 | `/v5/user/query-api` | ✅ | API Key 信息 |
| 2 | `/v5/user/sub-members` | ❌ | 子账户列表 (权限不足) |
| 3 | `/v5/user/create-sub-api` | ❌ | 创建子账户API |
| 4 | `/v5/user/create-sub-member` | ❌ | 创建子账户 |
| 5 | `/v5/user/del-submember` | ❌ | 删除子账户 |
| 6 | `/v5/user/delete-api` | ❌ | 删除API |
| 7 | `/v5/user/delete-sub-api` | ❌ | 删除子账户API |
| 8 | `/v5/user/escrow_sub_members` | ❌ | 托管子账户 |
| 9 | `/v5/user/frozen-sub-member` | ❌ | 冻结子账户 |
| 10 | `/v5/user/get-member-type` | ❌ | 获取会员类型 |
| 11 | `/v5/user/query-api` | ❌ | 查询API |
| 12 | `/v5/user/query-sub-members` | ❌ | 查询子账户 |
| 13 | `/v5/user/sub-apikeys` | ❌ | 子账户API密钥 |
| 14 | `/v5/user/submembers` | ❌ | 子账户列表 |
| 15 | `/v5/user/update-api` | ❌ | 更新API |
| 16 | `/v5/user/update-sub-api` | ❌ | 更新子账户API |
| 17 | `/v5/affiliate/aff-user-list` | ❌ | 代理用户列表 |
| 18 | `/v5/user/aff-customer-info` | ❌ | 代理客户信息 |

### 7-16. 其他模块 (未测试)

| 模块 | 接口数 | 状态 |
|------|--------|------|
| Spot Margin | 22 | ❌ 全部未测试 |
| Crypto Loan | 38 | ❌ 全部未测试 |
| RFQ | 15 | ❌ 全部未测试 |
| Spread | 11 | ❌ 全部未测试 |
| Broker | 7 | ❌ 全部未测试 |
| Earn | 6 | ❌ 全部未测试 |
| Fiat | 7 | ❌ 全部未测试 |
| Spot Lever Token | 5 | ❌ 全部未测试 |
| Institutional Loan | 7 | ❌ 全部未测试 |
| Pre Upgrade | 6 | ❌ 全部未测试 |

---

## 测试结论

### 当前状态

- **已测试接口**: 33 个 (13.4%)
- **未测试接口**: 214 个 (86.6%)
- **主要覆盖模块**: Market, Account, Position, Trade (基础接口)

### 测试优先级建议

#### 高优先级 (常用交易接口)

1. `/v5/order/cancel` - 撤销订单
2. `/v5/order/cancel-all` - 撤销所有订单
3. `/v5/position/closed-pnl` - 已平仓盈亏
4. `/v5/position/set-risk-limit` - 设置风险限额
5. `/v5/account/set-margin-mode` - 设置保证金模式

#### 中优先级 (资产管理)

6. `/v5/asset/transfer/inter-transfer` - 内部转账
7. `/v5/asset/withdraw/create` - 创建提币
8. `/v5/spot-margin-trade/data` - 现货杠杆数据

#### 低优先级 (特殊功能)

9. Crypto Loan 接口 (需要开通借贷功能)
10. RFQ 接口 (需要询价权限)
11. Broker 接口 (需要经纪商权限)

---

*文档生成时间: 2026-03-04 13:20 SGT*
*数据来源: pybit SDK v5.14.0*
