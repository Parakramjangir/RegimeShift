# RegimeShift: Core Video Resources & Study Plan

## 📽️ Video Resources (Curated by Summer of Quant 2026)

### Phase 1: Understanding Hidden Markov Models (HMMs)
Watch these when you start building the regime classifier.

1. **Hidden Markov Model: Data Science Concepts** by ritvikmath (13 mins)
   - **Why**: Best whiteboard explanation of HMM intuition
   - **Topics**: Hidden states, transition matrices, emission distributions
   - **Link**: https://www.youtube.com/watch?v=kqSzLvKL62o
   - **Action**: Watch before implementing hmmlearn in Phase 3

2. **Hidden Markov Models for Quant Finance** by Roman Paolucci (56 mins)
   - **Why**: Deep dive specifically for quantitative finance and market regime prediction
   - **Topics**: HMMs applied to real market data, regime detection workflow
   - **Action**: Watch before fitting your HMM on multi-asset returns

### Phase 2: Convex Portfolio Optimization (CVXPY)
Watch these when you start defining regime-specific portfolio weights.

1. **Convex Optimization in Python with CVXPY** by Steven Diamond @ SciPy 2018 (29 mins)
   - **Why**: Taught by the co-creator of CVXPY itself
   - **Topics**: Objective functions, constraints, solving optimization problems
   - **Link**: https://www.youtube.com/watch?v=WulLv5Asmap
   - **Action**: Watch before Phase 5 (portfolio optimization)

2. **Portfolio Optimization in Python: Boost Your Financial Performance** by Ryan O'Connell, CFA, FRM (23 mins)
   - **Why**: Practical end-to-end workflow (yfinance → optimization → results)
   - **Topics**: Data download, weight solving, risk/return tradeoffs
   - **Action**: Watch for portfolio construction patterns

### Phase 3: ⚠️ Lookahead Bias & Backtesting (Critical!)
Watch these RIGHT BEFORE you build your walk-forward validation loop.

1. **Look-Ahead Bias - Future Data** by Quantra / Dr. Ernest P. Chan (3 mins)
   - **Why**: Quick, punchy explanation from a veteran quant (Ernie Chan)
   - **Topics**: How future data leaks into past training sets (the #1 killer of quant strategies)
   - **Action**: Watch before Phase 4 (walk-forward validation)

2. **3 Backtesting Pitfalls That Ruin Your Trading Strategy** by Roman Paolucci (14 mins)
   - **Why**: Breakdown of major pitfalls specific to Python backtesting
   - **Topics**: Data leakage, survivorship bias, improper train/test splits
   - **Link**: https://www.youtube.com/watch?v=gG7Z40RNsKU
   - **Action**: Watch before writing your backtesting loop

---

## 🎯 Viewing Strategy (Recommended Timeline)

| Phase | When to Watch | Duration | Videos |
|-------|---------------|----------|--------|
| **Phase 1-2: Data & Features** | After project notebook Sections 1-6 | — | *None yet* |
| **Phase 3: Regime Detection (HMM)** | Before implementing hmmlearn | 69 mins | ritvikmath (13m) + Paolucci HMM (56m) |
| **Phase 4: Walk-Forward Validation** | After Phase 3, before backtesting | 17 mins | Chan (3m) + Paolucci Pitfalls (14m) |
| **Phase 5: Portfolio Optimization** | Before cvxpy implementation | 52 mins | Diamond (29m) + O'Connell (23m) |
| **Phase 5: Backtesting Loop** | Right before final backtesting code | — | Re-watch Paolucci Pitfalls if needed |

---

## 💡 Key Takeaways by Topic

### Hidden Markov Models
- **What**: A model that infers "hidden" states (Bull/Bear/Crisis) from observable data (returns, volatility)
- **How**: Baum-Welch algorithm (EM-style) learns transition probabilities and emission distributions
- **In your project**: Use hmmlearn to fit on training features, then call `.predict()` (Viterbi) on test data

### Convex Optimization
- **What**: Mathematically solving for optimal portfolio weights given a risk/return objective
- **Why cvxpy**: Lets you express objectives (maximize Sharpe) and constraints (sum of weights = 1) declaratively
- **In your project**: Define different objectives per regime (max Sharpe in Bull, min volatility in Crisis)

### Lookahead Bias (The #1 Killer)
- **The trap**: Using statistics (mean, std) computed from the entire dataset to normalize features in the past
  - Example: z-scoring with full-sample mean/std makes early dates look artificially "calm" because future crashes are baked in
- **The fix**: Walk-forward validation where every number at time t is computed using only data ≤ t
- **In your project**: 
  - Re-fit HMM in each training fold
  - Compute feature scaling (z-score) using training data only
  - Never fit once on all data, then "predict" on early dates

---

## 🚨 Non-Negotiables (From Problem Statement & Videos)

1. **No lookahead bias** — Your regime label at day i must not use data from day i+1 or later
2. **Walk-forward validation** — Train on past, test on future, slide forward, repeat
3. **Re-fit HMM per fold** — Don't fit once and reuse; re-fit inside each training window
4. **Transaction costs** — Model 5-10 bps per rebalance; this alone can kill a strategy
5. **Multiple benchmarks** — Compare against 60/40 and equal-weight static portfolios

---

## 📌 If You Get Stuck

| Problem | Videos to Revisit |
|---------|-------------------|
| "What does the transition matrix tell me?" | ritvikmath (13m), Paolucci HMM (56m) |
| "How do I set up the cvxpy problem?" | Diamond (29m), O'Connell (23m) |
| "Am I leaking future data?" | Chan (3m), Paolucci Pitfalls (14m) |
| "My backtest looks too good..." | Paolucci Pitfalls (14m) — probably data leakage |
| "How do I structure walk-forward correctly?" | Project Guide Section 8 + Chan + Paolucci Pitfalls |

Good luck! 💪
