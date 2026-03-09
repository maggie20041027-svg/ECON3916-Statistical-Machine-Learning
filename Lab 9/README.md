# Recovering Experimental Truths via Propensity Score Matching

## Objective
Applied Propensity Score Matching (PSM) to an observational dataset to eliminate selection bias and recover the true causal treatment effect of a job training program — demonstrating that naive comparisons in non-experimental data can produce conclusions that are not only wrong, but directionally reversed.

---

## The Problem: Observational Failure
The raw observational data produced a naive difference-in-means estimate of **-$635**, suggesting that job training *reduces* earnings. This result is mathematically absurd — it is entirely an artifact of Selection Bias. The PSID control group consists of older, wealthier, and more stably employed individuals than the NSW trainees, making any direct comparison invalid.

---

## Methodology

- **Bias Diagnosis** — Quantified the "Crisis of Observation" by computing the naive Simple Difference in Outcomes (SDO) and confirming its statistical insignificance (p = 0.33), establishing that the raw comparison carries no evidentiary weight.

- **Propensity Score Estimation** — Trained a Logistic Regression model (`solver='liblinear'`) on pre-treatment covariates (`age`, `education`, `race`, `marital status`, `prior earnings`) to estimate each individual's probability of selecting into the training program — the propensity score.

- **Nearest Neighbor Matching** — Implemented a 1:1 Nearest Neighbor matching algorithm using `sklearn.neighbors.NearestNeighbors` to pair each treated individual with the most statistically similar control unit based on propensity score proximity, reconstructing a valid counterfactual comparison group.

- **Covariate Balance Verification** — Validated matching quality using a Love Plot of Standardized Mean Differences (SMD), confirming all covariates collapsed within the |0.1| threshold post-matching — the standard econometric benchmark for acceptable balance.

- **Causal Effect Estimation** — Computed the Average Treatment Effect on the Treated (ATT) on the matched sample and confirmed statistical significance via independent samples t-test.

---

## Key Findings

| Estimate | Value | P-Value | Interpretation |
|----------|-------|---------|----------------|
| Naive SDO (Before Matching) | -$635 | 0.3342 | Biased, insignificant — selection bias dominates |
| ATT (After PSM) | +$1,850 | 0.0109 | Causal, significant — true program effect recovered |

The $2,485 swing between the naive estimate and the matched estimate represents pure selection bias — the systematic pre-existing advantage of the comparison group, not any effect of the treatment itself.

The recovered ATT of approximately **+$1,850** aligns closely with the true experimental benchmark (~$1,794) established by the original randomized NSW trial, validating that PSM successfully reconstructed the counterfactual and isolated the causal effect of job training on post-program earnings.

---

## Stack
`Python` · `Pandas` · `Scikit-Learn` · `SciPy` · `Matplotlib` · `Logistic Regression` · `Nearest Neighbor Matching`
