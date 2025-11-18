# MODELING & ANOMALY DETECTION APPROACH

This project uses two main techniques:

1) Machine Learning for Time-Series Forecasting
2) Statistical Anomaly Detection (Rolling Mean + Z-Score) 

Both methods are applied on GCC climate data across multiple stations.


### 1. Machine Learning for Time-Series Forecasting

We convert the climate time-series data into a supervised ML problem.<br>
This is a modern and accurate method used in real-world weather prediction.

#### How it works:
•⁠  ⁠Input (X): Today’s weather  
•⁠  ⁠Output (y):Tomorrow’s weather  

This allows the model to learn the relationship between past weather and next-day conditions.


#### Preparing the GOLD dataset
From the cleaned Silver dataset, we generate ML features:

##### Lag Features (yesterday’s values)
•⁠  ⁠⁠ TMAX_lag1 ⁠  
•⁠  ⁠⁠ TMIN_lag1 ⁠  
•⁠  ⁠⁠ PRCP_lag1 ⁠  

These capture short-term weather momentum.

##### Rolling Averages (weekly trends)
•⁠  ⁠⁠ TMAX_7d_avg ⁠  
•⁠  ⁠⁠ TMIN_7d_avg ⁠  
•⁠  ⁠⁠ TAVG_7d_avg ⁠  

These capture gradual warming/cooling trends.

##### Targets (what we predict)
•⁠  ⁠⁠ tmax_nextday ⁠ → Tomorrow’s max temperature (regression)  
•⁠  ⁠⁠ rain_nextday ⁠ → Will it rain tomorrow? (0/1 classification)

This transforms the time-series into a clean ML table ready for training.


#### Models Used

##### 1) Regression Model — Predicting Temperature
Model: RandomForestRegressor

##### 2) Classification Model — Predicting Rain
Model: RandomForestClassifier

RandomForest is:
•⁠  ⁠Simple  
•⁠  ⁠Fast  
•⁠  ⁠Accurate  
•⁠  ⁠Easy to deploy in Azure ML  

#### Training Workflow
1.⁠ ⁠Load GOLD dataset  
2.⁠ ⁠Split into training & testing sets (80/20)  
3.⁠ ⁠Train both models on historical GCC data  
4.⁠ ⁠Evaluate accuracy  
6.⁠ ⁠Deploy to Azure ML


### 2. Anomaly Detection (Rolling Mean + Z-Score)

To identify heatwaves, cold spells, or unusual rainfall, we apply a statistical
anomaly detection approach.<br>
So we compare each day not to the whole dataset, but to recent days around it.


#### Step 1 — Rolling Mean (30 days)
For each station, we calculate a 30-day rolling:
•⁠  ⁠Mean  
•⁠  ⁠Standard deviation  

This represents the “normal” climate for that time of year.

#### Step 2 — Z-Score Calculation
We compute:
•⁠  ⁠Z = (value − rolling_mean) / rolling_std

This shows how extreme today’s temperature or rainfall is.


#### Step 3 — Flagging Anomalies
•⁠  ⁠|Z| ≥ 2 → moderate anomaly  
•⁠  ⁠|Z| ≥ 3 → strong anomaly  

Interpretation:
•⁠  ⁠High positive Z → Heatwave  
•⁠  ⁠High negative Z → Cold spell  
•⁠  ⁠High PRCP Z → Rainfall spike  

These flags are added to the dataset.


#### Step 4 — Visualization
On a line chart:
•⁠  ⁠Normal days follow the rolling mean  
•⁠  ⁠Anomalies appear far above or below the band  
•⁠  ⁠Heatwaves, cold spells, and rain spikes are visually obvious  

This method is simple, interpretable, and matches the project proposal.




# Creating the pipeline:

We created a 3-step automated data pipeline in Databricks that takes raw GCC climate data and transforms it into a clean dataset ready for machine-learning.

### 1.⁠ ⁠Raw → Bronze

Loads the raw JSON climate file from the storage account.
Converts it into Parquet format (faster + optimized).
No cleaning, no processing — just structured storage.

### 2.⁠ ⁠Bronze → Silver

Cleans and organizes the climate data.
Fixes date format (YYYYMMDD → real date).
Scales numeric values (DATA_VALUE / 10).
Pivots ELEMENT (TMAX, TMIN, TAVG, PRCP...) into columns.
Removes invalid or irrelevant fields.
Fills missing values safely using forward/backward fill.

### 3.⁠ ⁠Silver → Gold

Generates all machine-learning features.
Creates next-day prediction targets:
tmax_nextday (regression)
rain_nextday (classification)

### Adds:
lag features (yesterday’s data)
rolling averages (7-day)
date-based features (month, day, week, etc.)

Gold is fully prepared ML dataset.


#### Jobs Creation:

<img width="1307" height="774" alt="jobs creaton" src="https://github.com/user-attachments/assets/c86b76fc-b5ad-4f0a-ab63-cdcafb78795b"/>

#### Pipleline Running:

<img width="1305" height="769" alt="pipline creation" src="https://github.com/user-attachments/assets/f1e54a22-9d39-4cd2-9434-c11ec63f0a37" />

