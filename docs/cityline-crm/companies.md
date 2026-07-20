# Companies

## 1. Purpose
The organization/account entity that Contacts and Deals attach to. Identical purpose to Bharatnet's Companies module.

## 2. Navigation path
Home → **Companies** tab. URL: `index?TRANST=crm_companies`. **+ Company** button opens create form.

## 3. UI / page design
Dashboard-widget pattern: "Companies (\<range\>)" widget ("No Companies") + "Status" widget ("No Data"). Screenshots: `08-companies-tab.png`, `09-company-create-form.png`.

## 4. Create Company form ("+ Company")
Matches Bharatnet's Company form exactly:

**Company Information**
| Field | Type | Required |
|---|---|---|
| Company Name | text | Yes |
| Originator | dropdown, default "CRM Admin" | Yes |
| Website URL | text | No |
| Pan No. | text | No | India tax ID (PAN) |
| GST No. | text | No | India tax ID (GST) |
| Fax Number | text | No |
| Image | file upload | No |

**Address Information** (heading visible, fields below the fold not re-scrolled — inferred identical to Bharatnet: Address/Country/State/City/Post Code).

Below the form, two related-record accordion panels are visible even in create mode: **Contacts** ("No Contacts") and **Deals** (collapsed, "+ Deals") — confirms Company has-many Contact and has-many Deal, matching Bharatnet's confirmed relationship finding.

## 5. Underlying data structure (inferred)
`Company.country/state/city → Geography`; `Company` carries India tax IDs (PAN, GST) — same "billing entity" role as in Bharatnet. `Company` has many `Contact`, has many `Deal`.

## 6. Functionality / logic
Not exercisable — 0 records.

## 7. Inputs and outputs
Create form as above; Submit not exercised.

## 8. Data conditions
This tenant: **0 Companies** (tenant-wide counter).
