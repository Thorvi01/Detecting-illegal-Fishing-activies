# Detecting-illegal-Fishing-activies
Illegal, unreported, and unregulated (IUU) fishing threatens marine ecosystems and global food security.This project leverages Satellite Synthetic Aperture Radar (SAR) detections, Marine Protected Areas (MPA) and Economic Exclusivity Zone (EEZ) data to detect and predict potentially illegal fishing activities, especially when vessels turn off AIS signals (known as “dark vessels”).
Using the Global Fishing Watch (GFW) datasets, we combine geospatial, temporal, and behavioral patterns of vessels with deep learning and traditional machine learning models to identify suspicious fishing activity.

Objective:-
Detect and classify potentially illegal fishing vessels by combining:
1) SAR detections (vessel presence even when AIS is off)
2) Marine boundaries (Exclusive Economic Zones & Marine Protected Areas)

Build an end-to-end pipeline:
From data ingestion → cleaning → feature engineering → model training → detection visualization.

Dataset
Global Fishing Watch SAR Vessel Detections:-
Provides satellite radar (SAR) detections of vessels.
Core source for identifying dark vessels.
Link: https://globalfishingwatch.org/data-download/datasets/public-sar-vessel-detections%3Av20231026

Recommended Datasets
Global Fishing Watch AIS Presence:
Helps detect AIS gaps, loitering, and suspicious route patterns.
Link: https://globalfishingwatch.org/platform-update/global-ais-vessel-presence-dataset/

Exclusive Economic Zones (EEZ) shapefile
Source: Marine Regions EEZ Boundaries
Defines national maritime boundaries to identify when vessels enter foreign EEZs.
Link: https://www.marineregions.org/downloads.php

Marine Protected Areas (MPA) shapefile
Identifies restricted fishing zones; presence inside an MPA is a strong indicator of illegal activity.

Struture:
illegal-fishing-detection/
│
├── data/
│   ├── raw/                # raw downloaded datasets
│   ├── processed/          # cleaned and feature-engineered datasets
│
├── notebooks/
│   ├── DS_mk2.ipynb
│
├── models/
│   ├── random_forest_sar_fishing.pkl
│   └── xgboost_sar_fishing.pkl
│
├── outputs/
│   ├── suspicious_predictions.csv
│   └── suspicious_map.html
│
│
├── README.md
└── requirements.txt

Step-by-Step Implementation

1. Data Acquisition
Download GFW SAR detections and AIS presence datasets for your region and timeframe.
Download EEZ and MPA shapefiles from MarineRegions.
Save all raw files under /data/raw/.

2. Data Inspection & Cleaning
Convert timestamps to UTC.
Drop invalid/missing coordinates.
Normalize the fishing_score to a [0,1] range.
Remove duplicates and outliers.

3. Spatial Integration
Convert SAR detections to GeoDataFrame.
Perform spatial joins:
SAR detections → EEZ boundaries (mark inside/outside)
SAR detections → MPA polygons (mark protected areas)
Flag each detection as inside_eez and/or inside_mpa.

4. Feature Engineering
Feature Type	   Examples
Spatial	         lat, lon, inside_eez, inside_mpa, distance_to_port
Temporal       	 hour_of_day, day_of_week, season
Behavioral	     matched (AIS), loitering_flag, avg_speed_last_6h
SAR-based	       fishing_score, backscatter_intensity

5. Labeling (Target Variable)
Define suspicious or illegal heuristically
OR
build a composite risk score

6. Model Building
Start simple → scale up.

Baseline models:
Logistic Regression
Random Forest (interpretable)

Deep Learning models:
Feedforward Neural Network for tabular data
LSTM/Transformer for AIS time-series data
CNN for raw SAR image chips (future enhancement)

7. Evaluation Metrics
Use:
Precision, Recall, F1-score, ROC-AUC
Domain-specific:
False positives per 1000 km²
Detection latency (time from intrusion → detection)

8. Visualization

9. Automation Pipeline
Schedule daily/weekly ingestion using Airflow or cron.
Automatically run preprocessing, prediction, and alert generation.
Push top suspicious detections to a dashboard or analyst review system.

10. How to run the file: Download all datasets provided along with the DS_mk2.ipnyb file provided.
Load the dataset and the ipynb file to your local machine or Colab and run the ipynb after ensuring the dataset file locations are coded properly.


Why it works?
| Component                              | Why it matters                                                          |
| -------------------------------------- | ----------------------------------------------------------------------- |
| **SAR detections**                     | Capture all vessels, even those with AIS off (“dark vessels”)           |
| **EEZ & MPA zones**                    | Adds legal/geopolitical context to detections                           |
| **Heuristic + ML/Deep Learning combo** | Balances explainability and performance                                 |
| **Human-in-the-loop**                  | Validates suspicious detections and improves retraining quality         |

Tech Stack
Languages: Python 3.9+
Libraries:
pandas, numpy, geopandas, shapely, scikit-learn, tensorflow/pytorch, folium, matplotlib, plotly
Tools: Jupyter Notebook / Google Colab
Visualization: Folium, Streamlit (optional dashboard)
Version Control: Git & GitHub

