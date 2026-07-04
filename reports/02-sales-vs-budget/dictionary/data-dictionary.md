# Data dictionary — Sales vs Budget (VANARSDEL)

Lock-step with the semantic model in `../pbip/BranchChannelPerformance.SemanticModel`.
Currency = **USD**.
Hidden columns are not shown to report users (keys, raw fact fields). `★` = table key.

## Dimensions

### `Date`  (DimDate sheet)
| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Date ★ | Date | dateTime | no | Calendar date (converted from Excel serial) |
| Year | Year | int64 | no | Calendar year |
| Quarter | Quarter | string | no | Q1-Q4 |
| Month No | MonthNo | int64 | yes | Month number sort key |
| Month Name | MonthName | string | no | Jan-Dec |
| Month ID | MonthID | int64 | yes | YYYYMM key |
| Month | Month | string | no | Month label (for example Jan-21) |

### `Product`  (DimProduct sheet)
| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Product ID ★ | ProductID | int64 | yes | Product key |
| Product Name | Product | string | no | Product display name |
| Category | Category | string | no | Product category |
| Segment | Segment | string | no | Product segment |
| Manufacturer ID | ManufacturerID | int64 | yes | Manufacturer key |
| Manufacturer | Manufacturer | string | no | Manufacturer name |
| Unit Cost | Unit Cost | double | yes | Cost per unit |
| Unit Price | Unit Price | double | yes | Selling price per unit |

### `Customer`  (DimCustomer sheet)
| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Customer ID ★ | CustomerID | string | yes | Customer key |
| Zip Code | ZipCode | string | no | ZIP/postal code |
| Email Name | Email Name | string | no | Customer email/name descriptor |

### `Geo`  (DimGeo sheet)
| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Zip ★ | Zip | string | yes | ZIP/postal code key |
| City | City | string | no | City |
| State | State | string | no | State |
| Region | Region | string | no | Region |
| District | District | string | no | District |
| Country | Country | string | no | Country |

### `Campaign`  (VanArsdel_Actuals_Campaign.csv)
| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Campaign ID ★ | CampaignID | int64 | yes | Campaign key |
| Traffic Channel | TrafficChannel | string | no | Organic Search, SEO, Banner, SEM, Email, SMO, Mail |
| Device | Device | string | no | Desktop, Mobile, Tablet, Paper |

## Facts

### `Sales`  (Sales/VanArsdel_Actuals.xlsx, sheet `Sales`)
| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Product ID | ProductID | int64 | yes | -> Product |
| Date | Date | dateTime | yes | -> Date |
| Customer ID | CustomerID | string | yes | -> Customer |
| Campaign ID | CampaignID | int64 | yes | -> Campaign |
| Units | Units | int64 | no | Quantity sold |

### `Budget Forecast`  (Budget/VanArsdel_Budget_Forecast.xlsx, sheet `Sheet1`, unpivoted)
| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Type | Type | string | no | Budget or Forecast |
| Year | Year | int64 | no | Calendar year |
| Month | Month | string | no | Month name |
| Category Segment | Unpivoted column name | string | no | Product category-segment bucket |
| Budget Value | Unpivoted column value | double | yes | Planned amount |

## Recommended Measures  (all on `Key Measures`)

| Model column | Source | Type | Hidden | Description |
|---|---|---|---|---|
| Total Units | `SUM(Sales[Units])` | whole number | no | Total sold units |
| Actual Revenue | `SUMX(Sales, Sales[Units] * RELATED(Product[Unit Price]))` | currency | no | Realized revenue |
| Actual COGS | `SUMX(Sales, Sales[Units] * RELATED(Product[Unit Cost]))` | currency | no | Cost of goods sold |
| Actual Profit | `[Actual Revenue] - [Actual COGS]` | currency | no | Gross profit |
| Profit Margin % | `DIVIDE([Actual Profit], [Actual Revenue])` | percentage | no | Profitability ratio |
| Budget Revenue | `CALCULATE(SUM('Budget Forecast'[Budget Value]), 'Budget Forecast'[Type] = "Budget")` | currency | no | Budget baseline |
| Forecast Revenue | `CALCULATE(SUM('Budget Forecast'[Budget Value]), 'Budget Forecast'[Type] = "Forecast")` | currency | no | Forecast baseline |
| Revenue Variance | `[Actual Revenue] - [Budget Revenue]` | currency | no | Actual minus budget |
| Revenue Variance % | `DIVIDE([Revenue Variance], [Budget Revenue])` | percentage | no | Budget attainment gap |
| Budget Achievement % | `DIVIDE([Actual Revenue], [Budget Revenue])` | percentage | no | Actual/budget ratio |
| Actual Revenue PM | `CALCULATE([Actual Revenue], DATEADD('Date'[Date], -1, MONTH))` | currency | yes | Previous-month revenue helper |
| Revenue MoM % | `DIVIDE([Actual Revenue] - [Actual Revenue PM], [Actual Revenue PM])` | percentage | no | Month-over-month growth |
| Actual Revenue PY | `CALCULATE([Actual Revenue], DATEADD('Date'[Date], -1, YEAR))` | currency | yes | Previous-year revenue helper |
| Revenue YoY % | `DIVIDE([Actual Revenue] - [Actual Revenue PY], [Actual Revenue PY])` | percentage | no | Year-over-year growth |
| Actual Profit YTD | `TOTALYTD([Actual Profit], 'Date'[Date])` | currency | no | Year-to-date profit |
| Revenue Share % | `DIVIDE([Actual Revenue], CALCULATE([Actual Revenue], ALL(Product)))` | percentage | no | Product contribution share |

## Relationship keys

| From | To | Join key |
|---|---|---|
| Sales | Product | Product ID |
| Sales | Customer | Customer ID |
| Sales | Campaign | Campaign ID |
| Sales | Date | Date |
| Customer | Geo | Zip Code -> Zip |

## Data handling notes

- Convert Excel serial dates to calendar dates in both Sales and DimDate.
- Trim trailing spaces in `TrafficChannel` before grouping.
- Keep source column names stable to avoid partition and measure regressions.
