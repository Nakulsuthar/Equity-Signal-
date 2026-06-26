# Equity-Signal-
Measuring predictable signal in cross-sectional equity returns from company fundamentals

Measuring predictable signal in cross-sectional equity returns 
from company fundamentals using linear and logistic regression 
built from scratch.

## Research Questions
1. Can a linear model predict relative forward returns from 
   company fundamentals, to a degree distinguishable from noise?
2. Which fundamentals carry the most signal?
3. Does framing the task as classification outperform regression?

## Data
- **Fundamentals:** SEC XBRL dataset (vladosht, Kaggle) — 
  point-in-time monthly snapshots from 2013 to present, sliced to
  yearly (January) cross-sections to avoid treating correlated
  monthly filings as independent observations
- **Prices:** yfinance — historical prices for forward 
  return computation, joined on ticker
- **Target:** 12-month forward excess return vs. SPY, winsorized
  at the 1st/99th percentile

## Methodology
- Six fundamental ratios engineered from scratch: ROE, ROA,
  GrossMargin, DebtToEquity, AssetTurnover, EPS
- Linear regression via gradient descent and the Normal Equation
  (from scratch), cross-checked against each other
- Ridge regularisation, with λ selected via validation R²
- Logistic regression via maximum likelihood (from scratch),
  binary target = whether excess return > 0
- Time-based train/validation/test split (train ≤2020, val
  2021–2022, test 2023–2025) — no random shuffling, to respect
  the temporal structure of the data and avoid lookahead bias
- Evaluation via R², per-year R² breakdown, and Information
  Coefficient (Spearman rank correlation, per year)
- Honest train/validation/test evaluation — test set touched
  exactly once, after all modelling decisions were finalised

## Status
- [x] Phase 0 — Data sourcing
- [x] Phase 1 — Theory
- [x] Phase 2 — EDA & preprocessing
- [x] Phase 3 — Linear regression
- [x] Phase 4 — Evaluation harness
- [x] Phase 5 — Logistic regression
- [x] Phase 6 — Ridge regression
- [x] Final test set evaluation
- [x] Findings writeup

## Findings

**Short answer: barely, and what little exists doesn't hold up reliably out-of-sample.**

### Linear Regression (predicting return magnitude)

A linear model on the six fundamental ratios was fit via both gradient
descent and the Normal Equation — the two converged to the same solution
(gradient norm ~1e-16, matching thetas to 4+ decimal places), confirming
the optimisation itself is correct.

| Split | R² | Mean IC | IC std |
|---|---|---|---|
| Train (2013–2020) | 0.88% | 0.089 | 0.080 |
| Val (2021–2022) | 0.01% | -0.031 | 0.073 |
| Test (2023–2025) | 0.77% | 0.018 | 0.044 |

The signal is real but unstable: per-year R² and IC both swing in sign
across periods, with no clean trend tying the swings to anything other
than broad macro regime (e.g. 2022's significant negative IC coincides
with the rate-driven bear market, where company fundamentals were
swamped by macro factors entirely outside the feature set).

### Ridge Regression

Swept λ from 0 to 1000. Regularisation barely changed the learned
coefficients (theta norm shrank by under 15% even at λ=1000) and did
not meaningfully improve validation R² — both numbers stayed within
noise of zero. The weak generalisation isn't caused by unstable,
overfit coefficients; they were already small and stable. There's
simply not much linear signal to regularise away.

### Logistic Regression (predicting direction only)

Reframed as binary classification (did the company beat the market,
yes/no) to test whether direction is easier to predict than magnitude.

| Split | Accuracy | Majority baseline | Difference |
|---|---|---|---|
| Train | 53.94% | 53.95% | -0.01 pp |
| Val | 55.55% | 58.20% | -2.65 pp |
| Test | 63.19% | 67.96% | -4.77 pp |

The classification framing did not help — it underperformed the trivial
"always guess the majority outcome" baseline on every split, more
severely on val and test than on train.

### Overall conclusion

Across three years of out-of-sample test data and two more validation
years, six static fundamental ratios show, at most, a weak and
regime-dependent relationship with 12-month forward excess returns.
The result is consistent with the efficient market hypothesis: a
simple, fully public signal of this kind would likely already be
priced in by the market, leaving little to exploit. `GrossMargin`,
`AssetTurnover`, and `ROA` were the most consistent positive
contributors across both models; `EPS` was consistently negative —
a small but genuine point of agreement between two independently
trained models.

### Limitations / honest caveats

- Ticker coverage in the source data improved from ~31% to ~80% over
  the sample period, meaning the early-period universe is smaller
  and skews toward larger companies.
- Time-series cross-validation (k-fold with error bars) was scoped
  but not implemented; per-year IC/R² breakdowns were used instead,
  which respect temporal ordering and arguably give more diagnostic
  detail than a single averaged error bar would.
- Features are static (single-snapshot) ratios; no momentum,
  earnings-surprise, or valuation (price-based) features were
  included — a natural extension for future work, especially since
  price data is already available in this pipeline.

## Author
Nakul — MPhys Physics with Astrophysics, Lancaster University