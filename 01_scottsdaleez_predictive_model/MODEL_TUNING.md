# Model Tuning Summary — ScottsdaleEZ v1

Selection log for the ScottsdaleEZ next-month volume forecaster. Each row reflects a change tested against held-out WMAPE on the same evaluation window. "Kept" entries compose the final production model; "Reverted" entries are recorded for traceability.

## Baseline
XGBoost with default parameters — **WMAPE 0.476**

## Experiments

| # | Change | Held-out WMAPE | Decision |
|---|---|---|---|
| 1 | XGBoost `count:poisson` objective | 0.505 | Reverted |
| 2 | XGBoost `reg:tweedie` objective | 0.612 | Reverted |
| 3 | Request-family split models (4-family, 2-family, family-as-feature) | > 0.50 | Reverted |
| 4 | Added time-series features (same-month-last-year, EWMA, rolling change rate) | 0.493 | Reverted |
| 5 | Blended-shrinkage fallback for sparse ZIP × request-type cells | — | **Kept** (stability, not WMAPE) |
| 6 | XGBoost light hyperparameter tuning | 0.456 | Kept (interim best) |
| 7 | LightGBM drop-in swap | 0.451 | **Kept** |
| 8 | LightGBM CV-tuned hyperparameters | 0.459 | Reverted (overfit to CV folds) |

## Final Production Model
LightGBM with fixed production regularization parameters — **WMAPE 0.451** (5.3% relative improvement over baseline).

## Notes
- Blended-shrinkage fallback was retained as an operational safeguard, not a headline WMAPE driver. It improves output stability and interpretability on rare ZIP × request-type pairs where pure model predictions are noisy.
- CV-tuned LightGBM was rejected because gains on cross-validation folds did not generalize to the held-out test set. Fixed regularization parameters outperformed the tuned variant out of sample.
- All experiments evaluated against the same held-out window and the same evaluation cohort to keep comparisons valid.
