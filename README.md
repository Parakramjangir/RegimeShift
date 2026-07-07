# RegimeShift: Macro-Aware Tactical Asset Allocation Engine

## Overview

RegimeShift is a market-regime-aware portfolio allocation project developed for the **Summer of Quant 2026 Advanced Track**. The objective of this project is to detect hidden market regimes using a Hidden Markov Model and use those regimes to dynamically rebalance a multi-asset portfolio.

Traditional static portfolios, such as 60/40 equity-bond allocations, do not adapt when market conditions change. This project attempts to build a more responsive allocation system that identifies whether the market is in a **Bull**, **Bear**, or **Crisis** regime and adjusts portfolio weights accordingly.

The complete pipeline covers:

* Market data collection
* Feature engineering
* Hidden Markov Model based regime detection
* Portfolio optimization using convex optimization
* Walk-forward validation
* Transaction-cost-aware backtesting
* Benchmark comparison against static portfolios

---

## Project Objective

The main goal is to build a realistic tactical asset allocation system that:

1. Detects hidden market regimes from financial time-series data.
2. Maps each regime to an economically meaningful label: Bull, Bear, or Crisis.
3. Uses regime-specific portfolio optimization to determine asset weights.
4. Tests the strategy using walk-forward validation to reduce lookahead bias.
5. Compares the dynamic strategy against static benchmark portfolios.

---

## Repository Structure

```text
RegimeShift/
│
├── data/
│   ├── asset_prices.csv
│   ├── master_returns.csv
│   ├── features_raw.csv
│   ├── features_all.csv
│   └── features_normalized.csv
│
├── outputs/
│   ├── regimes.csv
│   ├── transition_matrix.csv
│   ├── hmm_model.pkl
│   ├── allocations_regime_aware.csv
│   ├── allocations_benchmarks.csv
│   ├── portfolio_returns.csv
│   ├── portfolio_performance_summary.csv
│   ├── oos_regimes_walkforward.csv
│   ├── oos_target_weights.csv
│   ├── oos_execution_weights.csv
│   ├── oos_dynamic_returns.csv
│   ├── oos_bench_6040_returns.csv
│   ├── oos_bench_equalweight_returns.csv
│   ├── oos_performance_summary.csv
│   └── oos_transition_matrix_avg.csv
│
├── notebooks/
│   ├── phase_01_data.ipynb
│   ├── phase_02_features.ipynb
│   ├── phase_03_hmm.ipynb
│   ├── phase_04_portfolio.ipynb
│   └── phase_05_backtest.ipynb
│
├── README.md
├── RESOURCES.md
├── requirements.txt
└── .gitignore
```

---

## Methodology

### Phase 1: Data Collection and Preprocessing

Daily market data was collected using `yfinance` for assets representing different asset classes such as equity, bonds, gold, and volatility indicators. The raw price data was aligned on a common trading-date index to ensure consistency across all assets.

The adjusted price series were converted into log returns, which are commonly used in quantitative finance due to their additive properties over time. Missing values were inspected and handled carefully before constructing the final master returns dataset.

The output of this phase is a clean and aligned dataset that forms the foundation for feature engineering, regime classification, and portfolio backtesting.

---

### Phase 2: Feature Engineering

The second phase focused on creating features that describe the current state of the market. Since the market regime is hidden and cannot be directly observed, the quality of the input features is critical for the performance of the HMM.

The feature set includes:

* Momentum features over multiple rolling windows
* Volatility features using rolling standard deviation of returns
* Market stress indicators
* Normalized versions of selected features

Momentum features help capture whether the market is trending upward or downward, while volatility features help identify periods of uncertainty and stress. Rolling-window calculations were used so that features at any point in time only depend on information available up to that date.

---

### Phase 3: Hidden Markov Model Regime Detection

A Gaussian Hidden Markov Model was used to identify three hidden market states. The model was trained on the engineered feature set and produced numerical state labels.

Since HMM states are not automatically named, the states were interpreted using their statistical properties. The state with the highest volatility was labeled as the **Crisis** regime. The remaining states were separated using momentum and return characteristics and labeled as **Bull** and **Bear** regimes.

The final output of this phase includes:

* Regime labels for historical dates
* HMM transition probability matrix
* Saved trained HMM model
* Regime visualization over market price data

The transition matrix provides insight into regime persistence and the probability of switching from one regime to another.

---

### Phase 4: Regime-Aware Portfolio Optimization

After detecting market regimes, the next step was to connect each regime to portfolio allocation decisions. A separate optimization logic was used for each market regime.

The general allocation behavior is:

| Regime | Portfolio Objective                     |
| ------ | --------------------------------------- |
| Bull   | Growth-oriented allocation              |
| Bear   | Balanced and defensive allocation       |
| Crisis | Risk reduction and capital preservation |

Portfolio weights were computed using `cvxpy`, subject to practical constraints. This allowed the portfolio to shift dynamically depending on the detected regime instead of remaining fixed throughout the backtest period.

The project also includes static benchmark allocations for comparison, including:

* 60/40 benchmark portfolio
* Equal-weight benchmark portfolio

---

### Phase 5: Walk-Forward Backtesting

The final phase implemented walk-forward validation and out-of-sample backtesting. This is one of the most important parts of the project because financial models are highly vulnerable to lookahead bias.

Instead of training the HMM once on the full dataset, the model was repeatedly trained only on past data and tested on the next unseen time window. For each fold:

