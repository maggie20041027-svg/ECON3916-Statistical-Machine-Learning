# Assignment 5: The Sovereign Risk Engine

**Regularization, Classification, and Model Evaluation for Macroeconomic Early Warning Systems**

Role: Quantitative Economist | Organization: IMF Global Financial Stability Division

---

## Project Overview

The IMF Global Financial Stability Division is building a next-generation Early Warning System (EWS) to identify emerging-market economies at elevated risk of economic crisis. The legacy model — a brute-force OLS regression on 30+ World Development Indicators — was suspected of catastrophic overfitting. This project diagnoses that failure, rebuilds the forecasting engine with Ridge and Lasso regularization, constructs a logistic regression crisis classifier, and evaluates it under realistic IMF operational constraints where missed crises cost ~$50B in contagion and false alarms deplete scarce mission capacity.

---

## Data

- **Source:** World Bank API (live download via `wbgapi`)
- **Window:** 2013–2019, collapsed to country-level means
- **Predictors:** 27 retained WDI indicators after dropping countries/indicators with >40% missingness
- **Sample:** 246 countries (172 train / 74 test) after a 70/30 split with `random_state=42`
- **Outcome (continuous):** `gdp_growth_pc` — average GDP per capita growth
- **Outcome (binary):** `crisis = 1` if `gdp_growth_pc < 0`, else 0
- **Crisis base rate:** 16.3%

---

## Methodology

### Phase 1: Regularization Rescue
- Diagnosed OLS overfitting via Train–Test R² gap and p/n ratio (0.157)
- Compared **OLS, RidgeCV, and LassoCV** with 5-fold cross-validation
- Computed the full **Lasso Path** to track which predictors enter the model first
- Identified `population_growth` as the first predictor to enter the active set

### Phase 2: Crisis Classifier
- Demonstrated the **Linear Probability Model (LPM)** failure: 17/74 test predictions fell below 0
- Fitted **Logistic Regression** on the Lasso-selected features, verifying all probabilities lie in [0, 1]
- Computed **odds ratios** ranked by absolute magnitude — `urban_pop` emerged as the strongest predictor (OR = 3.58)
- Side-by-side LPM vs. Sigmoid visualization with shaded impossible regions

### Phase 3: Operational Deployment
- Exposed the **accuracy paradox**: naive baseline hits 81.1% accuracy with 0% recall on crises
- Built confusion matrix and full classification report at τ = 0.5
- Plotted **ROC and Precision-Recall curves** (ROC-AUC = 0.824, PR-AUC = 0.628)
- Threshold sweep identifying the **capacity-constrained τ = 0.68** (≤5 missions/quarter) and **F1-optimal τ = 0.37**

### Phase 4: P.R.I.M.E. Framework (AI-Assisted Diagnostics)
- **Bootstrap stability** of the Lasso Path across 200 resamples — 8 stable predictors (>80% selection), 1 fragile (<30%)
- **Cost-sensitive threshold optimization** under FN = $50B, FP = $2M cost asymmetry

---

## Key Findings

| Model | λ* | Non-zero | Test R² | Test RMSE |
|-------|-----|----------|---------|-----------|
| OLS | — | 27 | 0.502 | 2.21 |
| Ridge | 104.81 | 27 | 0.368 | 2.49 |
| Lasso | 0.126 | 12 | 0.452 | 2.32 |

**Lasso** delivered the best balance of sparsity, interpretability, and out-of-sample performance — a 12-feature model the IMF can defend operationally.

### Threshold Tradeoff Summary

| Threshold | τ | FN | FP | Expected Cost |
|-----------|-----|-----|-----|----------------|
| Cost-minimizing | 0.04 | 0 | 38 | $0.08B |
| Capacity-constrained | 0.68 | 11 | 2 | $550B |
| F1-optimal | 0.37 | 6 | 4 | $300B |

The 5-mission capacity cap forces the IMF to miss roughly **4 out of 5 crises** — quantifying the institutional cost of staffing constraints at ~$550B per quarter in expected losses.

---

## Tech Stack

- **Python 3.10+**
- `wbgapi` — World Bank API client
- `scikit-learn` — `LinearRegression`, `RidgeCV`, `LassoCV`, `lasso_path`, `LogisticRegression`, `StandardScaler`, `train_test_split`, `confusion_matrix`, `roc_curve`, `precision_recall_curve`
- `pandas`, `numpy` — data manipulation
- `matplotlib`, `seaborn` — visualization
- `statsmodels` — statistical diagnostics
- **Platform:** Google Colab

---

## Repository Contents

- `Assignment_5_Sovereign_Risk_Engine.ipynb` — full notebook with all four phases
- `README.md` — this file

---

## How to Run

1. Open `Assignment_5_Sovereign_Risk_Engine.ipynb` in Google Colab
2. Run `Kernel > Restart & Run All`
3. Data downloads live from the World Bank API — no CSV uploads required
4. Total runtime: ~3–4 minutes (the bootstrap loop in Phase 4 is the slowest cell)

---

## Author

Maggie | ECON3916 Statistical Machine Learning
