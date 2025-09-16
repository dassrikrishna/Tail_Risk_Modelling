# Time Series Analysis and Volatility Modeling of SBIN.NS and INFY.NS

## Overview
This project constructs and validates marginal distribution models for the financial returns of two major Indian stocks: State Bank of India (SBIN.NS) and Infosys (INFY.NS). The primary goal is to create a robust model that accurately captures the stylized facts of financial returns, such as volatility clustering, leverage effects, and heavy-tailed distributions.

The modeling framework integrates three key components:
1.  **Autoregressive Moving Average (ARMA) model**: Used for modeling the conditional mean.
2.  **Glosten-Jagannathan-Runkle GARCH (GJR-GARCH) model with Student’s t-distribution**: Applied for modeling conditional volatility.
3.  **Extreme Value Theory (EVT)**: Utilized for modeling the tail distributions.

The final composite model is validated using the Probability Integral Transform (PIT) and Kolmogorov-Smirnov (KS) tests to ensure an adequate description of the entire return distribution.

## Contents
1.  [Installation](#installation)
2.  [Data Acquisition and Preparation](#data-acquisition-and-preparation)
3.  [ARMA Model Analysis](#arma-model-analysis)
    *   [SBIN.NS](#sbinns-arma)
    *   [INFY.NS](#infyns-arma)
4.  [GJR-GARCH-StudentT Model Analysis](#gjr-garch-studentt-model-analysis)
    *   [SBIN.NS](#sbinns-gjr-garch)
    *   [INFY.NS](#infyns-gjr-garch)
5.  [Rolling Forecasting](#rolling-forecasting)
    *   [SBIN.NS](#sbinns-rolling-forecast)
    *   [INFY.NS](#infyns-rolling-forecast)
6.  [Extreme Value Theory (EVT) Analysis](#extreme-value-theory-evt-analysis)
    *   [SBIN.NS EVT](#sbinns-evt)
    *   [INFY.NS EVT](#infyns-evt)
7.  [Author](#author)
8.  [Date](#date)

## Installation
The following Python libraries are necessary to run the analysis:
*   `yfinance`
*   `numpy`
*   `matplotlib`
*   `pandas`
*   `statsmodels`
*   `pmdarima`
*   `sklearn`
*   `arch`
*   `scipy`
*   `seaborn`
*   `pyextremes`

You can install these using pip:
```bash
pip install yfinance numpy matplotlib pandas statsmodels pmdarima scikit-learn arch scipy seaborn pyextremes
```

## Data Acquisition and Preparation
Historical daily stock price data for **SBIN.NS** and **INFY.NS** were obtained from Yahoo Finance for the maximum available period.

Daily log returns are calculated using the formula: `Rt = 100 * ln(Pt / Pt-1)` where `Pt` is the closing price at time `t`.
*   Initial log returns on 1996-01-02:
    *   **INFY.NS**: -0.405307%
    *   **SBIN.NS**: -3.234742%

The log return series exhibit clear evidence of **volatility clustering**, where periods of high volatility are followed by similar high-volatility periods, and vice versa.

## ARMA Model Analysis
The optimal ARIMA specification for each stock's log returns was determined using a systematic approach. The dataset was partitioned with 95% for training. The `pm.auto_arima` function performed an exhaustive search to identify the model minimizing the Akaike Information Criterion (AIC), with parameters `start_p=1, start_q=1, max_p=7, max_q=7, start_P=0, m=3, test='adf', seasonal=False, D=1`.

### SBIN.NS (ARMA)
The optimal model identified for SBIN.NS was **ARIMA(0,0,1)(0,0,0) with intercept**.
*   **Model**: SARIMAX(0, 0, 1)
*   **No. Observations**: 7086
*   **Log Likelihood**: -16132.767
*   **AIC**: **32271.535**
*   **BIC**: **32292.132**
*   **RMSE**: 1.6094
*   **Confidence Interval (first 2 rows)**:
    *   2024-03-15: [-4.611628, 4.631178]
    *   2024-03-18: [-4.572123, 4.687486]

### INFY.NS (ARMA)
The optimal model identified for INFY.NS was **ARIMA(2,0,1)(0,0,0) with intercept**.
*   **Model**: SARIMAX(2, 0, 1)
*   **No. Observations**: 7086
*   **Log Likelihood**: -16260.519
*   **AIC**: **32531.038**
*   **BIC**: **32565.367**
*   **RMSE**: 1.5297
*   **Confidence Interval (first 2 rows)**:
    *   2024-03-15: [-4.408879, 5.002040]
    *   2024-03-18: [-4.634076, 4.796797]

## GJR-GARCH-StudentT Model Analysis
Following ARIMA model fitting, residuals were extracted and used to estimate a GJR-GARCH model with Student’s t-distribution for modeling conditional volatility. The `arch_model` function was configured with parameters: `vol='Garch', p=1, o=1, q=1, dist='studentst', mean='Constant'`.

### SBIN.NS (GJR-GARCH)
*   **Model**: Constant Mean - GJR-GARCH (1,1,1) with Standardized Student's t-distribution
*   **Log-Likelihood**: -15383.9
*   **AIC**: **30779.8577**
*   **BIC**: **30821.0530**
*   **Key Volatility Model Parameters**:
    *   `omega`: 0.1378
    *   `alpha`: 0.0745
    *   `gamma`: 0.0358 (indicates leverage effect)
    *   `beta`: 0.8869
    *   `nu` (Student's t-distribution degrees of freedom): 5.8373

**Residual Diagnostics for SBIN.NS**:
*   **Ljung-Box on residuals (Lags 10, 20)**: p-values 0.0012, 0.0019
*   **Ljung-Box on squared residuals (Lags 10, 20)**: p-values 0.6715, 0.8281
*   **ARCH LM p-value**: 0.8366
*   **Visual diagnostics** include plots for standardized residuals, their distribution, Q-Q plot, and ACF of residuals.

### INFY.NS (GJR-GARCH)
*   **Model**: Constant Mean - GJR-GARCH (1,1,1) with Standardized Student's t-distribution
*   **Log-Likelihood**: -14747.6
*   **AIC**: **29507.2999**
*   **BIC**: **29548.4951**
*   **Key Volatility Model Parameters**:
    *   `omega`: 0.2404
    *   `alpha`: 0.1397
    *   `gamma`: 0.0313
    *   `beta`: 0.8145
    *   `nu` (Student's t-distribution degrees of freedom): 4.1291

**Residual Diagnostics for INFY.NS**:
*   **Ljung-Box on residuals (Lags 10, 20)**: p-values 0.6592, 0.4269
*   **Ljung-Box on squared residuals (Lags 10, 20)**: p-values 0.6700, 0.9260
*   **ARCH LM p-value**: 0.9102
*   **Visual diagnostics** include plots for standardized residuals, their distribution, Q-Q plot, and ACF of residuals.

## Rolling Forecasting
Rolling forecasting procedures were implemented for both stocks using the fitted GJR-GARCH models on ARIMA residuals. The models were recursively re-estimated at each step with updated residual histories, generating one-step-ahead volatility forecasts. Actual volatility was approximated using a 40-day rolling standard deviation window.

### SBIN.NS (Rolling Forecast)
*   **Forecast Accuracy (RMSE)**: 0.5047
*   Plots comparing actual rolling standard deviation vs. predicted volatility are generated.

### INFY.NS (Rolling Forecast)
*   **Forecast Accuracy (RMSE)**: 0.4664
*   Plots comparing actual rolling standard deviation vs. predicted volatility are generated.

## Extreme Value Theory (EVT) Analysis
The Peak Over Threshold (POT) methodology was employed to fit Generalized Pareto Distributions (GPD) to the tail regions of the log return distributions. Threshold selection involved systematic evaluation across quantile ranges (`q_range`), computing Kolmogorov-Smirnov (KS) p-values and Anderson-Darling (AD) statistics for exceedances, with scoring mechanisms to identify optimal fits.

A smooth cumulative distribution function (CDF) was constructed combining Student’s t-distribution for the central body with GPD tails, connected via logistic splicing. Model validation utilized PIT histograms and KS tests. A global PIT optimization was performed to find the best upper and lower thresholds that yield a KS p-value greater than 0.05 for the overall smoothed CDF.

### SBIN.NS EVT
*   **Mean Residual Life and Parameter Stability Plots**: Generated for both upper and lower extremes.
*   **Optimal Upper Tail Threshold**:
    *   **Quantile**: 0.950
    *   **Threshold**: 3.7167
    *   **Exceedances**: 373
    *   **Shape Parameter**: 0.0756
    *   **Scale Parameter**: 1.6505
    *   **KS p-value**: 0.4344
    *   **AD Statistic**: 1.1088
    *   **Combined Score**: 0.3235
*   **Optimal Lower Tail Threshold**:
    *   **Quantile**: 0.045
    *   **Threshold**: -3.6935
    *   **Exceedances**: 336
    *   **Shape Parameter**: 0.1344
    *   **Scale Parameter**: 1.4497
    *   **KS p-value**: 0.9168
    *   **AD Statistic**: 0.3173
    *   **Combined Score**: 0.8851
*   **PIT Validation Results (Smooth CDF)**:
    *   **KS Statistic**: 0.0133
    *   **p-value**: 0.1412
*   **Global PIT Optimization**:
    *   **Optimal Combination**: (i=9, j=6)
    *   **KS Statistic**: 0.0133
    *   **p-value**: 0.1412

### INFY.NS EVT
*   **Mean Residual Life and Parameter Stability Plots**: Generated for both upper and lower extremes.
*   **Optimal Upper Tail Threshold**:
    *   **Quantile**: 0.925
    *   **Threshold**: 2.9710
    *   **Exceedances**: 560
    *   **Shape Parameter**: -0.0180
    *   **Scale Parameter**: 2.1022
    *   **KS p-value**: 0.2443
    *   **AD Statistic**: 1.3934
    *   **Combined Score**: 0.1050
*   **Optimal Lower Tail Threshold**:
    *   **Quantile**: 0.055
    *   **Threshold**: -3.0617
    *   **Exceedances**: 411
    *   **Shape Parameter**: 0.1899
    *   **Scale Parameter**: 1.8274
    *   **KS p-value**: 0.6968
    *   **AD Statistic**: 0.4515
    *   **Combined Score**: 0.6517
*   **PIT Validation Results (Smooth CDF)**:
    *   **KS Statistic**: 0.0116
    *   **p-value**: 0.2668
*   **Global PIT Optimization**:
    *   **Optimal Combination**: (i=1, j=3)
    *   **KS Statistic**: 0.0116
    *   **p-value**: 0.2668

## Author
Srikrishna Das (MA24M025)

## Date
September 16, 2025