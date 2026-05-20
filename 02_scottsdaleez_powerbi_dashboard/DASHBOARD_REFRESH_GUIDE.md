# Power BI Dashboard Refresh Workflow

Part of the [AI Business Portfolio](../README.md).

Workflow documentation for refreshing a Power BI dashboard sourced from monthly prediction-pipeline CSV outputs.

This guide summarizes a repeatable dashboard refresh and validation process: replace the prior CSV outputs with the latest pipeline outputs, refresh the existing Power BI file, confirm that observed and prediction months display correctly, and review each dashboard tab for expected behavior.

## Monthly Refresh Goal

Each refresh cycle, the dashboard operator should be able to:

1. run or receive the latest prediction-pipeline outputs
2. replace the prior CSV output files with the latest versions
3. refresh the existing Power BI file
4. validate that the latest observed month and next prediction month display correctly
5. export or present the refreshed dashboard

## Files Used by the Dashboard

### Core Files

Place the updated CSV files in the same folder used by the existing Power BI file:

- `predicted_monthly_volume_by_zip_requesttype.csv`
- `monthly_volume_by_workgroup_zip.csv`
- `resolution_time_bins_by_workgroup.csv`
- `staff_vs_citizen_monthly_volume.csv`
- `sla_performance_by_workgroup.csv`
- `seasonal_comparison_zip_requesttype.csv`

### Additional Pipeline Files

The prediction pipeline may generate additional files that are useful for model review or future dashboard expansion, even if they are not required for the simplified dashboard view:

- `predicted_top10_concerns_by_zip.csv`
- `current_month_top10_concerns_by_zip.csv`
- `zip_month_cluster_assignments.csv`
- `cluster_summary_profiles.csv`
- `cluster_centers_requesttype_share.csv`
- `issue_relationship_rules.csv`
- `issue_ab_implies_c_rules.csv`

## Recommended File Setup

Keep one working folder that contains:

- the Power BI file
- the latest CSV outputs used by the dashboard

Example structure:

```text
Dashboard/
├── ScottsdaleEZ_PowerBI_Dashboard_v1.pbix
├── predicted_monthly_volume_by_zip_requesttype.csv
├── monthly_volume_by_workgroup_zip.csv
├── resolution_time_bins_by_workgroup.csv
├── staff_vs_citizen_monthly_volume.csv
├── sla_performance_by_workgroup.csv
└── seasonal_comparison_zip_requesttype.csv
```

Important:

- keep filenames stable
- overwrite the prior CSVs with the latest monthly outputs
- do not rename columns unless the Power BI model is also updated
- do not rebuild the dashboard file each month if the schema has not changed

## Refresh Workflow

### Step 1 - Confirm New Pipeline Outputs

Confirm that the prediction pipeline completed successfully and that the expected CSV outputs were created.

Before opening Power BI, verify that the required dashboard files exist and use the expected filenames.

### Step 2 - Replace Prior CSV Files

Copy the latest pipeline output CSVs into the dashboard folder.

When prompted:

- choose **Replace** or **Overwrite**
- keep the filenames exactly the same as the prior version

Do not:

- rename the files
- add month names into the filenames
- delete fields manually
- reorder columns manually

Stable filenames and schemas make the Power BI refresh process easier to maintain.

### Step 3 - Open the Existing Power BI File

Open the existing dashboard `.pbix` file.

Example:

- `ScottsdaleEZ_PowerBI_Dashboard_v1.pbix`

Do not create a new Power BI file for each refresh cycle unless the dashboard structure is intentionally being changed.

### Step 4 - Refresh the Data

In Power BI Desktop:

1. open the `.pbix`
2. go to **Home**
3. click **Refresh**
4. wait for all tables to refresh

If Power BI asks for file locations, point it to the folder where the updated CSVs are stored.

### Step 5 - Confirm Data Loaded Correctly

After refresh, validate the following.

#### Prediction Views

- `prediction_month` reflects the month after the most recent complete observed month
- the prediction page updates correctly
- map, cards, slicers, and tables respond consistently
- issue-status or problem-status filters behave as expected

#### Observed Views

- `created_month` reflects the latest complete observed month
- operational pages update correctly
- trend charts, cards, and request-type visuals display expected values

#### Slicers

- ZIP slicers work on ZIP-based pages
- month slicers work on date-based pages
- workgroup slicers work on workgroup pages
- synced slicers propagate across intended pages

## Tab-Level Validation

### 1. Overview

Check that:

- cards populate
- the monthly trend line extends through the latest observed month
- top request-type charts update correctly
- staff-versus-citizen visuals update correctly
- ZIP and month slicers function

Files commonly used:

- `seasonal_comparison_zip_requesttype.csv`
- `staff_vs_citizen_monthly_volume.csv`

Note: percent-change-from-prior-year metrics are most interpretable when a specific month is selected. When all months are selected, the metric aggregates across matched prior-year periods.

### 2. Hotspot / Predictions

Check that:

- the map loads ZIP-level predictions correctly
- the prediction card, ZIP count card, map, and table respond to the same filters
- the issue-status slicer filters the page correctly
- the table is sorted by predicted request volume descending
- the forecast shown is for the current `prediction_month`

File commonly used:

- `predicted_monthly_volume_by_zip_requesttype.csv`

