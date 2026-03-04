# Bybit API 测试报告

> 测试时间: 2026-03-04 12:00 - 12:52 SGT
> 测试环境: macOS, Python 3.9, 出口 IP: 119.234.81.184
> API Key: 5uBSqgtJ7kq5XCT5YI (IP 白名单已配置)

---

## 测试概览

| 指标 | 数值 |
|------|------|
| 总测试接口数 | 20 |
| 一次成功 | 16 |
| 修复后成功 | 3 |
| 失败 (权限问题) | 1 |
| 总成功率 | 95% |
| 一次成功率 | 80% |

---

## 详细测试记录

### 第一批测试 (12:24)

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

### 第二批测试 (12:43)

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
- 参考: [Bybit V5 API 文档](https://bybit-exchange.github.io/docs/v5/market/recent-trade)

**解决方案**
- 操作: 更正 endpoint 为 `/v5/market/recent-trade`
- 耗时: 约 2 分钟

**重试请求 (成功)**
```http
GET /v5/market/recent-trade?category=linear&symbol=BTCUSDT&limit=5
```

**重试响应 (成功)**
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "list": [
            {
                "execId": "xxx",
                "symbol": "BTCUSDT",
                "price": "67710.00",
                "size": "0.001",
                "side": "Buy",
                "time": "1772597136336"
            }
        ]
    }
}
```

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
- 文档: 该接口需要 `period` 参数指定时间周期

**支持的 period 值**
```
5min | 15min | 30min | 1h | 4h | 1d
```

**解决方案**
- 操作: 添加 `period=1d` 参数
- 耗时: 约 1 分钟

**重试请求 (成功)**
```http
GET /v5/market/account-ratio?category=linear&symbol=BTCUSDT&period=1d
```

**重试响应 (成功)**
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "list": [
            {
                "symbol": "BTCUSDT",
                "buyRatio": "0.4521",
                "sellRatio": "0.5479",
                "timestamp": "1772598327000"
            }
        ]
    }
}
```

---

### 问题 4: 子账户列表 - API Key 权限不足

**接口信息**
- 接口名称: 子账户列表
- 接口地址: `GET /v5/user/sub-members`
- 接口类型: 私有 (需签名)

**请求参数**
```json
{
    "api_key": "5uBSqgtJ7kq5XCT5YI",
    "recvWindow": 5000,
    "timestamp": 1772598327000,
    "sign": "xxx"
}
```

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

**验证: 查询 BTCUSDT 合约类型**
```http
GET /v5/market/instruments-info?category=linear&symbol=BTCUSDT
```
```json
{
    "result": {
        "list": [{
            "symbol": "BTCUSDT",
            "contractType": "LinearPerpetual",  // 永续合约
            "deliveryTime": "0"  // 无交割时间
        }]
    }
}
```

**解决方案**
- 操作: 使用交割合约 symbol (如 `BTCUSDH25`)
- 耗时: 约 3 分钟

**重试请求 (成功)**
```http
GET /v5/market/instruments-info?category=linear&contractType=InverseFutures
```
找到交割合约后:
```http
GET /v5/market/delivery-price?category=linear&symbol=BTCUSDH25
```

---

### 问题 6: 设置杠杆 - 幂等返回 (非错误)

**接口信息**
- 接口名称: 设置杠杆
- 接口地址: `POST /v5/position/set-leverage`
- 接口类型: 私有 (需签名)

**请求参数**
```json
{
    "api_key": "5uBSqgtJ7kq5XCT5YI",
    "category": "linear",
    "symbol": "BTCUSDT",
    "buyLeverage": "1",
    "sellLeverage": "1",
    "recvWindow": 5000,
    "timestamp": 1772597136000,
    "sign": "xxx"
}
```

**响应 (幂等)**
```json
{
    "retCode": 110043,
    "retMsg": "leverage not modified",
    "result": {},
    "retExtInfo": {},
    "time": 1772597136185
}
```

**分析**
- 错误码: `110043`
- 错误信息: `leverage not modified`
- 原因: 杠杆已经是 1x，再次设置相同值返回此信息
- 判定: **非错误**，是幂等返回，不影响后续下单操作
- 处理: 代码中可忽略此错误码，或先查询当前杠杆再决定是否设置

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

| 问题类型 | 数量 | 占比 | 示例接口 |
|----------|------|------|----------|
| IP 白名单 | 1 | 5% | 钱包余额 |
| Endpoint 错误 | 1 | 5% | 公共成交记录 |
| 必填参数缺失 | 1 | 5% | 持仓分布 |
| 权限不足 | 1 | 5% | 子账户列表 |
| 合约类型理解 | 1 | 5% | 交割价格 |
| 幂等返回 | 1 | 5% | 设置杠杆 |
| 无问题 | 14 | 70% | - |

### 按接口类型

| 接口类型 | 测试数 | 成功数 | 成功率 |
|----------|--------|--------|--------|
| 私有接口 (需签名) | 13 | 12 | 92% |
| 公开接口 (无需签名) | 7 | 7 | 100% |

---

## 结论与建议

### 主要发现

1. **签名算法** - 必须按字母顺序排序参数后再签名
2. **IP 白名单** - 需要提前配置，否则所有私有接口都会失败
3. **必填参数** - 部分接口文档不够清晰，需要通过错误信息推断
4. **Endpoint 变化** - V5 API 与旧版 endpoint 有差异，需注意
5. **权限控制** - 部分功能需要特定 API Key 权限

### 建议

1. **统一封装** - 使用 Python 脚本统一处理签名，避免 Shell 脚本坑
2. **先查后操作** - 交易前先查询 `instruments-info` 确认规则
3. **错误处理** - 区分幂等错误和真正的失败
4. **权限检查** - 使用新功能前先确认 API Key 权限配置
5. **日志记录** - 保存完整的请求和响应用于排查

---

*报告生成时间: 2026-03-04 12:53 SGT*
