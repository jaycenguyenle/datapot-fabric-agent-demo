# Report spec — Sales vs Budget Distribution

## Purpose & audience
- **Audience:** Chief Commercial Officer (CCO), Regional Sales Managers, Marketing Directors.
- **Decisions supported:** Objective: To monitor geographical performance, evaluate actual sales against allocated budget distributions across product categories and seasons, identify true profitability, and optimise future marketing campaign allocations.
- **Refresh:** monthly (drop new monthly files into `dataset/raw/…`; folder-combine picks them up).

## Key questions
1. How many units are sold and how much revenue/profit is generated across different regions, states, and districts each month, and what are the trends?
2. How do actual sales revenue and volume track against the monthly unpivoted budget and forecast targets?
3. Which product categories and segments drive the highest profit margins, and how does this change by season or region?
4. Which marketing campaigns deliver the highest ROI and profitability across different geographies?

## KPIs
| KPI | Measure | Notes |
|-----|---------|-------|
| Total Units Sold | `[Total Units Actual]` | headline volume |
| Actual Revenue | `[Revenue Actual]` | Units * Unit Price (USD) |
| Total Budget | `[Total Budget Amount]` | target value after unpivoting category columns |
| Actual Profits | `[Profit Actual]` | Revenue Actual - Cost Actual (using Unit Cost) |
| Profit Margin Actual % | `[Profit Margin Actual %]` | [Profit Actual] / [Revenue Actual] |
| Budget Achievement % | `[Budget Achievement %]` | actual revenue vs allocated monthly budget |

## Grain & dimensions
- **Fact grain:** transaction-level ('Sales'); monthly wide-format record ('Budget & Forecast').
- **Slicers / dimensions:** Date (Year/Quarter/Month/Season), Geo (Region → State → District → City), Product (Category → Segment → Product), Campaign (TrafficChannel, Device).

## Pages

### Page 1 — Overview  *(built)*
- KPI cards: Total Units Actual, Revenue Actual, Profit Actual, Profit Margin Actual %, Budget Achievement %.
- Trend: Actual Revenue vs Allocated Budget by Month (line & clustered column).
- Break down: Revenue by Region (filled map or bar chart); Category-Segment breakdown matrix.
- Slicer: Year.

### Page 2 — Product & Seasonality Performance  *(built)*
- Product breakdown: Revenue & Profit Margin % by Category and Segment (bar / scatter plot).
- Seasonality mix: 100% stacked column chart showing product category share by Season (Spring, Summer, Autumn, Winter, derived from MonthNo).
- Manufacturer Matrix: Row: Manufacturer → Category; Columns: Season; Values: [Revenue Actual], [Profit Margin Actual %].

### Page 3 — Campaign Optimization & Geo Scorecard  *(built)*
- Matrix: Region rows × {Revenue Actual, Profit Actual, Profit Margin Actual %, Total Budget Amount, Budget Achievement %, Campaign ROI %} with conditional formatting.
- Campaign analysis: Bar chart showing Revenue & Profit by TrafficChannel; breakdown by Device type.
- Top / Bottom Performers: Top 5 and Bottom 5 Districts by Budget Achievement % or Profit Margin.

## Filters
- Report-level: Year, Region.
- Page-level (P2): Category-Segment. (P3): TrafficChannel, Device.

## Design notes
- Theme `shared/themes/datapot-theme.json` (registered). Page 1280×720. Format via theme, not per-visual.
- All three pages are built. Pending polish (optional): conditional formatting (data bars / colour
  scale) on the Branch Scorecard matrix, and a Top/Bottom-N filter on "NPS by Branch" (50 branches).
