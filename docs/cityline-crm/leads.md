# Leads

## 1. Purpose
Captures raw, unqualified sales interest before it is worked into Contacts/Deals. Identical purpose/entity to Bharatnet's Leads module.

## 2. Navigation path
Home (top nav) → **Leads** tab in the CRM tab strip (`Home | Leads | Contacts | Companies | Deals | Campaign | Projects | Feasibility`). URL: `index?TRANST=crm_leads`. The **+ Leads** button opens the create form (right-side slide-over panel).

## 3. UI / page design
Same dashboard-widget pattern as Bharatnet (not a data grid): a **"Leads (\<date range\>)"** widget (empty — "No Leads" — this tenant has 0 leads) + a **"Status"** chart widget ("No Data"), both driven by the shared date-range filter (Today/This Week/This Month/This Year/Yesterday/Previous Week/Previous Month/Previous Year/Custom Date). Confirmed empty even with the widest custom range tried (01-01-2015 → 31-12-2026 was not re-tried specifically for Leads, but the tenant-wide "Total leads" counter on the dashboard reads **0**, which is date-independent — i.e., genuinely zero Lead records exist in this tenant, not just outside the visible date window).

Screenshots: `screenshots/cityline-crm/04-leads-tab-default.png`, `05-lead-create-form.png`.

## 4. Create Lead form ("+ Leads")
Fields are **identical** to Bharatnet's Lead form (`docs/crm-bharatnet/leads.md`), same 3 sections (Personal information / Address Information / Description Information), same field set, same required markers:

| Field | Type | Required | Notes |
|---|---|---|---|
| Lead Source | dropdown, `name=contact_source` | Yes | 9 options: Self (default), **Facebok** [sic], Instagram, **Linkdin** [sic], Website, Email Campaigns, Organic Search (SEO), Events and Trade Shows, Word of Mouth |
| Originator | dropdown | Yes | Defaults to logged-in user "CRM Admin". Options = this tenant's 4 users: Admin, CRM Admin, Billstack Admin, CONTRACT ADMIN |
| First Name | text | Yes | |
| Middle Name | text | No | |
| last Name | text | Yes | (label capitalization as-shown) |
| Email | email | No | |
| Phone No. | tel | Yes | |
| Office No. | tel | No | |
| Fax | text | No | |
| Website | text | No | |
| Company Name | text (free text, not FK) | No | |
| Campaign | dropdown, default "Select Any One" | No | FK → Campaign (empty on this tenant — 0 campaigns) |
| Lead Status | dropdown, default "New" | No | 10-value ladder: New, Contacted, Interested, Not Interested, Lost, Follow up Required, Referred, No Response, Junk Or Spam, Re engage Later — **identical enum to Bharatnet** |
| Image | file upload | No | |
| Address / Country / State / City / Post Code | text/dropdown, cascading | No | Country list = same 251-country global reference list as Bharatnet |
| Description | textarea | No | |

**Difference from Bharatnet found:** the Lead Source option spelled **"Facebok"** here (missing an "o") vs. Bharatnet's correctly-spelled "Facebook". Since both tenants share the same "Infistaq" platform codebase, this is either a per-tenant configurable enum value (unlikely for what looks like a hardcoded dropdown) or the Bharatnet doc's transcription was written from memory/cleanup — flagged as a minor, low-confidence discrepancy rather than a confirmed platform difference.

**Actions:** Submit (not exercised — would create a real record), Reset (not exercised).

## 5. Underlying data structure (inferred)
Identical to Bharatnet's Lead entity: `Lead.originator → User`, `Lead.campaign → Campaign`, `Lead.country/state/city → Geography`, `Lead.companyName` is a text snapshot not a Company FK.

## 6. Functionality / logic
No Convert-to-Contact/Deal action observed (same gap as Bharatnet — no leads exist to test against). "No Histories" panel below the form implies an activity-log feature, not directly exercisable.

## 7. Inputs and outputs
Input: the create form above. Output: (not observed — Submit not exercised per task's read-only rule).

## 8. Data conditions
This tenant: **0 Leads** (tenant-wide counter, date-independent). Same "entirely empty" condition as Bharatnet for this specific entity, even though this tenant is NOT globally empty (Projects has 27 records — see `projects.md`).
