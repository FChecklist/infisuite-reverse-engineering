# Contacts

## 1. Purpose

Stores individual people the organization deals with — the "qualified"/working counterpart to a Lead, optionally linked to a real Company record.

## 2. Navigation path

Home (top nav) → **Contacts** tab in the CRM tab strip. URL: `index?TRANST=crm_contacts`.

The **+ Contact** button on this tab opens the create form (right-side slide-over panel).

## 3. UI / page design

Identical dashboard-widget pattern to Leads (see `leads.md` §3 for the general pattern — not repeated in full here):
- Heading **"Contacts (<date range>)"**, scoped by the same shared date-range filter (Today/This Week/This Month/This Year/Yesterday/Previous Week/Previous Month/Previous Year/Custom Date).
- **+ Contact** button → create form.
- Empty state: "No Contacts" illustration (tenant has 0 contacts).
- Adjacent **Status** widget (chart placeholder), "No Data" in this tenant.
- No grid/table, no search, no pagination, no visible edit/delete/list — same gap as Leads.

Screenshot: `contacts-01-list-view.png`.

## 4. Create Contact form ("+ Contact")

Right-side slide-over titled **"Add Contact"**, sections: Additional Information, Address Information, Description Information, Feasibility. Screenshots: `contacts-02-create-form-top.png`, `contacts-03-create-form-bottom.png`, `contacts-04-company-dropdown.png`.

### Additional Information
| Field | Type | Required | Notes |
|---|---|---|---|
| Contact Source | searchable dropdown | **Yes** (*) | Default "Self" — same option set as Lead Source is likely shared (not re-verified, but UI/behavior identical) |
| Originator | searchable dropdown | **Yes** (*) | Defaults to logged-in user; FK to User, same as Lead |
| Company | searchable dropdown, default "Select Any One" | No | **Unlike Lead's free-text "Company Name", this is a real foreign-key dropdown into the Companies entity.** Confirmed empty (only the placeholder option renders) because this tenant has 0 Company records — i.e., it is genuinely populated from live Companies data, not a static list. |
| Phone No. | text | **Yes** (*) | |
| First Name | text | **Yes** (*) | |
| Middle Name | text | No | |
| last Name | text | **Yes** (*) | |
| Email | text | No | |
| Office No. | text | No | |
| Fax | text | No | |
| Website | text | No | |
| Campaign | searchable dropdown, default "Select Any One" | No | FK to Campaign |
| Image | file upload (circular avatar preview) | No | |

### Address Information
| Field | Type | Required |
|---|---|---|
| Mailing Address | text | No |
| Country | dropdown, default "Select Any One" | No |
| State | dropdown, default "Select Any One" | No |
| City | dropdown, default "Select Any One" | No |
| Post Code | text | No |

(Same Geography-sourced cascading dropdowns as Leads — see `leads.md`.)

### Description Information
| Field | Type | Required |
|---|---|---|
| Description | multi-line text | No |

### Feasibility
| Field | Type | Required | Notes |
|---|---|---|---|
| Feasibility | **toggle switch** (boolean) | No | Default off. Ties a Contact into the Feasibility module (see `feasibility.md`) — presumably flips on a "Feasibility" workflow/record for this contact. Not exercised further (would require submitting the form). |

**Actions:** Submit (not exercised), Reset (not exercised).

A **"No Histories"** panel appears next to the form, same as on the Lead form — implies a per-record activity/audit log feature.

## 5. Underlying data structure (inferred)

**Entity: Contact.** Key relationships (this time partially *confirmed*, not just inferred, because the Company dropdown is visibly wired to live data even though empty):
- `Contact.company` → **Company** (real FK, confirmed via dynamic-but-empty dropdown)
- `Contact.originator` → **User**
- `Contact.campaign` → **Campaign**
- `Contact.country/state/city` → **Geography**
- `Contact.feasibility` → boolean flag, likely gates a related **Feasibility** record

This is a materially different linkage model from Lead (Lead.companyName is a text snapshot; Contact.company is a real relation) — consistent with Contacts being the "promoted"/qualified stage where a real Company association exists, while Leads only capture a raw company name string pre-qualification.

## 6. Functionality / logic observed

- **Create** only (form inspected, not submitted). No List/Edit/Delete/Import/Export/Convert/Merge reachable in this tenant's empty state — flagged as a gap, not confirmed absent.

## 7. Inputs and outputs

- Input: Create form above.
- Output (expected, not observed): new Contact record; would increment "Total Contacts"/"Today's Contacts" counters and populate the Home "Recently Created Contacts" widget and this tab's "Status" chart.

## 8. Data conditions

- Required fields (red `*`): Contact Source, Originator, Phone No., First Name, Last Name.
- Company/Campaign/Country/State/City dropdowns are optional and — for Company at least — confirmed empty in this tenant (0 Companies exist), so no cross-entity linking could be exercised end-to-end.
