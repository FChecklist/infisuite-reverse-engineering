# Leads

## 1. Purpose

Captures raw, unqualified sales interest ("Leads") before they are worked into Contacts/Deals. Standard top-of-funnel CRM entity.

## 2. Navigation path

Home (top nav) → **Leads** tab in the CRM tab strip (`Home | Leads | Contacts | Companies | Deals | Campaign | Projects | Feasibility`). URL: `index?TRANST=crm_leads`.

The **+ Leads** button on this tab opens the create form (a right-side slide-over panel, not a new page).

## 3. UI / page design

**Important structural finding (directly observed, not inferred):** unlike the "Masters" and "Settings" areas (see `masters.md` / `settings.md`), which use a conventional data-grid list view (filters, Import/Export, paginated table with an Action column), the **Leads tab is a dashboard-style widget panel, not a data grid**. There is no visible table of lead records, no column headers, no pagination, and no per-record row actions anywhere in this tab. It is functionally a mini-dashboard:

- Heading **"Leads (<start-date> To <end-date>)"** — date range comes from a **date-range filter control** (button labelled e.g. "This Year", to its right a caret that opens a dropdown: **Today / This Week / This Month / This Year / Yesterday / Previous Week / Previous Month / Previous Year / Custom Date**). This filter is shared chrome for whichever CRM tab is active (Leads/Contacts/Companies/Deals/Campaign/Projects all show the same control in the same position).
- **+ Leads** button (top-right of the widget) — opens the create form.
- Body: when zero leads exist for the selected date range, shows a centered "No Leads" illustration (this tenant has 0 leads total, so this is the only state observed).
- A second widget to the right, **"Status"**, presumably a chart (pie/donut, given the "Lead Status" field on the record) breaking down leads by status; shows "No Data" placeholder here.

Screenshots: `leads-01-list-view.png` (empty dashboard state), `leads-06-date-range-options.png` (date filter dropdown open).

Because there is no grid, there is **no visible way to browse, search, edit, delete, or bulk-act on existing leads from this tab** — those functions may exist only once records exist (e.g. a row click might open a detail/edit view) or may not exist in this UI at all. This is a documented gap, not an assumption of absence — see Known Gaps in SUMMARY.md.

Guessing a direct grid URL (`index.php?option=COM_LIST_LEADS`, by analogy with `COM_LIST_ITEMS`/`COM_LIST_CUSTOMERS`) returned a themed **403 page**: "LEADS NOT FOUND — OOPS!! YOU DO NOT HAVE THE REQUIRED PERMISSION, PLEASE CONTACT YOUR ADMIN". This is very likely a generic "unknown COM_ page" handler mis-reporting a not-found route as a permission error (the app clearly does have Lead functionality — the create form works — so the honest explanation is a bad/guessed route, not a real permission gap). Documented as an observed error/bug rather than a confirmed permission restriction. Screenshot: `leads-05-try-com-list-leads.png`.

The Home dashboard's "Recently Created Leads" widget has a "View all" link; its `href` is the dead anchor `#!` and clicking it simply re-lands on this same Leads tab (`index?TRANST=crm_leads#!`) — confirmed by direct click, not just reading the href.

## 4. Create Lead form ("+ Leads")

Opens as a right-side slide-over panel titled **"Lead"**, in 3 sections. Screenshots: `leads-02-plus-leads-dropdown.png`, `leads-03-lead-source-options.png`, `leads-04-lead-status-options.png`.

