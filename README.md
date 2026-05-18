# 🍑 Peach Yield Prediction Modeling
### Incorporating Weather Stress Variables and Regional Effects

> **Statistical Data Analysis — Team 6**
> Shin Yu-bin · Lee Su-in · Heo Yeong-ryun



## Overview

This project predicts regional peach production in South Korea by integrating **weather stress variables** (frost damage, heat waves, rainfall, solar radiation) with **region-specific fixed effects**. A machine learning approach was adopted to capture the nonlinear, multi-factor interactions that govern agricultural yield.



## Objectives

| Goal | Description |
|------|-------------|
| Weather Factor Analysis | Analyze the relationship between weather stress variables and peach yield |
| Regional Characterization | Examine province-level production differences using regional dummy variables |
| Yield Prediction | Build an ML-based peach production forecasting model |



## Data Sources

| Source | Data |
|--------|------|
| **Korea Meteorological Administration (KMA)** | Daily/monthly temperature, precipitation, sunshine hours; peach bloom & full-bloom dates |
| **Statistics Korea (KOSTAT)** | Annual fruit production by province |
| **Rural Development Administration (RDA)** | Crop cultivation area, peach agri-technology information |

**Period:** 2015 – 2024 | **Scope:** 17 provinces/metropolitan cities



## Features

### Weather Stress Variables

| Variable | Definition |
|----------|------------|
| `frost_stress_log` | Days with min temp ≤ 0°C during bloom–full bloom period (log-transformed) |
| `heatwave_days` | Days with max temp ≥ 33°C during harvest season (Jun 15 – Sep 10) |
| `rainy_season_precip_avg` | Mean daily precipitation during monsoon season (Jun 15 – Jul 31) |
| `growing_max_temp` | Highest temperature recorded during the growing season (Mar 15 – Sep 10) |
| `avg_sunshine_hours` | Total sunshine hours during growing season (Mar 15 – Sep 10), averaged by station |
| `area_log` | Peach cultivation area in hectares (log-transformed) |

### Regional Variables
- One-hot encoded province dummy variables (17 regions) to capture fixed regional effects such as soil type, cultivar mix, and farming practices.

### Peach yield
- Since peach productions volumes vary greatly across provinces depending on the scale of cultivation, we use peach yield (ton/ha), the production divided by cultivation area, as the target variable to enable fair comparison.



## Data Preprocessing

- **Frost stress:** Bloom date to full-bloom date window; missing full-bloom dates imputed using regional average interval
- **Heat days:** Filtered by KMA standard (max temp ≥ 33°C); averaged across stations per region
- **Sejong City (2015–2018):** No observation station data → substituted with nearest Daejeon station data
- **Feature engineering:** Log transformation (`log1p`) applied to frost stress and cultivation area to reduce right-skew; sunshine hours retained as-is to avoid distortion



## Modeling

### Model Comparison

| | Random Forest (RF) | XGBoost (XGB) |
|--|--|--|
| **Training** | Parallel ensemble of decision trees | Sequential boosting to correct residuals |
| **Strengths** | Stable, resistant to overfitting | High accuracy on complex patterns |
| **Weaknesses** | Limited on highly complex patterns | Prone to overfitting; computationally heavier |

### Train / Test Split

- **Train:** 2015 – 2023
- **Test:** 2024
- Chronological split to reflect real-world forecasting conditions

### Results (2024 Test Set)

| Model | MAE | RMSE | MAE% | RMSE% |
|-------|-----|------|-------|-------|
| **Random Forest** | 573.32 | 1183.48 | 4.71% | 9.72% |
| XGBoost | 1173.21 | 2881.15 | 9.64% | 23.66% |

✅ **Random Forest** selected as the final model due to lower error on both metrics.



## Key Findings

- **Cultivation area** is the single most important predictor of production volume.
- **Heat days, growing-season max temperature, monsoon rainfall, and sunshine hours** each contribute at a moderate level.
- **Frost stress** has lower measured importance due to its infrequent occurrence.
- **Regional dummy variables** meaningfully improve prediction by encoding local factors (soil, cultivar, farming practices).
- Production is governed by a **complex interaction** of area, weather, and regional characteristics — not any single factor.



## Limitations

- **Information loss** from aggregating daily/station data to annual province-level summaries
- **Small dataset size** (~160 training samples) may limit Random Forest's generalization capacity
- **Non-weather factors** (pest/disease pressure, labor, policy subsidies) are excluded
- **Single test year (2024)** makes it difficult to robustly evaluate generalization performance



## Future Work

- Expand dataset size and time range for more stable model training
- Apply **XAI methods (e.g., SHAP)** to interpret individual feature contributions
- Incorporate region-specific attributes: soil type, elevation, slope, cultivar distribution
- Adopt a **rolling multi-year test strategy** for more realistic performance evaluation



## Reference

Kim Hwa-seok & Lee Chong-ho (2025). *Climate Change-Based Regression Model for Apple Yield Prediction in Cheongsong, Gyeongbuk Province.* Journal of Practical Engineering Education, 17(5), 825–833.
