# Masters: Items, Tax/Currency Reference Data, Organization, Workflow, Users

Covers the remaining Masters-menu entries not documented in their own files: **Items** (+ Item Categories/Items Type), **HSN Code**, **Currencies**, **Workflow**, **Organization** (Division/Departments/Cost Centres/Locations), and **Users/Invite Users**. All follow the same standard grid list template already established (Active/Inactive/All radio, Records + Search, Status/Import/Export, green **+ Create** button, Back, gear icon) — that chrome is not re-described per-module below, only what's distinctive.

## Items (`COM_LIST_ITEMS` / `COM_INSERT_ITEMS`)

### 1. Purpose
Master catalog of purchasable/sellable goods and services — the line-item source for both Supplier Bills and (presumably) Sales Invoices, carrying full inventory tracking and general-ledger account mappings.

### 2. Navigation
Masters mega-menu → Items column → **Items**. List filters beyond the standard set: **Item Type**, **Purchase UoM**, **Sale UoM**, **Category** (all default "All"). Create via **+ Items**. Screenshots: `47-items-list.png` (empty state: "Hello! There are no records added yet."), `48-items-create-form.png` (full page).

### 3–4. UI & data fields
The create form is the richest single form seen in this tenant, organized into 6 fieldsets:

**Items** (identity/classification): Item Name* (text), SKU (text), Ref SKU (text), Item Description (textarea), Specifications (text), Category* (dropdown) / Child Category / Sub-Child Category (cascading, up to 3 levels deep), Item Type (dropdown), Costing Type (dropdown), Sold Individually (toggle), Sold In Groups (toggle), Quality Check (toggle), Item Image / Item Image2 / Item Image3 (3 separate image upload slots).

**Price**: Purchase UoM* + Purchase Price, Sale UoM* + Sale Price, Minimum Sale Price (greyed/computed), Maximum Discount(%), Minimum Margin(%).

**Inventory**: Opening Quantity + Opening Quantity Date (defaults today), Warehouses (dropdown), Quantity Check (radio: Yes/No/Not Applicable), Minimum Level / Danger Level / Maximum Capacity / Reorder Qty (all numeric thresholds), Tracking Type (radio: **None / Serial / Lot** — defaults None), Negative Stock (toggle), Allow Back Order (toggle).

**Account** (chart-of-accounts mapping — 22 separate GL account dropdowns, all "Select Any one"): Inventory Asset, Inventory Adjustment, Inventory Cost Variance, Cogs, Purchase Expense, Sales Revenue, Sales Discount, Sales Return, Purchase Price Variance, Freight Expense, Packaging Expense, **Sales Tax Payable**, **Purchase Tax Payable**, Promotions And Discounts, Sales Allowances, Deferred Revenue, Ap Account, Ar Account, Accrued Expenses, Prepaid Expenses, Wip Asset, Goods Received Not Invoiced, Invoiced Not Received, Goods Sold Not Invoiced. This confirms Billstack items post to a full double-entry GL behind the scenes, with distinct tax-payable accounts for sales vs. purchase.

**Tax**: HSN/SAC Code (dropdown, sourced from the HSN Code master below), HS Code (separate dropdown — customs/import Harmonized System code, distinct from the domestic-GST HSN/SAC field).

**Specification Documents**: File Name + Download/Upload, repeatable via "+ Add More".

**Vendor Items / Customers Items** (two side-by-side repeatable blocks): Vendors/Part No/Part Name and Customers/Part No/Part Name — lets one internal Item be cross-referenced to multiple vendors' and customers' own part numbers/names for the same product.

### 5. Underlying data structure (inferred)
**Item** 1—1 mapping to ~22 **GL Account** FKs (per transaction type), 1—N **VendorItemCrossRef** and **CustomerItemCrossRef**, FK to **HSN/SAC Code** and **HS Code**, FK to up to 3-level **Category** hierarchy, FK to **Warehouse**. This is the central master that Supplier Bill / Sales Invoice line items reference (see `supplier-bills.md` §4 Products/Services grid).

