# Projects

## 1. Purpose
Internal project/delivery tracking entity (name, budget, timeline, team, linked Customer) — the only CRM entity on this tenant that actually has data (27 records), letting us observe real list/edit behavior for the first time across both tenants documented so far.

## 2. Navigation path
Home → **Projects** tab. URL: `index?TRANST=crm_projects`. **+ Project** button opens create form. Existing rows are clickable (open the same modal, pre-filled, titled "Add Project" — see §6 bug note).

## 3. UI / page design
Same dashboard-widget-that-becomes-a-grid pattern documented for Bharatnet: with the default "This Year" (01-01-2026 to 31-12-2026) filter, shows "No Projects" (because all 27 records have 2025 dates). Selecting **Custom Date** and widening the range (tested: 01-01-2015 → 31-12-2026) renders a real data table:

| Column | Notes |
|---|---|
| Project Name | clickable, opens edit modal |
| Originator | blank for 26/27 rows; only "test project 1" shows "CRM Admin" — suggests Originator was only reliably captured/displayed starting partway through this tenant's test data creation, or is simply often left unset |
| Project Start | dd-mm-yyyy |
| Project End | dd-mm-yyyy |
| Total Budget | 2-decimal currency, no currency symbol shown in the grid |

No visible filter/search/pagination controls on this grid (unlike the Masters list template) — just the date range and the table. No explicit "Import/Export" or "Action" column either — clicking the row itself is the only interaction. This is a narrower feature set than the Masters grids, consistent with the Projects tab being a CRM dashboard widget rather than a full Masters-style module.

Screenshots: `03-projects-tab.png` (empty w/ default range), `03b-projects-date-filter-open.png`, `03c-custom-date.png`, `03d-projects-wide-range.png` (populated grid, 27 rows visible).

## 4. Data — this tenant's 27 records
All 27 projects are clearly internal test/QA data: names include "My Project test hello", "IT Web Project", "CRM Call center setup", "CRM Implementation", "SAP Implementation", "Test roject name" [sic], "test" (×4, duplicate names), "project name", "ravi test project", "test project by jemin", "guyy", "Add testing project test", "Test Test project name", "Hello", "Dummy", "Abc", "Test project task", "Test project name", "test project 1", "test 2 project", "gdh", "bdhdb", "fhdhs n", "bdj". Dates cluster in Feb–Jul 2025 (one outlier: "IT Web Project" runs 20-02-2025 → 01-02-2029, a ~4-year span). Budgets range from 0.00 ("Hello") to 1,000,000.00 (two records: "test project 1", "test 2 project").

## 5. Create/Edit Project form ("+ Project" / click a row)
Titled **"Add Project"** even when opened by clicking an existing row and pre-filled with that row's data — **an observed UI bug**: the create and edit flows share one mislabeled modal (should read "Edit Project" in edit mode).

**Project Information** section:

| Field | `name`/type | Required | Notes |
|---|---|---|---|
| Project Name | text | Yes | |
| Originator | dropdown, default "CRM Admin" | Yes | |
| Customer | dropdown, default "Select Any One" | Yes (*) | FK → Customer master. On the one record opened ("My Project test hello"), this showed **"Select Any One" even in edit mode** — i.e., the record's Customer is actually unset despite the field being marked required. Since this tenant has 0 Customer master records (see `masters-customers.md`), it is structurally impossible to have satisfied this "required" field at creation time via the UI — either the required flag isn't enforced server-side, or these records were seeded directly in the DB bypassing the form. |
| Project Total Budget | number | No | pre-filled `12300.00` on the sampled record |
| Start Date | date | Yes | (label typo in UI: **"Start Sate"**) |
| End Date | date | Yes | |
| Team Member | multi-select listbox, `name` implies array | Yes (*) | Options = this tenant's 4 users (Admin, CRM Admin, Billstack Admin, CONTRACT ADMIN). Sampled record showed the field **empty** despite being required — same required-not-enforced pattern as Customer |
| Image | file upload ("Change Image") | No | "NO IMAGE" placeholder shown |
| Description | textarea | No | sampled value: "Its a Test Project test hello" |

**Actions:** Submit, Reset (neither exercised).

## 6. Underlying data structure (inferred)
```
Project
  .name, .totalBudget, .startDate, .endDate, .description
  .originator      → User
  .customer        → Customer (Masters entity) — REQUIRED by form but observed unset on real data
  .teamMembers     → many User
  (no observed FK to Deal, Company, or Contact)
```
This is the same shape Bharatnet inferred for Project (`.customer → Customer`, `.teamMembers → many User`, no link back to Deal/Company) — confirmed here with real data rather than an empty form.

## 7. Functionality / logic
- Date-range filtering genuinely queries the backend (confirmed: default "This Year" range legitimately returns 0 of the 27 records, since none fall in 2026; widening the range surfaces them) — this validates Bharatnet's inference that the CRM tab widgets are live, date-scoped queries and not static placeholders.
- Clicking a project row opens the same modal used for creation, pre-filled — implying Submit on this modal does double duty as both insert and update (not confirmed by submitting, since that's forbidden by task rules, but strongly implied by the shared "Add Project" title bug and pre-fill behavior).
- Required-field markers (Customer, Team Member) are **not actually enforced** on the data that exists — a real, observed data-integrity gap, not an inference.

## 8. Data conditions
This tenant: **27 Projects**, the only non-zero CRM entity. All other CRM totals (Leads/Contacts/Companies/Campaigns/Deals) are 0. This strongly suggests this Cityline tenant is used internally as a **QA/test sandbox for the Projects feature specifically**, rather than live production CRM data — consistent with the placeholder/junk naming pattern.
