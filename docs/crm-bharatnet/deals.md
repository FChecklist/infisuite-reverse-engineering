# Deals

## 1. Purpose

The sales-opportunity/pipeline record — an amount of budget being pursued with a specific Company/Contact, optionally composed of specific line-item products/services pulled from Masters > Items.

## 2. Navigation path

Home (top nav) → **Deals** tab in the CRM tab strip. URL: `index?TRANST=crm_deals`.

The **+ Deal** button opens the create form (right-side slide-over).

## 3. UI / page design

Same dashboard-widget pattern as Leads/Contacts/Companies (see `leads.md` §3): heading **"Deals (<date range>)"** with the shared date-range filter, **+ Deal** button, "No Campaigns" empty-state illustration (note: the empty-state copy says "No Campaigns", not "No Deals" — likely a shared/mislabeled empty-state component, a minor product bug), adjacent **Status** widget ("No Data"). No grid/table/search/pagination reachable — 0 Deals in this tenant. Screenshot: `deals-01-list-view.png`.

A **"+ Lead/Contact Assignings"** button/link is visible in the tab strip header on every CRM tab (not deal-specific) — see `lead-contact-assignings.md`.

## 4. Create Deal form ("+ Deal")

Right-side slide-over titled **"Add Deal"**, sections: Deal Information, a right-hand **Contacts** related-panel, an **Item Search** panel, and a **Description** field with Submit/Reset. Screenshots: `deals-02-create-form-top.png`, `deals-03-source-type-options.png`, `deals-04-remark-and-item-search.png`.

### Deal Information
| Field | Field name (attr) | Type | Required | Notes |
|---|---|---|---|---|
| Deal Name | `deal_name` | text | **Yes** (*) | |
| Originator | `originator` | searchable dropdown | **Yes** (*) | Defaults to logged-in user ("CRM Admin"); FK → User. Same option set as Lead/Contact Originator: Anthony Johnston (1), CRM Admin (2), Billstack Admin (3). |
| Source Type | `source_type` | searchable dropdown | **Yes** (*) | Default "Select Any One". Same underlying 9-code source list as Lead Source (see `leads.md`): `SELF` Self, `FCBK` "Facebok" [sic — spelled differently than Leads' "Facebook", a cross-module inconsistency], `INST` Instagram, `LIND` Linkdin, `WEVS` Website, `EMICAMP` Email Campaigns, `ORGSEO` Organic Search (SEO), `ETS` Events and Trade Shows, `WOM` Word of Mouth. |
| Company | `crm_company_id` | searchable dropdown, default "Select Any One" | **Yes** (*) | FK → Company. Confirmed empty in this tenant (0 Companies). |
| Primary Contact | `crm_contact_id` | searchable dropdown, default "Select Any One" | **Yes** (*) | FK → Contact. Confirmed empty (0 Contacts). |
| Deal Total Budget | `total_budget` | number | No (no asterisk despite being a core field) | Placeholder "Total Budget" |
| Due Date | `due_date` | date (text input + calendar picker icon) | **Yes** (*) | Defaulted to current date (20-07-2026) on open |
| Expected Close Date | `expected_close_date` | date (text input + calendar picker icon) | **Yes** (*) | Defaulted to current date (20-07-2026) on open |
| Image | `image` ("Change Image") | file upload (circular avatar preview) | No | |

### Contacts (related-records panel, right side)
"No Contacts" empty state — presumably would list Contacts tied to the selected Company, for choosing a Primary Contact from a filtered set. Not populated in this tenant.

### Item Search panel
A line-item search/attach tool, distinct from a simple field — lets the user find existing Masters > Items to attach to this Deal:
| Field | Type | Notes |
|---|---|---|
| Item Name/ SKU Code | text | `item_name_sku_code` |
| Item Type | dropdown | 3 values: `1` Stock Item, `2` Services, `3` Assets |
| Category | dropdown, default "Select Any one" | FK → Item Category master; empty in this tenant |
| Location | dropdown, default "Select Any one" | FK → Location master; empty |
| Warehouse | dropdown, default "Select Any one" | FK → Warehouse master; empty |
| Vendor | dropdown, default "Select Any one" | FK → Vendor master; empty |
| Customer | dropdown, default "Select Any one" | FK → Customer master; empty |
| **Search** button | action | Not exercised (would query; panel shows "Please Search" until a search is run) |

This confirms Deals have a **many-to-many relationship to Items** (products/services/assets), each Item itself carrying Type/Category/Location/Warehouse/Vendor attributes — a materially richer data model than Lead/Contact/Company, tying the CRM module directly into an inventory/ERP-style Items master (see `masters-items.md`).

### Description
| Field | Type | Required |
|---|---|---|
| Description (labeled "Description", input placeholder "Remark") | multi-line text | No |

**Actions:** Submit (not exercised), Reset (not exercised).

## 5. Underlying data structure (inferred / partially confirmed)

**Entity: Deal.** Relationships:
- `Deal.originator` → **User**
- `Deal.company` → **Company** (real FK, required)
- `Deal.primaryContact` → **Contact** (real FK, required) — note this is a single primary contact, though the adjacent "Contacts" panel implies Deals may associate with multiple Contacts of the Company more broadly
- `Deal.sourceType` → same source-code enum as Lead
- `Deal` **has many Items** (via the Item Search/attach panel) — each Item carries its own Type/Category/Location/Warehouse/Vendor/Customer attributes, i.e. Deals reference a shared inventory/product catalog (Masters > Items), not deal-specific line items typed inline
- No explicit "Deal Stage"/pipeline-stage field was observed on the create form (unlike Lead's `crm_status`) — Deals may derive stage from the "Status" dashboard widget instead, but that widget showed "No Data" and its underlying values were not observable in this tenant. **Gap.**

## 6. Functionality / logic observed

- **Create** only (inspected, not submitted). List/Edit/Delete/Import/Export/pipeline-stage-transition not reachable — 0 Deals in tenant, same gap pattern as other CRM modules.
- Item attachment is a search-then-select flow (search across Item Type/Category/Location/Warehouse/Vendor/Customer, presumably click a result row to attach it to the Deal) — not fully exercised since no Items exist to search for for real screenshots of a populated Item Search table.

## 7. Inputs and outputs

- Input: Create form above (Deal Information + optional attached Items + Description).
- Output (expected, not observed): new Deal record; increments "Total deals"/"Today's Deals" counters on Home and this tab's Status chart.

## 8. Data conditions

- Required fields (red `*`): Deal Name, Originator, Source Type, Company, Primary Contact, Due Date, Expected Close Date. Deal Total Budget and Image are optional, notably including the budget amount itself.
- Company and Primary Contact dropdowns are empty in this tenant (0 Companies/Contacts exist) — cross-entity linking could not be exercised end-to-end.
- Item Search panel defaults to a "Please Search" placeholder state until the user runs a search — no results were ever produced (0 Items in this tenant, confirmed separately via `masters-items.md`/`masters-items-list.png`).
