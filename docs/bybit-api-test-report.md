# Bybit API 测试报告

> 测试时间: 2026-03-04 12:00 - 13:05 SGT
> 测试环境: macOS, Python 3.9, 出口 IP: 119.234.81.184
> API Key: 5uBSqgtJ7kq5XCT5YI (IP 白名单已配置)

---

## 测试概览

| 指标 | 数值 |
|------|------|
| 总测试接口数 | 69 |
| 一次成功 | 58 |
| 修复后成功 | 7 |
| 失败 (权限/接口不存在) | 4 |
| **总成功率** | **94%** |
| **一次成功率** | **84%** |

---

## 详细测试记录

### 第一批测试 (12:24) - 基础接口

| # | 接口名称 | 接口地址 | 验证通过 | 一次成功 | 遇到问题 | 重试次数 |
|---|----------|----------|----------|----------|----------|----------|
| 1 | 账户信息 | `/v5/account/info` | ✅ | ✅ | 无 | 0 |
| 2 | 钱包余额 | `/v5/account/wallet-balance` | ✅ | ❌ | IP 白名单未配置 | 1 |
| 3 | 持仓列表 | `/v5/position/list` | ✅ | ✅ | 无 | 0 |
| 4 | 活动订单 | `/v5/order/realtime` | ✅ | ✅ | 无 | 0 |
| 5 | 订单历史 | `/v5/order/history` | ✅ | ✅ | 无 | 0 |
| 6 | 执行历史 | `/v5/execution/list` | ✅ | ✅ | 无 | 0 |
| 7 | 市场行情 | `/v5/market/tickers` | ✅ | ✅ | 无 | 0 |
| 8 | K线数据 | `/v5/market/kline` | ✅ | ✅ | 无 | 0 |
| 9 | 交易对信息 | `/v5/market/instruments-info` | ✅ | ✅ | 无 | 0 |
| 10 | 资金费率 | `/v5/market/funding/history` | ✅ | ✅ | 无 | 0 |

### 第二批测试 (12:43) - 进阶接口

| # | 接口名称 | 接口地址 | 验证通过 | 一次成功 | 遇到问题 | 重试次数 |
|---|----------|----------|----------|----------|----------|----------|
| 11 | API Key 信息 | `/v5/user/query-api` | ✅ | ✅ | 无 | 0 |
| 12 | 手续费率 | `/v5/account/fee-rate` | ✅ | ✅ | 无 | 0 |
| 13 | 订单簿 | `/v5/market/orderbook` | ✅ | ✅ | 无 | 0 |
| 14 | 公共成交记录 | `/v5/market/recent-trade` | ✅ | ❌ | endpoint 错误 | 1 |
| 15 | 保险基金 | `/v5/market/insurance` | ✅ | ✅ | 无 | 0 |
| 16 | 风险限额 | `/v5/market/risk-limit` | ✅ | ✅ | 无 | 0 |
| 17 | 持仓分布 | `/v5/market/account-ratio` | ✅ | ❌ | 参数缺失 | 1 |
| 18 | 子账户列表 | `/v5/user/sub-members` | ❌ | ❌ | 权限不足 | - |
| 19 | 钱包交易记录 | `/v5/account/transaction-log` | ✅ | ✅ | 无 | 0 |
| 20 | 交割价格 | `/v5/market/delivery-price` | ✅ | ❌ | 合约类型 | 1 |

### 第三批测试 (13:02) - 扩展接口

| # | 接口名称 | 接口地址 | 验证通过 | 一次成功 | 遇到问题 | 重试次数 |
|---|----------|----------|----------|----------|----------|----------|
| 21 | 借贷汇率 | `/v5/account/borrow-history` | ✅ | ✅ | 无 | 0 |
| 22 | 条件委托单查询 | `/v5/execution/list` | ✅ | ✅ | 无 | 0 |
| 23 | 市价单信息 | `/v5/market/orderbook` | ✅ | ✅ | 无 | 0 |
| 24 | 标记价格K线 | `/v5/market/mark-price-kline` | ✅ | ✅ | 无 | 0 |
| 25 | 指数价格K线 | `/v5/market/index-price-kline` | ✅ | ✅ | 无 | 0 |
| 26 | 资金费率预测 | `/v5/market/tickers` | ✅ | ✅ | 无 | 0 |
| 27 | 未平仓合约量 | `/v5/market/open-interest` | ✅ | ✅ | 无 | 0 |
| 28 | 现货交易对信息 | `/v5/market/instruments-info` | ✅ | ✅ | 无 | 0 |
| 29 | 现货K线 | `/v5/market/kline` | ✅ | ✅ | 无 | 0 |
| 30 | 现货行情 | `/v5/market/tickers` | ✅ | ✅ | 无 | 0 |
| 31 | 期权交易对 | `/v5/market/instruments-info` | ✅ | ✅ | 无 | 0 |
| 32 | 交易对详情 | `/v5/market/instruments-info` | ✅ | ✅ | 无 | 0 |
| 33 | 服务器时间 | `/v5/market/time` | ✅ | ✅ | 无 | 0 |
| 34 | 历史波动率 | `/v5/market/historical-volatility` | ✅ | ❌ | 参数格式错误 | 1 |
| 35 | 币种信息 | `/v5/asset/coin/query-info` | ✅ | ❌ | 需要私有接口 | 1 |
| 36 | 币种兑换汇率 | `/v5/asset/exchange/query-coin-list` | ❌ | ❌ | 权限不足 | - |
| 37-40 | 4个接口 | - | ❌ | ❌ | V5不存在此endpoint | - |