### 3. Workgroup + Service Target Performance

Check that:

- workload by workgroup updates correctly
- service target performance visuals update correctly
- percent-on-target visuals update correctly
- workgroup and month slicers function

Files commonly used:

- `monthly_volume_by_workgroup_zip.csv`
- `sla_performance_by_workgroup.csv`

### 4. Request Completion Time

Check that:

- the histogram uses the intended bin order:
  - `0-1d`
  - `1-3d`
  - `3-7d`
  - `7-14d`
  - `14-30d`
  - `30d+`
- blank bins are not showing unintentionally
- workgroup delay visuals update correctly
- workgroup and month slicers function

File commonly used:

- `resolution_time_bins_by_workgroup.csv`

Note: if bin names change in the CSV, any Power BI lookup table used for bin ordering should be updated as well.

### 5. Current vs Prior Year

Check that:

- cards update correctly
- current versus prior-year trend visuals update correctly
- request-type change visuals update correctly
- ZIP and month slicers function

File commonly used:

- `seasonal_comparison_zip_requesttype.csv`

Note: this view is most interpretable when a specific month is selected. When all months are selected, prior-year comparison cards and charts reflect matched same-month comparisons aggregated across multiple months.

### 6. Leadership Watchlist

Check that:

- hotspot tables update correctly
- problem-area cards behave correctly under selected filters
- year-over-year increase visuals update correctly
- ZIP and problem-status slicers function

Files commonly used:

- `predicted_monthly_volume_by_zip_requesttype.csv`
- `sla_performance_by_workgroup.csv`
- `seasonal_comparison_zip_requesttype.csv`

Note: if a filter is set to a normal-range status, some problem-area cards may go blank by design if they are intended to count only potentially rising items.

### 7. Definitions and Notes

Check that:

- worksheet summaries still match the final tab names
- forecast-timing notes reflect the refreshed observed and prediction months
- caution text remains readable and unchanged after refresh

Common note topics:

- post-launch focus period
- ZIP-level interpretation caution
- internal versus resident workflow caution
- time-to-close comparability caution

## Month Logic

Use this logic when reviewing a refreshed dashboard:

- observed tabs use the latest complete `created_month`
- prediction tabs use the next `prediction_month`

This separation prevents users from confusing historical activity with forecasted activity.

## Successful Refresh Criteria

A refresh is successful when:

- all visuals load without errors
- no CSV source is missing
- cards show values instead of blanks
- slicers work normally
- observed and prediction months make sense
- dashboard pages can be reviewed without manual rework

## Troubleshooting

### Problem: Power BI cannot find a file

Fix:

- point Power BI to the folder containing the updated CSVs
- confirm the filename still matches exactly

### Problem: a visual goes blank after refresh

Fix:

- check whether the visual is filtered to a month that no longer exists
- check whether a slicer selection is still active
- check whether a visual-level filter conflicts with a slicer
- clear filters and test again

### Problem: histogram bins are out of order

Fix:

- check the resolution-bin lookup table in Power BI
- confirm the relationship to the resolution-bin field still exists
- confirm the sort-order table still contains the expected bin labels

### Problem: a slicer no longer syncs across tabs

Fix:

- go to **View → Sync slicers**
- confirm the correct pages are checked for **Sync** and **Visible**

### Problem: prediction visuals do not agree with each other

Fix:

- confirm the card, slicer, map, and table are all using the same prediction table
- confirm the issue-status slicer is built from the same prediction table used by the page visuals
- confirm visual interactions are set to **Filter**, not **None**

### Problem: columns or fields are missing

Fix:

- compare the new CSV against the prior version
- if the pipeline schema changed, update the Power BI model before relying on the refreshed dashboard

## Refresh Checklist

Before sharing a refreshed dashboard, confirm:

- [ ] latest CSVs replaced the prior files
- [ ] Power BI refresh completed successfully
- [ ] observed month is correct
- [ ] prediction month is correct
- [ ] Overview tab looks correct
- [ ] Hotspot / Predictions tab looks correct
- [ ] Workgroup + Service Target Performance tab looks correct
- [ ] Request Completion Time tab looks correct
- [ ] Current vs Prior Year tab looks correct
- [ ] Leadership Watchlist tab looks correct
- [ ] Definitions and Notes tab looks correct
- [ ] slicers work correctly
- [ ] no blank or broken visuals remain
- [ ] file saved after refresh

## Recommended File Naming

Keep a working copy and, if needed, a dated archive copy.

Examples:

- working file: `ScottsdaleEZ_PowerBI_Dashboard_v1.pbix`
- archived monthly copy: `ScottsdaleEZ_PowerBI_Dashboard_YYYY_MM.pbix`

## Public Portfolio Note

This file is a public-facing workflow summary. It does not include raw data, model artifacts, cloud paths, credentials, private operational materials, or restricted source files.

## Related Materials

- [ScottsdaleEZ Predictive Model README](../01_scottsdaleez_predictive_model/README.md)
- [Pipeline Summary](../01_scottsdaleez_predictive_model/PIPELINE_SUMMARY.md)
- [Model Tuning Summary](../01_scottsdaleez_predictive_model/MODEL_TUNING.md)
- [Redacted Public Poster](scottsdaleez_public_poster_redacted.pdf)