### Personal information
| Field | Type | Required | Notes |
|---|---|---|---|
| Lead Source | searchable dropdown | **Yes** (*) | Options observed: Self (default), Facebook, Instagram, Linkdin [sic — typo in product], Website, Email Campaigns |
| Originator | searchable dropdown | **Yes** (*) | Defaults to the logged-in user ("CRM Admin"). Almost certainly sourced from the platform's Users list (Masters > Users) — i.e. Lead.originator is a FK to a User. |
| First Name | text | **Yes** (*) | |
| Middle Name | text | No | |
| last Name | text | **Yes** (*) | Label capitalization as-shown in UI ("last Name", lowercase L) |
| Email | text | No | no visible format hint |
| Phone No. | text | **Yes** (*) | |
| Office No. | text | No | |
| Fax | text | No | |
| Website | text | No | |
| Company Name | **plain text**, not a dropdown | No | Notable: this is free text, not a foreign key to the Companies entity — a Lead is not linked to a real Company record until/unless a later "convert" step exists (not observed — no leads to convert) |
| Campaign | searchable dropdown, default "Select Any One" | No | References the Campaign entity (see `campaign.md`) |
| Lead Status | searchable dropdown | No (no asterisk) | Options: **New** (default), Contacted, Interested, Not Interested, Lost, Follow up Required. This is the Lead pipeline/status field. |
| Image | file upload (avatar-style circular preview, "Change Image") | No | |

### Address Information
| Field | Type | Required | Notes |
|---|---|---|---|
| Address | text | No | |
| Country | dropdown, default "Select Any One" | No | Sourced from Settings > Geography > Countries |
| State | dropdown, default "Select Any One" | No | Sourced from Settings > Geography > States; almost certainly cascades from Country (inferred, not confirmed — did not test since it requires a Country selection to observe) |
| City | dropdown, default "Select Any One" | No | Sourced from Settings > Geography > Cities; likely cascades from State (inferred) |
| Post Code | text | No | |

### Description Information
| Field | Type | Required |
|---|---|---|
| Description | multi-line text | No |

**Actions:** **Submit** (not exercised — would create a real record, forbidden by task rules), **Reset** (clears the form, not exercised to avoid any accidental state change).

Below the form, a **"No Histories"** panel is visible — implies a Lead detail/edit view has an activity-history log (audit trail of changes/status transitions), consistent with the "Logs" tab seen in the global notifications panel. Not directly confirmed since no lead exists to open.

## 5. Underlying data structure (inferred)

**Entity: Lead**, fields as above. Relationships (inferred from dropdown sources, not confirmed via a populated record):
- `Lead.originator` → **User** (Masters > Users / Settings > User Management)
- `Lead.campaign` → **Campaign**
- `Lead.country/state/city` → **Geography** reference tables (Settings > Geography)
- `Lead.companyName` is a **text snapshot**, not a Company FK — supports the standard CRM pattern where a Lead is later "converted" into a real Contact + Company (+ possibly Deal), but no Convert action was directly observed anywhere in this UI (no leads exist to test on, and no such button appeared on the create form or elsewhere). This is a **gap**, not a confirmed absence.

## 6. Functionality / logic observed

- **Create** — via "+ Leads" slide-over form (fields above). Not submitted (rule: no real records created).
- No List/Search/Filter/Edit/Delete/Import/Export/Convert/Bulk-assign functionality was reachable for Leads specifically in this tenant — the tab is dashboard-only and the tenant has zero records to click into. **Explicitly flagged as a gap**, not evidence these functions don't exist.
- The standalone **"+ Lead/Contact Assignings"** button next to the tab strip is a related but separate function — documented in `lead-contact-assignings.md`.

## 7. Inputs and outputs

- Input: the Create form above.
- Output (expected, not observed): a new Lead record; presumably increments the "Total leads" / "Today's Leads" dashboard counters and populates the "Recently Created Leads" Home widget and the Leads-tab "Status" chart.

## 8. Data conditions

- Date-range filter (Today/This Week/.../Custom Date) scopes the "Leads (…)" and "Status" widgets to a period — confirmed by the heading text changing to reflect the selected range ("01-01-2026 To 31-12-2026" for "This Year").
- Required fields (marked with a red `*` in the UI): Lead Source, Originator, First Name, Last Name, Phone No. All other fields are optional.
- No client-side validation errors were triggered (form was not submitted, per task rules), so exact error messages/format rules for Email/Phone/Website are unknown — **gap**.
