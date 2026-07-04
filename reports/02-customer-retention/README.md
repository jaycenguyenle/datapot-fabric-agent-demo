# 02 — Customer Retention Scorecard

> Track customer retention and churn by segment and region, and monitor monthly revenue quality.

| | |
|---|---|
| **Domain** | Banking — customer retention |
| **Status** | 🟡 In progress — model and report shell created |
| **Audience** | Retail banking growth leads, CRM managers, regional managers |
| **Semantic model** | `pbip/CustomerRetention.SemanticModel` (TMDL, Import) |
| **Report** | `pbip/CustomerRetention.Report` (PBIR) |
| **Data** | Synthetic sample files in `dataset/raw/` |
| **Refresh** | Monthly — replace input CSVs in `dataset/raw/` |

## Contents
- `dataset/` — source files + [DATASET-CONTRACT.md](dataset/DATASET-CONTRACT.md)
- `dictionary/` — [data-dictionary.md](dictionary/data-dictionary.md) · [.csv](dictionary/data-dictionary.csv)
- `docs/` — [report-spec](docs/report-spec.md) · [model-design](docs/model-design.md) · [data-profile](docs/data-profile.md) · [build-log](docs/build-log.md)
- `pbip/` — Power BI project (`CustomerRetention.pbip`)

## How to open / refresh
1. Ensure `dataset/raw/` contains the contracted files.
2. Open `pbip/CustomerRetention.pbip` in Power BI Desktop.
3. Update the `DataFolder` parameter to your local raw-data absolute path and refresh.
4. Run JSON/TMDL validation checks before commit.

## Status notes
- ✅ Scope, KPIs, grain, and dimensions/facts documented.
- ✅ Initial synthetic source files added and profiled.
- ✅ Initial star-schema semantic model created.
- ✅ Three-page PBIR shell created (Overview, Drivers, Scorecard).
- ⏳ Visual build-out and advanced DAX still pending.
