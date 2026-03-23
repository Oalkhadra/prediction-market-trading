# Systematic Exploitation of Inefficient Prediction Markets

## Overview

Kalshi temperature markets consistently and systematically misprice the forecast uncertainty of maximum temperature outcomes. Analysis of 1,911 city-date observations reveals that market-implied uncertainty exceeds realized uncertainty by a factor of 1.27x.

This strategy trades binary outcome contracts by generating calibrated probability estimates for each temperature bucket and comparing them against live market prices to identify mispriced positions across 10 U.S. cities. The probability model: A boosted decision tree, trained on 23 features derived from multiple independent weather data sources and Kalshi market microstructure, predicts the forecast error distribution for each city-date. 


## Live Trading Performance

The strategy has been deployed in its current state since 2026-02-22 (earlier versions have been trading since January).

### Performance Summary (As of 2026-03-22)

![Performance Summary](results/plots/performance_tab.png)

### Model Diagnostics

![Model Diagnostic](results/plots/diagnostics_tab.png)


## Backtest Results (Out-of-Sample)

**Period:** 2025-01-01 to 2026-02-20 | **Sizing:** $1 per trade | **Fees:** Kalshi fee formula + $0.02 spread assumption

| Metric | Value |
|--------|-------|
| Trades | 1179 |
| Total Return | 34% |
| Daily Sharpe (ann.) | 3.1 |
| Max Drawdown | 6.8% |
| Win Rate | 30.6% |
| W/L Ratio | 3.3 |
| Profit Factor | 1.46 |
| Calmar Ratio | 12.09 |

### Equity Curve

![Cumulative P&L](results/plots/equity_curve.png)

### Model Calibration

The model's predicted probabilities align closely with empirical outcomes across 16,681 out-of-sample markets. When the model assigns probability *p* to a bucket, it resolves YES approximately *p* of the time, across all probability bins.

![Model Calibration](results/plots/calibration.png)

### Benchmark Comparison

Every naive and baseline strategy yields negative returns over the same period. The model outperforms the best profitable baseline by **2.0x P&L** and **1.7x Sharpe**. The best profitable baseline uses the same underlying model to adjust forecasts, but trades every available forecast without selectivity, demonstrating trade selection and mispricing identification as critical as the model itself.

| Strategy | Trades | Win% | Total P&L | Sharpe | Max DD |
|----------|--------|------|-----------|--------|--------|
| **This Strategy** | 1,179 | 30.6% | $396.1 | 3.1 | $-28.83 |
| Best Baseline | 2,963 | 47.9% | $191.2 | 1.8 | $-41.82 |
| Naive Forecast | 2,901 | 26.1% | $-865.1 | -6.76 | $-890.17 |
| Random | 4,154 | 49.8% | $-875.22 | -6.2 | $-909.06 |

![Benchmark Comparison](results/plots/benchmark_comparison.png)

### Parameter Robustness

A sweep of **66 parameter configurations** shows that **all are profitable**, with annualized Sharpe ratios ranging from 1.84 to 3.08. The chosen parameters rank #1 by composite score but sit in the center of the profitable region rather than on a boundary.

![Parameter Sensitivity](results/plots/parameter_sensitivity.png)

### Monte Carlo Simulation

25,000 forward paths simulated with conservative assumptions including win degradation (5% flip probability, simulating edge decay and/or model degradation), daily exposure caps, and stop-loss triggers.

| Metric | Value |
|--------|-------|
| Median Sharpe (ann.) | 2.7 |
| Profitable Paths | 96.3% |
| Mean Max Drawdown | 16.1% |
| Stop Loss Hit | 3.7% |

![Monte Carlo Simulation](results/plots/monte_carlo_equity.png)

![Monte Carlo Distributions](results/plots/monte_carlo_distributions.png)

## Limitations

- **Time Shifts:** Daylight Savings Time shifts the NWS observation window by one hour, which mainly affects early spring, as the maximum temperature can be observed near midnight. The current backtest does not account for this.
- **Liquidity:** Kalshi temperature market volume, ranging between $10K–$500K per city per day (variable), constrains scalability, especially for newer cities.
- **Edge decay:** As markets attract more informed participants, the mispricings will likely compress.
- **Execution:** Backtest uses historical ask prices + spread; live limit order fills may differ in thinner markets.

## Scalability

The strategy scales horizontally as Kalshi adds cities. New cities plug directly into the existing pipeline with no architectural changes. Minimum temperature markets and other prediction market platforms could further expand the opportunity set. Polymarket currently has 82 cities, while Kalshi only hosts 21. Although, Polymarket is still unavailable in the U.S. for these markets.

## Tech Stack

Python · XGBoost · Kalshi API · BigQuery · VPS

---

*Source code is private due to live deployment; happy to walk through the full methodology in a technical discussion.*
