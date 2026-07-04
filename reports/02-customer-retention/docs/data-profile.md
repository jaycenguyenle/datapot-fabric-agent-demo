# Data profile — Customer Retention Scorecard

## Profiling summary

### `customer_dim.csv`
- Rows: 10
- Columns: 5
- Key checks: `customer_id` unique and non-null.
- Distincts: `segment_id`=3, `region_id`=3, `city`=3.
- Date range: `join_date` from 2024-01-15 to 2024-06-02.

### `segment_dim.csv`
- Rows: 3
- Columns: 2
- Key checks: `segment_id` unique and non-null.

### `region_dim.csv`
- Rows: 3
- Columns: 2
- Key checks: `region_id` unique and non-null.

### `customer_monthly_status.csv`
- Rows: 60 (10 customers × 6 months)
- Columns: 6
- Distincts: `month`=6, `customer_id`=10.
- Date range: `month` from 2025-01-01 to 2025-06-01.
- Nulls: none.
- Referential integrity: all `customer_id` values resolve to `customer_dim.csv`.
- Consistency: all `is_churned=1` rows have `is_active=0`.

## Reconciliation decisions
- Contract and actual file schemas match exactly.
- Churn represented as monthly event flag on customer-month rows.
- Date table range set to 2025-01-01 through 2025-06-30 for current sample.
