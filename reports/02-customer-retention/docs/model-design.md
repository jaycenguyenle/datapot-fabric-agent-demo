# Model design — Customer Retention Scorecard

## Business goal
Measure monthly retention performance and identify churn concentration by segment and region.

## Proposed star schema

### Dimensions
- `Date` — calendar support for time slicing.
- `Customer` — customer attributes and keys.
- `Segment` — segment classification.
- `Region` — geographic classification.

### Fact
- `Customer Status` — one row per customer-month with active/churn flags, tenure, revenue.

### Measures (`Key Measures`)
- `Active Customers`
- `Churned Customers`
- `Churn Rate %`
- `Retention Rate %`
- `Monthly Revenue`
- `ARPU`

## Grain and relationships
- Fact grain is customer × month.
- Relationship paths:
  - `Customer Status[Date]` → `Date[Date]`
  - `Customer Status[Customer ID]` → `Customer[Customer ID]`
  - `Customer[Segment ID]` → `Segment[Segment ID]`
  - `Customer[Region ID]` → `Region[Region ID]`

## Modeling rules applied
- Star schema with dimensions on one-side and fact on many-side.
- Surrogate/business keys hidden where appropriate.
- All business calculations via explicit measures in disconnected `Key Measures`.
- Data imported using `DataFolder` parameterized file path.
