# Welcome to the Hedgehog Edge API

The Hedgehog Edge Data API provides institutional-grade historical OHLCV data paired with deeply calculated technical indicators (MACD, RSI, Ichimoku, Bollinger Bands, and more) specifically formatted for crypto backtesting and algorithmic trading.

## Autonomous Payments with x402

We do not use traditional API keys, monthly subscriptions, or fiat credit cards. Our API is built entirely on the **x402 protocol**.

This allows trading algorithms and AI agents to autonomously pay for data on a strictly **per-request basis**. Payments are settled instantly in **USDC on the Base network** (Ethereum Layer 2). You only pay for the exact amount of data rows you request.

### Why x402?

- **🤖 AI-Native**: Perfect for autonomous agents and algorithmic systems
- **💰 Pay-Per-Use**: No subscriptions, no unused quota, no wasted money
- **⚡ Instant Settlement**: Sub-second payment confirmation on Base L2
- **🔐 No API Keys**: No credentials to manage, rotate, or secure
- **🌍 Decentralized**: Direct wallet-to-wallet payments, no intermediaries

---

## Base API URL

All API requests should be directed to our dedicated x402 data server:

```
https://x402.hedgehogedge.com/api
```

---

## Prerequisites

Before you begin, ensure you have:

1. **Node.js** (v16 or higher)
2. **An Ethereum wallet** with a private key
3. **USDC on Base Mainnet** (~$0.10+ for testing)
4. **ETH on Base Mainnet** (~$0.01+ for gas fees)

**Important**: This API operates on **Base Mainnet** (not Ethereum mainnet). You'll need to bridge USDC and ETH to Base network.

---

## Quickstart: Fetching Paid Data

To interact with our API, you or your AI agent will need an Ethereum-compatible wallet funded with USDC on Base, and the official x402 SDK to handle the cryptographic payment handshake.

### 1. Install Dependencies

Install the required x402 packages and a Web3 wallet handler (like `viem`) in your Node.js environment:

```bash
npm install axios @x402/axios @x402/evm viem dotenv
```

### 2. Configure Your Environment

Create a `.env` file in your project root:

```env
TEST_BUYER_PRIVATE_KEY=0x_your_private_key_here
```

**⚠️ Security Warning**: Never commit your `.env` file to version control! Add it to `.gitignore`.

### 3. Implement the Client

The x402 SDK automatically intercepts our `402 Payment Required` response, calculates the dynamic price based on your requested data range, signs the USDC transfer with your private key, and retries the request to fetch the data.

Here is the complete implementation to fetch 1-hour BTC/USD data:

```javascript
require("dotenv").config();
const axios = require("axios");
const https = require("https");
const { privateKeyToAccount } = require("viem/accounts");
const { x402Client, wrapAxiosWithPayment } = require("@x402/axios");
const { registerExactEvmScheme } = require("@x402/evm/exact/client");

async function fetchHedgehogData() {
  // 1. Initialize your wallet (Ensure this wallet holds USDC on Base)
  let privateKey = process.env.TEST_BUYER_PRIVATE_KEY;
  if (!privateKey.startsWith("0x")) {
    privateKey = `0x${privateKey}`;
  }
  const account = privateKeyToAccount(privateKey);

  console.log(`Using wallet: ${account.address}`);

  // 2. Setup the x402 client and register the EVM payment scheme
  const client = new x402Client();
  registerExactEvmScheme(client, { signer: account });

  // 3. Wrap Axios to handle the 402 payment handshake automatically
  const agent = new https.Agent({ family: 4 });
  const api = wrapAxiosWithPayment(
    axios.create({
      httpsAgent: agent,
      timeout: 30000,
    }),
    client,
  );

  try {
    console.log("Requesting data and negotiating x402 payment...");

    // 4. Make the request to the Hedgehog Edge API
    const response = await api.get(
      "https://x402.hedgehogedge.com/api/data/1h",
      {
        params: {
          symbol: "BTC/USD",
          startDate: "2025-01-01",
          endDate: "2025-01-02",
          limit: 100,
          indicators: "sma20,sma50,rsi14,macd_line,macd_signal", // Optional: specify indicators
        },
      },
    );

    console.log("✓ Payment Verified & Data Received!");
    console.log(`✓ Successfully fetched ${response.data.data.length} records`);

    // View the first record
    console.log("\nFirst record:");
    console.log(JSON.stringify(response.data.data[0], null, 2));

    // View pricing information
    if (response.data.meta) {
      console.log("\nRequest metadata:");
      console.log(JSON.stringify(response.data.meta, null, 2));
    }

    return response.data;
  } catch (error) {
    console.error("Request Failed:", error.response?.data || error.message);
    throw error;
  }
}

fetchHedgehogData();
```

