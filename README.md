# GCC Climate Analysis – Full Pipeline, Modeling, and Anomaly Detection

This project performs end-to-end climate data engineering, forecasting, and anomaly detection for GCC countries using Databricks, Spark, and MLflow.

It includes:

* Complete ETL pipeline (Raw → Bronze → Silver → Gold)
* Feature engineering for time-series forecasting
* Machine learning models for temperature and rain prediction
* Climate visualization per country and per station
* Model logging, registration, and prediction workflows

# ETL Pipeline (Raw → Bronze → Silver → Gold)

## 1. Raw → Bronze

* Ingest raw JSON climate files (GHCN-Daily)
* Store as Parquet
* No transformations

## 2. Bronze → Silver

* Clean and standardize weather rows
* Parse dates and convert types
* Pivot daily measurements (TMAX, TMIN, PRCP, etc.)
* Handle missing values
* Ensure one clean row per station per day

## 3. Silver → Gold

Adds all ML feature engineering.

### A. Base Features

* PRCP
* TAVG
* TMAX
* TMIN

### B. Prediction Targets

* tmax_nextday
* rain_nextday

### C. Lag Features

* TMAX_lag1, TMAX_lag2, TMAX_lag3
* TMIN_lag1
* TAVG_lag1
* PRCP_lag1, PRCP_lag2

### D. Rolling Window Features

* TMAX_7d_avg, TMIN_7d_avg, TAVG_7d_avg
* TMAX_30d_avg, TMIN_30d_avg, TAVG_30d_avg
* PRCP_30d_sum, PRCP_30d_avg
* TMAX_30d_std

### E. Seasonal and Date Encodings

* day_of_week
* month
* day_of_year
* sin_day_of_year
* cos_day_of_year

# Machine Learning

Two ML models are trained on the Gold dataset.

## 1. Regression Model: Predict Next-Day Temperature

* Algorithm: RandomForestRegressor
* Predicts tmax_nextday
* Evaluated using MAE, RMSE, R2
* Achieved strong predictive performance

## 2. Classification Model: Predict Rain

Rain in GCC is rare, making the dataset highly imbalanced.

Models and methods tested:

* Baseline RandomForestClassifier
* Class-weight balancing
* Oversampling
* XGBoost with imbalance control
* Probability threshold tuning

Final model: XGBoostClassifier with threshold = 0.3.
This improved rain detection and recall for rare rainfall signals.

# Training Workflow

1. Load Gold dataset
2. Split chronologically into training and testing sets
3. Train regression and classification models
4. Evaluate using all main performance metrics
5. Log runs and models using MLflow
6. Register both models with valid signatures
7. Load registered versions for prediction

# Climate Visualization

Time-series visualizations were created for the GCC:

* Qatar
* UAE
* Saudi Arabia
* Oman
* Bahrain
* Kuwait

Plots are generated per station and as country-level averages by grouping IDs and ordering by date.

# MLflow Model Management

### Logged

* Parameters
* Metrics
* Feature importances
* Models with signatures (sklearn and XGBoost)

### Registered

* TMAX_Regression_Model
* Rain_Classifier_Model

### Loaded

Both models tested successfully for inference.

# Batch Prediction

After registration:

1. Load Gold dataset
2. Load registered model
3. Produce predictions for all rows
4. Save outputs to:

```
gold/predictions/
```

5. Use predictions for dashboards or extended analysis
