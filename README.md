# Multi-Asset Bootstrap Monte Carlo Portfolio Simulator

A quantitative finance tool that performs a **Multi-Asset Bootstrap Monte Carlo simulation** to forecast future portfolio trajectories. Instead of assuming a normal distribution or calculating a parametric covariance matrix, this model directly samples multi-asset historical log return rows simultaneously. This approach inherently preserves historical correlation structures and empirical asset anomalies (such as fat tails and skewness).

## Core Features

*   **Empirical Asset Correlations:** Extracts real-world asset correlations by slicing multi-asset historical return rows as unified vectors during bootstrapping.
*   **Non-Parametric Projections:** Bypasses theoretical distribution assumptions (μ, σ) to project asset trajectories using raw historical market data.
*   **Portfolio Risk Modeling:** Computes advanced downside tail-risk benchmarks, including **Value at Risk (VaR)** and **Expected Shortfall / Conditional VaR (CVaR)** at customizable confidence percentiles.
*   **Inflation & Friction Controls:** Features an annualized inflation discount formula alongside a built-in **1.01% transaction fee cushion** (`* 0.9899`) to pinpoint real, net profit probabilities.
*   **Visual Portfolio Path Tracking:** Dynamically charts all simulated cash trajectories over time using Matplotlib, mapping the starting capital step-by-step through to day T.

## Getting Started

### Prerequisites

Ensure you have the required statistical, financial, and graphing libraries installed:

```bash
pip install numpy pandas yfinance matplotlib
```

### Usage Example

```python
from datetime import datetime
# Import or paste the function here

# Run a bootstrap simulation for a 60/40 Apple & Microsoft portfolio worth $50,000
bootstrap_monte_carlo_simulation(
    portfolio_worth=50000,
    stocks=["AAPL", "MSFT"],
    weights=[0.6, 0.4],
    sim=1000,
    VaR=95
)
```

## Function Arguments & Parameters

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `portfolio_worth` | `float` | `10000` | Starting dollar capital allocation value used to purchase relative stock shares. |
| `sim` | `int` | `1000` | The total number of randomized bootstrap portfolio paths to generate. |
| `start` | `str` | `'2020-01-01'` | Start date boundary for pulling historical historical returns (`YYYY-MM-DD`). |
| `end` | `str` | *Dynamic (Today)* | End date boundary for historical asset tracking. Defaults to the current active date. |
| `stocks` | `list` | `["AAPL", "MSFT"]` | Array of yFinance asset ticker symbols composing the active portfolio. |
| `weights` | `list` | `[0.5, 0.5]` | Capital weight allocation per asset. Targets should scale relative to `1.0`. |
| `days_simulated` | `int` | `252` | Trading days into the future to forecast (252 days ≈ 1 calendar trading year). |
| `value_for_probability_calculation` | `float` | `50` | Targeted portfolio percentage growth threshold to evaluate success probability. |
| `inflation_rate` | `float` | `2` | Annualized inflation percentage applied to erode future expected portfolio values. |
| `VaR` | `float` | `95` | Confidence percentile parameter used to extract VaR and CVaR tail metrics. |
| `best_outcome_percent` | `float` | `5` | The top percentile target window used to measure right-tail market outliers. |

## Terminal Outputs Explained

When executed, the script processes your multi-asset matrix and logs percentage performance relative to your initial capital:

*   **Median:** The exact 50th percentile checkpoint. Half of all simulated portfolio endings finished above this percentage, half below.
*   **Expected Price:** The basic nominal arithmetic mean return profile across all randomized market paths.
*   **Real Expected Price:** The portfolio's true future compound return adjusted for the purchasing power degradation of your `inflation_rate`.
*   **Value at Risk (VaR):** The percentage loss boundary marking your worst outcomes within your confidence limit (e.g., at 95% VaR, there is a 5% chance your portfolio losses exceed this percentage).
*   **Expected Shortfall (CVaR):** The expected average loss percentage *if* the portfolio breaches your specified VaR threshold into the extreme left tail.
*   **Probability of Profit:** The statistical likelihood that your portfolio finishes above its starting allocation, net of trading fees.
