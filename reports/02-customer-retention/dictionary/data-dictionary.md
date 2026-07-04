# Data dictionary — Customer Retention Scorecard

## Table: `Date` *(dimension)*
- **Grain:** one row per day.
- **Source file:** calculated table.

| Model Name | Source Column | Type | Key | Hidden | Description | Example |
|------------|---------------|------|-----|--------|-------------|---------|
| Date | Date | dateTime | PK | no | Calendar date | 2025-01-01 |
| Year | Year | int64 | – | no | Calendar year | 2025 |
| Month Number | MonthNumber | int64 | – | yes | Month number for sorting | 1 |
| Month Name | MonthName | string | – | no | Month label | January |
| Month Year | MonthYear | string | – | no | Month-year label | Jan 2025 |

## Table: `Segment` *(dimension)*
- **Grain:** one row per segment.
- **Source file:** `dataset/raw/segment_dim.csv`

| Model Name | Source Column | Type | Key | Hidden | Description | Example |
|------------|---------------|------|-----|--------|-------------|---------|
| Segment ID | segment_id | string | PK | yes | Segment key | S1 |
| Segment Name | segment_name | string | – | no | Segment label | Mass Retail |

## Table: `Region` *(dimension)*
- **Grain:** one row per region.
- **Source file:** `dataset/raw/region_dim.csv`

| Model Name | Source Column | Type | Key | Hidden | Description | Example |
|------------|---------------|------|-----|--------|-------------|---------|
| Region ID | region_id | string | PK | yes | Region key | R1 |
| Region Name | region_name | string | – | no | Region label | North |

## Table: `Customer` *(dimension)*
- **Grain:** one row per customer.
- **Source file:** `dataset/raw/customer_dim.csv`

| Model Name | Source Column | Type | Key | Hidden | Description | Example |
|------------|---------------|------|-----|--------|-------------|---------|
| Customer ID | customer_id | string | PK | yes | Customer key | C001 |
| Join Date | join_date | dateTime | – | no | Customer onboarding date | 2024-01-15 |
| Segment ID | segment_id | string | FK | yes | Segment key | S1 |
| Region ID | region_id | string | FK | yes | Region key | R1 |
| City | city | string | – | no | Customer city | Hanoi |

## Table: `Customer Status` *(fact)*
- **Grain:** one row per customer per month.
- **Source file:** `dataset/raw/customer_monthly_status.csv`

| Model Name | Source Column | Type | Key | Hidden | Description | Example |
|------------|---------------|------|-----|--------|-------------|---------|
| Date | month | dateTime | FK | yes | Month start date | 2025-01-01 |
| Customer ID | customer_id | string | FK | yes | Customer key | C001 |
| Is Active | is_active | boolean | – | yes | Active status flag | true |
| Is Churned | is_churned | boolean | – | yes | Churn event flag | false |
| Tenure Months | tenure_months | int64 | – | yes | Tenure in months | 12 |
| Monthly Revenue | monthly_revenue | double | – | yes | Revenue in month | 430000 |

## Measures (`Key Measures`)

| Measure | Folder | Format | DAX | Definition |
|---------|--------|--------|-----|------------|
| Active Customers | 01 Retention | #,##0 | `CALCULATE(DISTINCTCOUNT('Customer Status'[Customer ID]), 'Customer Status'[Is Active] = TRUE())` | Count of active customers in filter context. |
| Churned Customers | 01 Retention | #,##0 | `CALCULATE(DISTINCTCOUNT('Customer Status'[Customer ID]), 'Customer Status'[Is Churned] = TRUE())` | Count of customers marked churned in context month. |
| Churn Rate % | 01 Retention | 0.0% | `DIVIDE([Churned Customers], [Active Customers])` | Share of churned over active customers. |
| Retention Rate % | 01 Retention | 0.0% | `1 - [Churn Rate %]` | Complement of churn rate. |
| Monthly Revenue | 02 Revenue | #,##0 | `SUM('Customer Status'[Monthly Revenue])` | Revenue from active customer-month rows. |
| ARPU | 02 Revenue | #,##0 | `DIVIDE([Monthly Revenue], [Active Customers])` | Average revenue per active customer. |
