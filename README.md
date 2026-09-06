# Materials & Process Machine Learning

Applied data engineering, analytics, and machine-learning projects focused on industrial processes, energy systems, manufacturing data, and engineering decision-making.

## Projects

### 02 — Steel Energy Forecasting | End-to-End Industrial ML

[Open project folder](projects/02-steel-energy-full-project/)

Built an end-to-end forecasting workflow for 15-minute steel-industry energy demand using Python, SQL, DuckDB, pandas, scikit-learn, and XGBoost.

#### Highlights

- Audited **35,040** time-ordered industrial observations for schema, missing values, duplicates, timestamp validity, temporal gaps, categorical consistency, and numerical-domain constraints.
- Built a **Bronze → Silver → Gold** analytical pipeline in DuckDB, including timestamp standardization, reusable curated tables, SQL window functions, lag features, and rolling historical features.
- Designed a leakage-aware forecasting problem using only information available before prediction; contemporaneous electrical variables were excluded from the forecasting feature set.
- Engineered short-term history, daily lag, rolling-average, calendar, and operating-state features.
- Implemented **expanding-window walk-forward validation** across seven development months and benchmarked training-mean, persistence, previous-hour mean, and same-time-yesterday baselines.
- Compared Random Forest and XGBoost forecasting pipelines, performed feature-group ablation, and tuned hyperparameters using the same temporal validation protocol.
- Froze the selected tuned XGBoost model before opening the final December test period.
- Final held-out performance: **MAE 3.237 kWh, RMSE 6.892 kWh, R² 0.935**.
- Reduced held-out **MAE by 15.24%** and **RMSE by 28.30%** versus the 15-minute persistence benchmark.
- Performed post-hoc error analysis and model interpretation; rapid load transitions were the dominant failure mode, with **14.90 kWh MAE** in the largest 10% of transitions versus **1.94 kWh** for the remaining 90%.
- Used native XGBoost and permutation importance to quantify model reliance; the most recent 15-minute energy measurement was the strongest predictive feature.

#### Workflow

| Notebook | Stage |
| --- | --- |
| [01_problem_definition.ipynb](projects/02-steel-energy-full-project/01_problem_definition.ipynb) | Problem formulation, target definition, forecasting vs. estimation, leakage risks |
| [02_data_ingestion_quality.ipynb](projects/02-steel-energy-full-project/02_data_ingestion_quality.ipynb) | DuckDB ingestion, schema checks, nulls, duplicates, timestamp and domain validation |
| [03_data_transformation.ipynb](projects/02-steel-energy-full-project/03_data_transformation.ipynb) | Bronze/Silver/Gold transformation, SQL window functions, lag and rolling features |
| [04_forecasting_eda_validation_strategy.ipynb](projects/02-steel-energy-full-project/04_forecasting_eda_validation_strategy.ipynb) | Forecasting EDA and chronological validation design |
| [05_baseline_models.ipynb](projects/02-steel-energy-full-project/05_baseline_models.ipynb) | Persistence and historical forecasting baselines |
| [06_model_comparison.ipynb](projects/02-steel-energy-full-project/06_model_comparison.ipynb) | Candidate model-family comparison with walk-forward validation |
| [07_feature_comparison.ipynb](projects/02-steel-energy-full-project/07_feature_comparison.ipynb) | Feature-group ablation and information-value comparison |
| [08_hyperparameter_tuning.ipynb](projects/02-steel-energy-full-project/08_hyperparameter_tuning.ipynb) | Random Forest and XGBoost hyperparameter search |
| [09_final_test.ipynb](projects/02-steel-energy-full-project/09_final_test.ipynb) | One-time held-out December evaluation against persistence |
| [10_error_analysis_feature_importance.ipynb](projects/02-steel-energy-full-project/10_error_analysis_feature_importance.ipynb) | Failure-mode analysis, segmented errors, XGBoost and permutation importance |

#### Tech stack

`Python` · `pandas` · `NumPy` · `SQL` · `DuckDB` · `scikit-learn` · `XGBoost` · `Matplotlib` · `Jupyter`

#### Core skills demonstrated

Data ingestion and validation · SQL transformation · dimensional/analytical data layers · time-series feature engineering · data leakage prevention · walk-forward validation · forecasting baselines · ML pipelines · categorical preprocessing · model benchmarking · hyperparameter tuning · held-out testing · error analysis · feature importance · engineering interpretation

---

### 01 — Steel Industry Energy Consumption | Data & ML Foundations

[Open project folder](projects/01-steel-energy/)

Foundational industrial data-analysis and machine-learning project using the same steel-energy domain to develop practical skills in pandas, SQL/DuckDB, regression, preprocessing, cross-validation, model comparison, and hyperparameter tuning.

**Topics practiced:**

- pandas-based exploratory data analysis and descriptive statistics
- SQL filtering, aggregation, CTEs, joins, and window functions
- DuckDB database creation and querying
- Linear and Ridge Regression
- Decision Trees, Random Forest, Gradient Boosting, and XGBoost
- scikit-learn `Pipeline`, `ColumnTransformer`, scaling, and one-hot encoding
- cross-validation, MAE/RMSE/R², overfitting analysis, and leakage checks
- model benchmarking, hyperparameter optimization, and held-out evaluation

---

## Focus

This repository documents a progression from engineering-domain data analysis toward production-minded data and machine-learning workflows: raw-data validation, SQL transformation, feature engineering, reproducible evaluation, model development, and interpretable engineering conclusions.
