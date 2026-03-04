# Bybit API 测试报告 - 第六批系统化测试

> 测试时间: 2026-03-04 14:38 SGT
> 测试范围: 86 个新接口（CryptoLoan, RFQ, Spread, Broker, Fiat, InsLoan, PreUpgrade, Asset补充）
> 基于 pybit SDK v5.14.0 完整接口清单

---

## 测试概览

| 指标 | 之前累计 | 本批测试 | 累计总计 |
|------|----------|----------|----------|
| 测试接口数 | 109 | 86 | **195** |
| 成功数 | 59 | 30 | **89** |
| 失败数 | 50 | 56 | **106** |
| **覆盖率** | 44% | 35% | **79%** |
| **成功率** | 54.1% | 34.9% | **45.6%** |

---

## 本批测试结果（按模块）

| 模块 | 测试数 | 成功 | 失败 | 成功率 |
|------|--------|------|------|--------|
| RFQ | 14 | 9 | 5 | **64.3%** |
| CryptoLoan | 22 | 13 | 9 | **59.1%** |
| Spread | 11 | 5 | 6 | 45.5% |
| InsLoan | 7 | 2 | 5 | 28.6% |
| Broker | 7 | 1 | 6 | 14.3% |
| Asset (补充) | 12 | 0 | 12 | 0% |
| Fiat | 7 | 0 | 7 | 0% |
| PreUpgrade | 6 | 0 | 6 | 0% |
| **总计** | **86** | **30** | **56** | **34.9%** |

---

## 成功接口列表

### CryptoLoan 模块 (13个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 可借数据 | `/v5/crypto-loan-common/loanable-data` | ✅ |
| 2 | 抵押数据 | `/v5/crypto-loan-common/collateral-data` | ✅ |
| 3 | 持仓 | `/v5/crypto-loan-common/position` | ✅ |
| 4 | 调整历史 | `/v5/crypto-loan-common/adjustment-history` | ✅ |
| 5 | 借款历史 | `/v5/crypto-loan/borrow-history` | ✅ |
| 6 | 还款历史 | `/v5/crypto-loan/repayment-history` | ✅ |
| 7 | 进行中订单 | `/v5/crypto-loan/ongoing-orders` | ✅ |
| 8 | Flex:借款历史 | `/v5/crypto-loan-flexible/borrow-history` | ✅ |
| 9 | Flex:进行中币种 | `/v5/crypto-loan-flexible/ongoing-coin` | ✅ |
| 10 | Flex:还款历史 | `/v5/crypto-loan-flexible/repayment-history` | ✅ |
| 11 | Fixed:借款合约信息 | `/v5/crypto-loan-fixed/borrow-contract-info` | ✅ |
| 12 | Fixed:借款订单信息 | `/v5/crypto-loan-fixed/borrow-order-info` | ✅ |
| 13 | Fixed:供应合约信息 | `/v5/crypto-loan-fixed/supply-contract-info` | ✅ |

### RFQ 模块 (9个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 配置 | `/v5/rfq/config` | ✅ |
| 2 | RFQ列表 | `/v5/rfq/rfq-list` | ✅ |
| 3 | RFQ实时 | `/v5/rfq/rfq-realtime` | ✅ |
| 4 | 取消所有RFQ | `/v5/rfq/cancel-all-rfq` | ✅ |
| 5 | 报价列表 | `/v5/rfq/quote-list` | ✅ |
| 6 | 报价实时 | `/v5/rfq/quote-realtime` | ✅ |
| 7 | 取消所有报价 | `/v5/rfq/cancel-all-quotes` | ✅ |
| 8 | 交易列表 | `/v5/rfq/trade-list` | ✅ |
| 9 | 公共交易 | `/v5/rfq/public-trades` | ✅ |

### Spread 模块 (5个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 交易对 | `/v5/spread/instrument` | ✅ |
| 2 | 活动订单 | `/v5/spread/order/realtime` | ✅ |
| 3 | 订单历史 | `/v5/spread/order/history` | ✅ |
| 4 | 撤销所有订单 | `/v5/spread/order/cancel-all` | ✅ |
| 5 | 执行列表 | `/v5/spread/execution/list` | ✅ |

### InsLoan 模块 (2个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 产品信息 | `/v5/ins-loan/product-infos` | ✅ |
| 2 | 确保代币转换 | `/v5/ins-loan/ensure-tokens-convert` | ✅ |

### Broker 模块 (1个)

| # | 接口名称 | Endpoint | 状态 |
|---|----------|----------|------|
| 1 | 子账户充值记录 | `/v5/broker/asset/query-sub-member-deposit-record` | ✅ |

