Creating the pipeline:

We created a 3-step automated data pipeline in Databricks that takes raw GCC climate data and transforms it into a clean dataset ready for machine-learning.

1.⁠ ⁠Raw → Bronze

Loads the raw JSON climate file from the storage account.
Converts it into Parquet format (faster + optimized).
No cleaning, no processing — just structured storage.

2.⁠ ⁠Bronze → Silver

Cleans and organizes the climate data.
Fixes date format (YYYYMMDD → real date).
Scales numeric values (DATA_VALUE / 10).
Pivots ELEMENT (TMAX, TMIN, TAVG, PRCP...) into columns.
Removes invalid or irrelevant fields.
Fills missing values safely using forward/backward fill.

3.⁠ ⁠Silver → Gold

Generates all machine-learning features.
Creates next-day prediction targets:
tmax_nextday (regression)
rain_nextday (classification)

Adds:
lag features (yesterday’s data)
rolling averages (7-day)
date-based features (month, day, week, etc.)

Gold is fully prepared ML dataset.


Jobs Creation:
<img width="1307" height="774" alt="jobs creaton" src="https://github.com/user-attachments/assets/c86b76fc-b5ad-4f0a-ab63-cdcafb78795b"/>

Pipleline Running:
<img width="1305" height="769" alt="pipline creation" src="https://github.com/user-attachments/assets/f1e54a22-9d39-4cd2-9434-c11ec63f0a37" />

