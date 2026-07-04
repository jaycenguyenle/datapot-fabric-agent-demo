# Dataset contract — Vanardel Revenue & Customer Performance

All source files must be placed in `dataset/raw/`.

- **Report scope:** Vanardel commercial performance across customers, products, channels, and monthly order activity.
- **File formats:** CSV (UTF-8, comma-delimited, header row required) unless explicitly noted.
- **Naming rule:** file names and columns use `snake_case`.

## Files

### 1) `customer_dim.csv`
- **Grain:** one row per customer.
- **Refresh cadence:** monthly full refresh.
- **Columns:**
  - `customer_id` (string) — unique customer key. **PK**
  - `customer_name` (string) — customer display name.
  - `customer_segment` (string) — business segment label.
  - `region_id` (string) — region foreign key. **FK → region_dim.region_id**
  - `country_code` (string) — ISO country code (for example `VN`).
  - `onboard_date` (date) — first active date for customer.
  - `is_active` (boolean; 0/1) — active status flag.

### 2) `region_dim.csv`
- **Grain:** one row per region.
- **Refresh cadence:** low-change, update as needed.
- **Columns:**
  - `region_id` (string) — unique region key. **PK**
  - `region_name` (string) — region label.
  - `region_group` (string) — higher-level cluster (for example North/Central/South).

### 3) `product_dim.csv`
- **Grain:** one row per product.
- **Refresh cadence:** monthly full refresh.
- **Columns:**
  - `product_id` (string) — unique product key. **PK**
  - `product_name` (string) — product display name.
  - `category` (string) — product category.
  - `sub_category` (string) — product subcategory.
  - `launch_date` (date) — product launch date.
  - `is_discontinued` (boolean; 0/1) — discontinued flag.

### 4) `channel_dim.csv`
- **Grain:** one row per channel.
- **Refresh cadence:** low-change, update as needed.
- **Columns:**
  - `channel_id` (string) — unique channel key. **PK**
  - `channel_name` (string) — channel label.
  - `channel_type` (string) — channel grouping (for example Digital/Assisted/Partner).

### 5) `order_header_YYYY-MM.csv`
- **Grain:** one row per order.
- **Refresh cadence:** monthly append.
- **Columns:**
  - `order_id` (string) — unique order key. **PK**
  - `order_date` (date) — order booking date.
  - `customer_id` (string) — customer key. **FK → customer_dim.customer_id**
  - `channel_id` (string) — channel key. **FK → channel_dim.channel_id**
  - `order_status` (string) — order lifecycle status (`booked`, `completed`, `cancelled`, `returned`).
  - `currency_code` (string) — ISO currency code (expected `VND` for current scope).
  - `gross_amount` (decimal) — pre-discount order amount.
  - `discount_amount` (decimal) — order-level discount amount.
  - `net_amount` (decimal) — `gross_amount - discount_amount`.

### 6) `order_line_YYYY-MM.csv`
- **Grain:** one row per order line.
- **Refresh cadence:** monthly append.
- **Columns:**
  - `order_line_id` (string) — unique order line key. **PK**
  - `order_id` (string) — order key. **FK → order_header_YYYY-MM.order_id**
  - `product_id` (string) — product key. **FK → product_dim.product_id**
  - `quantity` (int64) — quantity sold on the line.
  - `unit_price` (decimal) — unit selling price.
  - `line_discount_amount` (decimal) — line-level discount amount.
  - `line_net_amount` (decimal) — net line amount after discount.

## Data quality expectations

- **Uniqueness**
  - `customer_dim.customer_id`, `region_dim.region_id`, `product_dim.product_id`, `channel_dim.channel_id` are unique and non-null.
  - `order_header_YYYY-MM.order_id` is unique and non-null per file.
  - `order_line_YYYY-MM.order_line_id` is unique and non-null per file.
- **Referential integrity**
  - Every `order_header.customer_id` exists in `customer_dim.customer_id`.
  - Every `order_header.channel_id` exists in `channel_dim.channel_id`.
  - Every `order_line.order_id` exists in `order_header.order_id`.
  - Every `order_line.product_id` exists in `product_dim.product_id`.
  - Every `customer_dim.region_id` exists in `region_dim.region_id`.
- **Allowed values / ranges**
  - `order_status ∈ {booked, completed, cancelled, returned}`.
  - Boolean flags use only `0` or `1`.
  - `quantity > 0`.
  - `gross_amount >= 0`, `discount_amount >= 0`, `net_amount >= 0`, `line_net_amount >= 0`.
  - `discount_amount <= gross_amount`.
- **Nullability**
  - All primary keys and foreign keys are required (non-null).
  - Measure-driving numeric columns in facts (`quantity`, `gross_amount`, `net_amount`, `line_net_amount`) are required.
  - Descriptive attributes (`sub_category`, `channel_type`) may be null only if not yet classified.
- **Date rules**
  - Date format: ISO `YYYY-MM-DD`.
  - `order_date` must fall within the month indicated by file name `order_header_YYYY-MM.csv`.
  - `onboard_date` and `launch_date` must be on or before current extraction date.

## Refresh / change rules

- Add each new monthly period as new files following the same patterns:
  - `order_header_YYYY-MM.csv`
  - `order_line_YYYY-MM.csv`
- Do not rename files, reorder columns, or change column types without coordinated model updates.
- Allowed additive change: append new nullable descriptive columns after review.
- Breaking changes (rename, remove, type change, semantic change) require updating:
  1. semantic model partitions and mappings in `pbip/<Project>.SemanticModel/definition/tables/*.tmdl`
  2. `dictionary/data-dictionary.md` and `dictionary/data-dictionary.csv`
  3. this contract file before new data is pasted.

## Out-of-scope / reserved files (optional)

These files are reserved for future Vanardel report expansions and are not consumed by the current report scope:

- `returns_YYYY-MM.csv` — return reason analytics and refund timing.
- `marketing_spend_YYYY-MM.csv` — campaign ROI and CAC analysis.
- `inventory_snapshot_YYYY-MM.csv` — stock health and stockout impact analysis.
