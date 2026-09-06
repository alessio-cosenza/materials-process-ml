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