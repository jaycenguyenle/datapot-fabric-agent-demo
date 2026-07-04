# Report spec — Customer Retention Scorecard

## Purpose & audience
- **Audience:** Retail growth leads, CRM managers, regional managers.
- **Decisions supported:** Retention interventions, churn prevention focus by segment/region, revenue quality monitoring.
- **Refresh / latency:** Monthly refresh with prior-month close.

## Key questions
1. How are retention and churn trending month over month?
2. Which segments and regions are driving churn risk?
3. How is retained-customer revenue changing over time?

## KPIs
| KPI | Measure | Target / benchmark |
|-----|---------|--------------------|
| Active Customers | `[Active Customers]` | Maintain stable/growing base |
| Churned Customers | `[Churned Customers]` | Minimize absolute churn |
| Churn Rate % | `[Churn Rate %]` | < 5% monthly |
| Retention Rate % | `[Retention Rate %]` | > 95% monthly |
| Monthly Revenue | `[Monthly Revenue]` | Positive trend |
| ARPU | `[ARPU]` | Stable or improving |

## Grain & dimensions
- **Fact grain:** one row per customer per month.
- **Dimensions / slicers:** Date, Segment, Region, Customer.

## Pages
### Page 1 — Overview
- Purpose: Headline retention and revenue health.
- Visuals: KPI cards, monthly trend, segment breakdown.

### Page 2 — Drivers
- Purpose: Understand where churn is concentrated.
- Visuals: Churn by segment, churn by region, tenure distribution.

### Page 3 — Scorecard
- Purpose: Operational view for managers.
- Visuals: Segment × Region matrix with KPIs and month filter.

## Filters
- Report-level: Date range (latest 12 months where available).
- Page-level: Segment, Region.

## Design notes
- Theme: `shared/themes/datapot-theme.json`.
- Page size: 1280×720.
- Measures from `Key Measures` only.
