# Dataset contract — Customer Retention Scorecard

All source files are CSV (UTF-8, comma-delimited) and must be placed in `dataset/raw/`.

## Files

### 1) `customer_dim.csv`
- **Grain:** one row per customer.
- **Refresh cadence:** monthly full refresh.
- **Columns:**
  - `customer_id` (string) — unique customer key.
  - `join_date` (date) — customer onboarding date.
  - `segment_id` (string) — segment foreign key.
  - `region_id` (string) — region foreign key.
  - `city` (string) — customer city.

### 2) `segment_dim.csv`
- **Grain:** one row per segment.
- **Refresh cadence:** low-change, update as needed.
- **Columns:**
  - `segment_id` (string) — unique segment key.
  - `segment_name` (string) — segment label.

### 3) `region_dim.csv`
- **Grain:** one row per region.
- **Refresh cadence:** low-change, update as needed.
- **Columns:**
  - `region_id` (string) — unique region key.
  - `region_name` (string) — region label.

### 4) `customer_monthly_status.csv`
- **Grain:** one row per customer per month.
- **Refresh cadence:** monthly append/replace.
- **Columns:**
  - `month` (date) — month start date (`YYYY-MM-01`).
  - `customer_id` (string) — customer key.
  - `is_active` (boolean; 0/1) — active status in month.
  - `is_churned` (boolean; 0/1) — churn event in month.
  - `tenure_months` (int64) — tenure in months as of month.
  - `monthly_revenue` (double) — monthly revenue amount.

## Data quality expectations
- `customer_id` exists in `customer_dim.csv` for all monthly rows.
- `segment_id` and `region_id` in `customer_dim.csv` must exist in corresponding dimension files.
- `month` must be first day of month and within model calendar coverage.
- `is_churned = 1` rows should have `is_active = 0` for the same customer-month.
