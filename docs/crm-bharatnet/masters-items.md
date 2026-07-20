# Masters > Items

## 1. Purpose

The product/service/asset catalog master — a full inventory-and-accounting item record (SKU, pricing, stock tracking, GL account mappings, tax codes), not a CRM-only concept. This is the entity referenced by the Deal "Item Search" panel (`deals.md` §4) and by the Home dashboard's "Pending BOM Returns" widget (`feasibility.md`). Confirms the Infistaq platform is a shared ERP where CRM is one vertical slice.

## 2. Navigation path

Top nav → **Masters** (mega-menu) → **Items** → `index.php?option=COM_LIST_ITEMS`.

Create form: green **"+ Items"** button on the list view → `index.php?option=COM_INSERT_ITEMS`.

## 3. UI / page design

**List view** (`masters-items-01-list-view.png`): follows the standard Masters/list template noted in `00-navigation-map.md` §6 — Active/Inactive/All radio filter, Records page-size dropdown (default 20, options up to 100 or "All"), Search box ("Min 3 Characters Required"), 4 filter dropdowns (**Item Type**, **Purchase UoM**, **Sale UoM**, **Category**), a **Status** dropdown-button, **Import**/**Export** links, green **+ Items** create button, **Back** button, and a gear icon (per-column Display Attributes config — see §5). Empty state: "Hello! There are no records added yet. Kickstart by adding a your first record" with a shortcut "Items" button — a friendlier/different empty-state copy than the CRM tabs' illustrated "No <Entity>" panels, confirming Masters list views are a different UI component family from the CRM dashboard tabs.

**Create form** (`masters-items-02-create-form.png`, full page, very long) is a two-column layout with 7 card sections:

### Items (left column)
| Field | Field name | Type | Required | Notes |
|---|---|---|---|---|
| Item Name | `item_name` (inferred) | text | **Yes** (*) | |
| SKU | `sku` | text | No | |
| Ref SKU | `ref_sku` | text | No | |
| Item Description | `item_description` | multi-line text | No | |
| Specifications | `specifications` | text | No | |
| Category | `category_id` | dropdown, default "Select Any one" | **Yes** (*) | Empty in this tenant |
| Child Category | `child_category_id` | dropdown, default "Select Any One" | No | Cascades from Category (3-level category tree: Category → Child Category → Sub-Child Category) |
| Sub-Child Category | `sub_child_category_id` | dropdown | No | |
| Item Type | `item_type` | dropdown, default "Select Any one" | No (no asterisk, though functionally important) | 3 values: `1` Stock Item, `2` Services, `3` Assets — same enum reused in Deal's Item Search filter |
| Costing Type | `default_costing_type` | dropdown | No | 4 values: FIFO [UI typo: "FIFIO"], LIFO, STANDARD, WEIGHTED AVERAGE |
| Sold Individually | toggle | No | Boolean, default off |
| Sold In Groups | toggle | No | Boolean, default off |
| Quality Check | toggle | No | Boolean, default off — ties to the "Quality Control" sub-item seen under Masters > Customer Managements in the Settings tree |
| Item Image / Item Image2 / Item Image3 | file upload ×3 | No | Up to 3 images per item |

### Price
| Field | Field name | Type | Required |
|---|---|---|---|
| Purchase UoM | `purchase_uom` | dropdown, default "Select Any one Purchase UoM" | No |
| Purchase Price | `purchase_price` | number | No |
| Sale UoM | `sale_uom` | dropdown, default "Select Any one Sale UoM" | No |
| Sale Price | `sale_price` | number | No |
| Minimum Sale Price | `minimum_sale_price` | number (rendered read-only/greyed in this screenshot) | No |
| Maximum Discount(%) | `minimum_discount`(!) | number | No — note the field *labeled* "Maximum Discount" maps to a form field literally named `minimum_discount` in the underlying schema (per the Display-Attributes field list), a naming inconsistency between the UI label and the DB/attribute name |
| Minimum Margin(%) | `minimum_margin` | number | No |

**Purchase UoM / Sale UoM shared 32-value unit-of-measure list** (sample): Barrel, Box, Case, Centimeter, Cubic Foot, Cubic Meter, Dozen, Each, Fluid Ounce, Foot, Gallon, Gram, Inch, Kilogram, … (32 total, not fully enumerated here — a standard UoM master, see Settings > Items > UOM in `00-navigation-map.md`).

### Inventory
| Field | Field name | Type | Required |
|---|---|---|---|
| Opening Quantity | `opening_quantity` | number | No |
| Warehouses | `warehouse_id` | dropdown, default "Select Any one" | No — empty in this tenant |
| Opening Quantity Date | `opening_quantity_date` | date | No — defaulted to current date (20-07-2026) |
| Quantity Check | radio: Yes / No / Not Applicable | No | Default appears unset in screenshot |
| Minimum Level | `minimum_level` | number | No |
| Danger Level | `danger_level` | number | No |
| Maximum Capacity | `maximum_capacity` | number | No |
| Reorder Qty | `reorder_qty` | number | No |
| Tracking Type | radio: **None** (default) / Serial / Lot | No | `tracking_type` — item-level serial/lot/no tracking |
| Negative Stock | toggle | No | Boolean, default off |
| Allow Back Order | toggle | No | Boolean, default off |

### Account (right column — full chart-of-accounts mapping per item)
21 GL-account dropdowns, all "Select Any one" / empty in this tenant: Inventory Asset Account, Inventory Adjustment Account, Inventory Cost Variance Account, Cogs Account, Purchase Expense Account, Sales Revenue Account, Sales Discount Account, Sales Return Account, Purchase Price Variance Account, Freight Expense Account, Packaging Expense Account, Sales Tax Payable Account, Purchase Tax Payable Account, Promotions And Discounts Account, Sales Allowances Account, Deferred Revenue Account, Ap Account, Ar Account, Accrued Expenses Account, Prepaid Expenses Account, Wip Asset Account, Goods Received Not Invoiced Account, Invoiced Not Received Account, Goods Sold Not Invoiced Account. This is unambiguously a full double-entry-accounting item template — confirms items post to a general ledger (out of CRM scope, presumably a Billstack/accounting-module concern surfaced here for item setup).

### Tax
| Field | Field name | Type | Required |
|---|---|---|---|
| HSN/SAC Code | `hsn_and_sac_code_id` | dropdown, default "Select Any One" | No — empty in this tenant (India GST classification code, ties to the Settings > Others > HSN and SAC Codes master) |
| HS Code | `hs_code` | dropdown, default "Select Any one" | No — **21 pre-seeded international Harmonized System codes** visible (e.g. `01012100 - Live horses, purebred breeding animals`, `01022100 - Live cattle, purebred breeding animals`, `01023100 - Live sheep`…) — this looks like generic seed/demo data shipped with the product, not customer-entered data, and is a strong signal this ERP template targets agricultural/commodity trade by default. |
| SGST / CGST / IGST | (fields present per the Display Attributes field list: `sgst`, `cgst`, `igst`) | number | Not visible in the screenshotted viewport but confirmed to exist in the schema — India's 3-part GST tax split |

### Specification Documents
Repeatable file-attachment rows: **File Name** (text) + **Download/Upload** (file picker with upload icon) + delete icon, **"+ Add More"** button to add additional rows.

### Vendor Items
Repeatable rows: **Vendors** (dropdown, empty in this tenant), **Part No** (text), **Part Name** (text), delete icon, **"+ Add More"**. Lets one Item be cross-referenced to multiple Vendor part numbers.

### Customers Items
Repeatable rows: **Customers** (dropdown, empty in this tenant), **Part No** (text), **Part Name** (text), delete icon, **"+ Add More"**. Lets one Item be cross-referenced to multiple Customer-specific part numbers — this is the field wired to the `customer_id` filter seen on the Deal "Item Search" panel.

**Action:** **Submit** button (not exercised, per task rules).

## 4. Full field/schema list (via Display Attributes column-picker, 84 fields)

The list view's gear-icon column-configuration control exposes the complete underlying field set for the Items table (`displayattributesfilterfields[]`, confirmed via DOM query, not just the create-form's visible subset): `id, company_id, sku, item_name, item_type, item_description, purchase_uom, sale_uom, sale_price, purchase_price, opening_quantity, category_id, tax_id, tax_ref_code, item_image, item_image2, item_image3, allow_back_order, default_costing_type, sold_individually, sold_in_groups, tracking_type, quality_check, specifications, hsn_and_sac_code_id, minimum_sale_price, conversion_box, minimum_discount, minimum_margin, maximum_capacity, negative_stock, danger_level, reorder_qty, reorder_days, sgst, cgst, igst, barcode, qrcode, Hazardous, stocking_type, catalog_no, abc_codes, ref_sku, quantity_check, minimum_level, hs_code, inventory_asset_account, inventory_adjustment_account, inventory_cost_variance_account, cogs_account, purchase_expense_account, sales_revenue_account, sales_discount_account, sales_return_account, purchase_price_variance_account, freight_expense_account, packaging_expense_account, sales_tax_payable_account, purchase_tax_payable_account, promotions_and_discounts_account, sales_allowances_account, deferred_revenue_account, ap_account, ar_account, accrued_expenses_account, prepaid_expenses_account, wip_asset_account, goods_received_not_invoiced_account, invoiced_not_received_account, purchase_base_uom, sale_base_uom, goods_sold_not_invoiced_account, warehouse_id, opening_quantity_date, is_active, current_status, status_date, from_date, to_date, created_date, created_by, updated_by, updated_date`.

This confirms several fields that exist in the DB but are **not** exposed on the create form observed (e.g. `barcode`, `qrcode`, `Hazardous`, `stocking_type`, `catalog_no`, `abc_codes`, `reorder_days`, `conversion_box`, `purchase_base_uom`, `sale_base_uom`, `tax_id`/`tax_ref_code`, `company_id`, `current_status`/`status_date`/`from_date`/`to_date`, standard audit columns `created_date/created_by/updated_by/updated_date`) — likely populated via Import, a later edit screen, or simply unused fields carried over from a shared platform template. Documented as inferred from schema, not confirmed via UI.

## 5. Underlying data structure (inferred)

**Entity: Item.** Relationships:
- `Item.category` → 3-level self-referencing or separate Category/ChildCategory/SubChildCategory hierarchy
- `Item.warehouse` → **Warehouse** master
- `Item.hsnSacCode` → **HSN/SAC Code** master (India tax classification)
- `Item.vendors` → many **Vendor** (via Vendor Items rows, each with its own Part No/Part Name)
- `Item.customers` → many **Customer** (via Customer Items rows) — this is the FK the Deal Item Search "Customer" filter queries against
- `Item.*Account` fields → many **Chart of Accounts** entries (21 distinct GL account roles per item)
- Referenced by: **Deal** (via Item Search/attach, `deals.md`), **Feasibility**'s BOM-return tracking (`feasibility.md`)

## 6. Functionality / logic observed

- **List** (with Active/Inactive/All status filter, Item Type/Purchase UoM/Sale UoM/Category filters, search, configurable page size)
- **Create** (`+ Items`, inspected in full, not submitted)
- **Import** (`index.php?option=COM_INSERT_IMPORT_EXCEL_DATA` — a generic shared import handler, not Items-specific by URL, entry point noted but not exercised)
- **Export** (link present, `href="#!"` placeholder — not exercised)
- **Column/Display-Attributes configuration** (gear icon) — lets a user choose which of the 84 underlying fields show as list columns; see `display-attributes.md`
- Edit/Delete/Detail-view: not reachable, 0 Items exist in this tenant

## 7. Inputs and outputs

- Input: the multi-section create form above.
- Output (expected, not observed): new Item record, becomes selectable in the Deal "Item Search" panel and in any other module's Item picker (Vendor Items/Customer Items cross-references).

## 8. Data conditions

- Only Item Name and Category carry a visible required asterisk on the create form — every other field, including Item Type, Purchase/Sale UoM, and pricing, is technically optional at creation time, which is unusual for a field this operationally central; likely enforced later (e.g. required before the item can be sold/purchased) rather than at record-creation time.
- All FK dropdowns (Category, Warehouse, HSN/SAC Code, all 21 Account fields, Vendors, Customers) are empty in this tenant — none of the dependent Masters records exist, so the item's relational richness could not be exercised end-to-end.
- The HS Code dropdown is the **only** populated reference dropdown found anywhere in this CRM/Masters exploration (21 seeded international commodity codes) — everything else in this tenant is genuinely zero-configured.
