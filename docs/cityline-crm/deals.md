# Deals

## 1. Purpose
The sales-opportunity/order entity tying a Company + primary Contact to Items (a quote/order-in-progress). Identical purpose to Bharatnet's Deals module.

## 2. Navigation path
Home → **Deals** tab. URL: `index?TRANST=crm_deals`. **+ Deal** button opens create form.

## 3. UI / page design
Dashboard-widget pattern. **Observed bug:** the empty-state text on the Deals widget reads **"No Campaigns"** instead of "No Deals" — a copy-paste/mislabeled-component defect (documented as observed, not inferred). Screenshots: `10-deals-tab.png` (shows the "No Campaigns" bug), `11-deal-create-form.png`.

## 4. Create Deal form ("+ Deal")
Matches Bharatnet's Deal form:

| Field | Type | Required |
|---|---|---|
| Deal Name | text | Yes |
| Originator | dropdown, default "CRM Admin" | Yes |
| Source Type | dropdown, default "Select Any One" | Yes — same 9-value source enum as Lead/Contact Source (shared enum across the platform, per Bharatnet's finding) |
| Company | dropdown, default "Select Any One" | Yes — real FK, empty (0 companies) |
| Primary Contact | dropdown, default "Select Any One" | Yes — real FK, empty (0 contacts) |
| Deal Total Budget | number | No |
| Due Date | date, defaults to today+1yr-ish (pre-filled `20-07-2026`) | Yes |
| Expected Close Date | date, same default | Yes |
| Image | file upload | No |

Below the form: a **Contacts** panel ("No Contacts" — presumably filtered to the selected Company's contacts) and an **Item Search** panel with filter columns **Item Name/SKU Code, Item Type, Category, Location** (+ more columns off-screen, per Bharatnet's doc: Warehouse/Vendor/Customer) — confirms Deal has-many Item via an item-attach search UI, same as Bharatnet.

## 5. Underlying data structure (inferred)
`Deal.company → Company` (required), `Deal.primaryContact → Contact` (required), `Deal.sourceType` shares the 9-code source enum with Lead/Contact, `Deal` has-many `Item` (via Item Search/attach). No explicit Campaign FK or pipeline-stage field observed — same as Bharatnet.

## 6. Functionality / logic
Not exercisable — 0 records (Company/Contact FKs have nothing to select from).

## 7. Inputs and outputs
Create form as above; Submit not exercised.

## 8. Data conditions
This tenant: **0 Deals** (tenant-wide counter). The dashboard's "No Campaigns" mislabel on this tab is a UI bug present regardless of data volume.