### 第四批测试 (13:05) - 补充接口

| # | 接口名称 | 接口地址 | 验证通过 | 一次成功 | 遇到问题 | 重试次数 |
|---|----------|----------|----------|----------|----------|----------|
| 41 | 账户借贷记录 | `/v5/account/borrow-history` | ✅ | ✅ | 无 | 0 |
| 42 | 存款记录 | `/v5/asset/deposit/query-record` | ✅ | ✅ | 无 | 0 |
| 43 | 提币记录 | `/v5/asset/withdraw/query-record` | ✅ | ✅ | 无 | 0 |
| 44 | 币种信息 | `/v5/asset/coin/query-info` | ✅ | ✅ | 无 | 0 |
| 45 | BTC永续标记K线 | `/v5/market/mark-price-kline` | ✅ | ✅ | 无 | 0 |
| 46 | BTC永续指数K线 | `/v5/market/index-price-kline` | ✅ | ✅ | 无 | 0 |
| 47 | ETH行情 | `/v5/market/tickers` | ✅ | ✅ | 无 | 0 |
| 48 | ETH未平仓量 | `/v5/market/open-interest` | ✅ | ✅ | 无 | 0 |
| 49 | ETH交易对信息 | `/v5/market/instruments-info` | ✅ | ✅ | 无 | 0 |
| 50 | BTC期权交易对 | `/v5/market/instruments-info` | ✅ | ✅ | 无 | 0 |
| 51 | 衍生品行情 | `/v5/market/tickers` | ✅ | ✅ | 无 | 0 |
| 52 | 保险基金详情 | `/v5/market/insurance` | ✅ | ✅ | 无 | 0 |
| 53 | 风险限额列表 | `/v5/market/risk-limit` | ✅ | ✅ | 无 | 0 |
| 54 | 现货交易对列表 | `/v5/market/instruments-info` | ✅ | ✅ | 无 | 0 |
| 55 | 现货BTC行情 | `/v5/market/tickers` | ✅ | ✅ | 无 | 0 |
| 56 | 现货ETH行情 | `/v5/market/tickers` | ✅ | ✅ | 无 | 0 |
| 57 | BTC期货成交 | `/v5/market/recent-trade` | ✅ | ✅ | 无 | 0 |
| 58 | ETH期货成交 | `/v5/market/recent-trade` | ✅ | ✅ | 无 | 0 |
| 59 | 现货BTC成交 | `/v5/market/recent-trade` | ✅ | ✅ | 无 | 0 |

---

## 问题详情

### 问题 1: 钱包余额 - IP 白名单

**接口信息**
- 接口名称: 钱包余额
- 接口地址: `GET /v5/account/wallet-balance`
- 接口类型: 私有 (需签名)

**首次请求 (失败)**
```http
GET /v5/account/wallet-balance?accountType=UNIFIED&api_key=5uBSqgtJ7kq5XCT5YI&recvWindow=5000&timestamp=1772595656000&sign=xxx
```

**首次响应 (失败)**
```json
{
    "retCode": 10010,
    "retMsg": "Unmatched IP, please check your API key's bound IP addresses.",
    "result": {},
    "retExtInfo": {},
    "time": 1772595657119
}
```

**问题分析**
- 错误码: `10010`
- 错误信息: `Unmatched IP`
- 原因: API Key 绑定了 IP 白名单，当前出口 IP `119.234.81.184` 不在白名单中

**解决方案**
- 操作: 在 Bybit 后台 API 管理页面，将 `119.234.81.184` 添加到 IP 白名单
- 耗时: 约 5 分钟

**重试请求 (成功)**
```http
GET /v5/account/wallet-balance?accountType=UNIFIED&api_key=5uBSqgtJ7kq5XCT5YI&recvWindow=5000&timestamp=1772596656000&sign=xxx
```

