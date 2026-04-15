

# Trader Performance vs Market Sentiment — Primetrade.ai Intern Assignment

## Overview

This repository analyses how Bitcoin market sentiment (Fear/Greed Index) correlates with trader behaviour and performance on the Hyperliquid DEX. The goal is to surface actionable trading strategy rules from historical data.

---

## Setup & How to Run

### Requirements
```
pandas >= 2.0
numpy >= 1.24
matplotlib >= 3.7
seaborn >= 0.12
scikit-learn >= 1.3
jupyter >= 1.0
```

Install all dependencies:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### Data
Place both CSV files in the project root:
- `fear_greed_index.csv` — columns: `date`, `classification`
- `historical_data.csv` — columns: `account`, `symbol`, `execution_px`, `size_usd`, `side`, `timestamp_ist`, `closed_pnl`, `leverage`, etc.

### Run
```bash
jupyter notebook analysis_primetrade.ipynb
```
Run all cells top to bottom (Kernel → Restart & Run All).

---

## Methodology

### Part A — Data Preparation
1. Loaded both datasets; documented shape, missing values, and duplicates.
2. Standardised column names; parsed timestamps; extracted daily date key.
3. Left-joined trades ← sentiment on `date` (0 unmatched rows).
4. Engineered features: `win` flag, `is_long` flag, `lev_tier`, `activity`, `profit_label`, day-of-week.

### Part B — Analysis

**Q1: Does performance differ between Fear and Greed days?**
- **Yes, dramatically.** Mean PnL during Fear = **$-45.67** vs Greed = **$-5.69** (8× gap).
- Win rate: Fear = 46.8%, Greed = 51.6% (+4.7 percentage points).
- Total net PnL: Fear = **−$2.6M**, Greed = **−$222k** despite more trading days in Greed.

**Q2: Do traders change behaviour by sentiment?**
- **Trade frequency:** 45% more trades placed during Fear vs Greed — consistent with panic/revenge trading.
- **Leverage:** Marginally higher during Fear (14.1× vs 13.2×) — traders increase risk when they should reduce it.
- **Direction:** Strong long bias during Greed (55% long) vs near-neutral during Fear (45% long).
- **Position size:** Slightly larger during Fear ($6,414 avg vs $5,843 in Greed).

**Q3: Trader Segments**

| Segment | Fear Mean PnL | Greed Mean PnL | Key Finding |
|---------|-------------|--------------|-------------|
| High-Leverage Traders | −$78 | −$28 | Leverage amplifies losses 3× during Fear |
| Low-Leverage Traders | −$4.83 | +$8.92 | Nearly breakeven during Fear; profitable in Greed |
| Frequent Traders | −$51 | −$8 | Overtrade during Fear, destroying value |
| Infrequent Traders | −$7 | +$4 | More disciplined, outperform in both regimes |
| Consistent Winners | −$0.43 | +$11.18 | Low leverage (4×), high win rate, small size |
| Consistent Losers | −$86 | −$39 | High leverage (17×), lose regardless of sentiment |

### Part C — Strategy Recommendations

**Rule 1 — Sentiment-Gated Leverage Cap**
> During Fear days, cap all account leverage at **5×**. Allow normal limits during Greed.

*Rationale:* Low-leverage traders are near-breakeven during Fear (−$4.83/trade); high-leverage traders lose $78/trade. Capping leverage during Fear removes the primary loss amplifier.

**Rule 2 — Conviction-First Trade Throttle**
> During Fear days, limit accounts to **≤3 new positions per day** and enforce a **4-hour minimum hold time** before closing.

*Rationale:* Frequent traders place 45% more trades during Fear with a lower win rate. Throttling prevents panic/revenge trading patterns that compound losses.

---

## Bonus — Predictive Model

A **Random Forest classifier** predicts next-day profitability (profitable vs unprofitable) using:
- Today's win rate, average leverage, trade count, long ratio, position size
- Market sentiment (binary: Fear=0, Greed=1)

**Results:** ~95% test accuracy, 0.95 macro F1.  
**Sentiment is the 2nd most important feature**, confirming it has significant predictive power beyond simple correlation.

---

## File Structure

```
├── analysis_primetrade.ipynb   ← Main notebook (all outputs pre-rendered)
├── README.md                   ← This file
├── fear_greed_index.csv        ← Sentiment dataset (add manually)
└── historical_data.csv         ← Trades dataset (add manually)
```

---

*Submitted for Primetrade.ai Data Science / Analytics Intern — Round 0*
