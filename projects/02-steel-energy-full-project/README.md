# Steel Industry Energy Forecasting — End-to-End Data & ML Project

End-to-end industrial forecasting project built with **Python, SQL, DuckDB, pandas, scikit-learn, and XGBoost**.

The objective was to predict steel-industry energy consumption **15 minutes ahead** while preserving realistic prediction-time constraints and preventing temporal data leakage.

## Headline Results

Final evaluation was performed once on a completely held-out future month: **December 2018**.

| Metric | Tuned XGBoost | 15-min Persistence |
|---|---:|---:|
| MAE | **3.237 kWh** | 3.819 kWh |
| RMSE | **6.892 kWh** | 9.612 kWh |
| R² | **0.935** | 0.873 |

The final model achieved:

- **15.24% lower MAE** than the persistence benchmark
- **28.30% lower RMSE**
- **R² = 0.935** on unseen future data
- median absolute error of approximately **0.62 kWh**

## Project Architecture

```text
Raw CSV
   ↓
Data ingestion + quality validation
   ↓
BRONZE / raw DuckDB table
   ↓
SQL transformation
   ↓
SILVER / cleaned reusable table
   ↓
SQL window functions + temporal feature engineering
   ↓
GOLD / forecasting feature table
   ↓
Forecasting EDA + validation design
   ↓
Naive forecasting baselines
   ↓
Random Forest + XGBoost comparison
   ↓
Feature-group ablation
   ↓
Hyperparameter tuning
   ↓
Model configuration frozen
   ↓
Untouched December test
   ↓
Error analysis + feature importance
```

## Data Engineering

The source dataset contains **35,040 observations** covering a full year at 15-minute resolution.

The workflow includes:

- schema and datatype validation
- missing-value and duplicate checks
- timestamp parsing and temporal-gap validation
- categorical and numerical-domain checks
- Bronze / Silver / Gold data modeling in DuckDB
- SQL joins, aggregations, CTEs, and window functions
- leakage-safe lag and rolling-window feature generation

The final forecasting feature table includes recent energy history, daily lag, rolling historical usage, calendar variables, and operating-state information.

Contemporaneous electrical variables were intentionally excluded from the forecasting feature set because their availability before prediction could not be guaranteed.

## Forecast Validation Strategy

Random train/test splitting was avoided because it would violate the temporal structure of the forecasting problem.

Model development used **expanding-window walk-forward validation** across seven development months:

```text
Jan–Apr → May
Jan–May → Jun
Jan–Jun → Jul
Jan–Jul → Aug
Jan–Aug → Sep
Jan–Sep → Oct
Jan–Oct → Nov
```

December remained completely untouched during baseline comparison, model-family selection, feature comparison, and hyperparameter optimization.

Only after the final model configuration was frozen was December used for final evaluation.

## Baselines

Machine-learning performance was compared against multiple forecasting baselines:

- historical training mean
- 15-minute persistence
- same time one day earlier
- previous-hour mean

The strongest development baseline was **15-minute persistence**, with mean walk-forward MAE of approximately **5.99 kWh**.

## Machine Learning

Candidate models included **Random Forest** and **XGBoost**.

Preprocessing and modeling were implemented with:

- `Pipeline`
- `ColumnTransformer`
- `OneHotEncoder`

The same temporal folds were preserved across model comparison, feature ablation, and hyperparameter tuning.

The selected tuned XGBoost model achieved mean development walk-forward performance of approximately:

- **MAE: 4.84 kWh**
- **RMSE: 9.95 kWh**
- **R²: 0.901**

The frozen model was then retrained on all usable January–November data and evaluated once on December.

## Error Analysis

Post-hoc analysis showed that the strongest remaining failure mode was **rapid load transition**.

The correlation between 15-minute transition magnitude and absolute forecast error was:

**r = 0.693**

The largest 10% of transitions produced:

- **MAE: 14.90 kWh**
- **RMSE: 18.10 kWh**

compared with **1.94 kWh MAE** for the remaining 90% of observations.

The model therefore performs particularly well during stable operation but has difficulty anticipating abrupt process-state changes.

## Model Interpretation

Permutation importance showed that recent operating history was the dominant predictive signal.

Shuffling `usage_15min_ago` increased held-out MAE by approximately **23.15 kWh**.

Other important predictors included:

- previous-hour average energy usage
- hour of day
- load type
- usage one hour earlier
- same-time-previous-day usage

The analysis suggests that additional leading process indicators—such as machine state, equipment setpoints, production schedules, throughput, or upstream process signals—could improve forecasts during abrupt operating transitions.

## Notebook Workflow

| # | Notebook | Purpose |
|---|---|---|
| 01 | [Problem Definition](01_problem_definition.ipynb) | Define the prediction problem, target, horizon, metrics, and leakage risks |
| 02 | [Data Ingestion & Quality](02_data_ingestion_quality.ipynb) | Load raw data into DuckDB and validate data quality |
| 03 | [SQL Transformation](03_data_transformation.ipynb) | Build Silver/Gold tables and engineer temporal features |
| 04 | [Forecasting EDA & Validation](04_forecasting_eda_validation_strategy.ipynb) | Analyze temporal structure and design chronological validation |
| 05 | [Baseline Models](05_baseline_models.ipynb) | Benchmark naive forecasting strategies |
| 06 | [Model Comparison](06_model_comparison.ipynb) | Compare candidate ML model families |
| 07 | [Feature Comparison](07_feature_comparison.ipynb) | Perform feature-group ablation |
| 08 | [Hyperparameter Tuning](08_hyperparameter_tuning.ipynb) | Tune model configurations under temporal validation |
| 09 | [Final Held-Out Test](09_final_test.ipynb) | Evaluate the frozen final model once on December |
| 10 | [Error Analysis & Feature Importance](10_error_analysis_feature_importance.ipynb) | Analyze failure modes and model reliance |

## Tech Stack

**Python · SQL · DuckDB · pandas · NumPy · scikit-learn · XGBoost · Matplotlib**

Core methods:

- ETL / analytical data pipelines
- data-quality validation
- Bronze / Silver / Gold data modeling
- SQL window functions
- time-series feature engineering
- expanding-window cross-validation
- model benchmarking
- feature ablation
- hyperparameter tuning
- held-out testing
- permutation importance
- error and failure-mode analysis

## Reproducibility

The raw dataset and generated DuckDB database are intentionally not versioned in the repository.

To reproduce the workflow:

1. Download the Steel Industry Energy Consumption dataset.
2. Place `Steel_industry_data.csv` in this project directory.
3. Run notebooks `01` through `10` sequentially.

Notebook 02 creates the local DuckDB database, and subsequent notebooks build the transformed analytical tables and forecasting workflow.

## Key Engineering Takeaway

The strongest result came from the complete workflow—not model complexity alone:

**data-quality validation → realistic problem formulation → leakage-safe temporal features → chronological validation → baseline benchmarking → model selection → untouched future testing → failure-mode analysis**

The final system generalized strongly to the held-out future period while revealing a clear operational limitation: abrupt changes in energy demand require leading process information that is not fully captured by historical energy measurements alone.
