# 📈 OU-Based Pairs Trading Backtester

This repository implements a **Pairs Trading strategy** leveraging a statistically consistent **Ornstein-Uhlenbeck (OU)** mean-reversion model. It includes:

- Spread construction via OLS hedge ratio
- Stationarity filtering (ADF tests)
- AR(1) → OU mean-reversion modeling
- Cost-aware signal thresholds
- Walk-forward backtesting with performance analytics

---

## 🎯 Strategy Overview

Pairs trading profits from **temporary divergence** between two historically related securities.

### Steps:
1️⃣ Estimate hedge ratio using log-price OLS  

2️⃣ Check **mean-reversion validity** using the Augmented Dickey-Fuller (ADF) test  
3️⃣ Fit **AR(1)** model to the spread  
4️⃣ Convert to OU parameters:

| OU Parameter | Meaning |
|------------|---------|
| μ | Equilibrium level |
| θ | Speed of mean reversion |
| Half-life | Expected time to revert halfway |
| σ | Long-run volatility |

Pairs are discarded if:
- Low correlation
- ADF p-value too high
- Half-life too large (slow convergence)

---

## ⚙️ Trade Signal Generation

Trade based on z-scores of spread deviation:

| Condition | Action |
|----------|--------|
| z > z_in | Short spread (short Y, long X) |
| z < −z_in | Long spread |
| | |
| \|z\| ≤ z_out | Exit position |

Thresholds adapt to both:
✅ Statistical floor (OU signal-to-noise)  
✅ Economic floor (transaction costs using β)

---

## 🔄 Supported Z-Score Modes

| Mode | Description |
|------|-------------|
| `ou_fixed` | OU parameters fixed from formation window |
| `rolling_plain` | Rolling mean / std |
| `rolling_ou` | Rolling AR(1) → OU re-fit |
| `ewma_ou` | EWMA using OU half-life decay |

---

## 🧪 Walk-Forward Backtesting

Performance is evaluated **only on out-of-sample future data**.

Metrics computed:
- CAGR (annualized return)
- Annualized volatility
- Sharpe ratio
- Max drawdown

---

## ▶️ Minimal Example

```python
pairs = select_pairs(px, formation=252)
results = backtest(px, pairs, formation=252, trading=126)
results.sort_values("sharpe", ascending=False)
```
📂 Project Structure

├── pairs_trading_ou.py   ← Full model & backtesting engine
└── README.md

✅ Requirements
numpy
pandas
statsmodels
yfinance   # optional
