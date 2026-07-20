# Contacts

## 1. Purpose
Individual people associated with a Company — the qualified/working counterpart to a Lead. Identical purpose to Bharatnet's Contacts module.

## 2. Navigation path
Home → **Contacts** tab. URL: `index?TRANST=crm_contacts`. **+ Contact** button opens create form.

## 3. UI / page design
Same dashboard-widget pattern (not a grid): "Contacts (\<range\>)" widget ("No Contacts") + "Status" widget ("No Data"). Screenshots: `06-contacts-tab.png`, `07-contact-create-form.png`.

## 4. Create Contact form ("+ Contact")
Matches Bharatnet's Contact form structure. First section observed ("Additional Information"):

| Field | Type | Required |
|---|---|---|
| Contact Source | dropdown, default "Self" | Yes (same 9-value enum as Lead Source, incl. the "Facebok" typo) |
| Originator | dropdown, default "CRM Admin" | Yes |
| Company | dropdown, default "Select Any One" | No — **real FK to Company** (unlike Lead's free-text Company Name), per Bharatnet's confirmed finding; empty here since 0 Companies exist |
| Phone No. | tel | Yes |
| First Name / Middle Name / last Name | text | First+Last Yes, Middle No |
| Email | email | No |
| Office No. | tel | No |
| Fax | text | No |
| Website | text | No |
| Campaign | dropdown, default "Select Any One" | No — empty (0 campaigns) |
| Image | file upload | No |

(Address Information and Description Information sections below the fold were not re-captured in full for this tenant — assumed identical to Bharatnet's documented Address/Description sections based on the consistent 3-section pattern seen on Lead/Company/Deal/Campaign forms; **this is an inference**, not directly re-verified by scrolling this specific form on this tenant.)

## 5. Underlying data structure (inferred)
`Contact.company → Company` (real FK), `Contact.campaign → Campaign`, `Contact.country/state/city → Geography`. Bharatnet also documented a `Contact.feasibility` boolean flag inferred to trigger a Feasibility record — not independently re-verified here, but plausible given this tenant's Feasibility Types/Inputs masters are actively configured (see `feasibility.md`).

## 6. Functionality / logic
Not exercisable — 0 records.

## 7. Inputs and outputs
Create form as above; Submit not exercised.

## 8. Data conditions
This tenant: **0 Contacts** (tenant-wide counter).