---

## 失败原因分析

### 按失败原因分类

| 失败原因 | 数量 | 占比 | 说明 |
|----------|------|------|------|
| **权限不足** (`10005`) | 22 | 39.3% | Fiat、Asset提币/兑换等 |
| **参数错误** (`10001`) | 15 | 26.8% | 需要正确的业务参数 |
| **HTTP 404** | 9 | 16.1% | 接口可能不存在 |
| **业务限制** | 10 | 17.9% | 账户异常、非经纪商等 |

### 权限不足详情

| 模块 | 接口 | 所需权限 |
|------|------|----------|
| Fiat | 全部接口 | Fiat交易权限 |
| Asset | 提币/兑换/转账 | Withdraw/Exchange/Wallet |
| Broker | 账户信息/收益 | Exchange Broker主账户 |

### HTTP 404 接口

| Endpoint | 说明 |
|----------|------|
| `/v5/crypto-loan/borrow` | Crypto借款 |
| `/v5/broker/award/info` | 经纪商奖励信息 |
| `/v5/broker/award/distribution-record` | 分发记录 |
| `/v5/ins-loan/association-uid` | 关联UID |
| `/v5/pre-upgrade/*` | 全部PreUpgrade接口 |

### 业务限制

| 接口 | 错误信息 | 原因 |
|------|----------|------|
| InsLoan:贷款订单 | `Account exception` | 账户状态异常 |
| Broker:账户信息 | `Only available to exchange broker` | 非经纪商账户 |
| CryptoLoan:还款 | `Illegal characters` | 参数格式错误 |
| RFQ:创建RFQ | `counterparties is empty` | 需要交易对手方 |

---

## 累计测试进度

| 模块 | 总接口数 | 已测试 | 成功 | 覆盖率 | 成功率 |
|------|----------|--------|------|--------|--------|
| Market | 22 | 19 | 19 | 86% | **100%** |
| SpotMargin | 22 | 13 | 8 | 59% | 62% |
| CryptoLoan | 38 | 22 | 13 | 58% | 59% |
| Account | 26 | 18 | 13 | 69% | 72% |
| RFQ | 15 | 14 | 9 | 93% | **64%** |
| Position | 14 | 13 | 5 | 93% | 38% |
| Spread | 11 | 11 | 5 | 100% | 45% |
| Trade | 12 | 12 | 4 | 100% | 33% |
| Asset | 33 | 25 | 8 | 76% | 32% |
| User | 16 | 5 | 4 | 31% | 80% |
| InsLoan | 7 | 7 | 2 | 100% | 29% |
| Broker | 7 | 7 | 1 | 100% | 14% |
| Earn | 6 | 5 | 0 | 83% | 0% |
| LeverToken | 5 | 2 | 0 | 40% | 0% |
| Fiat | 7 | 7 | 0 | 100% | 0% |
| PreUpgrade | 6 | 6 | 0 | 100% | 0% |
| **总计** | **247** | **195** | **89** | **79%** | **45.6%** |

---

## 剩余未测试接口

| 模块 | 未测试数 | 说明 |
|------|----------|------|
| Market | 3 | 指数价格组件、ADL预警等 |
| Account | 8 | 借款、还款、升级UTA等 |
| CryptoLoan | 16 | Fixed系列接口 |
| Asset | 8 | 转账、提币等 |
| User | 11 | 子账户管理接口 |
| 其他 | 9 | LeverToken、Earn补充 |
| **总计** | **52** | - |

---

## 测试建议

### 需要开通的权限

| 权限 | 接口数 | 受影响模块 |
|------|--------|------------|
| Wallet | 8 | Asset余额查询 |
| Withdraw | 6 | Asset提币 |
| Exchange | 5 | Asset兑换 |
| Fiat | 7 | Fiat交易 |
| Sub-account | 6 | User子账户 |
| Earn | 5 | Earn理财 |
| Exchange Broker | 5 | Broker经纪商 |

### 接口状态确认

以下接口需要确认是否在 V5 API 中存在：

- `/v5/pre-upgrade/*` 系列 (全部 404)
- `/v5/ins-loan/association-uid` (404)
- `/v5/broker/award/*` 系列 (部分 404)

---

## 下一步计划

1. **开通权限** - 申请 Wallet/Withdraw/Exchange/Fiat 等权限
2. **确认接口** - 查证 404 接口是否存在
3. **补充测试** - 完成剩余 52 个接口
4. **参数优化** - 修复参数错误的接口

---

*报告生成时间: 2026-03-04 14:38 SGT*
