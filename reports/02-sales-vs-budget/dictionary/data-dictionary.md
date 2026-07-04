# Data dictionary — VANARSDEL Sales vs Budget

This dictionary documents the VANARSDEL source dataset used by report 02.
Currency-related numeric values are in USD unless downstream model logic defines otherwise.

## Source files in scope

| Source file | Logical entity | Format |
|---|---|---|
| `dataset/raw/Sales/VanArsdel_Actuals.xlsx` (sheet `Sales`) | Sales actuals | Excel |
| `dataset/raw/Campaign/VanArsdel_Actuals_Campaign.csv` | Campaign dimension/lookup | CSV |
| `dataset/raw/Budget/VanArsdel_Budget_Forecast.xlsx` (sheet `Sheet1`) | Budget and forecast | Excel |
| `dataset/raw/Dimensions/VanArsdel_Dimensions.xlsx` | Shared dimensions | Excel |

## Fact-like entities

### Sales (from `VanArsdel_Actuals.xlsx` -> sheet `Sales`)

| Column | Type | Required | Description |
|---|---|---|---|
| ProductID | integer | yes | Product key; joins to `DimProduct.ProductID`. |
| Date | date (Excel serial date) | yes | Transaction date; joins to `DimDate.Date` after conversion. |
| CustomerID | text/integer id | yes | Customer key; joins to `DimCustomer.CustomerID`. |
| CampaignID | integer | yes | Campaign key; joins to `Campaign.CampaignID`. |
| Units | integer | yes | Quantity sold for the transaction row. |

Row grain: one sales transaction line.

### Budget & Forecast (from `VanArsdel_Budget_Forecast.xlsx` -> sheet `Sheet1`)

| Column | Type | Required | Description |
|---|---|---|---|
| Type | text | yes | Planning scenario (`Budget` or `Forecast`). |
| Year | integer | yes | Calendar year. |
| Month | text | yes | Month label (for example `Jan`, `Feb`). |
| `<Category>-<Segment>` columns | decimal | yes | Amount by product category-segment bucket. Examples: `Accessory-Accessory`, `Mix-Productivity`, `Urban-Regular`. |

Modeling note: this table is typically unpivoted to `[Type, Year, Month, CategorySegment, Value]`.

## Lookup/dimension entities

### Campaign (from `VanArsdel_Actuals_Campaign.csv`)

| Column | Type | Required | Description |
|---|---|---|---|
| CampaignID | integer | yes | Campaign key. |
| TrafficChannel | text | yes | Channel label (for example `Organic Search`, `SEO`, `Banner`, `SEM`, `Email`, `SMO`, `Mail`). |
| Device | text | yes | Device class (`Desktop`, `Mobile`, `Tablet`, `Paper`). |

### DimProduct (from `VanArsdel_Dimensions.xlsx` -> sheet `DimProduct`)

| Column | Type | Required | Description |
|---|---|---|---|
| ProductID | integer | yes | Product key. |
| Product | text | yes | Product name. |
| Category | text | yes | Product category. |
| Segment | text | yes | Product segment. |
| ManufacturerID | integer | yes | Manufacturer key. |
| Manufacturer | text | yes | Manufacturer name. |
| Unit Cost | decimal | yes | Unit cost value. |
| Unit Price | decimal | yes | Unit selling price value. |

### DimCustomer (from `VanArsdel_Dimensions.xlsx` -> sheet `DimCustomer`)

| Column | Type | Required | Description |
|---|---|---|---|
| CustomerID | text/integer id | yes | Customer key. |
| ZipCode | text | yes | Customer ZIP/postal code. |
| Email Name | text | yes | Customer email/name descriptor field. |

### DimDate (from `VanArsdel_Dimensions.xlsx` -> sheet `DimDate`)

| Column | Type | Required | Description |
|---|---|---|---|
| Date | date (Excel serial date) | yes | Calendar date key. |
| MonthNo | integer | yes | Month number (`1`-`12`). |
| MonthName | text | yes | Month short name (for example `Jan`). |
| MonthID | integer | yes | Compound month key (for example `202101`). |
| Month | text | yes | Month label (for example `Jan-21`). |
| Quarter | text | yes | Quarter label (`Q1`-`Q4`). |
| Year | integer | yes | Calendar year. |

### DimGeo (from `VanArsdel_Dimensions.xlsx` -> sheet `DimGeo`)

| Column | Type | Required | Description |
|---|---|---|---|
| Zip | text | yes | ZIP/postal code key. |
| City | text | yes | City text. |
| State | text | yes | State code/name. |
| Region | text | yes | Region grouping (for example `East`). |
| District | text | yes | District grouping. |
| Country | text | yes | Country name/code. |

## Relationship keys

| From | To | Join key |
|---|---|---|
| Sales | DimProduct | ProductID |
| Sales | DimCustomer | CustomerID |
| Sales | Campaign | CampaignID |
| Sales | DimDate | Date |
| DimCustomer | DimGeo | ZipCode -> Zip |

## Data handling notes

- Excel serial dates should be converted to calendar dates during ingestion.
- Keep source column names unchanged to avoid breaking Power Query/TMDL mappings.
- Trim trailing spaces on text fields such as traffic channel names before modeling.
