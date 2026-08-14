# Cleaning Log — NYC 311 Service Requests (Brooklyn Slice)

| Column | Issue Found | Action Taken | Rows Affected |
|---|---|---|---|
| (all columns) | Raw CSV export used human-readable names with spaces/parentheses (e.g. `Problem (formerly Complaint Type)`) | Standardized to lowercase snake_case, stripped "(formerly ...)" suffixes | 44 columns |
| `problem` | `PAINT - PLASTER` and `PAINT/PLASTER` were the same HPD category with inconsistent formatting | Merged into `PAINT - PLASTER` | 578 |
| `problem` | ALL CAPS vs. Title Case labels initially looked inconsistent | Investigated — confirmed casing reflects agency-of-origin (HPD vs. others), not an error. Left unchanged | — |
| `vehicle_type`, `road_ramp`, `bridge_highway_*`, `taxi_*`, `facility_type`, `landmark`, `intersection_street_*` | 95–100% missing — only applicable to complaint subtypes rare in this slice | Dropped columns | 8 columns |
| `due_date` | 97% missing — only applies to SLA-tracked complaint types | Left as-is, not imputed | — |
| `latitude`, `longitude`, `incident_zip` | ~0.05% missing, mostly complaints without a specific street address | Flagged with `has_geo` boolean rather than dropped | 10 |
| `created_date`, `closed_date` | Stored as text strings, inconsistent implicit format | Parsed to datetime with explicit format string | 21,002 |
| `status` vs `closed_date` | 1,528 rows had `status == 'Open'` but a filled `closed_date` — some dating to 2012, indicating status wasn't reliably updated | Created `is_closed` boolean derived from `closed_date` presence instead of trusting `status` | 1,528 |
| `closed_date` | 293 closed requests had `closed_date` after 2020 — outside the dataset's stated 2010–2019 window, likely administrative placeholder dates | Flagged with `plausible_closure`, excluded from response-time stats only (retained for category counts) | 293 |
| `response_time_days` | Response-time mean (93.6 days) was heavily skewed by outliers vs. median (12.8 days) | Reported median as the headline stat; excluded implausible closures from the calculation | — |
| `year` (dataset scope) | Export intended as "Brooklyn, 2018" actually spans 2010–2019, with 62% of rows from 2011 alone — portal date filter did not apply as intended | Corrected project scope documentation; reframed seasonal-trend analysis to reflect the real 2011 concentration rather than false year-over-year seasonality | 13,083 (2011 rows) |
| (duplicates) | Checked for fully duplicate rows and duplicate `unique_key` values | None found — no action needed | 0 |

**Final row count:** 21,002 (no rows dropped; all issues handled via flags/derived columns to preserve auditability)
**Final column count:** 44 raw → several dropped for near-total missingness, several new engineered (`has_geo`, `is_closed`, `plausible_closure`, `response_time_days`, `day_of_week`, `month`, `year`, `is_weekend`)