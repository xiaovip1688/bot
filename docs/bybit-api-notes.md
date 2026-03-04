# Bybit API 操作问题总结

> 记录于 2026-03-04，基于实际开发调试过程

---

## 1. 签名生成方式不一致

| 项目 | 详情 |
|------|------|
| **现象** | Shell 脚本直接拼接参数签名失败，返回 `Error sign` |
| **原因** | Bybit 要求参数按**字母顺序 (alphabetical)** 排序后再进行 HMAC-SHA256 签名 |
| **解决** | 使用 Python 的 `sorted(params.items())` 确保参数顺序正确 |

```python
# 正确做法
sign_str = "&".join(f"{k}={v}" for k, v in sorted(params.items()))
signature = hmac.new(secret.encode(), sign_str.encode(), hashlib.sha256).hexdigest()
```

---

## 2. POST vs GET 请求混淆

| 项目 | 详情 |
|------|------|
| **现象** | 设置杠杆接口返回 `Invalid parameter` |
| **原因** | 部分接口要求 POST 请求，但误用 GET 方式调用 |
| **解决** | 仔细阅读 API 文档，确认请求方法 |

**常见 POST 接口：**
- `/v5/position/set-leverage` — 设置杠杆
- `/v5/order/create` — 创建订单
- `/v5/position/trading-stop` — 设置止盈止损

---

## 3. 环境变量在 Shell 中传递失败

| 项目 | 详情 |
|------|------|
| **现象** | Python heredoc 中 `$API_KEY` 变成空字符串 |
| **原因** | 单引号 heredoc `'PYEOF'` 阻止变量展开，双引号又被 shell 提前解释 |
| **解决** | 写入临时 Python 文件再执行，或在 Python 中使用 `os.environ.get()` |

```bash
# 不推荐
python3 << 'EOF'
api_key = "$API_KEY"  # 变量不会展开
EOF

# 推荐
python3 << EOF
api_key = os.environ.get("API_KEY")
EOF
```

---

## 4. 最小下单量限制

| 项目 | 详情 |
|------|------|
| **现象** | 10 USDT 的订单被拒绝 |
| **原因** | 每个交易对有最小下单量要求，BTCUSDT 最小 0.001 BTC (约 $67) |
| **解决** | 下单前调用 `/v5/market/instruments-info` 查询规则 |

```json
// instruments-info 返回的关键字段
{
  "lotSizeFilter": {
    "minOrderQty": "0.001",
    "qtyStep": "0.001",
    "minNotionalValue": "5"
  }
}
```

---

## 5. 杠杆设置幂等性

| 项目 | 详情 |
|------|------|
| **现象** | 返回错误码 `110043: leverage not modified` |
| **原因** | 杠杆已经是目标值，再次设置相同值会报错 |
| **解决** | 这是正常情况，不影响后续下单，可在代码中忽略此错误 |

---

## 6. 止盈止损价格精度

| 项目 | 详情 |
|------|------|
| **现象** | 止盈止损价格设置后显示为空或不正确 |
| **原因** | 变量传递问题或价格精度不符合要求 |
| **解决** | 确保价格符合 `tickSize` 精度要求，使用正确的触发方式 |

```python
# 设置止盈止损示例
params = {
    "symbol": "BTCUSDT",
    "takeProfit": "74481",    # 符合 tickSize 0.10
    "stopLoss": "60939",
    "tpTriggerBy": "LastPrice",
    "slTriggerBy": "LastPrice",
    "tpslMode": "Full"
}
```

---

## 7. IP 白名单限制

| 项目 | 详情 |
|------|------|
| **现象** | 返回 `10010: Unmatched IP` |
| **原因** | API Key 绑定了 IP 白名单，当前 IP 不在列表中 |
| **解决** | 在 Bybit 后台将当前出口 IP 添加到白名单 |

**查看当前出口 IP：**
```bash
curl -s ifconfig.me
```

---

## 最佳实践

1. **统一用 Python 脚本** 处理签名和请求，避免 Shell 的各种坑
2. **先查后下单** — 调用 `instruments-info` 确认最小下单量和价格精度
3. **错误码要区分** — 有些 "错误" 其实是幂等返回，不影响业务
4. **日志完整记录** — 保存 API 返回的原始 JSON，便于排查
5. **使用环境变量** — API Key 和 Secret 通过环境变量注入，不要硬编码

---

## 参考

- [Bybit API 文档](https://bybit-exchange.github.io/docs/v5/intro)
- [Bybit V5 API 签名说明](https://bybit-exchange.github.io/docs/v5/guide#authentication)
