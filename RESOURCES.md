# Resources

This file lists the main learning resources, libraries, tools, and data sources used in the RegimeShift project.

---

## Project Theme

**RegimeShift: Macro-Aware Tactical Asset Allocation Engine**

The project focuses on building a regime-aware portfolio allocation system that detects hidden market states and adjusts portfolio weights accordingly. The main concepts used are Hidden Markov Models, feature engineering, walk-forward validation, and convex portfolio optimization.

---

## Data Sources

### Yahoo Finance

Yahoo Finance was used as the primary data source for downloading historical daily market data through the `yfinance` Python library.

Used for:

* Equity price data
* Bond ETF price data
* Gold ETF price data
* Volatility index data

Python package:

```bash
pip install yfinance
```

Official package page:

```text
https://pypi.org/project/yfinance/
```

---

## Main Python Libraries

### NumPy

Used for numerical computation, arrays, matrix operations, and return calculations.

```bash
pip install numpy
```

Resource:

```text
https://numpy.org/doc/
```

---

### Pandas

Used for handling time-series data, aligning asset prices, computing returns, rolling features, and saving processed datasets.

```bash
pip install pandas
```

Resource:

```text
https://pandas.pydata.org/docs/
```

---

### Matplotlib

Used for visualizing price series, feature behavior, regime overlays, allocation changes, equity curves, and drawdowns.

```bash
pip install matplotlib
```

Resource:

```text
https://matplotlib.org/stable/users/index.html
```

---

### yfinance

Used to download historical asset price data from Yahoo Finance.

```bash
pip install yfinance
```

Resource:

```text
https://pypi.org/project/yfinance/
```

---

### hmmlearn

Used to build and train the Gaussian Hidden Markov Model for market regime detection.

```bash
pip install hmmlearn
```

Resource:

```text
https://hmmlearn.readthedocs.io/
```

---

### CVXPY

Used for convex portfolio optimization and regime-aware asset allocation.

```bash
pip install cvxpy
```

Resource:

```text
https://www.cvxpy.org/
```

---

### SciPy

Used for scientific computing and statistical calculations.

```bash
pip install scipy
```

Resource:

```text
https://docs.scipy.org/doc/scipy/
```

---

### scikit-learn

Used for preprocessing support and general machine learning utilities.

```bash
pip install scikit-learn
```

Resource:

```text
https://scikit-learn.org/stable/
```

---

## Key Concepts Studied

### Hidden Markov Models

Hidden Markov Models were used to infer unobserved market regimes from observable financial features such as returns, momentum, and volatility.

Important ideas:

* Hidden states
* Emission probabilities
* Transition probability matrix
* Viterbi algorithm
* Gaussian HMM
* Regime persistence

Useful references:

```text
https://hmmlearn.readthedocs.io/en/latest/
https://en.wikipedia.org/wiki/Hidden_Markov_model
```

---

### Market Regimes

The project classifies market behavior into three broad regimes:

| Regime | Interpretation                       |
| ------ | ------------------------------------ |
| Bull   | Positive or stable market conditions |
| Bear   | Weak or declining market conditions  |
| Crisis | High-volatility stress periods       |

The HMM produces numerical hidden states, which are later mapped to these economic labels based on volatility, momentum, and return characteristics.

---

### Feature Engineering

Feature engineering was used to convert raw price and return data into meaningful signals for regime detection.

Main feature groups:

* Daily log returns
* Rolling momentum
* Rolling volatility
* Market stress indicators
* Normalized features

Important methods:

```text
pct_change()
np.log().diff()
rolling().mean()
rolling().std()
shift()
```

Special attention was given to avoiding lookahead bias while creating features.

---

### Lookahead Bias

Lookahead bias occurs when a model uses information that would not have been available at the time of prediction. This is especially dangerous in financial backtesting because it can make a strategy appear unrealistically profitable.

Controls used in this project:

* Rolling features based only on past data
* Train-only normalization during walk-forward validation
* HMM retraining inside each walk-forward fold
* Out-of-sample regime prediction
* Next-day execution of generated portfolio weights

---

### Walk-Forward Validation

Walk-forward validation was used instead of random train-test splitting because financial time series must preserve chronological order.

The process followed:

1. Train the model on past data.
2. Test on the next unseen time window.
3. Move the training and testing windows forward.
4. Repeat the process.
5. Combine out-of-sample results.

This method gives a more realistic estimate of how the model would perform in real time.

---

### Portfolio Optimization

Portfolio optimization was performed using `cvxpy`. Different regimes used different allocation objectives.

General regime behavior:

| Regime | Allocation Style |
| ------ | ---------------- |
| Bull   | Growth-oriented  |
| Bear   | More defensive   |
| Crisis | Risk-minimizing  |

The optimization considered expected returns, covariance, and practical allocation constraints.

---

### Transaction Costs

Transaction costs were included to make the backtest more realistic. Costs were applied based on portfolio turnover whenever allocation weights changed.

Transaction cost range used:

```text
5–10 basis points per rebalance
```

This prevents the strategy from unrealistically assuming free trading.

---

## Performance Metrics

The following metrics were used to evaluate the dynamic strategy and benchmark portfolios:

| Metric                | Purpose                                           |
| --------------------- | ------------------------------------------------- |
| CAGR                  | Measures compounded annual growth                 |
| Annualized Volatility | Measures return variability                       |
| Sharpe Ratio          | Measures risk-adjusted return                     |
| Sortino Ratio         | Measures downside-risk-adjusted return            |
| Maximum Drawdown      | Measures worst peak-to-trough fall                |
| Calmar Ratio          | Compares return to drawdown risk                  |
| Turnover              | Measures frequency and size of allocation changes |

---

## Benchmark Portfolios

The regime-aware strategy was compared against static portfolios:

### 60/40 Portfolio

A traditional benchmark with higher equity exposure and defensive bond allocation.

### Equal-Weight Portfolio

A simple benchmark where all selected assets receive equal allocation.

These benchmarks help evaluate whether the dynamic regime-aware approach adds value over simpler allocation methods.

---

## Suggested Reading

### Quantitative Finance and Backtesting

```text
https://www.investopedia.com/terms/b/backtesting.asp
https://www.investopedia.com/terms/s/sharperatio.asp
https://www.investopedia.com/terms/m/maximum-drawdown-mdd.asp
```

### Portfolio Optimization

```text
https://www.cvxpy.org/examples/basic/quadratic_program.html
https://en.wikipedia.org/wiki/Modern_portfolio_theory
```

### Time-Series Validation

```text
https://scikit-learn.org/stable/modules/cross_validation.html#time-series-split
```

---

## Tools Used

| Tool             | Purpose                   |
| ---------------- | ------------------------- |
| Python           | Main programming language |
| Jupyter Notebook | Phase-wise implementation |
| VS Code          | Development environment   |
| Git              | Version control           |
| GitHub           | Repository hosting        |
| Yahoo Finance    | Market data source        |

---

## Notes

This project is a research-style implementation and is not intended as financial advice. The results depend on selected assets, historical data quality, feature choices, model assumptions, and backtesting design.

The main goal of the project is to demonstrate a complete quantitative workflow:

```text
data collection → feature engineering → regime detection → portfolio optimization → walk-forward backtesting → benchmark comparison
```
