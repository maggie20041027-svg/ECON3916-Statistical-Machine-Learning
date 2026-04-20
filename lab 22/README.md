# Clustering World Economies with K-Means & PCA

## Objective
This project applies unsupervised K-Means clustering and Principal Component Analysis to a multidimensional set of World Bank development indicators, identifying latent country groupings that capture structural economic similarity beyond single-variable income classifications.

---

## Methodology

- **Data Acquisition**: Downloaded 10 World Development Indicators for ~236 sovereign nations via the `wbgapi` package, including GDP per capita (PPP), life expectancy, infant mortality, primary enrollment, Gini index, CO2 emissions per capita, internet penetration, trade openness, unemployment, and urbanization
- **Data Preparation**: Reshaped from long to wide format, averaged across the 2021–2025 window for coverage, dropped country aggregates, removed countries with >40% missingness, and median-imputed remaining gaps
- **Feature Standardization**: Applied `StandardScaler` to transform all features to mean 0 and standard deviation 1, ensuring K-Means' Euclidean distance metric weighted each indicator equally rather than being dominated by GDP's larger numerical range
- **Clustering**: Fit K-Means with K=4 using `k-means++` initialization and a fixed `random_state=42`, matching the World Bank's four-tier income classification structure
- **Dimensionality Reduction**: Projected the 10-dimensional standardized feature space onto 2 principal components for visualization, capturing 55.1% of total variance (PC1: 43.2%, PC2: 11.9%)
- **Model Selection**: Ran the elbow method and silhouette analysis across K=2 through K=10 to evaluate cluster quality and the bias-variance tradeoff in cluster granularity
- **Validation**: Cross-tabulated algorithmic cluster assignments against the World Bank's official income group classifications (Low, Lower-Middle, Upper-Middle, High) to assess where data-driven groupings agreed with and diverged from institutional categories
- **Generalization**: Replicated the full pipeline on the California Housing dataset (~20,640 census tracts, 8 demographic and housing features) to demonstrate the methodology's transferability across economic geographies

---

## Key Findings

- **Optimal K is context-dependent**: Silhouette score peaked at K=2 (0.27), reflecting that the strongest signal in the data is a binary rich-vs-poor split. K=4 sat at a lower silhouette (0.21) but produced the more economically meaningful four-tier structure used for downstream analysis — a reminder that the statistically optimal K is not always the operationally useful K.
- **Strong alignment at the top**: The "advanced economy" cluster matched the World Bank's High-income classification with 100% precision (all 45 of its members were World-Bank-classified High-income), validating that the algorithm independently rediscovered the developed-world group from a multidimensional development profile alone.
- **Informative mismatches in the middle**: The largest cluster (n=86) cut across institutional categories, mixing 47 Upper-Middle, 20 Lower-Middle, and 18 High-income countries. These mismatches reveal that wealthy emerging markets and oil/finance economies share infrastructure and human-development profiles even when their GNI per capita differs, capturing structural variation that a single-threshold GDP classification misses.
- **California Housing replication**: The same pipeline cleanly separated 20,640 California census tracts into four interpretable groups along PC1 (wealth gradient: income + home value) and PC2 (urban density vs. rural sprawl), recovering the well-documented coastal-vs-inland divide in California's housing market.
- **Methodological takeaway**: Multidimensional clustering complements rather than replaces institutional classifications — it surfaces structural similarity hidden by single-indicator thresholds, and the divergences between algorithmic and expert groupings are themselves the most analytically valuable output.

---

## Tech Stack

`Python` · `wbgapi` · `pandas` · `numpy` · `scikit-learn` (KMeans, StandardScaler, PCA, silhouette_score) · `matplotlib` · `seaborn`

---

## Repository Contents

- `lab-ch22-guided.ipynb` — full Jupyter notebook with all parts implemented (Parts 1–6 + California Housing Challenge)
- `README.md` — this file

---

## How to Run

1. Open `lab-ch22-guided.ipynb` in Google Colab or VS Code
2. Run cells top-to-bottom (`Kernel > Restart & Run All`)
3. Data downloads live from the World Bank API — no CSV uploads required
4. Total runtime: ~1–2 minutes

---

## Author

ECON 3916: Data Science for Economists
