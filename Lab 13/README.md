# The Architecture of Dimensionality: Hedonic Pricing & the FWL Theorem

## Objective
Demonstrate how omitted variable bias catastrophically corrupts a naive pricing
algorithm and prove algebraically — via manual execution of the Frisch-Waugh-Lovell
theorem — that a multivariate OLS hyperplane achieves true ceteris paribus isolation
of each feature's independent contribution to real estate valuation.

## Methodology
- Ingested the **2026 California Zillow Hedonic Dataset** containing `Sale_Price`,
  `Property_Age`, and `Distance_to_Tech_Hub` directly into a pandas DataFrame
- Estimated a **naive bivariate OLS model** regressing `Sale_Price` strictly on
  `Property_Age` to expose the raw, unadjusted — and severely biased — age coefficient
- Expanded to a **multivariate OLS model** controlling for `Distance_to_Tech_Hub`,
  observing the coefficient shift as the location confounder was absorbed into the
  hyperplane
- Manually executed the **Frisch-Waugh-Lovell (FWL) Theorem** in three steps:
  - Partialled `Distance_to_Tech_Hub` out of `Sale_Price` to extract Price Residuals
  - Partialled `Distance_to_Tech_Hub` out of `Property_Age` to extract Age Residuals
  - Regressed Price Residuals strictly on Age Residuals (suppressing the intercept)
    to isolate the pure, uncorrelated signal of property age on sale price
- Performed the **Epistemological Proof** by comparing the FWL-isolated coefficient
  to the multivariate coefficient, confirming an exact match to multiple decimal places

## Key Findings
The naive algorithm produced a severely biased age coefficient, falsely attributing
inflated pricing power to the physical age of the home — a direct consequence of
omitting the correlated location variable `Distance_to_Tech_Hub`. In the 2026
California market, older homes are disproportionately located near high-value coastal
tech hubs, causing the bivariate model to steal mathematical credit from proximity
and misattribute it to age. The multivariate OLS hyperplane corrected this by
simultaneously controlling for both dimensions. Manual execution of the FWL theorem
provided the algebraic proof: by extracting and regressing pure residual variance,
the algorithm achieved exact ceteris paribus isolation — the FWL coefficient matched
the multivariate coefficient precisely, confirming that the hyperplane is not
approximating but mathematically guaranteeing independent signal extraction.
