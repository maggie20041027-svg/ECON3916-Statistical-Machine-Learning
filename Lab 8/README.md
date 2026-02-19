# 🧪 Hypothesis Testing & Causal Evidence Architecture

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-Statistical%20Testing-orange?logo=scipy&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Method](https://img.shields.io/badge/Method-Parametric%20%2B%20Non--Parametric-purple)

> *Operationalizing the scientific method to adjudicate between competing narratives of causality using the Lalonde (1986) dataset.*

---

## 📌 Table of Contents

- [Objective](#-objective)
- [Dataset](#-dataset)
- [Technical Approach](#-technical-approach)
- [Key Findings](#-key-findings)
- [Business Insight](#-business-insight-hypothesis-testing-as-the-safety-valve-of-the-algorithmic-economy)
- [Stack](#-stack)

---

## 🎯 Objective

Most applied analytics stops at **estimation** — computing a number and reporting it. This project pivots to **falsification**: structuring the analysis as a rigorous attempt to *disprove* the null hypothesis before accepting any causal claim as actionable.

Using the **Lalonde (1986) dataset** — the canonical benchmark of causal inference — this lab treats job-training program evaluation not as a descriptive exercise, but as an epistemic one.

| Question Type | Example |
|---|---|
| ❌ Estimation (weak) | *"What is the treatment effect?"* |
| ✅ Falsification (rigorous) | *"Can we rule out that this effect is noise?"* |

> That distinction is the difference between data science and data storytelling.

---

## 📂 Dataset

**Lalonde Experimental Subset** — the "Hello World" of causal inference.

| Feature | Description |
|---|---|
| `treat` | Binary — 1 = Received job training, 0 = Control |
| `re78` | Real earnings in 1978 (post-program outcome) |
| Design | Randomized controlled experiment |

---

## ⚙️ Technical Approach

### Step 1 — Signal-to-Noise Decomposition (Parametric)

The Average Treatment Effect (ATE) was computed as the raw difference in means between treatment and control groups on post-program real earnings. This signal was normalized against sampling noise using **Welch's T-Test**, selected to account for unequal variance between groups — a common failure mode when comparing earnings distributions across demographic strata.

```
T-Statistic = Signal (Difference in Means) / Noise (Standard Error)
```

### Step 2 — Non-Parametric Validation via Permutation Test

Because earnings data is notoriously heavy-tailed and non-normal, the parametric result was stress-tested using a **Permutation Test (10,000 resamples)**. This procedure simulates the null world — one where treatment assignment is arbitrary — and empirically builds a null distribution without relying on Gaussian assumptions.

### Step 3 — Type I Error Control

Both tests were evaluated against a **pre-specified significance threshold (α = 0.05)**, ensuring the decision rule was fixed prior to observing results. This guards against post-hoc threshold shifting — a leading cause of spurious findings in exploratory analyses.

---

## 📊 Key Findings

| Metric | Result |
|---|---|
| Mean Earnings — Treated | $6,349.14 |
| Mean Earnings — Control | $6,984.17 |
| Treatment Effect (ATE) | ~$1,795 lift |
| T-Test p-value | < 0.05 ✅ |
| Permutation Test p-value | < 0.05 ✅ |
| Decision | **Reject Null Hypothesis** |

> Both parametric and non-parametric methods converged on the same conclusion, confirming robustness across distributional assumptions.

The null hypothesis — that the observed difference is attributable to chance — was rejected via **Proof by Statistical Contradiction**: the data is sufficiently inconsistent with the null world to warrant a causal interpretation.

---

## 💡 Business Insight: Hypothesis Testing as the Safety Valve of the Algorithmic Economy

In production environments, models are constantly interrogated for patterns — and patterns are *always* findable. The danger is not a lack of signal; it is an excess of apparent signal.

Without a rigorous falsification framework, data teams are exposed to:

- **Data Dredging** — iterative mining until a nominally significant result surfaces
- **Spurious Correlations** — noise patterns promoted to production as causal features
- **p-hacking** — retroactively adjusting thresholds after observing results

Hypothesis testing — properly implemented with pre-specified thresholds, appropriate test selection, and non-parametric validation — functions as the **safety valve** of the algorithmic economy. It forces analysts to define what would count as *not* finding an effect **before they look**.

> In an era where algorithmic decisions influence credit, hiring, and resource allocation, a spurious correlation shipped to production is not an academic error — it is a risk management failure.

---

## 🛠 Stack

| Tool | Purpose |
|---|---|
| `pandas` | Data loading & manipulation |
| `numpy` | Numerical operations |
| `scipy.stats` | Welch's T-Test & Permutation Test |
| `matplotlib` | Visualization |
| `seaborn` | Statistical plotting |

---

*Dataset: Lalonde (1986) Experimental Subset*
