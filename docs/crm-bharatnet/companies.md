# Companies

## 1. Purpose

The organization-level record ("Account" in generic CRM terminology) that Contacts and Deals attach to. Also carries Indian tax-registration identifiers (PAN, GST), confirming this product targets Indian businesses.

## 2. Navigation path

Home (top nav) → **Companies** tab in the CRM tab strip. URL: `index?TRANST=crm_companies`.

The **+ Company** button opens the create form (right-side slide-over).

## 3. UI / page design

Same dashboard-widget pattern as Leads/Contacts (see `leads.md` §3): heading **"Companies (<date range>)"** with the shared date-range filter, **+ Company** button, "No Companies" empty-state illustration, adjacent **Status** widget ("No Data"). No grid/table/search/pagination reachable. Screenshot: `companies-01-list-view.png`.

## 4. Create Company form ("+ Company")

Right-side slide-over titled **"Add Company"**. Screenshot: `companies-02-create-form.png`.

### Company Information
| Field | Type | Required | Notes |
|---|---|---|---|
| Company Name | text | **Yes** (*) | |
| Originator | searchable dropdown | **Yes** (*) | Defaults to logged-in user; FK to User |
| Website URL | text | No | |
| Pan No. | text | No | India PAN (tax ID) |
| GST No. | text | No | India GST registration number |
| Fax Number | text | No | |
| Image | file upload (circular avatar preview) | No | |

### Address Information
| Field | Type | Required |
|---|---|---|
| Address | text | No |
| Country | dropdown, default "Select Any One" | No |
| State | dropdown, default "Select Any One" | No |
| City | dropdown, default "Select Any One" | No |
| Post Code | text | No |
| Description | multi-line text | No (grouped under Address Information in the DOM, unlike Lead/Contact where Description is its own section) |

**Actions:** Submit (not exercised), Reset (not exercised).

### Related records panel

To the right of the form, an accordion with two tabs — **confirms the Company↔Contact and Company↔Deal relationships directly in the UI**, not just inferred:
- **Contacts** (expanded by default): "No Contacts" empty state — this is where Contacts belonging to this Company would list.
- **Deals** (collapsed, "+ Deals"): not expanded further (not clicked, to avoid triggering any create action), but its presence confirms a Company has related Deals.

This "related records" panel is a UI element not seen on the Lead or Contact create forms (those instead show a "No Histories" panel) — on Company it appears even in create mode, which is a minor UI oddity (a not-yet-saved Company already showing "related contacts/deals" tabs, necessarily empty). Documented as observed.

## 5. Underlying data structure (inferred / partially confirmed)

**Entity: Company.** Relationships:
- `Company.originator` → **User**
- `Company` **has many Contacts** (confirmed via the related-records panel; also matches `Contact.company` FK observed in `contacts.md`)
- `Company` **has many Deals** (confirmed via the related-records panel tab; matches expectation from `deals.md`)
- `Company.country/state/city` → **Geography**
- Company carries India-specific tax fields (PAN, GST) not present on Lead/Contact — supports Company being the "billing entity" of the three (Lead/Contact/Company), consistent with GST being invoiced at the company level.

## 6. Functionality / logic observed

- **Create** only (inspected, not submitted). List/Edit/Delete/Import/Export not reachable — 0 records in tenant, same gap pattern as other modules.

## 7. Inputs and outputs

- Input: Create form above.
- Output (expected, not observed): new Company record; increments "Total Companies" counter; would populate this Company as a selectable option in the Contact create form's "Company" dropdown (confirmed dropdown wiring in `contacts.md`).

## 8. Data conditions

- Required fields (red `*`): Company Name, Originator. Every other field, including the India tax IDs, is optional.
