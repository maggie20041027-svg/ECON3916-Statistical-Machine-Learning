# Lab 2: The Illusion of Growth & The Composition Effect

## Objective

Built a Python-based economic data pipeline to investigate wage stagnation in the United States by ingesting live macroeconomic time series from the Federal Reserve Economic Data (FRED) API. The project analyzes the "money illusion" phenomenon and corrects for composition bias—a critical statistical artifact that emerged during the COVID-19 pandemic.

**Core Research Question:** Are American workers truly earning more, or is wage growth an illusion masked by inflation and labor market composition changes?

---

## Methodology

### 1. Data Acquisition via FRED API
- **Technology Stack:** Python, `fredapi`, `pandas`, `matplotlib`
- **Data Sources:**
  - `AHETPI`: Average Hourly Earnings of Production Workers (Nominal Wages)
  - `CPIAUCSL`: Consumer Price Index for All Urban Consumers (Inflation)
  - `ECIWAG`: Employment Cost Index for Wages (Composition-Adjusted Wages)

### 2. Real Wage Calculation
Converted nominal wages to **real wages** by adjusting for inflation:

```
Real Wages = (Nominal Wages / CPI) × 100
```

This removed the "money illusion"—the psychological bias where people focus on nominal dollar amounts rather than purchasing power.

### 3. Anomaly Detection: The Pandemic Spike
Identified a suspicious **+8% surge** in average wages during March-April 2020, coinciding with the onset of the COVID-19 pandemic. This spike contradicted the economic reality of mass unemployment and business closures.

### 4. Composition Effect Correction
To isolate the statistical artifact, I compared:
- **Standard Average Wages** (susceptible to composition bias)
- **Employment Cost Index (ECI)** (holds job composition constant)

The ECI methodology fixes worker demographics, industries, and occupations to measure *true* wage growth for identical jobs over time, eliminating distortions caused by shifts in who is employed.

---

## Key Findings

### Finding 1: The Money Illusion (1964–2024)
When adjusted for inflation, **real wages have been essentially flat for 50+ years**, despite nominal wages increasing from $2.50/hour to $30+/hour. Workers today have roughly the same purchasing power as their counterparts in the 1970s.

**Visualization Insight:** The dramatic upward slope of nominal wages collapses into a nearly horizontal line when deflated by CPI, revealing decades of stagnant living standards.

---

### Finding 2: The Pandemic Paradox (2020)
The 2020 wage "boom" was a **statistical mirage** caused by the composition effect:

**What Happened:**
- Low-wage workers (retail, hospitality, service sectors) were disproportionately laid off during lockdowns
- High-wage workers (tech, finance, remote-capable jobs) retained employment
- The remaining workforce had a higher average wage—not because anyone got raises, but because lower earners disappeared from the dataset

**Evidence:**
- **Standard Wages:** Spiked to 108+ on the rebased index (artificial growth)
- **ECI (Composition-Fixed):** Remained at ~102, showing stable, modest growth

**Conclusion:** The spike was an artifact of *who was counted*, not a genuine increase in compensation. This demonstrates why composition-adjusted metrics are essential for labor market analysis.

---

## Technical Highlights

- **Live API Integration:** Automated data ingestion ensures analysis reflects the most current economic conditions
- **Defensive Programming:** Handled missing data, timezone issues, and series alignment across datasets with different frequencies
- **Statistical Rigor:** Applied rebasing techniques (indexing to 100) to enable direct comparison of series with different units
- **Data Visualization:** Created publication-quality charts with annotations to communicate complex economic phenomena to non-technical audiences

---

## Impact & Applications

This analysis has direct relevance to:
- **Monetary Policy:** Central banks must distinguish real wage pressure from composition effects when setting interest rates
- **Labor Economics:** Researchers studying wage inequality need composition-adjusted data
- **Public Policy:** Debates over minimum wage, labor market health, and "worker shortages" require accurate measurement of compensation trends

**Methodological Contribution:** Demonstrated a reproducible framework for detecting and correcting composition bias in economic time series—applicable beyond wages to any aggregated dataset where the underlying population composition changes over time.
