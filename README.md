# Distributed Analysis of Chicago Crime Data with Apache Spark

Coursework for **7002 Big Data Technologies – Practical 1**.

This project is an end-to-end PySpark workflow on **7.78 million** City of Chicago crime records (2001 to 2023, 1.8 GB CSV):

1. **Task 1 – ETL pipeline:** schema enforcement, data-quality rules, weather and socio-economic enrichment, and Parquet partitioned by year and district.
2. **Task 2 – Exploratory analysis in PySpark SQL:** temporal and spatial patterns, rolling crime rates per community area, weather effects, and broadcast joins.
3. **Task 3 – Spatio-temporal clustering:** K-Means on location and time of day, with the elbow and silhouette methods.
4. **Task 4 – Distributed predictive modelling:** GBT and Random Forest classifiers for arrest likelihood and crime type, with an analysis of data skew.

## Repository contents

| File | Description |
|---|---|
| `Chicago_Crime_Spark_Practical_corrected.ipynb` | PySpark notebook with all four tasks, including saved outputs and figures |

The datasets are **not** included because of their size. See [Data](#data) below.

## Key results

| Area | Result |
|---|---|
| Storage | CSV 1,838 MB → Parquet 388 MB (**4.7× smaller**); a partition-pruned query took 0.38 s against 2.19 s for a full scan |
| Data quality | 86,966 coordinates and 613,547 community areas imputed (with flags); 322,707 placeholder timestamps flagged |
| Trends | Crime fell from 486,802 incidents (2002) to 208,571 (2021); the arrest rate fell from 29.1 % to 11.5 % |
| Socio-economics | Violent crime vs poverty: Spearman ρ = 0.53; arrest rate vs poverty: ρ = 0.77 |
| Weather | Temperature anomaly vs violent-crime anomaly r = 0.39; extreme-cold days −11 % crime (p < 0.001) |
| Broadcast join | 2.7 s against 9.1 s for a sort-merge join (**3.4× faster**) |
| Clustering | K = 7 (elbow), silhouette 0.454; every zone spans 7–10 police districts (ARI with districts 0.07) |
| Arrest model | GBT ROC-AUC **0.858**, PR-AUC 0.598, F1 0.543 at a tuned threshold (test year 2022) |
| Crime-type model | Macro-F1 0.164 → 0.211 with class weights; the weights trade precision for minority-class recall |

## Data

| Role | Dataset |
|---|---|
| Primary | [Crimes – 2001 to Present](https://www.kaggle.com/datasets/utkarshx27/crimes-2001-to-present) (Chicago Data Portal) |
| Secondary | [Socio-economic indicators in Chicago 2008–2012](https://www.kaggle.com/datasets/umermjd11/socioeconomic-indicators-in-chicago-2008-2012) |
| Weather | [The Weather Dataset](https://www.kaggle.com/datasets/guillemservera/global-daily-climate-data) (Meteostat daily data) |

Download the files and place them like this:

```
data/raw/Crimes_-_2001_to_Present.csv
data/raw/socioeconomic.csv
data/raw/daily_weather.parquet
data/raw/cities.csv
```

The notebook writes the curated Parquet tables to `data/curated/`.

> **Note:** the weather dataset has no station inside Chicago. The pipeline therefore uses Springfield, IL (station 72439) as a regional proxy. This is a limitation: rain and snow effects are likely underestimated.

## How to run

**Requirements:** Python 3.10+, Java 17, and PySpark 4.x.

```bash
pip install pyspark "pandas<3" pyarrow matplotlib seaborn scipy scikit-learn
```

**Windows only:** download `winutils.exe` and `hadoop.dll` for Hadoop 3.x. Then set `HADOOP_HOME` in the notebook's setup cell to the folder that contains `bin\winutils.exe`.

Open the notebook and run the cells in order. The setup cell uses `local[4]` and 6 GB of driver memory; adjust `DRIVER_MEM` to suit your machine.

## Tech stack

PySpark (Spark SQL, MLlib), Parquet, pandas, matplotlib, seaborn, SciPy, scikit-learn.