### 6–8. Functionality / conditions
Not submitted (task rule). Empty on this tenant (0 items) — meaning **no Supplier Bill or Sales Invoice line item can currently be completed end-to-end**, consistent with Vendors/Customers also being empty. Item Categories (`COM_LIST_ITEM_CATEGORIES`) and Items Type (`COM_LIST_ITEM_TYPES`) list pages were not opened individually in this pass (time-boxed) — both are simple lookup-table masters feeding the Category/Item Type dropdowns above; **gap**, not confirmed empty or populated.

---

## HSN Code (`COM_LIST_HSN_AND_SAC_CODES`)

### 1. Purpose
Platform-wide, pre-seeded master of Indian GST **HSN** (goods) and **SAC** (services) classification codes with their associated GST rate — the authoritative tax-rate lookup that Items/Invoices reference.

### 2–3. Navigation & UI
Masters → Others → HSN Code. **Unlike Vendors/Customers/Items/Division, this table is NOT empty** — it is fully pre-populated with what appears to be the complete official HSN code list (observed rows span at least chapter 01 "Live Animals" with dozens of sub-codes; e.g. `0101.21.00` "Live Horses" at 12.00% GST, most animal codes at 0%). List columns: **Code, Code Description, Gst, Action** (edit/duplicate/view icons). Screenshot: `49-hsn-codes-list.png`.

Detail view (`COM_VIEW_HSN_AND_SAC_CODES`) shows a helpful in-app description banner: *"This function is used for maintaining Hsn And Sac Codes... essential for accurate classification and compliance with GST regulations. Defining HSN and SAC codes ensures smooth filing of returns and correct GST rate application during transactions."* Fields: **Code**, **Code Description**, **Gst** (single flat percentage — no separate CGST/SGST/IGST split stored here; that split is presumably computed at transaction time from the Tax State field, see `supplier-bills.md` §4). Screenshot: `50-hsn-code-detail-view.png`.

### 5. Underlying data structure
**HsnSacCode** entity: Code (string, dotted format like `0101.21.00`), Description, GST rate (decimal %). Referenced by `Item.hsnSacCode` FK.

### 8. Data conditions
This being the one fully-seeded reference master (vs. all-empty tenant data) confirms HSN/SAC codes are shipped as **platform-level reference data**, not tenant-entered — new tenants get GST compliance "for free" without manual code entry.

---

## Currencies (`COM_LIST_CURRENCIES`)

