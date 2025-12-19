#  Intraday Machine Learning Trading Strategy

## TL;DR
A regime-aware intraday trading system using decision trees, technical indicators, and volatility-based risk management, backtested on multi-year 15-minute Indian equity data with strict train/test separation.

---

## Overview

This project implements a **systematic intraday trading framework** that combines traditional technical indicators with **machine learning–based signal generation** and **robust risk management**.

The strategy is designed to:
- Trade multiple stocks intraday
- Adapt position sizing based on volatility
- Filter trades based on market regime
- Avoid overfitting via out-of-sample testing

All results are generated using historical 15-minute OHLCV data from Indian equities.

---

## Strategy Architecture

### 1. Data Ingestion
- Uses pre-downloaded **15-minute OHLCV CSV files**
- Each stock is loaded into a separate pandas DataFrame
- Data is cleaned, sorted, and indexed by timestamp

---

### 2. Feature Engineering

For each ticker, the following indicators are computed:

**Trend & Momentum**
- EMA (5, 10, 20, 30)
- SMA (20, 50)
- RSI (5, 14)

**Volatility**
- ATR (14)
- Rolling standard deviation

**Volume & Price Context**
- VWAP (reset daily)
- Volume ratio vs rolling average

**Market Structure**
- Hurst exponent (trend vs mean-reversion detection)

---

### 3. Market Regime Detection

A simple regime classifier categorizes the market as:
- Bullish
- Bearish
- Sideways

This is used to:
- Allow only long trades in bullish regimes
- Allow only short trades in bearish regimes
- Reduce exposure during sideways conditions

---

### 4. Machine Learning Signal Generation

- A **Decision Tree Classifier** is trained per ticker
- Target variable:
  - Direction of future returns (up / down / neutral)
- Features include trend, momentum, volatility, and volume signals
- Strict **time-based train/test split**
  - Training: 2019–2023
  - Testing: 2024
- Only **out-of-sample predictions** are used for evaluation

---

### 5. Daily Stock Selection

Each trading day:
- Stocks are ranked by ML prediction confidence
- Top-N BUY and SELL candidates are selected
- Low-confidence and low-volatility stocks are filtered out

---

### 6. Position Sizing & Allocation

- Capital is allocated using **inverse volatility weighting**
- Higher ATR → smaller position size
- Prevents concentration risk across tickers

---

### 7. Intraday Execution Logic

Trades follow strict intraday rules:
- Entry only during market hours
- Stop-loss based on **ATR multiple**
- Exit conditions:
  - Stop-loss hit
  - Signal reversal
  - Mandatory square-off before market close

This ensures:
- No overnight risk
- Controlled downside per trade

---

### 8. Portfolio Aggregation & Metrics

After trade simulation, the strategy computes:
- Cumulative return
- Equity curve
- Daily P&L
- Maximum drawdown
- Volatility
- Sharpe ratio
- Sortino ratio
- Trade statistics

Metrics are reported **separately for training and testing periods** to detect overfitting.

---

## Results Summary (Example)

| Metric | Training | Testing |
|------|---------|---------|
| Cumulative Return | ~31% | ~0.2% |
| Max Drawdown | ~-21% | ~-7.6% |
| Sharpe Ratio | Negative | Negative |
| Trades | 3,000+ | 500+ |

> ⚠️ Results show potential signal edge but weak risk-adjusted performance, highlighting realistic limitations and overfitting risks.

---

## Dataset

This project uses the **NIFTY 100 15-minute OHLCV dataset** from Kaggle.

🔗 **Dataset link:**  
https://www.kaggle.com/datasets/debashis74017/stock-market-data-nifty-100-stocks-15-min-data

> 📌 The dataset is **not included in this repository** due to size and licensing constraints.  
> Please download the CSV files from Kaggle and place them locally.

### Expected directory structure

Each CSV file must contain:
- `date`
- `open`
- `high`
- `low`
- `close`
- `volume`

---

## How to Run

1. Clone the repository
2. Download the dataset from the Kaggle link above
3. Place all CSV files inside a `data/` folder
4. Run the main notebook or script
5. Review generated metrics and logs

---

## Design Philosophy

- No lookahead bias
- Strict out-of-sample evaluation
- Risk-first portfolio construction
- Modular, readable research code
- Honest reporting of performance

---

## Disclaimer

This project is for **educational and research purposes only**.  
It does **not constitute financial advice**.  
Past performance does not guarantee future results.