### 4. Run Your Script

```bash
node your-script.js
```

**Expected Output**:

```
Using wallet: 0x9F723FDC86b90Da0F94409b082Aa84dE1539B97c
Requesting data and negotiating x402 payment...
✓ Payment Verified & Data Received!
✓ Successfully fetched 24 records

First record:
{
  "time": "2025-01-01T00:00:00.000Z",
  "open": "94188.00000000",
  "high": "94188.00000000",
  "low": "93416.00000000",
  "close": "93421.00000000",
  "volume": "41.00016881",
  "sma20": "93978.8",
  "sma50": "93437.18",
  "rsi14": "47.20627021",
  "macd_line": "65.69013816",
  "macd_signal": "166.91188847"
}
```

---

## Understanding the Payment Flow

When you make a request, here's what happens behind the scenes:

1. **Initial Request**: Your client makes a GET request to our API
2. **402 Payment Required**: Server responds with payment instructions (amount, wallet address, asset)
3. **SDK Negotiation**: x402 SDK parses the payment requirement
4. **USDC Transfer**: SDK executes USDC payment on Base mainnet using your wallet
5. **Payment Proof**: SDK creates cryptographic proof of payment
6. **Retry with Proof**: SDK retries the original request with payment proof
7. **Data Delivery**: Server validates payment and returns your data

All of this happens **automatically** in 1-3 seconds when using the x402 SDK!

---

## Dynamic Pricing

Our pricing is calculated dynamically based on the volume of data you request.

### Cost Factors

- **Number of rows**: Based on the date range and interval
- **Indicators requested**: More indicators = slightly higher cost
- **Timeframe**: Hourly data costs more than daily (more rows)

### Example Costs

- **24 hours of 1h data** with all indicators: ~$0.10 USDC
- **7 days of 1d data** with specific indicators: ~$0.05 USDC
- **1 month of 4h data** with basic indicators: ~$0.15 USDC

### Verification

The precise USDC amount required will be transmitted to your client during the initial 402 HTTP handshake. The x402 SDK displays this before proceeding with payment.

---

## Available Timeframes

The API supports 5 different timeframes:

| Interval | Endpoint Path   | Use Case              |
| -------- | --------------- | --------------------- |
| 1 hour   | `/api/data/1h`  | Day trading, scalping |
| 4 hours  | `/api/data/4h`  | Swing trading         |
| 12 hours | `/api/data/12h` | Multi-day positions   |
| 1 day    | `/api/data/1d`  | Position trading      |
| 1 week   | `/api/data/1w`  | Long-term investing   |

---

## Request Parameters

### Required Parameters

| Parameter   | Type   | Example        | Description             |
| ----------- | ------ | -------------- | ----------------------- |
| `symbol`    | string | `"BTC/USD"`    | Trading pair symbol     |
| `startDate` | string | `"2025-01-01"` | Start date (YYYY-MM-DD) |
| `endDate`   | string | `"2025-01-31"` | End date (YYYY-MM-DD)   |

### Optional Parameters

| Parameter    | Type    | Default     | Example                   | Description                                   |
| ------------ | ------- | ----------- | ------------------------- | --------------------------------------------- |
| `indicators` | string  | Limited set | `"sma20,rsi14,macd_line"` | Comma-separated indicator list or `"all"`     |
| `limit`      | integer | 100         | `500`                     | Maximum records to return                     |
| `offset`     | integer | 0           | `0`                       | Pagination offset (not recommended with x402) |

### Indicator Examples

```javascript
// Get all 110+ indicators
indicators: "all";

// Get specific trend indicators
indicators: "sma20,sma50,sma200,ema20,ema50";

// Get momentum indicators
indicators: "rsi14,macd_line,macd_signal,adx14";

// Get Ichimoku Cloud suite
indicators: "tenkan_sen,kijun_sen,senkou_span_a,senkou_span_b,tk_cross_bullish";

// Get volatility indicators
indicators: "atr14,bb_upper,bb_middle,bb_lower,bb_squeeze";
```

