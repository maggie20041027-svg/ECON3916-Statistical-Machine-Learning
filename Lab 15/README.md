# The Polynomial Trap: Bias-Variance Tradeoff

## Portfolio Summary

This notebook operationalizes the bias-variance tradeoff from statistical theory
into measurable, deployable insight using both synthetic and real-world datasets.

Using 50 training observations drawn from a known sine-wave function (y = sin(2πx)
+ noise), polynomial regression models of degree 1 through 15 were systematically
fitted and evaluated against a held-out test set of 200 observations. The resulting
complexity curve confirmed that polynomial degrees 3–5 minimized out-of-sample
prediction error, while higher-degree models exhibited classic overfitting behavior —
training RMSE approaching zero as test RMSE diverged.

K-fold cross-validation (k=5) was then implemented both manually and via
scikit-learn's cross_val_score API, successfully identifying the optimal degree
without accessing the test set — validating CV as a reliable model selection
mechanism. The CV-selected degree matched the true test-optimal degree, demonstrating
the practical power of cross-validation for generalization estimation.

Applied to the Ames Housing dataset (1,121 observations, 37 numeric features), a
parsimonious 5-feature OLS model outperformed the full kitchen-sink model in CV RMSE
despite achieving lower training R-squared — a direct demonstration of the
generalization gap and the cost of high variance in production deployments.

**Tools:** Python, NumPy, scikit-learn, Matplotlib
