# Architecting the Prediction Engine: OLS, Hedonic Pricing & RMSE Evaluation

## Objective
Engineer a multivariate Ordinary Least Squares (OLS) prediction model using 
contemporary real estate market data to minimize out-of-sample predictive loss 
and quantify algorithmic business risk in US Dollar terms.

## Methodology
- Ingested the **Zillow ZHVI 2026 Micro Dataset** directly into a pandas 
  DataFrame for cross-sectional real estate market analysis
- Performed Exploratory Data Analysis (EDA) to assess the scale and variance 
  of the target variable, `Home_Value`
- Architected a multivariate hedonic pricing model using the **statsmodels 
  Patsy Formula API**, regressing `Home_Value` on `Square_Footage`, 
  `Property_Age`, and `Distance_to_Transit`
- Evaluated model diagnostics including R-squared, coefficient signs, and 
  t-statistics to verify economic intuition
- Transitioned from classical causal explanation to **predictive engineering** 
  by extracting the fitted values vector
- Calculated **Root Mean Squared Error (RMSE)** in actual US Dollars to 
  produce an interpretable, real-world measure of predictive loss

## Key Findings
Successfully operationalized a hedonic pricing OLS engine on live 2026 real 
estate data. By calculating RMSE in US Dollars, the model's precise financial 
error margin was identified — enabling direct assessment of algorithmic 
business risk. This establishes a critical distinction between a model that 
*explains* market behavior and one that can be *deployed* for valuation 
decisions, where an inflated RMSE signals exposure to mispriced or toxic 
real estate inventory.
