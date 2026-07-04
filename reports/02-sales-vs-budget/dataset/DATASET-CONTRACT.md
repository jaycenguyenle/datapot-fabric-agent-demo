# Dataset contract — Sales vs Budget Distribution

What `dataset/raw/` must contain. This is the agreement between the data producer and the semantic
model. The model's Power Query partitions read these files relative to the `DataFolder` parameter.
The dataset is the **VANARSDEL** sample (historical actuals and budget forecasts, 2020-01 → 2021-12).

> ✅ Status: data is present and the model is bound to it. See [docs/data-profile.md](../docs/data-profile.md).

## Files consumed by THIS report (#1)

### `Sales/VanArsdel_Actuals.xlsx`  → fact `Sales`

| Column | Type | Notes |
|--------|------|-------|
| ProductID | integer | → DimProduct |
| Date | date | transaction date → Date |
| CustomerID | integer | → DimCustomer |
| CampaignID | text | → DimCampaign |
| Units | integer | Quantity sold (signed, negative for returns) |

### `Campaign/VanArsdel_Actuals_Campaign.csv`  → fact `Campaign`
CampaignID (int), TrafficChannel (text: Organic Search, SEO, Banner, SEM, Email, SMO, Mail), Device (text: Desktop, Mobile, Tablet, Paper).

### `Budget/VanArsdel_Budget_Forecast.xlsx`, sheet `1`  → fact `Budget & Forecast`
Wide format: `Type, Year, Month` + one column per Product Category-Segment combination (Accessory-Accessory … Youth-Youth)
`Type ∈ {Budget, Forecast}`. The model **unpivots** the product columns.

### `Dimensions/VanArsdel_Dimensions.xlsx`  → dimensions
| Sheet | Model table | Key columns |
|-------|-------------|-------------|
| DimProduct | `Product` | ProductID, Product, Category, Segment, ManufacturerID, Manufacturer, Unit Cost, Unit Price |
| DimCustomer | `Customer` | CustomerID, ZipCode, Email Name |
| DimDate | `Date` | Date, MonthNo, MonthName, MonthID, Month, Quarter, Year |
| DimGeo | `Geo` | Zip, City, State, Region, District, Country |

`Manufacturer` and `Segment` dimensions are **derived** from distinct values in `Product`
(no sheet exists for them).

## Refresh rules
- Add new monthly files into the matching folder using the same name pattern; the folder-combine
  queries pick them up automatically — no model change needed.
- Keep column names and order stable. If they change, update the matching `*.tmdl` partition and the
  [data dictionary](../dictionary/data-dictionary.md).
- Encoding must be **UTF-8** (Vietnamese text); the queries read with `Encoding = 65001`.
