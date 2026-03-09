# Econ 3916 — Assignment 3: The Causal Architecture

**Module 3: Bootstrapping, Permutation & Causal Inference**  
**Role:** Senior Data Economist @ SwiftCart Logistics  
**Platform:** Google Colab

---

## Overview

This notebook investigates three contested business claims at SwiftCart Logistics using computation-heavy, assumption-free statistical methods. Rather than relying on fragile parametric assumptions, each phase builds empirical evidence from scratch using manual simulation engines.

---

## Dataset

| File | Description |
|------|-------------|
| `swiftcart_loyalty.csv` | 8,941 user records with pre-treatment order volume, account age, support tickets, and post-treatment spending |

---

## Structure

### Phase 1 — Bootstrapping Non-Parametric Uncertainty
**Problem:** The labor union is challenging SwiftCart's "Median Driver Compensation" claim. Tip data is zero-inflated and heavily right-skewed — the Central Limit Theorem is unreliable here.

- **Step 1.1** — Simulates 250 driver tips: 100 zeros + 150 exponential draws (`scale=5.0`)
- **Step 1.2** — Manual bootstrap engine: 10,000 resamples with replacement, extracts asymmetric 95% CI using `np.percentile`

> Key finding: The bootstrap CI is asymmetric `[0.27, 1.36]` — a parametric t-interval would incorrectly force symmetry on this skewed distribution.

---

### Phase 2 — Falsification in Logistics A/B Testing
**Problem:** Engineering claims the new "Batch Routing" algorithm significantly reduces delivery times. The treatment group contains extreme crash-loop outliers that violate the homoscedasticity assumption of a standard t-test.

- **Step 2.1** — Generates 1,000 synthetic deliveries: Control (`Normal`, mean=35, sd=5) vs Treatment (`LogNormal`, mean=3.4, σ=0.4)
- **Step 2.2** — Manual permutation test: 5,000 shuffles using `np.random.permutation`, computes exact empirical p-value

> Key finding: Permutation p-value = 0.0004 → Reject H0. The algorithm has a significant effect, confirmed without any distributional assumptions.

---

### Phase 3 — Causal Control & Mitigation of Selection Bias
**Problem:** Marketing claims SwiftPass subscribers spend 300% more. This is pure selection bias — power users self-select into the program.

- **Step 3.1** — Calculates the naive Simple Difference in Means (SDO = $17.57)
- **Step 3.2** — Propensity Score Matching (PSM):
  - Logistic Regression on pre-treatment covariates (`pre_spend`, `account_age`, `support_tickets`)
  - 1-Nearest Neighbor matching via `sklearn.neighbors.NearestNeighbors`
  - Computes ATT (Average Treatment Effect on the Treated)

> Key finding: ATT = $9.91 vs Naive SDO = $17.57. The $7.66 gap is pure selection bias. Marketing's claim collapses when comparing apples to apples.

---

### Phase 4 — AI Expansion: The Love Plot
**Problem:** Visually prove that PSM successfully eliminated selection bias across all covariates.

- Computes Standardized Mean Differences (SMD) before and after matching
- Generates a Love Plot showing covariate balance
- All after-matching SMDs fall within the `|0.1|` threshold

> Key finding: `pre_spend` SMD dropped from 0.67 → 0.01. All three covariates balanced. Selection bias successfully mitigated.

---

## Requirements

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.linear_model import LogisticRegression
from sklearn.neighbors import NearestNeighbors
```

---

## Key Results Summary

| Metric | Value |
|--------|-------|
| Bootstrap Median | $0.76 |
| Bootstrap 95% CI | [0.27, 1.36] — asymmetric |
| Permutation P-Value | 0.0004 |
| Naive SDO (Biased) | $17.57 |
| ATT (Causal Estimate) | $9.91 |
| pre_spend SMD (Before) | 0.674 |
| pre_spend SMD (After) | 0.014 |
