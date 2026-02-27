# Technical Indicator Reference

Complete catalog of all 110+ technical indicators available through the Crypto Data API.

---

## Quick Navigation

- [OHLCV Data](#ohlcv-data)
- [Moving Averages](#moving-averages)
- [Ichimoku Cloud - Standard](#ichimoku-cloud---standard)
- [Ichimoku Cloud - Alternate](#ichimoku-cloud---alternate)
- [Ichimoku Cloud - Projected](#ichimoku-cloud---projected)
- [Heiken Ashi](#heiken-ashi)
- [Bollinger Bands](#bollinger-bands)
- [RSI (Relative Strength Index)](#rsi-relative-strength-index)
- [ATR (Average True Range)](#atr-average-true-range)
- [ADX (Average Directional Index)](#adx-average-directional-index)
- [MACD](#macd)
- [Moving Average Crosses](#moving-average-crosses)
- [Price Position Indicators](#price-position-indicators)

---

## OHLCV Data

These fields are **always included** in every response:

| Indicator | Type      | Description                         |
| --------- | --------- | ----------------------------------- |
| `time`    | timestamp | Candle timestamp (ISO 8601 format)  |
| `open`    | numeric   | Opening price                       |
| `high`    | numeric   | Highest price in period             |
| `low`     | numeric   | Lowest price in period              |
| `close`   | numeric   | Closing price                       |
| `volume`  | numeric   | Trading volume                      |
| `price`   | numeric   | Current/close price (same as close) |

**Example**:

```json
{
  "time": "2025-01-01T01:00:00.000Z",
  "open": "94188.00000000",
  "high": "94188.00000000",
  "low": "93416.00000000",
  "close": "93421.00000000",
  "volume": "41.00016881",
  "price": "93421.00000000"
}
```

---

## Moving Averages

### Simple Moving Average (SMA)

| Indicator | Period | Description                      |
| --------- | ------ | -------------------------------- |
| `sma9`    | 9      | 9-period simple moving average   |
| `sma20`   | 20     | 20-period simple moving average  |
| `sma50`   | 50     | 50-period simple moving average  |
| `sma100`  | 100    | 100-period simple moving average |
| `sma200`  | 200    | 200-period simple moving average |

**Use Cases**: Trend identification, support/resistance levels, crossover strategies

### Exponential Moving Average (EMA)

| Indicator | Period | Description                           |
| --------- | ------ | ------------------------------------- |
| `ema9`    | 9      | 9-period exponential moving average   |
| `ema12`   | 12     | 12-period EMA (MACD component)        |
| `ema20`   | 20     | 20-period exponential moving average  |
| `ema26`   | 26     | 26-period EMA (MACD component)        |
| `ema50`   | 50     | 50-period exponential moving average  |
| `ema100`  | 100    | 100-period exponential moving average |
| `ema200`  | 200    | 200-period exponential moving average |

**Use Cases**: Faster trend following, reduced lag compared to SMA

---

## Ichimoku Cloud - Standard

The standard Ichimoku Cloud uses settings: 9/26/52

### Core Components

| Indicator       | Calculation          | Description                                 |
| --------------- | -------------------- | ------------------------------------------- |
| `tenkan_sen`    | (9H + 9L) / 2        | Conversion line - fast signal               |
| `kijun_sen`     | (26H + 26L) / 2      | Base line - support/resistance              |
| `senkou_span_a` | (Tenkan + Kijun) / 2 | Leading span A (projected 26 periods ahead) |
| `senkou_span_b` | (52H + 52L) / 2      | Leading span B (projected 26 periods ahead) |
| `chikou_span`   | Close                | Lagging span (plotted 26 periods back)      |

**Cloud (Kumo)**: The area between senkou_span_a and senkou_span_b

### Signal Indicators

#### TK Cross (Tenkan/Kijun Cross)

| Indicator          | Type    | Description                                 |
| ------------------ | ------- | ------------------------------------------- |
| `tk_cross_bullish` | boolean | Tenkan crossed above Kijun (bullish signal) |
| `tk_cross_bearish` | boolean | Tenkan crossed below Kijun (bearish signal) |

#### Kijun Cross

| Indicator             | Type    | Description                         |
| --------------------- | ------- | ----------------------------------- |
| `kijun_cross_bullish` | boolean | Price crossed above Kijun (bullish) |
| `kijun_cross_bearish` | boolean | Price crossed below Kijun (bearish) |

#### Cloud Breakouts

| Indicator               | Type    | Description                              |
| ----------------------- | ------- | ---------------------------------------- |
| `kumo_breakout_bullish` | boolean | Price broke above cloud (strong bullish) |
| `kumo_breakout_bearish` | boolean | Price broke below cloud (strong bearish) |

#### Senkou Span Cross

| Indicator                   | Type    | Description                                           |
| --------------------------- | ------- | ----------------------------------------------------- |
| `senkou_span_cross_bullish` | boolean | Span A crossed above Span B (trend change to bullish) |
| `senkou_span_cross_bearish` | boolean | Span A crossed below Span B (trend change to bearish) |

#### Advanced Signals

| Indicator              | Type    | Description                                       |
| ---------------------- | ------- | ------------------------------------------------- |
| `tk_cross_above_cloud` | boolean | TK cross occurred above cloud (strongest bullish) |
| `tk_cross_below_cloud` | boolean | TK cross occurred below cloud (strongest bearish) |
| `edge_to_edge_bull`    | boolean | Price moved from top to bottom of bullish cloud   |
| `edge_to_edge_bear`    | boolean | Price moved from bottom to top of bearish cloud   |

---

## Ichimoku Cloud - Alternate

Alternative Ichimoku settings: 20/60/120 (for longer timeframes)

### Core Components

| Indicator                 | Calculation          | Description                          |
| ------------------------- | -------------------- | ------------------------------------ |
| `tenkan_sen_alternate`    | (20H + 20L) / 2      | Conversion line - alternate settings |
| `kijun_sen_alternate`     | (60H + 60L) / 2      | Base line - alternate settings       |
| `senkou_span_a_alternate` | (Tenkan + Kijun) / 2 | Leading span A - alternate           |
| `senkou_span_b_alternate` | (120H + 120L) / 2    | Leading span B - alternate           |
| `chikou_span_alternate`   | Close                | Lagging span - alternate             |

### Signal Indicators

| Indicator                             | Type    | Description                      |
| ------------------------------------- | ------- | -------------------------------- |
| `tk_cross_bullish_alternate`          | boolean | Alternate TK cross bullish       |
| `tk_cross_bearish_alternate`          | boolean | Alternate TK cross bearish       |
| `kijun_cross_bullish_alternate`       | boolean | Alternate Kijun cross bullish    |
| `kijun_cross_bearish_alternate`       | boolean | Alternate Kijun cross bearish    |
| `kumo_breakout_bullish_alternate`     | boolean | Alternate cloud breakout bullish |
| `kumo_breakout_bearish_alternate`     | boolean | Alternate cloud breakout bearish |
| `senkou_span_cross_bullish_alternate` | boolean | Alternate span cross bullish     |
| `senkou_span_cross_bearish_alternate` | boolean | Alternate span cross bearish     |
| `tk_cross_above_cloud_alternate`      | boolean | Alternate TK cross above cloud   |
| `tk_cross_below_cloud_alternate`      | boolean | Alternate TK cross below cloud   |
| `edge_to_edge_bull_alternate`         | boolean | Alternate edge-to-edge bullish   |
| `edge_to_edge_bear_alternate`         | boolean | Alternate edge-to-edge bearish   |

---

## Ichimoku Cloud - Projected

Future cloud projections help anticipate support/resistance levels.

### Projected Cloud Components

| Indicator                           | Description                            |
| ----------------------------------- | -------------------------------------- |
| `senkou_span_a_projected`           | Standard Span A projected into future  |
| `senkou_span_b_projected`           | Standard Span B projected into future  |
| `senkou_span_a_alternate_projected` | Alternate Span A projected into future |
| `senkou_span_b_alternate_projected` | Alternate Span B projected into future |

### Projected Cross Signals

| Indicator                                       | Type    | Description                            |
| ----------------------------------------------- | ------- | -------------------------------------- |
| `senkou_span_cross_bullish_projected`           | boolean | Projected standard span cross bullish  |
| `senkou_span_cross_bearish_projected`           | boolean | Projected standard span cross bearish  |
| `senkou_span_cross_bullish_alternate_projected` | boolean | Projected alternate span cross bullish |
| `senkou_span_cross_bearish_alternate_projected` | boolean | Projected alternate span cross bearish |
| `projected_cross_bullish`                       | boolean | Any projected bullish cross            |
| `projected_cross_bearish`                       | boolean | Any projected bearish cross            |

**Use Case**: Anticipate future cloud twists and potential trend changes.

---

## Heiken Ashi

Heiken Ashi candles smooth price action and filter noise.

| Indicator        | Type    | Description                          |
| ---------------- | ------- | ------------------------------------ |
| `ha_open`        | numeric | Heiken Ashi open price               |
| `ha_high`        | numeric | Heiken Ashi high price               |
| `ha_low`         | numeric | Heiken Ashi low price                |
| `ha_close`       | numeric | Heiken Ashi close price              |
| `ha_daily_green` | boolean | True if HA candle is green (bullish) |

**Calculation**:

- HA close = (O + H + L + C) / 4
- HA open = (previous HA open + previous HA close) / 2
- HA high = max(H, HA open, HA close)
- HA low = min(L, HA open, HA close)

**Use Cases**:

- Trend following
- Reducing false signals
- Clearer trend visualization

---

## Bollinger Bands

Bollinger Bands measure volatility and overbought/oversold conditions.

| Indicator    | Type    | Description                                                   |
| ------------ | ------- | ------------------------------------------------------------- |
| `bb_upper`   | numeric | Upper Bollinger Band (SMA20 + 2σ)                             |
| `bb_middle`  | numeric | Middle Bollinger Band (SMA20)                                 |
| `bb_lower`   | numeric | Lower Bollinger Band (SMA20 - 2σ)                             |
| `bb_width`   | numeric | Band width normalized (upper - lower) / middle                |
| `bb_squeeze` | boolean | True when bands are unusually narrow (volatility compression) |

**Settings**: 20-period SMA with 2 standard deviations

**Use Cases**:

- Identifying overbought/oversold levels
- Volatility measurement
- Squeeze detection (potential breakout setup)

---

## RSI (Relative Strength Index)

RSI measures momentum and overbought/oversold conditions.

### RSI Values

| Indicator | Period | Range | Description              |
| --------- | ------ | ----- | ------------------------ |
| `rsi14`   | 14     | 0-100 | 14-period RSI (standard) |
| `rsi9`    | 9      | 0-100 | 9-period RSI (faster)    |

### RSI Conditions

| Indicator          | Threshold | Description                   |
| ------------------ | --------- | ----------------------------- |
| `rsi14_overbought` | > 70      | RSI14 in overbought territory |
| `rsi14_oversold`   | < 30      | RSI14 in oversold territory   |
| `rsi9_overbought`  | > 70      | RSI9 in overbought territory  |
| `rsi9_oversold`    | < 30      | RSI9 in oversold territory    |

**Interpretation**:

- **0-30**: Oversold (potential buy signal)
- **30-70**: Neutral zone
- **70-100**: Overbought (potential sell signal)

---

## ATR (Average True Range)

ATR measures volatility (not direction).

| Indicator | Type    | Units      | Description                                 |
| --------- | ------- | ---------- | ------------------------------------------- |
| `atr14`   | numeric | Price      | 14-period Average True Range in price units |
| `natr14`  | numeric | Percentage | 14-period Normalized ATR (ATR/Close × 100)  |

**Use Cases**:

- Setting stop-loss levels (e.g., 2× ATR)
- Position sizing based on volatility
- Comparing volatility across different assets (use NATR)

**Example**: If ATR14 = 500 and price = 94000, then NATR14 ≈ 0.53%

---

## ADX (Average Directional Index)

ADX measures trend strength (not direction).

### ADX Values

| Indicator | Type    | Range | Description   |
| --------- | ------- | ----- | ------------- |
| `adx14`   | numeric | 0-100 | 14-period ADX |

### ADX Conditions

| Indicator          | Threshold | Description                            |
| ------------------ | --------- | -------------------------------------- |
| `adx_strong_trend` | > 25      | Strong trending market                 |
| `adx_weak_trend`   | < 20      | Weak trend / ranging market            |
| `adx_bullish`      | DI+ > DI- | Positive directional movement dominant |
| `adx_bearish`      | DI- > DI+ | Negative directional movement dominant |

**Interpretation**:

- **0-20**: Weak trend (ranging, choppy)
- **20-25**: Developing trend
- **25-50**: Strong trend
- **50-100**: Very strong trend

---

## MACD

MACD (Moving Average Convergence Divergence) is a momentum oscillator.

### MACD Components

| Indicator        | Type    | Description                         |
| ---------------- | ------- | ----------------------------------- |
| `macd_line`      | numeric | MACD line (EMA12 - EMA26)           |
| `macd_signal`    | numeric | Signal line (9-period EMA of MACD)  |
| `macd_histogram` | numeric | Histogram (MACD line - Signal line) |

### MACD Signals

| Indicator            | Type    | Description                         |
| -------------------- | ------- | ----------------------------------- |
| `macd_cross_bullish` | boolean | MACD crossed above signal (bullish) |
| `macd_cross_bearish` | boolean | MACD crossed below signal (bearish) |

**Interpretation**:

- **Bullish**: MACD line above signal line
- **Bearish**: MACD line below signal line
- **Divergence**: Price and MACD moving in opposite directions

---

## Moving Average Crosses

Classic crossover signals using moving averages.

| Indicator         | Type    | Components     | Description                                |
| ----------------- | ------- | -------------- | ------------------------------------------ |
| `golden_cross`    | boolean | SMA50 × SMA200 | SMA50 crossed above SMA200 (major bullish) |
| `ema_cross_9_21`  | boolean | EMA9 × EMA21   | EMA9 crossed above EMA21 (bullish)         |
| `ema_cross_12_26` | boolean | EMA12 × EMA26  | EMA12 crossed above EMA26 (bullish)        |

**Note**: These only signal the bullish cross. Inverse indicates bearish cross.

**Golden Cross**: One of the most watched signals in traditional markets. Often followed by sustained uptrends.

---

## Price Position Indicators

Boolean indicators showing price position relative to moving averages.

### Price vs SMA

| Indicator            | Type    | Description                   |
| -------------------- | ------- | ----------------------------- |
| `price_above_sma9`   | boolean | Price is above 9-period SMA   |
| `price_above_sma20`  | boolean | Price is above 20-period SMA  |
| `price_above_sma50`  | boolean | Price is above 50-period SMA  |
| `price_above_sma100` | boolean | Price is above 100-period SMA |
| `price_above_sma200` | boolean | Price is above 200-period SMA |

### Price vs EMA

| Indicator            | Type    | Description                   |
| -------------------- | ------- | ----------------------------- |
| `price_above_ema9`   | boolean | Price is above 9-period EMA   |
| `price_above_ema12`  | boolean | Price is above 12-period EMA  |
| `price_above_ema20`  | boolean | Price is above 20-period EMA  |
| `price_above_ema26`  | boolean | Price is above 26-period EMA  |
| `price_above_ema50`  | boolean | Price is above 50-period EMA  |
| `price_above_ema100` | boolean | Price is above 100-period EMA |
| `price_above_ema200` | boolean | Price is above 200-period EMA |

**Use Cases**:

- Quick trend assessment
- Filter for long/short trades
- Support/resistance confirmation

**Example Strategy**: Only take long trades when `price_above_sma200 === true`

---

## Indicator Combinations

### Trend Following Strategy Example

```javascript
indicators: "sma50,sma200,ema20,adx14,atr14,price_above_sma200";

// Look for:
// - price_above_sma200 === true (uptrend)
// - adx14 > 25 (strong trend)
// - price > ema20 (short-term uptrend)
// - Use atr14 for stop-loss (e.g., 2 × ATR)
```

### Ichimoku + RSI Strategy Example

```javascript
indicators: "tenkan_sen,kijun_sen,senkou_span_a,senkou_span_b,tk_cross_bullish,kumo_breakout_bullish,rsi14";

// Look for:
// - tk_cross_bullish === true
// - kumo_breakout_bullish === true
// - 30 < rsi14 < 70 (not overbought/oversold)
```

### Volatility Breakout Strategy Example

```javascript
indicators: "bb_upper,bb_lower,bb_squeeze,atr14,volume";

// Look for:
// - bb_squeeze === true (compression)
// - Wait for breakout above bb_upper or below bb_lower
// - Confirm with volume increase
```

---

## Request Examples by Use Case

### Day Trading (Scalping)

```javascript
// Fast indicators on 1h timeframe
indicators: "ema9,ema12,rsi9,macd_line,macd_signal,atr14,bb_upper,bb_lower";
```

### Swing Trading

```javascript
// Medium-term indicators on 4h or 1d timeframe
indicators: "sma20,sma50,ema20,rsi14,macd_line,macd_signal,adx14,atr14";
```

### Position Trading (Long-term)

```javascript
// Longer-term indicators on 1d or 1w timeframe
indicators: "sma50,sma200,ema50,ema200,golden_cross,adx14";
```

### Ichimoku Specialist

```javascript
// Complete Ichimoku suite
indicators: "tenkan_sen,kijun_sen,senkou_span_a,senkou_span_b,chikou_span,tk_cross_bullish,tk_cross_bearish,kumo_breakout_bullish,kumo_breakout_bearish,senkou_span_cross_bullish";
```

### Technical Analysis Research

```javascript
// Get everything
indicators: "all";
```

---

## Indicator Calculation Periods

Most indicators are calculated using these standard periods:

| Indicator Type     | Standard Period             | Notes                       |
| ------------------ | --------------------------- | --------------------------- |
| SMA                | 9, 20, 50, 100, 200         | Classic periods             |
| EMA                | 9, 12, 20, 26, 50, 100, 200 | 12/26 for MACD              |
| RSI                | 9, 14                       | 14 is standard              |
| ATR                | 14                          | Industry standard           |
| NATR               | 14                          | Normalized version of ATR14 |
| ADX                | 14                          | Welles Wilder original      |
| MACD               | 12, 26, 9                   | Standard settings           |
| Bollinger Bands    | 20, 2σ                      | Classic Bollinger settings  |
| Ichimoku Standard  | 9, 26, 52                   | Original Japanese settings  |
| Ichimoku Alternate | 20, 60, 120                 | For longer timeframes       |

---

## Null Values

Some indicators may return `null` when:

- **New assets**: Recently listed cryptocurrencies
- **Data gaps**: Missing historical records

Always check for `null` values in your code before using indicator data.

---

## Performance Tips

### 1. Request Only What You Need

Instead of:

```javascript
indicators: "all"; // 110+ indicators
```

Use:

```javascript
indicators: "sma20,sma50,rsi14,macd_line"; // Only 4 indicators
```

**Benefits**: Smaller payload, faster response, lower cost

### 2. Use Appropriate Timeframes

For long-term analysis, use daily or weekly data instead of hourly:

```javascript
// Instead of 1 year of hourly data (8760 records)
interval: "1h", startDate: "2024-01-01", endDate: "2024-12-31"

// Use daily data (365 records)
interval: "1d", startDate: "2024-01-01", endDate: "2024-12-31"
```

### 3. Cache Historical Data

Historical data doesn't change - cache it locally and only request new data.

---

## Support

For questions about indicators:

- See main [DATA-API-DOCUMENTATION.md](./DATA-API-DOCUMENTATION.md)
- Test with small requests first (1-2 days of data)
- Use `indicators: "all"` once to see all available data

---

**Last Updated**: 2026-02-26  
**Total Indicators**: 110+
