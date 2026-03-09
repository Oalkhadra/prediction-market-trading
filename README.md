# Systematic Exploitation of Inefficient Prediction Markets

## Overview

Kalshi temperature markets consistently and systematically misprice the probability of maximum temperature outcomes. Analysis of 1,911 city-date observations reveals that market-implied uncertainty exceeds realized uncertainty by a factor of 1.27x — evidence of a structural inefficiency driven by retail-dominated order flow.

This strategy trades binary outcome contracts by generating calibrated probability estimates for each temperature bucket and comparing them against live market prices to identify mispriced positions across 10 U.S. cities.


## Live Trading Performance

The strategy has been deployed in its current state since 2026-02-22 (earlier versions have been trading since January).
(Will be updated soon...)

### Performance Summary

![Performance Summary](results/plots/performance_tab.png)

### Model Diagnostics

![Model Diagnostic](results/plots/diagnostics_tab.png)


## Backtest Results (Out-of-Sample)

**Period:** 2025-01-01 to 2026-02-20 | **Sizing:** $1 per trade | **Fees:** Kalshi fee formula + $0.02 spread assumption

| Metric | Value |
|--------|-------|
| Trades | 923 |
| Total Return | 38% |
| Daily Sharpe (ann.) | 3.16 |
| Max Drawdown | 8.1% |
| Win Rate | 33.9% |
| W/L Ratio | 3.01 |
| Profit Factor | 1.54 |
| Calmar Ratio | 10.28 |

### Equity Curve

![Cumulative P&L](results/plots/equity_curve.png)

### Model Calibration

The model's predicted probabilities align closely with empirical outcomes across 15,187 out-of-sample markets. When the model assigns probability *p* to a bucket, it resolves YES approximately *p* of the time, across all probability bins.

![Model Calibration](results/plots/calibration.png)

### Benchmark Comparison

Every naive and baseline strategy yields negative returns over the same period. The model outperforms the best profitable baseline by **2.8x P&L** and **2.3x Sharpe**. The best profitable baseline uses the same underlying model to adjust forecasts, but trades every available forecast without selectivity, demonstrating trade selection and mispricing identification as critical as the model itself.

| Strategy | Trades | Win% | Total P&L | Sharpe | Max DD |
|----------|--------|------|-----------|--------|--------|
| **This Strategy** | 923 | 33.9% | $350.57 | 3.16 | $-29.99 |
| Best Baseline | 2,549 | 47.6% | $125.17 | 1.37 | $-68.39 |
| Naive Forecast | 2,504 | 26.0% | $-772.24 | -6.49 | $-795.15 |
| Random | 4,143 | 49.1% | $-889.97 | -6.13 | $-895.92 |

![Benchmark Comparison](results/plots/benchmark_comparison.png)

### Parameter Robustness

A sweep of **66 parameter configurations** shows that **all are profitable**, with annualized Sharpe ratios ranging from 1.84 to 3.26. The chosen parameters rank #1 by composite score but sit in the center of the profitable region rather than on a boundary.

![Parameter Sensitivity](results/plots/parameter_sensitivity.png)

### Monte Carlo Simulation

25,000 forward paths simulated with conservative assumptions including win degradation (simulating edge decay and/or model degradation), daily exposure caps, and stop-loss triggers.

| Metric | Value |
|--------|-------|
| Median Sharpe (ann.) | 2.26 |
| Profitable Paths | 95.1% |
| Median Max Drawdown | 14.3% |

![Monte Carlo Simulation](results/plots/monte_carlo_equity.png)

![Monte Carlo Distributions](results/plots/monte_carlo_distributions.png)

## Limitations

- **Liquidity:** Kalshi temperature market volume ($10K–$150K per city-day) constrains scalability, especially for newer cities.
- **Edge decay:** As markets attract more informed participants, the mispricings will likely compress.
- **Execution:** Backtest uses historical ask prices + spread; live limit order fills may differ in thinner markets.

## Scalability

The strategy scales horizontally as Kalshi adds cities. New cities plug directly into the existing pipeline with no architectural changes. Minimum temperature markets (being added) and other prediction market platforms could further expand the opportunity set.

## Tech Stack

Python · XGBoost · Kalshi API · BigQuery · VPS

---

*Source code is private. For methodology details or questions, feel free to reach out.*
