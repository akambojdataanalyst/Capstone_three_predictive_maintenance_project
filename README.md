# Capstone Three — Predictive Maintenance with NASA C-MAPSS FD001

## Business problem
Estimate remaining useful life (RUL) from sensor histories to support a hypothetical maintenance team's inspection and scheduling decisions. The dataset contains **simulated** turbofan engine degradation, not real manufacturing equipment.

## Data source
[NASA Prognostics Center of Excellence — C-MAPSS](https://www.nasa.gov/intelligent-systems-division/discovery-and-systems-health/pcoe/pcoe-data-set-repository/). Download the CMAPSS archive and place `train_FD001.txt`, `test_FD001.txt`, and `RUL_FD001.txt` in `data/`. NASA's usage/license conditions apply. Raw data are not included in this repository.

## Reproduce
1. `python -m pip install -r requirements.txt`
2. Launch Jupyter with `jupyter notebook`.
3. Run **all cells** in `notebooks/01_preprocessing.ipynb`.
4. Run **all cells** in `notebooks/02_modeling.ipynb`.
5. Review the generated `outputs/` artifacts. **Results are not claimed until the dataset is downloaded and notebooks are executed.**

## Methodology
- Audit missing values, duplicates, feature types; exclude constant predictors using development training data only.
- No one-hot encoding is necessary for FD001 numeric predictors; engine IDs are grouping keys, not features.
- Standardize predictors inside sklearn pipelines, fit on training data only.
- Group-aware 80/20 development split prevents sharing engine trajectories across training/validation.
- Compute training RUL as each engine's maximum cycle minus current cycle; use NASA-provided test RUL labels on the last observed test cycle.
- Compare Ridge, Random Forest, and Histogram Gradient Boosting; tune the leading family with grouped CV; choose by validation MAE.
- Report held-out NASA test MAE, RMSE, R², save test predictions and trained model.

## Repository layout
- `notebooks/01_preprocessing.ipynb` — audit, RUL, engine-grouped split, scaling demonstration, exports.
- `notebooks/02_modeling.ipynb` — three models, grouped hyperparameter tuning, final test evaluation, plots.
- `data/README.md` — data acquisition instructions.
- `outputs/` — generated data and metrics after execution.

## Interpretation and caveats
RUL is a continuous regression target. Observations are time ordered within engines. Independent NASA test units are held out for final evaluation. Validation contains all cycles while the NASA test metric is evaluated at each test engine's final observed cycle; the two estimates therefore cover different sampling conditions. Predicted RUL is not a calibrated safety recommendation, and no measured downtime savings are claimed.