1. Training data was selected from the past.
2. Feature normalization was fitted only on the training data.
3. The HMM was trained only on the training window.
4. Regimes were predicted on the next test window.
5. Portfolio weights were assigned based on predicted regimes.
6. Strategy performance was evaluated out-of-sample.

To make the backtest more realistic, execution weights were shifted by one trading day. This ensures that a regime detected at the end of day `t` is only used for trading from day `t+1`.

Transaction costs were also included whenever portfolio weights changed.

---

## Backtesting Assumptions

The backtest uses the following assumptions:

* Daily rebalancing decisions based on detected regimes
* Transaction cost applied on portfolio turnover
* Walk-forward model retraining
* No random train-test splitting
* No full-sample normalization during out-of-sample evaluation
* Next-day execution of generated allocation signals

These assumptions were used to make the strategy evaluation more realistic and to reduce the possibility of lookahead bias.

---

## Performance Metrics

The strategy and benchmarks were evaluated using the following metrics:

| Metric           | Description                                           |
| ---------------- | ----------------------------------------------------- |
| CAGR             | Compound Annual Growth Rate                           |
| Volatility       | Annualized standard deviation of returns              |
| Sharpe Ratio     | Risk-adjusted return using total volatility           |
| Sortino Ratio    | Downside-risk-adjusted return                         |
| Maximum Drawdown | Largest peak-to-trough decline                        |
| Calmar Ratio     | CAGR divided by maximum drawdown                      |
| Turnover         | Measures how frequently the portfolio changes weights |

The dynamic regime-aware strategy was compared against the static 60/40 and equal-weight benchmarks using these metrics.

---

## How to Run the Project

### 1. Clone the repository

```bash
git clone https://github.com/Parakramjangir/RegimeShift.git
cd RegimeShift
```

### 2. Create and activate a virtual environment

Using Conda:

```bash
conda create -n regimeshift python=3.10
conda activate regimeshift
```

Or using Python venv:

```bash
python -m venv venv
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run notebooks in order

Run the notebooks in the following sequence:

```text
notebooks/phase_01_data.ipynb
notebooks/phase_02_features.ipynb
notebooks/phase_03_hmm.ipynb
notebooks/phase_04_portfolio.ipynb
notebooks/phase_05_backtest.ipynb
```

Each phase depends on outputs generated by the previous phases.

---

## Main Libraries Used

* `numpy`
* `pandas`
* `matplotlib`
* `yfinance`
* `hmmlearn`
* `cvxpy`
* `scipy`
* `scikit-learn`

---

## Key Outputs

The important final outputs are stored in the `outputs/` folder:

| File                                    | Description                                      |
| --------------------------------------- | ------------------------------------------------ |
| `regimes.csv`                           | Historical regime labels                         |
| `transition_matrix.csv`                 | HMM transition probability matrix                |
| `hmm_model.pkl`                         | Saved trained HMM model                          |
| `allocations_regime_aware.csv`          | Regime-aware portfolio weights                   |
| `allocations_benchmarks.csv`            | Static benchmark allocations                     |
| `portfolio_returns.csv`                 | Portfolio return series                          |
| `portfolio_performance_summary.csv`     | Initial strategy performance summary             |
| `oos_regimes_walkforward.csv`           | Walk-forward out-of-sample regime labels         |
| `oos_target_weights.csv`                | Strategy weights generated from regime signals   |
| `oos_execution_weights.csv`             | Next-day executable portfolio weights            |
| `oos_dynamic_returns.csv`               | Out-of-sample dynamic strategy returns           |
| `oos_bench_6040_returns.csv`            | Out-of-sample 60/40 benchmark returns            |
| `oos_bench_equalweight_returns.csv`     | Out-of-sample equal-weight benchmark returns     |
| `oos_performance_summary.csv`           | Final out-of-sample performance comparison       |
| `oos_transition_matrix_avg.csv`         | Average transition matrix from walk-forward HMMs |

---

## Lookahead Bias Controls

Several steps were taken to reduce lookahead bias:

1. Rolling features were computed using only past data.
2. Feature normalization during walk-forward validation was fitted only on training data.
3. The HMM was retrained separately in each walk-forward fold.
4. Out-of-sample regimes were predicted only after training on past data.
5. Portfolio execution weights were shifted by one day.
6. Strategy performance was evaluated only on out-of-sample periods.

These controls are central to the project because a financial backtest can look artificially strong if future information is accidentally used during model training, feature scaling, or strategy execution.

---

## Limitations

This project is a simplified research implementation and has some limitations:

* The strategy relies on historical daily data and does not include intraday execution effects.
* Asset choices are based on Yahoo Finance availability.
* Transaction cost modeling is simplified.
* The HMM assumes Gaussian emissions, which may not fully capture fat-tailed financial returns.
* Regime labels are interpreted statistically and economically, but they are not directly observed ground truth labels.
* Real-world implementation would require more robust risk controls, slippage modeling, and live data handling.

---

## Conclusion

RegimeShift demonstrates a complete regime-aware tactical asset allocation pipeline. The project combines Hidden Markov Models, feature engineering, convex portfolio optimization, and walk-forward validation to test whether a dynamic allocation strategy can adapt to changing market conditions.

The final system detects Bull, Bear, and Crisis regimes and uses them to drive portfolio allocation decisions. Its performance is evaluated against static benchmark portfolios using standard financial metrics, with transaction costs and out-of-sample testing included for a more realistic assessment.
