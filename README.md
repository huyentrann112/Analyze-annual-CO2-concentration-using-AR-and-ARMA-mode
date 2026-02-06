# Time Series Analysis: Monthly CO₂ Concentration Forecasting

Statistical modeling and forecasting of atmospheric CO₂ concentrations using AR(1) and ARMA(1,1) models.

## Problem Statement

Atmospheric CO₂ concentration exhibits both long-term upward trends and seasonal patterns. This project analyzes monthly measurements to:
- Compare forecasting accuracy between autoregressive models
- Generate point forecasts with prediction intervals
- Validate statistical assumptions through residual diagnostics

**Dataset:** Mauna Loa Observatory, Hawaii (March 1958 - December 2001)

## Methodology

### Models Evaluated

**1. AR(1) - First-Order Autoregressive**
```
Y_t = c + φ·Y_{t-1} + e_t
```
- Single lag dependency
- Stationarity condition: |φ| < 1
- Estimated via Maximum Likelihood Estimation (MLE)

**2. ARMA(1,1) - Autoregressive Moving Average**
```
Y_t = c + φ·Y_{t-1} + θ·e_{t-1} + e_t
```
- Combines AR and MA components
- Captures both direct dependencies and shock propagation

### Theoretical Framework

**Stationarity Analysis:**
- Derived mean, variance, and autocorrelation functions for AR(1)
- Proved stationarity condition |φ| < 1
- Explored near-unit-root behavior (φ ≈ 1)

**Estimation Methods:**
- Method of Moments (MoM): Simple, lag-1 autocorrelation
- Least Squares (LSE): Conditional sum-of-squares
- Maximum Likelihood (MLE): Unconditional, uses all observations *(selected)*

## Experimental Setup

**Data Split:**
- Training: First 514 months (Mar 1958 - Dec 2000)
- Test: Last 12 months (Jan 2001 - Dec 2001)

**Evaluation Metrics:**
- RMSE (Root Mean Squared Error)
- MAPE (Mean Absolute Percentage Error)

**Diagnostics:**
- Residual plots (zero mean, constant variance)
- Shapiro-Wilk test (normality)
- ACF plots + Ljung-Box test (autocorrelation)

## Key Results

### Forecasting Performance
ARMA(1,1) outperforms AR(1) with ~37% improvement in both RMSE and MAPE. The MA component captures short-term dynamics that pure AR structure misses.

### Prediction Intervals (One-Step Ahead)
Both models provide 95% confidence intervals for next-month forecasts, with ARMA(1,1) producing narrower intervals due to lower innovation variance.

### Assumption Validation

| Assumption | AR(1) Status | Issue |
|------------|--------------|-------|
| Zero Mean | ✓ PASS | Residual mean ≈ 0 |
| Constant Variance | ✓ PASS | Homoscedastic residuals |
| Normality | ✗ FAIL | Shapiro-Wilk p ≈ 0 |
| No Autocorrelation | ✗ SEVERE FAIL | Ljung-Box p ≈ 0 |

**Root Cause:** AR(1) cannot capture 12-month seasonality. Seasonal structure leaks into residuals, causing significant autocorrelation at lags 12, 24, 36.

## Key Findings

**Strengths:**
- AR(1) and ARMA(1,1) capture upward trend effectively
- High short-term predictability (φ ≈ 0.999)
- ARMA(1,1) provides better forecast accuracy

**Critical Limitations:**
- **Model misspecification:** Neither model handles seasonality
- **Failed diagnostics:** Severe autocorrelation in residuals
- **Invalid inference:** Confidence intervals unreliable due to violated assumptions
- **Systematic bias:** Forecasts miss seasonal peaks/troughs

**Estimated Parameters:**
- Near-unit-root behavior (φ ≈ 1) indicates non-stationarity
- Differencing required to achieve stationarity

## Recommendations

### Primary: SARIMA Modeling
Implement **SARIMA(1,1,0)(1,0,1)[12]**:
- **Seasonal differencing (D=1):** Remove 12-month cycles
- **First differencing (d=1):** Remove trend
- **Seasonal AR/MA:** Capture year-over-year dependencies

### Alternative Approaches
- Structural time series models with seasonal decomposition
- Dynamic regression with seasonal dummy variables
- Exponential smoothing (Holt-Winters)

## Conclusions

While AR(1) and ARMA(1,1) provide reasonable forecasting performance, **both models are fundamentally inadequate** for data with strong trend and seasonality. The severe autocorrelation in residuals invalidates:
- Parameter standard errors
- Hypothesis tests
- Prediction interval coverage

**For rigorous analysis of seasonal time series, seasonal models (SARIMA) are essential.**