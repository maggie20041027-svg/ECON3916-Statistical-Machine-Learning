# Causal ML — Double Machine Learning for 401(k) Policy Evaluation

Estimating the causal effect of 401(k) eligibility on household net financial assets using Double Machine Learning (Chernozhukov et al., 2018), with heterogeneity analysis by income quartile and sensitivity testing for unobserved confounding.

## Objective

Quantify how much of the observed savings gap between 401(k)-eligible and ineligible households is *causal* rather than reflecting selection on income, education, and firm characteristics — and test how robust that causal estimate is to plausible omitted variables.

## Methodology

- **Data:** Chernozhukov–Hansen 401(k) pension-plan sample (n = 9,915), retrieved via `doubleml.datasets.fetch_401K`.
- **Framework:** Partially Linear Regression (PLR) via `DoubleMLPLR`, which residualizes the outcome and treatment on the covariate vector before estimating the causal coefficient — isolating the treatment parameter from the regularization bias that would contaminate a naïve ML fit.
- **Regularization-bias demonstration:** Simulated DGP with known ATE = 5.0 and 100 covariates, showing that naïve LASSO shrinks the treatment coefficient as part of the global penalty. This motivates why DML is needed.
- **Nuisance learners:** Random Forest regressors (`n_estimators=200`, `max_depth=5`, `random_state=42`) for both E[Y | X] and E[D | X].
- **Cross-fitting:** 5-fold, ensuring nuisance predictions are always out-of-sample at the score-construction step.
- **Confounders (9):** `age`, `inc`, `educ`, `fsize`, `marr`, `twoearn`, `db`, `pira`, `hown`. Post-treatment variables (401(k) participation, non-401(k) financial assets, total wealth) are deliberately excluded to avoid blocking the causal pathway or inducing collider bias.
- **CATE:** DML refit separately within each income quartile to estimate heterogeneous effects.
- **Robustness:** `sensitivity_analysis(cf_y=0.03, cf_d=0.03)` bounds how much an unobserved confounder could distort the point estimate and confidence interval.

## Key Findings

- **Average Treatment Effect:** 401(k) eligibility raises net financial assets by **\$8,281** (95% CI: \$5,677 – \$10,886, p < 10⁻⁹). The effect is large, precise, and consistent with the published Chernozhukov–Hansen literature.
- **Heterogeneity by income:** CATE estimates rise sharply in the top quartile — Q1 \$4,365, Q2 \$3,391, Q3 \$6,537, Q4 **\$16,703**. All four quartile CIs exclude zero, but Q4's effect is roughly 4× larger than Q1–Q2's. This is consistent with three reinforcing mechanisms: (1) higher marginal tax rates make the contribution deduction more valuable, (2) higher disposable income enables households to actually approach contribution limits, and (3) higher-paying firms are more likely to offer generous employer matches.
- **Robustness:** Robustness Value = 6.49%, adjusted RV = 4.72%. An unobserved confounder would need to explain more than 6.5% of both outcome and treatment variance to drive the point estimate to zero — a high bar given that no single observed covariate approaches this level of residual explanatory power.
- **Policy implication:** A pure-efficiency program would target Q4, but equity considerations justify complementary interventions (automatic enrollment, saver's credit, employer-match subsidies for low-wage firms) aimed at relaxing the liquidity constraints that suppress Q1–Q2 effects.

## Repository Structure

```
econ-lab-24-double-ml/
├── README.md
├── notebooks/
│   └── lab_24_double_ml.ipynb
├── figures/
│   ├── cate_by_quartile.png
│   └── regularization_bias.png
└── verification-log.md
```

## Stack

Python · doubleml · scikit-learn · pandas · matplotlib

## Reference

Chernozhukov, V., Chetverikov, D., Demirer, M., Duflo, E., Hansen, C., Newey, W., & Robins, J. (2018). *Double/debiased machine learning for treatment and structural parameters.* The Econometrics Journal, 21(1), C1–C68.
