# NYC 311 Service Requests — Data Cleaning & EDA

## Problem
Raw, unfiltered public data can't answer anything reliably as-is. This project documents 
exactly how a messy 311 service-request export was audited, cleaned, and made usable — 
and what it reveals once it is.

## Approach
- Pulled a Brooklyn-scoped slice via NYC Open Data's portal export filter (~21,000 rows).
- Ran a full data-quality audit before any cleaning: missingness, duplicates, dtype 
  checks, and category-label consistency.
- Cleaned in documented, staged steps: dropped columns with near-total missingness, 
  merged inconsistent category labels, flagged (not dropped) rows with missing 
  geolocation, parsed and validated datetime fields, and resolved a status/closed_date 
  contradiction affecting 1,528 rows.
- During EDA, discovered the intended "2018-only" filter had not applied correctly — the 
  data actually spanned 2010–2019, concentrated in 2011. Caught this via the data itself, 
  corrected the analysis scope, and documented the discovery rather than re-pulling or 
  hiding it.
- Explored 3 non-obvious trends and visualized 5 supporting charts.

## Findings
- **Response time by agency:** median (not mean) response time reveals TLC and DPR are 
  the slowest to close requests, while NYPD and DEP close near-instantly — several 
  agencies have highly skewed distributions where the mean is misleading.
- **Weekend complaint patterns vary by category:** home-maintenance complaints (electric, 
  plumbing) are filed on weekends ~15% of the time, vs. only 4% for administrative 
  ("Borough Office") complaints.
- **Data concentration, not seasonality:** the dataset's apparent "seasonal spike" was 
  actually a one-time concentration of ~9,000 rows in March–June 2011 alone — not a 
  recurring yearly pattern. This was verified and documented as a correction to the 
  original assumption about the data's scope.

## Tech Stack
Python, pandas, Matplotlib, Seaborn, Jupyter Notebook

## How to Reproduce
1. Clone this repo.
2. Install dependencies: `pip install pandas matplotlib seaborn jupyter`
3. Data source: [NYC 311 Service Requests](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-2019/76ig-c548) 
   (filtered via the portal's export tool — see `CLEANING_LOG.md` for exact scope caveats)
4. Open `nyc_311_analysis.ipynb` and run all cells.

See `CLEANING_LOG.md` for a full column-by-column record of every cleaning decision.