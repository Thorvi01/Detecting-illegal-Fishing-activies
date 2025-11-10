# Detecting-illegal-Fishing-activies
Illegal, unreported, and unregulated (IUU) fishing threatens marine ecosystems and global food security.This project leverages Satellite Synthetic Aperture Radar (SAR) detections and Automatic Identification System (AIS) data to detect and predict potentially illegal fishing activities, especially when vessels turn off AIS signals (known as “dark vessels”).
Using the Global Fishing Watch (GFW) datasets, we combine geospatial, temporal, and behavioral patterns of vessels with deep learning and traditional machine learning models to identify suspicious fishing activity.

Objective:-
Detect and classify potentially illegal fishing vessels by combining:
1) SAR detections (vessel presence even when AIS is off)
2) AIS presence and behavioral data (movement patterns)
3) Marine boundaries (Exclusive Economic Zones & Marine Protected Areas)

Build an end-to-end pipeline:
From data ingestion → cleaning → feature engineering → model training → detection visualization.

Dataset
Global Fishing Watch SAR Vessel Detections:-
Provides satellite radar (SAR) detections of vessels.
Includes flags for AIS matching, fishing scores, timestamps, and geolocations.
Core source for identifying dark vessels.

Recommended Datasets
Global Fishing Watch AIS Presence:
AIS track data to infer vessel movement, speed, and transmission behavior.
Helps detect AIS gaps, loitering, and suspicious route patterns.

Exclusive Economic Zones (EEZ) shapefile
Source: Marine Regions EEZ Boundaries
Defines national maritime boundaries to identify when vessels enter foreign EEZs.

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
│   ├── 01_data_cleaning.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_model_training.ipynb
│   └── 04_visualization.ipynb
│
├── models/
│   ├── random_forest.pkl
│   └── deep_learning_model.pt
│
├── outputs/
│   ├── suspicious_predictions.csv
│   └── suspicious_map.html
│
├── src/
│   ├── preprocessing.py
│   ├── feature_engineering.py
│   ├── model.py
│   ├── utils.py
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

4. AIS Data Integration
Merge AIS data with SAR detections using time and location proximity.
Compute behavioral features:
time_since_last_ais
avg_speed_last_6h
loitering_flag (speed < 1 knot)
ais_on_off_count

5. Feature Engineering
Feature Type	   Examples
Spatial	         lat, lon, inside_eez, inside_mpa, distance_to_port
Temporal       	 hour_of_day, day_of_week, season
Behavioral	     matched (AIS), loitering_flag, avg_speed_last_6h
SAR-based	       fishing_score, backscatter_intensity

6. Labeling (Target Variable)
Define suspicious or illegal heuristically
OR
build a composite risk score

7. Model Building
Start simple → scale up.

Baseline models:
Logistic Regression
Random Forest (interpretable)

Deep Learning models:
Feedforward Neural Network for tabular data
LSTM/Transformer for AIS time-series data
CNN for raw SAR image chips (future enhancement)

8. Evaluation Metrics
Use:
Precision, Recall, F1-score, ROC-AUC
Domain-specific:
False positives per 1000 km²
Detection latency (time from intrusion → detection)

9. Visualization

10. Automation Pipeline
Schedule daily/weekly ingestion using Airflow or cron.
Automatically run preprocessing, prediction, and alert generation.
Push top suspicious detections to a dashboard or analyst review system.


Why it works?
| Component                              | Why it matters                                                          |
| -------------------------------------- | ----------------------------------------------------------------------- |
| **SAR detections**                     | Capture all vessels, even those with AIS off (“dark vessels”)           |
| **AIS behavior**                       | Reveals intent and patterns (loitering, transshipment, turning off AIS) |
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

