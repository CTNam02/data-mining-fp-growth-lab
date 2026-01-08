# Lab 4 – Time Series Forecasting: Air Quality Prediction (PM2.5)

**Course:** Data Mining  
**Group:** Group 2  
**Dataset:** Beijing Multi-Site Air Quality Dataset (PRSA2017)  
**Station:** Aotizhongxin  
**Objective:** Forecast hourly PM2.5 concentration using historical air-quality data.

---

## 1. Dataset Description

The dataset contains hourly air quality and meteorological measurements collected from multiple monitoring stations in Beijing from **2013 to 2017**.  
In this lab, we focus on the **Aotizhongxin station** and the **PM2.5** pollutant.

- Raw dataset size: **420,768 rows × 18 columns**
- Time resolution: **Hourly**
- Target variable: **PM2.5 concentration**

---

## 2. Data Preprocessing & Exploratory Data Analysis (Q1)

### 2.1 Data Cleaning
The following preprocessing steps were applied:
- Handling missing values
- Constructing a unified `datetime` index
- Generating time-based features (hour, day, month, year)
- Creating lag features: `lag_1`, `lag_3`, `lag_24`
- Creating an air quality classification label (`aqi_class`) based on 24-hour PM2.5 averages

**Processed dataset size:**  
- **420,768 rows × 55 features**

### 2.2 Exploratory Data Analysis
Key observations:
- The PM2.5 time series exhibits strong variability with frequent pollution spikes
- Mild class imbalance is observed in AQI categories, with "Unhealthy" levels dominating
- Lag-based features introduce missing values at the beginning of the series

---

## 3. Regression Baseline Model (Q2)

### 3.1 Methodology
A **Linear Regression** model was used as a baseline to predict PM2.5 concentration at time *t+1*.

- Input features: Lag-based features (`lag_1`, `lag_3`, `lag_24`)
- Train set: 2013–2016
- Test set: From **01/01/2017**

### 3.2 Model Performance

| Metric | Value |
|------|------|
| **RMSE** | **25.32** |
| **MAE** | **12.32** |
| **R² Score** | **0.949** |

The regression model closely follows the actual PM2.5 trend and effectively captures sharp pollution spikes.

---

## 4. Time Series Forecasting with ARIMA (Q3)

### 4.1 Stationarity & Model Selection
- Stationarity was assessed using rolling statistics and the Augmented Dickey-Fuller (ADF) test
- Clear daily seasonality was observed
- ARIMA parameters *(p, d, q)* were selected based on ACF/PACF analysis and grid search using AIC

### 4.2 ARIMA Results
- Best configuration: **ARIMA(1, 0, 3)**

| Metric | Value |
|------|------|
| **RMSE** | **104.10** |
| **MAE** | **77.69** |

The ARIMA model produces smoother forecasts but fails to capture sudden PM2.5 spikes effectively.

---

## 5. Model Comparison & Conclusion

| Model | RMSE | Evaluation |
|-----|------|-----------|
| **Regression (Lag-based)** | **25.32** | Best performance, highly accurate |
| **ARIMA** | 104.10 | Lower accuracy, sensitive to noise |

### Key Findings
- Lag-based regression significantly outperforms ARIMA for short-term (1-hour ahead) PM2.5 forecasting
- PM2.5 shows strong short-term autocorrelation, which regression models exploit effectively
- Classical ARIMA struggles with noisy and highly volatile pollution data

### Future Work
- Non-linear models: Random Forest, XGBoost
- Deep learning approaches: LSTM, GRU
- Incorporating exogenous variables (SARIMAX)

---

## 6. Project Structure

```text
data/
  raw/
  processed/
notebooks/
  preprocessing_and_eda.ipynb
  regression_modelling.ipynb
  arima_forecasting.ipynb
src/
  timeseries_library.py
  regression_library.py
run_papermill.py
requirements.txt
