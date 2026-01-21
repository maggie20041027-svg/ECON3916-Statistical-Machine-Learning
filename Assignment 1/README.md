# The Cost of Living Crisis: A Data-Driven Analysis

## Overview
The Consumer Price Index (CPI) is the most commonly cited measure of inflation in the United States. While it effectively tracks changes in the *average* cost of living, it often fails to reflect the experiences of specific demographic groups. Students, particularly those living in high-cost metropolitan areas, face a consumption basket that differs substantially from the national average.

This project evaluates whether national CPI understates inflation for students by constructing a **Student Price Index (SPI)** and comparing it against both **National CPI** and **Boston–Cambridge–Newton CPI**. The analysis focuses on the period beginning in 2016, reflecting the start of the student consumption timeline.

---

## The Problem
National CPI aggregates spending patterns across the entire population, weighting goods and services based on average household consumption. Students, however, allocate a disproportionate share of their spending to housing, tuition, food away from home, and essential digital services. Additionally, students in cities such as Boston face regional price pressures that national averages may smooth over.

As a result, relying on national CPI may lead to a systematic understatement of the inflation students actually experience. This project addresses the following questions:

- How does student-specific inflation compare to national CPI?
- Does regional inflation in Boston exceed the national average?
- Which cost categories drive divergence between student costs and official inflation?

---

## Data Sources
All data used in this project were obtained from the Federal Reserve Economic Data (FRED) database:

- **National CPI (CPI-U):** Consumer Price Index for All Urban Consumers (U.S.)
- **Boston CPI:** Consumer Price Index for All Urban Consumers, Boston–Cambridge–Newton
- **Category-Level CPI Proxies:**
  - Tuition, fees, and childcare
  - Rent of primary residence
  - Food away from home
  - Cable and streaming services

These category-level indices serve as proxies for key components of a student consumption basket.

---

## Methodology
The analysis applies principles from **index number theory** and macroeconomic measurement using Python.

Key methodological steps include:

1. **Data Retrieval:** Economic time-series data were retrieved programmatically using the `fredapi` Python library.
2. **Data Cleaning:** Series with differing frequencies (monthly vs. bimonthly) were aligned and missing observations handled to ensure consistent comparisons.
3. **Normalization:** All series were re-indexed so that **2016 = 100**, eliminating distortions caused by differing base years and enabling meaningful visual and numerical comparison.
4. **Index Construction:** A **Laspeyres-style Student Price Index** was constructed by holding expenditure weights fixed, mirroring the methodology used in official CPI calculations while tailoring the basket to student consumption patterns.
5. **Visualization:** Comparative plots were generated to examine divergence across national, regional, and student-specific inflation measures and to illustrate the importance of normalization.

---

## Key Findings
The results show a **persistent divergence between student cost inflation and national CPI**, particularly after 2020. While national CPI increases steadily, both Boston CPI and the Student SPI rise at a faster pace, indicating that students in Boston face significantly higher inflation than national statistics imply.

Housing and food away from home emerge as the primary drivers of this divergence, reflecting structural pressures in urban housing markets and service-sector pricing. Digital services, while increasing over time, contribute relatively little to overall student inflation.

These findings suggest that national CPI understates the inflationary burden faced by students in high-cost metropolitan areas.

---

## Conclusion
This project demonstrates how aggregate economic indicators can obscure meaningful variation across regions and demographic groups. By applying index theory to construct a customized Student Price Index, the analysis provides a more accurate representation of student cost pressures than headline CPI alone.

The results underscore the importance of demographic- and region-specific inflation measures for understanding real economic conditions and highlight the value of transparent, data-driven index construction in applied economic analysis.

---

## Tools & Skills
- Python (pandas, matplotlib)
- FRED API (`fredapi`)
- Time-series analysis
- Index number theory (Laspeyres index)
- Data normalization and visualization
- Applied macroeconomic analysis
