# ScottsdaleEZ Predictive Model

Public portfolio folder for the ScottsdaleEZ predictive analytics capstone.

This project developed a municipal decision-support pipeline for forecasting next-month ScottsdaleEZ service request volume by ZIP code and request type. The goal was to support early warning, operational planning, and executive review rather than exact deterministic prediction.

## Included Files

- [Pipeline Summary](PIPELINE_SUMMARY.md) — overview of the prediction pipeline, outputs, QA logic, limitations, and deployment framing.
- [Model Tuning Summary](MODEL_TUNING.md) — model-selection log comparing tested approaches and held-out WMAPE results.

## Model Summary

The final v1 model uses LightGBM with fixed production regularization settings. It achieved a held-out WMAPE of 0.451, improving on the original XGBoost baseline of 0.476.

The final approach retained a blended-shrinkage fallback for sparse ZIP × request-type cells to improve stability and interpretability in low-volume cases.

## Business Use

The model outputs are intended for:

- Ranking predicted service concerns by ZIP code
- Identifying possible next-month hotspots
- Supporting early operational review
- Informing staff planning and dashboard refreshes
- Helping nontechnical users interpret forecast signals

## Important Interpretation Note

The predictions should be used for prioritization and early warning, not exact-volume prediction. ZIP-level outputs are directional decision-support signals and should be interpreted with known geographic and data-quality limitations.

## Public Portfolio Note

This folder includes sanitized summary documentation only. It does not include raw city data, full model outputs, model artifacts, S3 paths, notebooks, API keys, or private operational materials.
