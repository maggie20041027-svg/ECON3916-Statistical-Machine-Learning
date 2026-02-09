# Lab 5: The Architecture of Bias

## Project Overview

This laboratory investigation examines the **Data Generating Process (DGP)** and its impact on machine learning validity. The project demonstrates how sampling mechanisms—not model architecture—determine the reliability of statistical inference. Through forensic analysis of the Titanic dataset and A/B test diagnostics, this work reveals the hidden structure of bias in real-world data pipelines.

---

## Objective

To understand how sampling bias emerges from the Data Generating Process and to engineer solutions using stratification, diagnostic testing, and selection modeling. This project proves that **"garbage in, garbage out"** is not a metaphor—it's a mathematical certainty that can be measured, diagnosed, and corrected.

---

## Tech Stack

- **Python 3.x** - Core programming language
- **pandas** - Data manipulation and analysis
- **numpy** - Numerical computing and random sampling
- **scipy.stats** - Chi-Square tests for Sample Ratio Mismatch detection
- **sklearn** - Stratified sampling implementation
- **matplotlib/seaborn** (optional) - Data visualization

---

## Methodology

### Phase 1: The Danger of Randomness (Manual Split)

**Problem**: Simple Random Sampling (SRS) can fail catastrophically on small datasets.

**Implementation**:
- Manually shuffled the Titanic dataset using `numpy.random.permutation()`
- Created an 80/20 train-test split without sklearn
- Computed the **Delta** (sampling bias) between training and test survival rates
- Ran 10+ trials to demonstrate variance in random splits

**Key Finding**: Random splits produced survival rate differences of 0.6% to 7%, with an average deviation of 3%. This variance creates unreliable model evaluation—the test set is no longer representative of the population.

```python
# Example: Observed Delta
Train Survival Rate: 36.5%
Test Survival Rate:  40.8%
Sampling Bias (Delta): 4.3%
```

**Insight**: This is not a model error—it's a **Sampling Error**. The randomization mechanism failed to preserve the population distribution.

---

### Phase 2: Stratified Sampling (The Fix)

**Solution**: Use `sklearn.model_selection.train_test_split()` with `stratify` parameter.

**Implementation**:
- Stratified by the target variable (`survived`) to ensure proportional representation
- Compared stratified vs. random splits across multiple trials
- Measured reduction in Covariate Shift

**Key Finding**: Stratification eliminated sampling bias, producing train-test splits with <0.5% survival rate differences. This ensures that model performance metrics (accuracy, precision, recall) are valid estimates of real-world performance.

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, stratify=y, random_state=42
)
```

**Insight**: Stratification doesn't just reduce variance—it fundamentally changes the sampling mechanism to respect the DGP's class distribution.

---

### Phase 3: Sample Ratio Mismatch (SRM) Forensic Audit

**Problem**: In production A/B tests, the load balancer may fail, creating biased treatment assignments.

**Implementation**:
- Observed split: 450 Control / 550 Treatment (expected 500/500)
- Conducted Chi-Square goodness-of-fit test using `scipy.stats.chisquare()`
- Simulated 10,000 random 50/50 splits to validate statistical significance

**Key Finding**:
```
Chi-Square Statistic: 10.0000
P-value: 0.001565

