# ScottsdaleEZ Prediction Pipeline Summary

Part of the [AI Business Portfolio](../README.md).

This file summarizes the ScottsdaleEZ predictive pipeline used to forecast next-month service request volume for the City of Scottsdale by ZIP code and request type.

## Project Scope

The public poster summarized the dataset as approximately 206K post-launch service requests, with approximately 139K cases used for modeling after exclusions, covering July 2022 through January 2026, 256 request types, and 31 workgroups.

The pipeline was designed for municipal decision support: early warning, operational planning, dashboard refresh, and executive review.

## Primary Outputs

- next-month predicted volume by ZIP code and request type
- next-month top concerns by ZIP code
- emerging issue flags
- issue relationship rules
- clustering-based issue pattern profiles
- Power BI-ready operational tables
- seasonal comparison tables
- run-level reproducibility artifacts

## Pipeline Flow

ScottsdaleEZ CSV Export → Filter + Feature Engineering → LightGBM Forecast → SageMaker + S3 → Power BI Dashboard

## Stage Descriptions

### Stage 1 - Data Ingestion

Reads CSV/XLSX source data, standardizes columns, parses dates to UTC, standardizes ZIP codes, and applies an optional inclusive end-of-day data cutoff so incomplete final months can be excluded consistently.

### Stage 2 - QA / Validation

Hard-fails on:

- missing required columns
- duplicate request IDs
- null ZIPs greater than 20%
- excluded request types still remaining after filtering
- scooter-related request types still remaining after filtering

Warnings include:

- null closed dates
- negative resolution times, clipped to zero
- partial final month

Fixed exclusions include airport and scooter-related categories that distorted the municipal demand signal.

### Stage 3 - Feature Engineering

Builds a monthly ZIP × request-type panel for the top citywide request types and creates lag, rolling, seasonal, and operational context features used in next-month forecasting.

### Stage 4 - Prediction Model

Uses LightGBM to predict next-month volume for top citywide request types by ZIP code.

The final v1 model achieved a held-out WMAPE of 0.451, improving on the original XGBoost baseline of 0.476. See [Model Tuning Summary](MODEL_TUNING.md) for the full model-selection log.

The final model retained fixed production regularization settings because they generalized better on the held-out future test period than the CV-tuned variant.

### Stage 5 - Issue Relationship Logic

Runs association-rule mining on the highest-volume request types first to preserve runtime stability, memory efficiency, and interpretability.

Outputs include:

- general issue relationship rules
- explicit A + B ⇒ C rules

### Stage 6 - Clustering

Clusters ZIP-month issue composition patterns using normalized request-type shares. This is separate from forecasting and is used for pattern discovery and operational interpretation.

### Stage 7 - Operational Tables

Produces backend tables for Power BI, including current-month top concerns by ZIP and observed operational summary tables for city review.

### Stage 8 - Seasonal Comparison

Builds year-over-year and seasonal comparison tables for operational context.

### Stage 9 - Reproducibility

Writes run-level artifacts used to support traceability, validation, and dashboard refresh.

## Output Files

Core outputs:

- `predicted_monthly_volume_by_zip_requesttype.csv`
- `predicted_top10_concerns_by_zip.csv`
- `current_month_top10_concerns_by_zip.csv`
- `issue_relationship_rules.csv`
- `issue_ab_implies_c_rules.csv`
- `zip_month_cluster_assignments.csv`
- `cluster_summary_profiles.csv`
- `cluster_centers_requesttype_share.csv`
- `seasonal_comparison_zip_requesttype.csv`
- `monthly_volume_by_workgroup_zip.csv`
- `resolution_time_bins_by_workgroup.csv`
- `staff_vs_citizen_monthly_volume.csv`
- `sla_performance_by_workgroup.csv`

Reproducibility / diagnostics:

- `qa_summary.csv`
- `qa_summary.json`
- `run_config.json`
- `lgbm_feature_importance.csv`

## Modeling Notes

- Forecasting unit: ZIP code × request type × month
- Target: next-month service request volume
- Top request types are selected citywide to avoid unstable long-tail forecasting
- Airport and scooter-related requests are excluded from predictive modeling
- Clustering is based on normalized request-type shares, not raw counts
- Association rules are intentionally limited to the highest-volume request types first for stability, interpretability, and runtime efficiency

## Known Limitations

- Missing ZIP values reduce geographic coverage.
- Sparse ZIP × request-type combinations limit exact forecast precision.
- Prediction outputs support prioritization and early warning, not deterministic volume prediction.
- ZIP-level results should be interpreted as directional operational signals, not perfect neighborhood demand maps.

## Deployment Note

The pipeline was designed for batch deployment with stable input/output schemas and reproducible monthly outputs. Public portfolio materials do not include raw data, model artifacts, cloud paths, credentials, or private operational materials.

## Related Materials

- [Model Tuning Summary](MODEL_TUNING.md)
- [Redacted Public Poster](../02_scottsdaleez_powerbi_dashboard/scottsdaleez_public_poster_redacted.pdf)
