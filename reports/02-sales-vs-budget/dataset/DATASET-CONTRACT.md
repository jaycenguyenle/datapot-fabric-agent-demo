# Dataset contract — Sales vs Budget (VANNAVARSDEL)

This file defines what `dataset/raw/` must contain for report 02. It is the contract between the
data producer and the semantic model. All Power Query partitions read files relative to the
`DataFolder` parameter.

Dataset name: **VANNAVARSDEL**

> Status: baseline data is present in `dataset/raw/`. Keep this contract updated whenever schema changes.

## Scope

Primary business use case for this report:
- Compare actual sales/performance against monthly budget targets.
- Analyze gaps by branch, product, customer segment, and channel.

## Required files consumed by this report

### `Transactions/transactions_YYYY-MM.csv` -> fact `Transactions` (actuals)

Naming rule:
- One file per month, `YYYY-MM` format.

Minimum required columns:

| Column | Type | Notes |
|--------|------|-------|
| Date | date | transaction date |
| BranchID | text | joins to `Branch` |
| ProductID | integer | joins to `Product` |
| CustomerID | integer | joins to `Customer` |
| ChannelID | text | joins to `Channel` (derived if needed) |
| TransactionTypeID | text | joins to `Transaction Type` (derived if needed) |
| Amount_VND | integer | signed amount |
| Fee_VND | integer | non-negative |

Optional columns (if available):
`TransactionID`, `ChannelName`, `TransactionTypeName`, `Direction`, `ServiceTimeMinutes`.

### `Targets/BranchTargets.xlsx` (sheet `MonthlyTargets`) -> fact `Branch Targets` (budget)

Required shape:
- Grain: Branch + TargetType + Month.
- Source can be wide format, but model will unpivot month columns.

Required columns before unpivot:

| Column | Type | Notes |
|--------|------|-------|
| BranchID | text | joins to `Branch` |
| BranchName | text | descriptive |
| TargetType | text | example: `Total Revenue (VND)` |
| `YYYY-MM` month columns | number | monthly budget values |

### `Dimensions/BankDIAD_Dimensions.xlsx` -> dimensions

Required sheets and keys:

| Sheet | Model table | Key columns |
|-------|-------------|-------------|
| DimBranch | `Branch` | BranchID, BranchName, Region, BranchType |
| DimProduct | `Product` | ProductID, ProductName, ProductCategory |
| DimCustomer | `Customer` | CustomerID, Segment, HomeBranchID |
| DimDate | `Date` | Date, Year, Month, MonthYear |

Derived dimensions:
- `Channel` and `Transaction Type` may be derived from distinct values in `Transactions`.

## Secondary file currently consumed by model

### `NPSSurveys/nps_YYYY-MM.csv` -> fact `NPS Surveys`

This file is used by the current semantic model but is secondary for Sales vs Budget analysis.
Required columns:
`SurveyID`, `SurveyDate`, `CustomerID`, `BranchID`, `Score`, `NPSCategory`, `PrimaryReasonID`, `PrimaryReason`.

## Files present but out of current report scope

| File | Purpose |
|------|---------|
| `Dimensions/Accounts.csv` | future account-level analysis |
| `AccountSnapshots/snapshot_YYYY-MM.csv` | future balance trend analysis |
| `CustomerProductMonth/cpm_YYYY-MM.csv` | future customer product-holding analysis |
| `LoanOriginations/origination_YYYY-MM.csv` | future lending/origination analysis |

## Data quality and refresh rules

- Encoding: UTF-8.
- Monthly partition files must follow the exact naming pattern.
- Column names are case-sensitive for ingestion scripts; do not rename without model update.
- Keys must be non-null where used for joins (`BranchID`, `ProductID`, `CustomerID`, `Date`).
- Budget values should be non-negative unless explicitly defined otherwise.
- On schema change: update this contract, `dictionary/data-dictionary.md`, and relevant `*.tmdl` partitions together.
