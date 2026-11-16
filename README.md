# Bollinger Bands & RSI Mean-Reversion Backtester

An exploratory Python quantitative analysis and backtesting engine investigating the joint behavior of volatility (**Bollinger Bands**) and momentum (**Relative Strength Index**) to identify statistical entry and exit points on historical equity data.

## Overview & Hypothesis
Single technical indicators frequently generate false signals during strong directional trends. This project tests whether combining **volatility extremes** (price closing outside 2 standard deviations of a 14-day moving average) with **momentum extremes** (14-day RSI thresholds) filters out noise and isolates high-probability mean-reversion entry and exit points.

- **Asset Tested:** Rocket Lab USA, Inc. (`RKLB`)
- **Sample Window:** January 1, 2024 – October 15, 2026 (699 trading days)
- **Data Source:** Daily OHLCV data retrieved via `yfinance`

## Methodology & Signal Rules
All indicators are computed from scratch using `pandas` and `numpy` over a 14-day rolling window:

1. **Bollinger Bands (14-day, $\pm 2\sigma$):**
   - **Middle Band (`M_Avg`):** 14-day simple moving average of `Close`
   - **Upper Band (`UB`):** $\text{M\_Avg} + 2\sigma_{14}$
   - **Lower Band (`LB`):** $\text{M\_Avg} - 2\sigma_{14}$
2. **Relative Strength Index (14-day RSI):**
   - Computed from rolling 14-day average gains and average losses via $\text{RSI} = 100 - \frac{100}{1 + \text{RS}}$.
3. **Trade Execution Logic:**
   - **Entry (`Buy`):** Triggered when $\text{RSI} < 30$, $\text{Close} < \text{LB}$, and $\text{Close} < \text{Open}$ (bearish candle piercing the lower volatility band under oversold momentum).
   - **Exit (`Sell`):** Triggered when $\text{RSI} > 70$, $\text{Close} > \text{UB}$, and $\text{Close} > \text{Open}$ (bullish candle piercing the upper volatility band under overbought momentum).
   - **Position Tracking:** Sequential event-driven loop accumulates open long entries during oversold regimes and closes all active positions upon the next qualifying overbought exit signal.

## Key Observations & Limitations
- **Signal Clustering:** During sustained drawdowns (e.g., Feb 2025) or prolonged rallies, multiple consecutive entry signals trigger before an exit condition is met, acting as an implicit dollar-cost-averaging mechanism.
- **Current Limitations & Next Steps:**
  - *Out-of-Sample Testing:* Parameters (14-day lookback, $2\sigma$ width, 30/70 RSI cutoffs) are evaluated in-sample on a single high-beta ticker; future iterations will test across multiple tickers and out-of-sample splits.
  - *Execution Realism:* Current P&L (`Difference`) measures raw price change per share without transaction costs, slippage, or capital allocation constraints.

## Tech Stack & Setup
- **Language:** Python 3.12
- **Libraries:** `pandas`, `numpy`, `yfinance`, `matplotlib`

```bash
pip install pandas numpy yfinance matplotlib
