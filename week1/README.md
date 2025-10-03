# Extreme Value Analysis of Stock Returns using ARMA-GJR-GARCH Model

## Overview
This report presents an extreme value analysis of stock returns for SBIN (State Bank of India) and INFY (Infosys) using the AR-GJR-GARCH model with Student’s t-distribution. The analysis includes model fitting, diagnostic tests, and threshold selection for extreme value modeling.

The modeling framework integrates key components:
1. **AR-GJR-GARCH model with Student’s t-distribution**: Used for modeling the conditional mean and volatility.
2. **Generalized Pareto Distribution (GPD)**: Applied for modeling the tail distributions.
3. **Smooth CDF Construction**: Combining GPD tails with Student’s t-body using logistic splice.
4. **Probability Integral Transform (PIT)**: Utilized for model validation.

The final composite model is validated using PIT histograms and Kolmogorov-Smirnov (KS) tests to ensure an adequate description of the entire return distribution.

## Contents
1. [Installation](#installation)
2. [Data Acquisition and Preparation](#data-acquisition-and-preparation)
3. [Visualization of Log Returns](#visualization-of-log-returns)
    * [SBIN.NS](#sbinns-visualization)
    * [INFY.NS](#infyns-visualization)
4. [Model Fitting and Diagnostics](#model-fitting-and-diagnostics)
    * [SBIN.NS](#sbinns-model)
    * [INFY.NS](#infyns-model)
5. [Extreme Value Analysis](#extreme-value-analysis)
    * [SBIN.NS EVT](#sbinns-evt)
    * [INFY.NS EVT](#infyns-evt)
6. [Author](#author)
7. [Date](#date)

## Installation
The following Python libraries are necessary to run the analysis:
* `yfinance`
* `numpy`
* `matplotlib`
* `pandas`
* `statsmodels`
* `arch`
* `scipy`
* `seaborn`
* `pyextremes`

You can install these using pip:
```bash
pip install yfinance numpy matplotlib pandas statsmodels arch scipy seaborn pyextremes
```

## Data Acquisition and Preparation
Financial data for SBIN.NS and INFY.NS were downloaded using the yfinance library for the maximum available period.

Daily log returns are calculated using the formula: `Rt = 100 * ln(Pt / Pt-1)` where `Pt` is the closing price at time `t`.
* Initial log returns on 1996-01-02:
    * **INFY.NS**: -0.4052%
    * **SBIN.NS**: -3.2347%

The log return series exhibit clear evidence of **volatility clustering**, where periods of high volatility are followed by similar high-volatility periods, and vice versa.

The first three observations of log returns are shown below:

| Date       | INFY.NS | SBIN.NS |
|------------|---------|---------|
| 1996-01-02 | -0.4052 | -3.2347 |
| 1996-01-03 | 0.6746  | -2.7025 |
| 1996-01-04 | -0.6624 | -0.3464 |

These values represent the percentage change in log prices from one day to the next.

## Visualization of Log Returns
The data was split into 95% training and 5% testing sets.

### SBIN.NS (Visualization)
Figure 1 shows the time series of log returns for State Bank of India (SBIN) over the entire period. The plot reveals periods of high volatility, particularly during market stress periods.

### INFY.NS (Visualization)
Figure 2 displays the log returns for Infosys (INFY). Similar to SBIN, the returns show volatility clustering, a common feature in financial time series.

## Model Fitting and Diagnostics
An AR-GJR-GARCH(1,1) model with Student’s t-distribution was fitted to both stocks.

### SBIN.NS (Model)
Data Split:
* Train data shape: 7097 observations
* Test data shape: 374 observations

Model Selection Criteria:
* AIC: 30744.0794
* BIC: 30853.9357

Lower AIC and BIC values indicate a better fit of the model. These values will be used to compare different model specifications.

Diagnostic Tests:
* Ljung-Box Test on Residuals (lag 20): LB statistic: 22.687, p-value: 0.3044
* Ljung-Box Test on Squared Residuals (lag 20): LB statistic: 14.449, p-value: 0.8070
* ARCH LM Test (lag 20): LM statistic: 14.6875, p-value: 0.7940

The high p-value (>0.05) indicates no significant autocorrelation in the residuals, suggesting the mean equation is adequate. The high p-value indicates no remaining ARCH effects, confirming the volatility model is well-specified. This test also confirms no remaining conditional heteroskedasticity in the residuals.

### INFY.NS (Model)
Data Split:
* Train data shape: 7097 observations
* Test data shape: 374 observations

Model Selection Criteria:
* AIC: 29512.4196
* BIC: 29622.2759

INFY has lower AIC and BIC values compared to SBIN, suggesting the model fits slightly better for INFY data.

Diagnostic Tests:
* Ljung-Box Test on Residuals (lag 20): LB statistic: 26.932, p-value: 0.1372
* Ljung-Box Test on Squared Residuals (lag 20): LB statistic: 11.662, p-value: 0.9272
* ARCH LM Test (lag 20): LM statistic: 12.6437, p-value: 0.8921

All diagnostic tests pass (p-values > 0.05), indicating the model is well-specified for INFY as well.

## Extreme Value Analysis
After fitting the GARCH model, standardized residuals were extracted for extreme value analysis using the Generalized Pareto Distribution (GPD).

### SBIN.NS EVT
Mean Residual Life Plots: Figure 3 shows the mean residual life plots for both upper and lower extremes. These plots help identify appropriate thresholds for the GPD model. A linear pattern above a certain threshold suggests the GPD is appropriate.

Parameter Stability Plots: Figure 4 displays parameter stability plots showing how the GPD shape and scale parameters change with different threshold choices. Stable (flat) regions indicate good threshold candidates.

Threshold Selection:

**Upper Threshold Selection**:
* Quantile: 0.970
* Threshold value: 1.9050
* Number of exceedances: 213
* Shape parameter: 0.1058
* Scale parameter: 0.6206
* KS p-value: 0.5947
* Anderson-Darling statistic: 0.6661

The high p-value (0.5951) indicates the GPD fits the upper tail well.

**Lower Threshold Selection**:
* Quantile: 0.025
* Threshold value: -1.9664
* Number of exceedances: 178
* Shape parameter: 0.1533
* Scale parameter: 0.5307
* KS p-value: 0.7756
* Anderson-Darling statistic: 0.4125

The very high p-value (0.7753) confirms an excellent fit of the GPD to the lower tail.

Smooth CDF Construction: A smooth cumulative distribution function (CDF) was constructed by combining GPD for the lower tail (below threshold -1.9664), Student’s t-distribution for the body (between thresholds), GPD for the upper tail (above threshold 1.9050), and logistic splice function for smooth transitions. Figure 7 shows the resulting smooth CDF across the entire range of standardized residuals.

PIT Validation: The Probability Integral Transform was applied to validate the fitted distribution. If the model is correctly specified, the PIT values should be uniformly distributed on [0,1]. Figure 8 shows the histogram of PIT values.

PIT Test Results: KS Statistic = 0.0116, p-value = 0.2974

Global PIT Optimization: Systematic search across threshold combinations to maximize KS p-value for global PIT:
* Optimal Combination: (i=9, j=9)
* KS Statistic: 0.0116
* p-value: 0.2974
* Upper Threshold: Quantile = 0.970, Threshold = 1.9050, Exceedances = 213
* Lower Threshold: Quantile = 0.025, Threshold = −1.9664, Exceedances = 178

The optimized p-value (0.2976) is greater than 0.05, indicating that the smooth CDF with optimized thresholds provides a good fit to the data. This validates the combined GPD-t-GPD model for the standardized residuals.

Summary Table for SBIN.NS:

| Metric              | Value     |
|---------------------|-----------|
| Model               | AR-GJR-GARCH-StudentsT + Composite (t-body + GPD tails) |
| LB (ε) p-value      | 0.3044    |
| LB (ε²) p-value     | 0.8070    |
| ARCH-LM p-value     | 0.7940    |
| Upper Threshold     | 1.9050    |
| Upper Exceedances   | 213       |
| GPD Upper ξ         | 0.1058    |
| GPD Upper β         | 0.6207    |
| Lower Threshold     | -1.9664   |
| Lower Exceedances   | 178       |
| GPD Lower ξ         | 0.1533    |
| GPD Lower β         | 0.5308    |
| PIT KS p-value      | 0.2976    |

### INFY.NS EVT
Mean Residual Life Plots: Figure 5 presents the mean residual life plots for INFY, used to guide threshold selection for modeling extreme values.

Parameter Stability Plots: Figure 6 shows parameter stability for INFY extremes.

Threshold Selection:

**Upper Threshold Selection**:
* Quantile: 0.935
* Threshold value: 1.4235
* Number of exceedances: 461
* Shape parameter: 0.1432
* Scale parameter: 0.5641
* KS p-value: 0.6600
* Anderson-Darling statistic: 0.8893

The high p-value indicates good GPD fit for the upper tail of INFY returns.

**Lower Threshold Selection**:
* Quantile: 0.040
* Threshold value: -1.5829
* Number of exceedances: 284
* Shape parameter: 0.3542
* Scale parameter: 0.5462
* KS p-value: 0.9623
* Anderson-Darling statistic: 0.2552

The very high p-value (0.9623) confirms excellent fit for the lower tail of INFY returns.

Smooth CDF Construction: A smooth cumulative distribution function (CDF) was constructed by combining GPD for the lower tail (below threshold -1.5829), Student’s t-distribution for the body (between thresholds), GPD for the upper tail (above threshold 1.4235), and logistic splice function for smooth transitions. Figure 9 shows the resulting smooth CDF across the entire range of standardized residuals.

PIT Validation: The Probability Integral Transform was applied to validate the fitted distribution. If the model is correctly specified, the PIT values should be uniformly distributed on [0,1]. Figure 10 shows the histogram of PIT values.

PIT Test Results: KS Statistic = 0.0125, p-value = 0.2192

Global PIT Optimization: Systematic search across threshold combinations to maximize KS p-value for global PIT:
* Optimal Combination: (i=2, j=1)
* KS Statistic: 0.0125
* p-value: 0.2197
* Upper Threshold: Quantile = 0.935, Threshold = 1.4235, Exceedances = 461
* Lower Threshold: Quantile = 0.040, Threshold = −1.5829, Exceedances = 284

The optimized p-value (0.2197) is greater than 0.05, indicating that the smooth CDF with optimized thresholds provides a good fit to the data. This validates the combined GPD-t-GPD model for the standardized residuals of INFY.

Summary Table for INFY.NS:

| Metric              | Value     |
|---------------------|-----------|
| Model               | AR-GJR-GARCH-StudentsT + Composite (t-body + GPD tails) |
| LB (ε) p-value      | 0.1372    |
| LB (ε²) p-value     | 0.9272    |
| ARCH-LM p-value     | 0.8921    |
| Upper Threshold     | 1.4235    |
| Upper Exceedances   | 461       |
| GPD Upper ξ         | 0.1432    |
| GPD Upper β         | 0.5640    |
| Lower Threshold     | -1.5829   |
| Lower Exceedances   | 284       |
| GPD Lower ξ         | 0.3543    |
| GPD Lower β         | 0.5462    |
| PIT KS p-value      | 0.2197    |

## Author
Srikrishna Das(MA24M025)

Under the guidance of: Neelesh S. Upadhye

## Date
October 3, 2025