### 1–3. Purpose, navigation, UI
Masters → Others → Currencies. Also pre-seeded platform-wide (not empty) with what looks like the full ISO currency-by-country list (Afghanis/AFN/Afghanistan, Leke/ALL/Aland Islands, Pesos/ARS/Antigua And Barbuda, etc. — note some Country associations look like data-quality artifacts, e.g. ALL/Albania's currency mapped to "Aland Islands" rather than Albania). Columns: **Name, Short Code, Country, Currency List** (symbol-Name-Country composite string), **Hundreds Name** (all "none" observed), **Decimal Places** (2 for all observed rows), **Exchange Rate**, **Action**. Screenshot: `51-currencies-list.png`.

### 8. Data conditions
**Every observed Exchange Rate reads ₹1.00** — i.e. the multi-currency conversion table exists structurally but has **no real live/manually-updated exchange rates configured** on this tenant. Combined with the tenant Config's `Default Currency = Rupees` and no multi-currency activity anywhere else observed, this strongly suggests **Bharatnet's Billstack usage is INR-only in practice**, even though the platform supports multi-currency. Documented as an observed data condition, not a limitation of the software itself.

---

## Workflow (`COM_LIST_WORKFLOW_CONFIGURATIONS_NEW`)

### 1. Purpose
Configures multi-level approval workflows for various transaction types.

### 3. UI / data
Columns: **Process Name, Process Description, Workflow Level, Action** (edit only — no create/delete affordance visible, this looks like a fixed set of system processes rather than user-definable workflows). Pre-configured rows observed (all "Active", not empty): **requisitions** ("approval process of Requisitions or Indents", level 8), **quotations** (level 8), **purchase_orders** (level 8), **goods_receipt_notes** ("GRN Workflow", level 8), **application_table_quality_check** ("Quality Check", level 1), **grn_returns** (level 8), **stock_requests** (level 8), **stock_transfers** (level 8). Screenshot: `52-workflow-list.png`.

### 5. Underlying data structure (inferred)
This is strong **independent confirmation of the full procure-to-pay chain** already inferred from the Supplier Bill line-item grid's SO/PO/Invoice Qty/GRN Qty reference columns (`supplier-bills.md` §5): **Requisition → Quotation → Purchase Order → Goods Receipt Note (GRN)**, each with up to an 8-level approval chain, plus separate Quality Check, GRN Returns, Stock Requests, and Stock Transfers workflows. None of the upstream screens (Requisitions/Quotations/POs/GRNs themselves) were found in this Billstack login's Masters/New menus — they likely live in a separate Procurement/Purchasing module of the Infistaq platform not exposed here, or require a different role. **No explicit `supplier_bills` or `sales_invoices` workflow row was found** in the visible portion of this list (searched for "invoice", result inconclusive — search interaction may not have filtered; not fully confirmed either way, gap). Screenshot: `53-workflow-search-invoice.png`.

---

## Organization: Division / Departments / Cost Centres / Locations

Masters → Organization column → **Division** (`COM_LIST_DIVISIONS`), Departments (`COM_LIST_DEPARTMENTS`), Cost Centres (`COM_LIST_COST_CENTERS`), Locations (`COM_LIST_LOCATIONS`). Only **Division** was opened in this pass: standard empty-state grid ("Hello! There are no records added yet."), **+ Division** create button not opened (time-boxed — gap). Screenshot: `55-divisions-list.png`.

These feed the **Cost Center** dropdown seen on the Supplier Bill create form (`supplier-bills.md` §4) and the **Cost Center** column on the Users grid (see below) — at least one numeric cost-center code ("27") is already in use by a user despite the Division/Cost Centre masters themselves appearing unpopulated, suggesting cost centers may be entered as free codes rather than strictly validated against this master, or that the Cost Centres master (not directly opened) already has that code seeded. **Not confirmed — gap.**

---

## Users (`COM_LIST_APPLICATION_USERS`) / Invite Users (`COM_LIST_APPLICATION_USER_INVITATIONS`)

### 1–3. Purpose, navigation, UI
Masters → Users column → **Users**. List filters: Location, Country, Reports To, User Type (all "All" by default), plus standard Records/Search. Columns: **Id, User Name, Display Name, User Email, User Mobile, Cost Center, Location, Country, Timezone, Mapping Code, Reports To, User Type** (+ presumably Action, cut off in viewport). Screenshot: `54-users-list.png`.

**Not empty** — shows the same 3 platform users already documented from the CRM side (`docs/crm-bharatnet/leads.md` Originator options): 

| Id | User Name | Display Name | Email | Mobile | Cost Center | User Type |
|---|---|---|---|---|---|---|
| 1 | INFI00114 | Anthony Johnston | admin@bharatnet.infisuite.in | 7281816472 | 27 | SYSTEM |
| 2 | CRM Admin | CRM Admin | crm_admin@bharatnet.infisuite.in | — | — | SYSTEM |
| 3 | Billstack Admin | Billstack Admin | billstack_admin@bharatnet.infisuite.in | — | — | SYSTEM |

This **confirms the Users table is tenant-wide and shared across the CRM and Billstack modules** (not module-scoped) — consistent with the platform-wide Settings/Display-Attributes findings in `00-navigation-map.md`. All 3 are typed "SYSTEM" (vs. presumably "STAFF"/other types available via the User Type filter — not enumerated, gap). User #1 (Anthony Johnston / "INFI00114") is the only one with a Cost Center (27), Mobile, and a distinct system-generated User Name format, suggesting they are the original platform-provisioning/super-admin account, with CRM Admin and Billstack Admin created afterward as per-module admin logins for this task.

**Invite Users** (`COM_LIST_APPLICATION_USER_INVITATIONS`) was not opened in this pass — gap.

### 6–8. Functionality / conditions
No Create/Edit forms opened for Users in this pass (read-only list view only) — gap. The presence of real (non-empty) user data here, contrasted with all-empty Vendors/Customers/Items/Division, confirms this tenant was set up with only user accounts and reference/lookup masters (HSN, Currencies, Workflow processes) pre-seeded, with all **transactional and party-master data** (Vendors, Customers, Items, actual bills) left for the person(s) actually operating the system to enter — this tenant is in a **freshly-provisioned, pre-go-live state**.
