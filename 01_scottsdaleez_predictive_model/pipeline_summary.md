# ScottsdaleEZ v1 Prediction Pipeline README

## Purpose
This pipeline builds a maintainable municipal decision-support workflow for ScottsdaleEZ using the City of Scottsdale dataset.

Primary outputs:
- next-month predicted volume by ZIP and request type
- next-month top 10 concerns by ZIP
- emerging issue flags
- issue relationship rules
- clustering-based issue pattern profiles
- Power BI-ready observed operational tables
- seasonal comparison tables
- run-level reproducibility artifacts

## Pipeline Diagram
ingest -> QA -> monthly ZIP x request-type feature table -> parallel outputs:
1. predictions
2. issue relationships
3. clustering profiles
4. operational tables
5. seasonal comparison tables
6. run traceability artifacts

## Stage Descriptions

### Stage 1 - Data Ingestion
Reads CSV/XLSX source, standardizes columns, parses dates to UTC, standardizes ZIP codes, and applies an optional inclusive end-of-day data cutoff so incomplete final months can be excluded consistently across the pipeline.

### Stage 2 - QA / Validation
Hard-fails on:
- missing required columns
- duplicate request IDs
- null ZIPs greater than 20%
- excluded request types still remaining after filtering
- scooter-related request types still remaining after filtering

Warnings:
- null closed dates
- negative resolution times (clipped to zero)
- partial final month

Fixed exclusions:
- Airport Complaint
- Bird (silver & black scooters)
- Fly Ride (silver with blue band)
- Lime (white & green scooters, white & green bicycles)
- Spin (orange & black scooters)
- Bike & Scooter Share Programs
- Boost (Orange and Black scooters)
- Jump (red & white scooters, bikes)
- Lyft scooters
- Razor (Red scooters)

### Stage 3 - Feature Engineering
Builds a monthly ZIP x request-type panel for the top citywide request types and creates lag, rolling, seasonal, and operational context features used in next-month forecasting.

### Stage 4 - Prediction Model (LightGBM)
Uses LightGBM to predict next-month volume for top citywide request types by ZIP, then publishes:
- predicted monthly volume by ZIP x request type
- top 10 predicted concerns by ZIP
- emerging issue flags
- model confidence fields based on held-out WMAPE

Model selection summary:
- LightGBM is the v1 production candidate because it achieved the best held-out WMAPE in notebook testing
- Final production parameters were fixed after notebook comparison testing and outperformed the CV-tuned variant on the held-out test set

Sparse publishing logic:
- predictions are only published when a ZIP x request-type pair meets the minimum historical support threshold
- this preserves stability and interpretability for city operations

### Stage 5 - Issue Relationship Logic
Runs association-rule mining on the highest-volume request types first to preserve runtime, memory stability, and interpretability. Produces:
- general issue relationship rules
- explicit A + B => C rules

### Stage 5B - Clustering
Clusters ZIP-month issue composition patterns using normalized request-type shares. This is separate from forecasting and is used for pattern discovery and operational interpretation.

### Stage 6 - Operational Tables
Produces backend tables for Power BI, including current-month top concerns by ZIP and other observed summary tables for city review.

### Stage 7 - Seasonal Comparison
Builds month-over-month and/or year-over-year comparison tables for operational context.

### Stage 8 - README / Reproducibility
Writes this README plus reproducibility artifacts:
- run_config.json
- lgbm_v1.pkl
- lgbm_feature_importance.csv
- qa_summary.csv / qa_summary.json

## Output Files
Core outputs:
- predicted_monthly_volume_by_zip_requesttype.csv
- predicted_top10_concerns_by_zip.csv
- current_month_top10_concerns_by_zip.csv
- issue_relationship_rules.csv
- issue_ab_implies_c_rules.csv
- zip_month_cluster_assignments.csv
- cluster_summary_profiles.csv
- cluster_centers_requesttype_share.csv
- seasonal_comparison_zip_requesttype.csv
- monthly_volume_by_workgroup_zip.csv
- resolution_time_bins_by_workgroup.csv
- staff_vs_citizen_monthly_volume.csv
- sla_performance_by_workgroup.csv

Reproducibility / diagnostics:
- qa_summary.csv
- qa_summary.json
- run_config.json
- lgbm_v1.pkl
- lgbm_feature_importance.csv

## Modeling Notes
- Forecasting unit is ZIP x request type x month
- Target is next-month volume
- Top request types are selected citywide to avoid unstable long-tail forecasting
- Airport and scooter-related requests are excluded from predictive modeling
- Clustering is based on normalized request-type shares, not raw counts
- Association rules are intentionally limited to the highest-volume request types first for stability, interpretability, and runtime efficiency

## Known Limitations
- Missing ZIPs reduce geographic coverage
- Held-out notebook model selection should be revalidated after SageMaker migration using the same cutoff logic and outputs
- Prediction outputs support prioritization and early warning, not exact deterministic counts

## SageMaker Migration Note
For parity-first migration, keep business logic unchanged and swap local file I/O for S3 paths. Preserve the same output filenames and validate SageMaker outputs against the Colab baseline before any post-parity tuning.

Generated UTC: 2026-03-23 21:52:57 UTC
