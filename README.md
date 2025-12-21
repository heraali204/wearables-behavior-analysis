# 📊 Wearables Data Science Project  
**From Raw Multi-Vendor Data → Clean Integration → Insightful Modeling**

## Overview
This project simulates a real-world data science workflow using **messy, multi-vendor wearable data** (Fitbit, Apple Health, WHOOP).

The goal was **not** to maximize predictive accuracy, but to demonstrate strong judgment in:
- data cleaning and validation  
- handling heterogeneous data sources  
- preserving missingness  
- avoiding silent data loss  
- drawing honest, defensible conclusions  

## Data Sources
Synthetic raw datasets were generated to mimic real wearable exports:

### Fitbit
- Daily activity  
- Sleep (multi-segment)  
- Resting heart rate  

### Apple Health
- Event-level measurements (steps, active energy, heart rate, sleep)

### WHOOP
- Daily cycles (strain, recovery, HRV, sleep performance)

Each vendor had **different grains, schemas, and coverage**, intentionally reflecting real-world challenges.

## Project Structure
- 01_data_audit.ipynb
- 02_clean_fitbit_activity.ipynb
- 03_clean_fitbit_sleep.ipynb
- 04_clean_fitbit_resting_hr.ipynb
- 05_integrate_fitbit_user_day.ipynb
- 06_clean_apple_health.ipynb
- 07_clean_whoop.ipynb
- 08_integrate_cross_vendor.ipynb
- 09_eda.ipynb
- 10_modeling.ipynb

---

## Key Design Decisions

### 🔹 Grain-first approach
- Every table was explicitly defined by grain (e.g., user-day, event-level).
- Deduplication was performed **before** integration.

### 🔹 Global user–day spine
- Instead of choosing a “base device,” a **union spine of all `(user_id, date)`** pairs was created.
- This prevented silent data loss and preserved vendor-specific coverage.

### 🔹 Missingness preserved
- No cross-device imputation.
- No forced alignment.
- Missing values are treated as **information**, not errors.

### 🔹 Apple Health handling
- Apple metrics were sparse and non-overlapping by day.
- Sleep intervals appeared as full-day artifacts (1440 minutes).
- Result: Apple data retained for completeness, **excluded from modeling** based on evidence.

## Exploratory Data Analysis (EDA)

### Coverage Findings
- Fitbit provided the most consistent daily activity coverage.
- WHOOP data was intermittent but internally consistent.
- Apple data was sparse and episodic.

This directly motivated **vendor-specific modeling**.

### Cross-device alignment
- Fitbit and WHOOP rarely recorded on the same calendar day.
- Naïve feature fusion would have introduced bias or dropped data.

## Modeling

### Modeling Question
> Can same-day WHOOP strain, HRV, and sleep performance explain WHOOP recovery?

### Dataset
- 6,907 complete WHOOP user-days  
- Features:
  - `whoop_strain`
  - `whoop_hrv`
  - `whoop_sleep_score`
- Target:
  - `whoop_recovery`

### Models Tried
- Baseline (mean predictor)
- Linear Regression
- Random Forest Regressor

### Evaluation Metric
- **Mean Absolute Error (MAE)**

## Results

| Model | MAE |
|------|-----|
| Baseline (mean) | ~9.97 |
| Linear Regression | ~10.03 |
| Random Forest | ~10.07 |

No model meaningfully outperformed the baseline.

## Interpretation & Conclusion

The inability of both linear and non-linear models to outperform a baseline suggests that **WHOOP recovery is weakly predictable from same-day strain, HRV, and sleep alone**.

This aligns with physiological intuition:
- Recovery is influenced by **temporal trends**, cumulative load, and latent signals not present in daily summaries.
- WHOOP’s proprietary recovery score likely incorporates information beyond observable daily metrics.

Rather than chasing marginal gains through overfitting, the project prioritised **honest conclusions grounded in data evidence**.

## What This Project Demonstrates
- Strong data modeling judgment  
- Real-world SQL + analytics engineering skills  
- Comfort with messy, heterogeneous data  
- Ability to stop modeling when signal is weak  
- Clear communication of limitations and next steps  

## Future Work
If extended further, this project could explore:
- Lagged and rolling features (e.g., 7-day strain averages)
- Vendor-specific temporal models
- Sequence-based approaches
- Comparative modeling across vendors separately

## Final Note
This project intentionally prioritises **process and reasoning over leaderboard performance**.  
It reflects how real data science work is done when data is imperfect — which is almost always.
