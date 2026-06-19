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
  point-in-time monthly snapshots from 2013 to present
- **Prices:** yfinance — historical prices for forward 
  return computation

## Methodology
- Linear regression via gradient descent (from scratch)
- Ridge regularisation
- Logistic regression via maximum likelihood (from scratch)
- K-fold cross-validation with error bars
- Honest train/validation/test evaluation

## Status
- [x] Phase 0 — Data sourcing
- [x] Phase 1 — Theory
- [x] Phase 2 — EDA & preprocessing
- [ ] Phase 3 — Linear regression
- [ ] Phase 4 — Evaluation harness
- [ ] Phase 5 — Logistic regression
- [ ] Phase 6 — Analysis
- [ ] Phase 7 — Writeup

## Author
Nakul — MPhys Physics with Astrophysics, Lancaster University

