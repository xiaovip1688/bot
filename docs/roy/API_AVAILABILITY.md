# Bybit V5 API Availability (Mainnet)

This is a snapshot of tested endpoints on mainnet (api.bybit.com) from the current environment. Status can change; re-run endpoint-check or targeted commands to refresh.

## ✅ Available

### WebSocket
- Public: wss://stream.bybit.com/v5/public/linear (tested subscribe tickers.MNTPERP)
- Private: wss://stream.bybit.com/v5/private (auth + subscribe order/position/wallet)

### Market
- GET /v5/market/time
- GET /v5/market/tickers
- GET /v5/market/instruments-info

### Trade / Position
- GET /v5/order/realtime
- GET /v5/order/history
- GET /v5/execution/list
- GET /v5/position/list
- POST /v5/order/create (requires full params)
- POST /v5/order/amend (requires params)
- POST /v5/order/cancel (requires params)
- POST /v5/position/set-leverage (requires params)
- POST /v5/position/trading-stop (requires params)

### Account / Asset
- GET /v5/account/wallet-balance
- GET /v5/asset/transfer/query-inter-transfer-list
- GET /v5/asset/exchange/query-convert-history

### Earn (query)
- GET /v5/earn/product (category FlexibleSaving/OnChain)
- GET /v5/earn/position
- GET /v5/earn/hourly-yield

### Spot Margin (UTA)
- GET /v5/spot-margin-trade/data
- GET /v5/spot-margin-trade/state
- GET /v5/spot-margin-trade/interest-rate-history

### Crypto Loan (new)
- GET /v5/crypto-loan-common/loanable-data
- GET /v5/crypto-loan-common/collateral-data

### Crypto Loan (legacy)
- GET /v5/crypto-loan/loanable-data
- GET /v5/crypto-loan/collateral-data

## ❌ Not Available (404 on mainnet)

### Earn
- POST /v5/earn/create-order
- GET /v5/earn/order-history
- GET /v5/earn/yield-history

### Market
- GET /v5/market/funding-rate (use /v5/market/tickers fundingRate instead)

### Asset / Exchange
- POST /v5/asset/transfer/sub-member
- POST /v5/asset/exchange/quote-execute

### Spot Margin (UTA)
- GET /v5/spot-margin-trade/collateral-tiers
- GET /v5/spot-margin-trade/max-borrowing-amount
- GET /v5/spot-margin-trade/coin-info

### Crypto Loan (new)
- GET /v5/crypto-loan-common/max-loan-amount
- GET /v5/crypto-loan-common/crypto-loan-position
- GET /v5/crypto-loan-common/ltv-adjust-history
- GET /v5/crypto-loan-common/reduce-max-collateral-amt
- POST /v5/crypto-loan-common/adjust-collateral

## Notes
- Funding rate on mainnet can be retrieved from /v5/market/tickers (fundingRate, nextFundingTime).
- Some endpoints may be region/VIP gated; retCode errors with valid paths usually mean parameter or permission issues.