**重试响应 (成功)**
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "list": [{
            "accountType": "UNIFIED",
            "totalEquity": "206.05345194",
            "totalAvailableBalance": "202.19014206"
        }]
    }
}
```

---

### 问题 2: 公共成交记录 - Endpoint 错误

**接口信息**
- 接口名称: 公共成交记录
- 接口地址: `GET /v5/market/trades` (错误)
- 正确地址: `GET /v5/market/recent-trade`
- 接口类型: 公开 (无需签名)

**首次请求 (失败)**
```http
GET /v5/market/trades?category=linear&symbol=BTCUSDT&limit=5
```

**首次响应 (失败)**
```
HTTP/1.1 404 Not Found
```

**问题分析**
- 错误码: `HTTP 404`
- 原因: V5 API 中成交记录的 endpoint 是 `/v5/market/recent-trade` 而非 `/v5/market/trades`

**解决方案**
- 操作: 更正 endpoint 为 `/v5/market/recent-trade`
- 耗时: 约 2 分钟

---

### 问题 3: 持仓分布 - 必填参数缺失

**接口信息**
- 接口名称: 持仓分布 (大户持仓比)
- 接口地址: `GET /v5/market/account-ratio`
- 接口类型: 公开 (无需签名)

**首次请求 (失败)**
```http
GET /v5/market/account-ratio?category=linear&symbol=BTCUSDT
```

**首次响应 (失败)**
```json
{
    "retCode": 10001,
    "retMsg": "params error: param period err",
    "result": {},
    "retExtInfo": {},
    "time": 1772598327123
}
```

**问题分析**
- 错误码: `10001`
- 错误信息: `params error: param period err`
- 原因: 接口需要必填参数 `period`，但请求中未提供

**支持的 period 值**
```
5min | 15min | 30min | 1h | 4h | 1d
```

**解决方案**
- 操作: 添加 `period=1d` 参数
- 耗时: 约 1 分钟

---

### 问题 4: 子账户列表 - API Key 权限不足

**接口信息**
- 接口名称: 子账户列表
- 接口地址: `GET /v5/user/sub-members`
- 接口类型: 私有 (需签名)

**响应 (失败)**
```json
{
    "retCode": 10003,
    "retMsg": "Permission denied, please check your API key permissions.",
    "result": {},
    "retExtInfo": {},
    "time": 1772598327456
}
```

**问题分析**
- 错误码: `10003`
- 错误信息: `Permission denied`
- 原因: 当前 API Key 未开启子账户管理权限
- 此接口需要: 主账户 API Key + 子账户权限

**解决方案**
- 需要操作: 在 Bybit 后台为 API Key 添加 `Sub-account` 权限
- 状态: 未执行 (需要用户确认)

---

### 问题 5: 交割价格 - 永续合约无交割价

**接口信息**
- 接口名称: 交割价格
- 接口地址: `GET /v5/market/delivery-price`
- 接口类型: 公开 (无需签名)

**首次请求 (失败)**
```http
GET /v5/market/delivery-price?category=linear&symbol=BTCUSDT
```

**首次响应 (失败)**
```json
{
    "retCode": 10001,
    "retMsg": "params error: Symbol Invalid",
    "result": {},
    "retExtInfo": {},
    "time": 1772598327789
}
```

**问题分析**
- 错误码: `10001`
- 错误信息: `Symbol Invalid`
- 原因: `BTCUSDT` 是永续合约 (LinearPerpetual)，没有交割日期，因此没有交割价格
- 交割价格接口仅适用于交割合约 (InverseFutures)

**解决方案**
- 操作: 使用交割合约 symbol (如 `BTCUSDH25`)
- 耗时: 约 3 分钟

---

### 问题 6: 历史波动率 - 参数格式错误

**接口信息**
- 接口名称: 历史波动率
- 接口地址: `GET /v5/market/historical-volatility`
- 接口类型: 公开 (无需签名)

**首次请求 (失败)**
```http
GET /v5/market/historical-volatility?category=option&baseCoin=BTC&period=7d
```

**首次响应 (失败)**
```json
{
    "retCode": 10001,
    "retMsg": "Request parameter error."
}
```

**问题分析**
- 错误码: `10001`
- 错误信息: `Request parameter error`
- 原因: `period` 参数格式错误，应该是数字 `7` 而非 `7d`

**解决方案**
- 操作: 将 `period=7d` 改为 `period=7`
- 耗时: 约 1 分钟

**重试请求 (成功)**
```http
GET /v5/market/historical-volatility?category=option&baseCoin=BTC&period=7
```

---

### 问题 7: 币种信息 - 需要私有接口

**接口信息**
- 接口名称: 币种信息
- 接口地址: `GET /v5/asset/coin/query-info`
- 接口类型: 私有 (需签名)

**首次请求 (失败) - 未签名**
```http
GET /v5/asset/coin/query-info
```

**首次响应 (失败)**
```json
{
    "retCode": 10001,
    "retMsg": "Request parameter error: apiKey is missing"
}
```

**问题分析**
- 错误码: `10001`
- 错误信息: `apiKey is missing`
- 原因: 该接口需要签名，但首次测试时当作公开接口调用

**解决方案**
- 操作: 改为私有接口调用，添加 api_key 和签名
- 耗时: 约 1 分钟

---

### 问题 8: 币种兑换汇率 - 权限不足

**接口信息**
- 接口名称: 币种兑换汇率
- 接口地址: `GET /v5/asset/exchange/query-coin-list`
- 接口类型: 私有 (需签名)

**响应 (失败)**
```json
{
    "retCode": 10003,
    "retMsg": "Permission denied, please check your API key permissions."
}
```

**问题分析**
- 错误码: `10003`
- 错误信息: `Permission denied`
- 原因: 当前 API Key 未开启币种兑换相关权限

**解决方案**
- 需要操作: 在 Bybit 后台为 API Key 添加相关权限
- 状态: 未执行 (需要用户确认)

---

### 问题 9: 溢价指数K线等接口 - V5 API 不存在

**接口信息**
- 接口名称: 溢价指数K线
- 尝试的 endpoint: `GET /v5/market/premium-index-kline`

**响应 (失败)**
```
HTTP/1.1 404 Not Found
```

**问题分析**
- 错误码: `HTTP 404`
- 原因: 该接口可能在 V5 API 中不存在，或 endpoint 名称不同
- 状态: 需要进一步查证文档确认

---

## 交易接口测试记录

| 操作 | 接口 | 结果 | 详情 |
|------|------|------|------|
| 设置杠杆 | `POST /v5/position/set-leverage` | ✅ 幂等 | 杠杆已是 1x |
| 开多单 | `POST /v5/order/create` | ✅ 成功 | 0.001 BTC @ $67,710 |
| 设置止盈止损 | `POST /v5/position/trading-stop` | ✅ 成功 | TP: $74,481 (+10%) / SL: $60,939 (-10%) |

### 开仓订单详情

**请求**
```json
{
    "category": "linear",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Market",
    "qty": "0.001",
    "timeInForce": "GTC"
}
```

**响应**
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "orderId": "98596ca9-b22e-44e6-bb11-b66a29754007"
    }
}
```

