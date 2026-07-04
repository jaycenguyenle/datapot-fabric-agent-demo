# Build log — Customer Retention Scorecard

## 2026-07-04
- Created report folder `reports/02-customer-retention` with standard contract structure.
- Documented scope, KPIs, grain, and page plan in `docs/report-spec.md`.
- Added synthetic initial input files to `dataset/raw/` and mirrored to `dataset/samples/`.
- Completed data profiling and reconciliation notes in `docs/data-profile.md`.
- Authored first-pass semantic model in TMDL with dimensions (`Date`, `Customer`, `Segment`, `Region`) and fact (`Customer Status`).
- Added explicit retention measures in disconnected `Key Measures` table.
- Built PBIR report shell with pages: Overview, Drivers, Scorecard.
- Updated data dictionary (`.md` + `.csv`) and report registry entry.
