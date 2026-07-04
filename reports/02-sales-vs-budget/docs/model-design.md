# Model design — Branch & Channel Performance

Semantic model: `pbip/BranchChannelPerformance.SemanticModel` (TMDL, Import). Reconciled to the
profiled BankDIAD data — see [data-profile.md](data-profile.md).

## Star schema

```
                 ┌──────────┐   ┌──────────┐   ┌──────────────┐
                 │   Geo    │   │ Customer │   │   Campaign   │
                 └────┬─────┘   └────┬─────┘   └──────┬───────┘
   ┌──────────┐       │              │                │       ┌──────────┐
   │   Date   │───────┼──────────────┼────────────────┼───────│ Product  │
   └────┬─────┘       │              │                │       └────┬─────┘
        │        ┌────┴──────────────┴────────────────┴────────────┴──────┐
        ├────────│                    Sales  (fact)                       │
        │        └────────────────────────────────────────────────────────┘
        │        ┌───────────────────────────────┐    
        ├────────│   Budget and forcast (fact)   │
                 └───────────────────────────────┘     
       
            ┌──────────────┐  
            │ Key Measures │   
            │(disconnected)│  
            └──────────────┘
```

- **Facts:** `Sales` (transaction grain), `Budget & Forecast` (product group × month × type grain, unpivoted).
- **Dimensions:** `Date` (marked Date table), `Geo`, `Product`, `Customer`, `Campaign`,
  `Customer`. `Channel` and `Transaction Type` are **derived from `Transactions`** (no source sheet).
- **`Key Measures`** is a disconnected calculated table that holds all core measures.

## Relationships (all single-direction, many-to-one)

| From (many) | To (one) |
|---|---|
| Sales[Date] | Date[Date] |
| Sales[CustomerID] | Customer[Customer ID] |
| Sales[ProductID] | Product[Product ID] |
| Sales[CampaignID] | Campaign[CampaignID] |
| Customer[ZipCode] | Geo[Zip] |
| Budget & Forecast[MonthKey] | Date[Date] |

## Bảng Budget & Forecast không có mối quan hệ vật lý nào với các bảng 'Geo', 'Customer', và 'Campaign'. Filtering actions with Budget data will be comptleted through DAX Measures.

## Design decisions

- **Import + folder combine.** Actuals and budget data are imported directly from Excel files (VanArsdel_Actuals.xlsx and VanArsdel_Budget_Forecast.xlsx). Column structures are kept stable. UTF-8 encoding (Encoding = 65001) is applied in Power Query to preserve special characters and text strings.
- **Budget Unpivoting & Month-Start Dating.** The raw budget sheet is in a wide format with product columns ranging from Accessory-Accessory to Youth-Youth. Power Query unpivots these columns into a long format consisting of Product_Group and Budget_Value. The Month/Year attributes are dated to the 1st of each month (YYYY-MM-01) to map correctly to the Date table.
- **Snowflake Geo-Customer Path.** To avoid creating an ambiguous filter path (Ambiguity), the Geo table connects Many-to-One to the Customer table via the Zip ↔ ZipCode key. Consequently, when filtering a State or Region, the filter path flows from Geo ➔ Customer ➔ Sales.
- **Derived Season Dimension.** The Season dimension does not exist in the raw source; it is derived directly within the Date table by grouping MonthNo values (Spring: 3–5, Summer: 6–8, Autumn: 9–11, Winter: 12–2).
- **'Date' table from source.** Uses the supplied Date sheet (ensuring it covers the entire actuals date range) and is marked as dataCategory: Time to enable DAX time intelligence functions.

## Open questions (resolve with the business)
1. **Budget Granularity Allocation.** Since the 'Budget & Forecast' data lacks geographical granularity ('Geo'), map-based Actuals vs Budget comparisons currently utilise a Dynamic DAX Allocation method (allocating budget based on historical actual revenue weights per region). Confirmation is needed from the Business Planning department regarding whether a fixed regional weight system should be applied instead.
2. **Product Mapping Key.** The relationship key in the unpivoted budget table is the group name ('Product_Group', e.g., Urban-Extreme). This key must exactly match the concatenated string 'Category & "-" & Segment' in the Product table. Any category name changes in the Actuals file without a matching update in the Budget file will result in orphaned rows

## Validation
`tmdl-validate` passes on the entire model structure; cross-reference checks confirm every relationship, table link, and DAX measure reference within the `Key Measures` table resolves correctly with no circular dependencies.