---

## Response Structure

Successful responses follow this structure:

```json
{
  "success": true,
  "data": [
    {
      "time": "2025-01-01T00:00:00.000Z",
      "open": "94188.00000000",
      "high": "94188.00000000",
      "low": "93416.00000000",
      "close": "93421.00000000",
      "volume": "41.00016881",
      "price": "93421.00000000",
      "sma20": "93978.8",
      "rsi14": "47.20627021"
    }
  ],
  "meta": {
    "symbol": "BTC/USD",
    "interval": "1h",
    "pricing": {
      "amount": "0.1",
      "currency": "USD",
      "assetSymbol": "USDC"
    }
  }
}
```

---

## Supported Symbols

We support most major cryptocurrency trading pairs with USD:

**Major Pairs**: BTC/USD, ETH/USD, SOL/USD, BNB/USD, XRP/USD, ADA/USD, DOGE/USD

**DeFi Tokens**: UNI/USD, LINK/USD, AAVE/USD, MATIC/USD, AVAX/USD

**Layer 1s**: DOT/USD, ATOM/USD, NEAR/USD, APT/USD

And many more! To check if a symbol is available, make a small test request (1-2 days of data).

---

## Error Handling

Always implement proper error handling in production:

```javascript
try {
  const response = await api.get("https://x402.hedgehogedge.com/api/data/1h", {
    params: {
      symbol: "BTC/USD",
      startDate: "2025-01-01",
      endDate: "2025-01-02",
    },
  });

  // Process data
  console.log(response.data);
} catch (error) {
  if (error.response?.status === 404) {
    console.error("No data found for this symbol/date range");
  } else if (error.response?.status === 400) {
    console.error("Invalid parameters:", error.response.data);
  } else {
    console.error("Request failed:", error.message);
  }
}
```

---

## Next Steps

Now that you can connect to the API, explore our documentation to unlock the full power of the platform:

### 📚 Documentation

- **[Indicator Reference](./INDICATOR-REFERENCE.md)** - Complete catalog of all 110+ technical indicators
- **[API Documentation](./DATA-API-DOCUMENTATION.md)** - Comprehensive API reference with advanced examples
- **[x402 Testing Guide](./X402-TESTING-GUIDE.md)** - Deep dive into x402 payment testing

### 🎯 Common Use Cases

**For Day Traders:**

```javascript
indicators: "ema9,ema12,rsi9,macd_line,macd_signal,bb_upper,bb_lower,atr14";
```

**For Swing Traders:**

```javascript
indicators: "sma20,sma50,ema20,rsi14,macd_line,adx14,atr14";
```

**For Ichimoku Specialists:**

```javascript
indicators: "tenkan_sen,kijun_sen,senkou_span_a,senkou_span_b,chikou_span,tk_cross_bullish,kumo_breakout_bullish";
```

**For Research & Backtesting:**

```javascript
indicators: "all"; // Get everything!
```

---

## Best Practices

### 🔐 Security

- **Never hardcode private keys** in your source code
- **Use environment variables** (`.env` files)
- **Add `.env` to `.gitignore`**
- **Use dedicated wallets** for API payments (not your main holdings)

### 💰 Cost Optimization

- **Request specific indicators** instead of `"all"` when possible
- **Use appropriate timeframes** (daily instead of hourly for long-term analysis)
- **Cache historical data** (it doesn't change!)

### ⚡ Performance

- **Set appropriate timeouts** (30 seconds recommended)
- **Implement retry logic** for network errors
- **Validate parameters** before sending requests

---

## Support & Community

Need help? We've got you covered:

- **📖 Read the Docs**: Start with [INDICATOR-REFERENCE.md](./INDICATOR-REFERENCE.md)
- **🧪 Test Locally**: Use small date ranges to experiment
- **💬 Issues**: Report bugs or request features via GitHub Issues

---

## Legal & Disclaimer

This API provides data for informational and research purposes only. **Not financial advice**. Cryptocurrency trading carries significant risk. Always conduct your own research (DYOR) before making trading decisions.

All payments are final and non-refundable. Ensure you have correct parameters before requesting data.

---

**Ready to build?** Start with a simple request and explore from there. Happy coding! 🚀