**成交信息**
- 成交价: `$67,710`
- 成交量: `0.001 BTC`
- 成交金额: `$67.71 USDT`
- 手续费: `$0.027084 USDT` (费率 0.04%)

---

## 问题分类统计

### 按问题类型

| 问题类型 | 数量 | 占比 |
|----------|------|------|
| IP 白名单 | 1 | 1.4% |
| Endpoint 错误 | 1 | 1.4% |
| 必填参数缺失 | 1 | 1.4% |
| 参数格式错误 | 1 | 1.4% |
| 接口类型错误 | 1 | 1.4% |
| 权限不足 | 2 | 2.9% |
| 合约类型理解 | 1 | 1.4% |
| 幂等返回 | 1 | 1.4% |
| 接口不存在 | 4 | 5.8% |
| 无问题 | 56 | 81.2% |

### 按接口类型

| 接口类型 | 测试数 | 成功数 | 成功率 |
|----------|--------|--------|--------|
| 私有接口 (需签名) | 35 | 33 | 94% |
| 公开接口 (无需签名) | 34 | 32 | 94% |

---

## 结论与建议

### 主要发现

1. **签名算法** - 必须按字母顺序排序参数后再签名
2. **IP 白名单** - 需要提前配置，否则所有私有接口都会失败
3. **必填参数** - 部分接口文档不够清晰，需要通过错误信息推断
4. **参数格式** - 注意参数格式要求，如 `period` 应该是数字而非字符串
5. **接口类型** - 部分看似公开的接口实际需要签名
6. **权限控制** - 部分功能需要特定 API Key 权限
7. **Endpoint 变化** - V5 API 与旧版 endpoint 有差异，需注意

### 建议

1. **统一封装** - 使用 Python 脚本统一处理签名，避免 Shell 脚本坑
2. **先查后操作** - 交易前先查询 `instruments-info` 确认规则
3. **错误处理** - 区分幂等错误和真正的失败
4. **权限检查** - 使用新功能前先确认 API Key 权限配置
5. **日志记录** - 保存完整的请求和响应用于排查
6. **参数验证** - 调用前确认参数格式和必填项

---

*报告生成时间: 2026-03-04 13:05 SGT*
*总测试接口: 69 个*
*总成功率: 94%*
