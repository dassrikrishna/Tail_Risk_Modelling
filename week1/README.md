# Time Series Analysis and Volatility Modeling of Indian Stocks (SBIN.NS & INFY.NS)

This repository contains a detailed time series analysis and volatility modeling project focusing on two prominent Indian stocks: **SBIN.NS (State Bank of India)** and **INFY.NS (Infosys)**. The analysis employs a range of sophisticated time series models, including ARIMA, GARCH, GJR-GARCH, and Extreme Value Theory (EVT) with a focus on Generalized Pareto Distribution (GPD), to forecast stock prices and their associated volatilities.

## Table of Contents

1.  [Introduction](#1-introduction)
2.  [Data Acquisition and Initial Exploration](#2-data-acquisition-and-initial-exploration)
3.  [Stationarity Tests on Close Prices](#3-stationarity-tests-on-close-prices)
4.  [ARIMA Model Forecasting](#4-arima-model-forecasting)
5.  [Log Returns Analysis and Volatility Modeling](#5-log-returns-analysis-and-volatility-modeling)
6.  [GARCH Model for Log Returns](#6-garch-model-for-log-returns)
7.  [GJR-GARCH Model with Students-T Distribution](#7-gjr-garch-model-with-students-t-distribution)
8.  [Extreme Value Theory (EVT) - Peaks Over Threshold (POT) and GPD Fit](#8-extreme-value-theory-evt---peaks-over-threshold-pot-and-gpd-fit)
9.  [Libraries Used](#9-libraries-used)
10. [Conclusion](#10-conclusion)

## 1. Introduction

This project aims to provide a comprehensive analysis of the closing prices and log returns for SBIN.NS and INFY.NS. The core objective is to **model and forecast both stock prices and their associated volatilities** using various time series techniques. The models employed include:
*   **ARIMA** (Autoregressive Integrated Moving Average) for price forecasting.
*   **GARCH** (Generalized Autoregressive Conditional Heteroskedasticity) and **GJR-GARCH** for volatility modeling, accounting for volatility clustering and asymmetric responses to shocks.
*   **Extreme Value Theory (EVT)**, specifically the Peaks Over Threshold (POT) method with a Generalized Pareto Distribution (GPD) fit, to model extreme market events.

The analysis is implemented using a suite of Python libraries for data handling, statistical modeling, and visualization.

## 2. Data Acquisition and Initial Exploration

*   **Data Slices**: Closing price data for SBIN.NS and INFY.NS.
*   **Decisions**:
    *   Historical closing prices were **downloaded from Yahoo Finance (`yfinance`)** for the maximum available period.
    *   Initial closing price data samples were displayed.
    *   Visualizations of historical closing prices were generated to observe **long-term trends and fluctuations**.
    *   **Classical decomposition** (additive model, 252-period seasonality) was applied to the closing prices.
*   **Conclusion**: Initial exploration provided a foundational understanding of the stock price movements.

## 3. Stationarity Tests on Close Prices

*   **Data Slices**: Closing price series for SBIN.NS and INFY.NS.
*   **Decisions**:
    *   **Augmented Dickey-Fuller (ADF) tests** were performed to check for stationarity.
    *   **Kwiatkowski-Phillips-Schmidt-Shin (KPSS) tests** were also performed for robust stationarity assessment.
*   **Conclusion**: Both the ADF and KPSS tests consistently indicated that the close price series for both SBIN.NS and INFY.NS are **not stationary**.

## 4. ARIMA Model Forecasting

*   **Data Slices**: First difference of the close prices for SBIN.NS and INFY.NS, split into training (95%) and testing (5%) sets.
*   **Decisions**:
    *   ACF and PACF plots were generated for the first differenced series to aid in **ARIMA order identification**.
    *   The `ndiffs` function, utilizing the ADF test, indicated that **one difference was required** for both series to achieve stationarity.
    *   The `auto_arima` function was used to automatically select the best ARIMA(p,d,q) model order by **minimizing the Akaike Information Criterion (AIC)**.
    *   Model performance on the test set was evaluated using **Root Mean Squared Error (RMSE)** and **Mean Absolute Percentage Error (MAPE)**.
*   **Conclusion**:
    *   **SBIN.NS**: Best model was **ARIMA(0,1,1)(0,0,0) with an intercept** (AIC: 40372.382). Performance metrics: **RMSE: 53.8107**, **MAPE: 5.70%**.
    *   **INFY.NS**: Best model was **ARIMA(1,1,1)(0,0,0) with an intercept** (AIC: 51137.707). Performance metrics: **RMSE: 193.9741**, **MAPE: 9.90%**.

## 5. Log Returns Analysis and Volatility Modeling

*   **Data Slices**: Daily log returns for both SBIN.NS and INFY.NS.
*   **Decisions**:
    *   Log returns were computed as a percentage.
    *   Stationarity of log returns was tested using the **ADF test**.
    *   The presence of **ARCH effects** was checked using the ARCH test to identify volatility clustering.
    *   Log return data was split into training (95%) and testing (5%) sets.
*   **Conclusion**:
    *   Log return series for both stocks were found to be **stationary** (SBI-Returns p-value: 0.0, INFY-Returns p-value: 3.11e-26).
    *   Significant **ARCH effects were present** in both log returns (ARCH Test p-value: 0.0000 for both), confirming volatility clustering.

## 6. GARCH Model for Log Returns

*   **Data Slices**: Training and testing sets of log returns for SBIN.NS and INFY.NS.
*   **Decisions**:
    *   A custom function (`best_garch_model`) was used to find the **best GARCH(p,q) model with a Student’s t-distribution** by minimizing AIC.
    *   Model performance was evaluated using **RMSE** on rolling forecasts.
*   **Conclusion**:
    *   **SBIN.NS**: Best GARCH model was **GARCH(1,2)** with an AIC of 30773.3989. **RMSE for forecast: 0.4651**.
    *   **INFY.NS**: Best GARCH model was **GARCH(1,5)** with an AIC of 29477.6792. **RMSE for forecast: 0.3951**.

## 7. GJR-GARCH Model with Students-T Distribution

*   **Data Slices**: Log returns of SBIN.NS and INFY.NS.
*   **Decisions**:
    *   A **GJR-GARCH(1,1,1) model with a Students-T distribution** was applied to capture asymmetric volatility responses to positive and negative shocks.
    *   AIC and BIC were considered for model selection.
    *   Ljung-Box tests on residuals and squared residuals, and the ARCH LM test, were used to diagnose model fit and ensure no remaining ARCH effects.
*   **Conclusion**:
    *   **SBIN.NS**: AIC: 30772.4217, BIC: 30813.6153. **RMSE for forecast: 0.4746**. The ARCH LM p-value was 0.8120734381292837, indicating **no remaining ARCH effects** in the standardized residuals.
    *   **INFY.NS**: AIC: 29508.6811, BIC: 29549.8746. **RMSE for forecast: 0.4364**. The ARCH LM p-value was 0.8951512570675989, also indicating **no remaining ARCH effects** in the standardized residuals.

## 8. Extreme Value Theory (EVT) - Peaks Over Threshold (POT) and GPD Fit

*   **Data Slices**: Log returns for SBIN.NS and INFY.NS.
*   **Decisions**:
    *   **Peaks Over Threshold (POT) method** was used with a Generalized Pareto Distribution (GPD).
    *   Initial exploration involved **Block Maxima and POT methods**, **Mean Residual Life plots**, and **Parameter Stability plots** for threshold selection.
    *   Specific **upper and lower thresholds** were set for each stock based on these analyses.
    *   GPD parameters were fitted to exceedances, and **return period summaries** were generated.
    *   **Goodness-of-Fit tests (Kolmogorov-Smirnov and Anderson-Darling)** were performed to assess the GPD fit.
    *   A **smooth CDF** was constructed using a t-distribution for the body and GPD tails with a logistic splice for transition.
*   **Conclusion**:
    *   **SBIN.NS**: Upper threshold: 6.8 (73 exceedances), Lower threshold: -7.1 (45 exceedances). Fitted GPD upper tail parameters: shape=0.2347, scale=1.2698. Fitted GPD lower tail parameters: shape=0.0930, scale=1.9219. KS test for uniformity of PIT values yielded a p-value of **0.0220**.
    *   **INFY.NS**: Upper threshold: 5 (210 exceedances), Lower threshold: -7.8 (60 exceedances). Fitted GPD upper tail parameters: shape=-0.0869, scale=2.2910. Fitted GPD lower tail parameters: shape=0.3693, scale=1.6635. KS test for uniformity of PIT values yielded a p-value of **0.0600**.

## 9. Libraries Used

The analysis relies on a comprehensive suite of Python libraries:

*   `yfinance`
*   `numpy`
*   `scipy`
*   `matplotlib.pyplot`
*   `pandas`
*   `statsmodels`
*   `pmdarima`
*   `sklearn.metrics`
*   `arch`
*   `seaborn`
*   `pyextremes`
*   `warnings`

## 10. Conclusion

This project successfully applied various time series and extreme value models to forecast stock prices and volatility for SBIN.NS and INFY.NS. The findings highlight the non-stationary nature of stock prices, the presence of volatility clustering in log returns, and the effectiveness of GARCH-family models and EVT in capturing these financial time series characteristics. The chosen models demonstrated reasonable performance in predicting future values and extreme events, providing valuable insights for risk management and investment strategies.