Conclusion: CRITICAL FAILURE - Sample Ratio Mismatch Detected
```

**Interpretation**:
- P-value < 0.01 indicates the 450/550 split is **not** due to random chance
- The deviation is **3.2 standard errors** from the expected mean
- Only 0.21% of random splits produce such extreme imbalances
- This invalidates the A/B test results—likely caused by faulty randomization, bot traffic, or data pipeline errors

**Insight**: SRM detection is a **prerequisite** for A/B test validity. Without it, you cannot distinguish treatment effects from assignment bias.

---

## Theoretical Deep Dive: Survivorship Bias & The Heckman Correction

### The Question

**"Why does analyzing only successful Unicorn startups (on TechCrunch) lead to Survivorship Bias, and what specific type of Ghost Data would I need to fix it using a Heckman Correction?"**

---

### The Problem: Survivorship Bias in Startup Analysis

**Definition**: Survivorship Bias occurs when analysis focuses only on entities that "survived" a selection process, ignoring those that failed and are no longer observable. This creates systematically biased estimates.

**TechCrunch Unicorn Example**:
- **Observable Data**: 500 unicorn startups featured on TechCrunch
- **Ghost Data**: ~50,000 failed startups that never achieved unicorn status or media coverage
- **Selection Mechanism**: Media outlets only report successful companies

**Why This Creates Bias**:

1. **Non-Random Selection**: Companies appear in your dataset *because* they succeeded. This is endogenous selection—the outcome (success) determines inclusion in the sample.

2. **Censored Dependent Variable**: You observe `success = 1` for unicorns, but you never observe `success = 0` for failed startups. The dependent variable is truncated.

3. **Biased Coefficient Estimates**: Suppose you regress unicorn valuation on founder experience:
   ```
   Valuation = β₀ + β₁(Founder_Experience) + ε
   ```
   This will **overestimate** the effect of experience because you're ignoring experienced founders whose companies failed.

4. **Omitted Variable Bias**: The selection mechanism (media coverage) is correlated with unobservable factors like luck, timing, and network effects. These omitted variables confound your causal estimates.

---

### The Solution: Heckman Correction (Two-Stage Selection Model)

The **Heckman Selection Model** corrects for survivorship bias by explicitly modeling the selection process that determines which observations appear in your dataset.

#### Stage 1: Selection Equation (The Ghost Data Problem)

**What You Need**:
- A dataset that includes **both successful and failed startups**
- Variables that predict *selection into the sample* (e.g., getting TechCrunch coverage) but don't directly affect the outcome (valuation)

**The Selection Equation**:
```
P(Observed = 1) = Φ(γ₀ + γ₁·Founder_Experience + γ₂·Media_Connections + γ₃·HQ_Location + ν)
```

Where:
- `Observed = 1` if the startup is featured on TechCrunch (unicorn)
- `Φ` is the cumulative normal distribution (Probit model)
- `Media_Connections` and `HQ_Location` are **exclusion restrictions** (instruments)

**Key Insight**: You need variables that predict *who gets observed* without directly causing unicorn status. For example:
- **Media_Connections**: Predicts TechCrunch coverage but doesn't directly cause business success
- **HQ_Location**: Bay Area startups get more media attention regardless of quality

**The Ghost Data Required**:
1. **Failed startups**: You need data on companies that *could have* been unicorns but failed (funding records, Y Combinator rejections, etc.)
2. **Exclusion restrictions**: Variables correlated with selection but not with success
3. **Common covariates**: Variables measured for both unicorns and failed startups (founder experience, funding rounds, team size)

#### Stage 2: Outcome Equation (Correcting the Bias)

From Stage 1, compute the **Inverse Mills Ratio (λ)**:
```
λᵢ = φ(γ'Zᵢ) / Φ(γ'Zᵢ)
```

This λ captures the "selection correction factor"—it quantifies how much more likely each observed unicorn was to be selected compared to the population.

**The Corrected Outcome Equation**:
```
Valuation = β₀ + β₁·Founder_Experience + β₂·λ + ε
```

**Interpretation**:
- **β₁** is now an **unbiased estimate** of founder experience's effect on valuation
- **β₂** (coefficient on λ) measures the severity of selection bias
- If β₂ ≠ 0, survivorship bias was present; if β₂ = 0, simple OLS was sufficient

---

### Practical Example: What Ghost Data Looks Like

| Company Name | Observed | Founder Exp (yrs) | Media Connections | Valuation ($M) | Failed? |
|--------------|----------|-------------------|-------------------|----------------|---------|
| UnicornCo A  | 1        | 15                | High              | 5,000          | No      |
| UnicornCo B  | 1        | 8                 | Medium            | 2,000          | No      |
| FailedCo X   | 0        | 12                | Low               | NA             | Yes     |
| FailedCo Y   | 0        | 20                | None              | NA             | Yes     |
| FailedCo Z   | 0        | 5                 | High              | NA             | Yes     |

**Key Variables**:
- `Observed = 1`: Appeared in TechCrunch (unicorn)
- `Observed = 0`: Never achieved unicorn status (ghost data)
- `Valuation`: Only observable for `Observed = 1`
- `Media_Connections`: Predicts coverage, not success (exclusion restriction)

---

### Why This Matters for Machine Learning

**Without Correction**:
- Your model learns: "Experienced founders → High valuation"
- Reality: "Experienced founders with luck and timing → High valuation; experienced founders without luck → Failure (unobserved)"

**With Heckman Correction**:
- Your model learns: "Experienced founders → Higher probability of success, but effect size is smaller than naive estimates suggest"
- You can predict: "This startup has a 15% chance of unicorn status" (not "This startup will definitely succeed because the founder is experienced")

---

## Key Takeaways

1. **Sampling Bias ≠ Model Bias**: The problem is upstream of your neural network. No amount of regularization or hyperparameter tuning will fix bad data.

2. **Stratification is Non-Negotiable**: For classification tasks with imbalanced classes, random splits are statistically invalid.

3. **SRM Detection is a Production Requirement**: Every A/B test should include automated Chi-Square diagnostics to catch engineering failures.

4. **Ghost Data Determines Validity**: The most important data is often the data you *don't* have. Selection bias requires modeling the missing data mechanism.

5. **The Heckman Correction is Causal Inference**: By explicitly modeling selection, you can estimate counterfactuals: "What would have happened to successful startups if they hadn't been featured in TechCrunch?"

---

## Files in This Repository

- `titanic_manual_split.py` - Phase 1: Manual random sampling demonstration
- `step2_split_bias_check.py` - Phase 2: Bias check and delta calculation
- `ab_test_srm_check.py` - Phase 3: Chi-Square SRM forensic audit
- `stratified_sampling.py` - Stratified sampling implementation (optional)
- `README.md` - This file

---

## How to Run

```bash
# Install dependencies
pip install pandas numpy scipy scikit-learn --break-system-packages

# Run Phase 1: Manual split demonstration
python titanic_manual_split.py

# Run Phase 2: Bias check
python step2_split_bias_check.py

# Run Phase 3: SRM detection
python ab_test_srm_check.py
```

---

## References

- Heckman, J. J. (1979). "Sample Selection Bias as a Specification Error." *Econometrica*, 47(1), 153-161.
- Kohavi, R., et al. (2009). "Controlled experiments on the web: survey and practical guide." *Data Mining and Knowledge Discovery*, 18(1), 140-181.
- Pearl, J. (2009). *Causality: Models, Reasoning, and Inference*. Cambridge University Press.

---

## Author

Lab 5 Completion - Data Generating Process & Sampling Bias Investigation

**Core Insight**: "The validity of your conclusions depends not on the sophistication of your model, but on the representativeness of your sample. Always interrogate the DGP before training."
