# Data Wrangling & Engineering Pipeline

## Objective
Engineer production-ready structural features and resolve missing data pathologies in a high-dimensional HR economics dataset to establish a clean, model-ready input matrix for downstream econometric estimation.

---

## Methodology

**Data Ingestion & Audit**
- Loaded `messy_hr_economics.csv` via `pandas` and performed a systematic structural audit — inspecting dtypes, cardinality distributions, and null counts across all feature dimensions.

**Missingness Diagnosis**
- Deployed `missingno` to render missingness matrices and heatmaps, enabling visual identification of null co-occurrence patterns across variables.
- Classified the dominant missingness mechanism as **Missing At Random (MAR)** — confirmed through conditional dependency analysis between observed covariates and the probability of missingness.

**Conditional Median Imputation**
- Designed a group-stratified imputation strategy: for each numeric column with nulls, rows were grouped by categorical covariates and missing values were filled using within-group medians.
- Applied a global median fallback to handle edge cases where an entire stratum was unobserved.

**Dummy Variable Trap Mitigation**
- One-hot encoded all nominal categorical variables using `pandas.get_dummies()` with `drop_first=True` to eliminate a reference class per feature, preventing exact linear dependence in the design matrix and ensuring full-rank input to OLS estimators.

**High-Cardinality Geographic Encoding**
- Applied **Target Encoding** via `category_encoders.TargetEncoder` to compress high-cardinality geographic identifiers (e.g., region, city) into continuous, outcome-informed numeric representations — avoiding dimensionality explosion while retaining predictive signal.

**Pipeline Validation**
- Confirmed zero residual null values post-imputation and verified design matrix rank sufficiency using `numpy.linalg.matrix_rank` prior to passing data to `statsmodels`.

---

## Key Findings

| Challenge | Diagnosis | Resolution |
|---|---|---|
| Structural missingness | MAR — nulls correlated with observed group membership | Conditional group-median imputation |
| Perfect multicollinearity | Dummy Variable Trap from full one-hot encoding | Reference class elimination via `drop_first=True` |
| Cardinality explosion | Geographic features with 50+ unique levels | Target Encoding → single continuous feature per variable |

The resulting pipeline transformed a structurally compromised raw dataset into a full-rank, null-free design matrix — meeting all preconditions for unbiased OLS estimation and valid standard error computation under classical linear model assumptions.

---

## Tech Stack

| Tool | Role |
|---|---|
| `pandas` | Data ingestion, wrangling, encoding |
| `missingno` | Visual missingness diagnostics |
| `category_encoders` | Target Encoding for high-cardinality features |
| `statsmodels` | Econometric modeling |
| `numpy` | Design matrix rank verification |
