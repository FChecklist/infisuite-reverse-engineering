# Campaign

## 1. Purpose
Marketing/outreach campaign entity referenced by Lead and Contact (`.campaign` FK). Identical purpose to Bharatnet's Campaign module.

## 2. Navigation path
Home → **Campaign** tab. URL note: clicking the tab sets `index?TRANST=crm_campaigns` (plural) even though the tab label and dashboard counter both say singular "Campaign"/"Total Campaigns" — a minor URL/label inconsistency, documented as observed. **+ Campaign** button opens create form.

## 3. UI / page design
Dashboard-widget pattern: "Campaigns (\<range\>)" widget ("No Campaigns") + "Status" widget ("No Data"). Screenshots: `12-campaign-tab.png`, `13-campaign-create-form.png`.

## 4. Create Campaign form ("+ Campaign")
Matches Bharatnet's Campaign form:

| Field | Type | Required |
|---|---|---|
| Campaign Name | text | Yes |
| Originator | dropdown, default "CRM Admin" | Yes |
| Campaign Type | dropdown, default "Marketing Campaigns" | Yes — Bharatnet documented a 6-value enum (Marketing/Sales & Lead Gen/Advertising/Fundraising/Political/Internal-Employee); not re-expanded here but default value matches |
| Campaign Objective | dropdown, default "Brand Awareness" | Yes — Bharatnet documented a 12-value enum; not re-expanded here but default value matches |
| Start Date / End Date | date | Yes |
| Total Budget | number | No |
| Redirect URL | text | No |
| Contact Email Id | email | No |
| Contact No. | tel | No |
| Image | file upload | No |

## 5. Underlying data structure (inferred)
`Campaign.originator → User`; referenced by `Lead.campaign` and `Contact.campaign` (both optional FKs); not referenced by `Deal` — same as Bharatnet.

## 6. Functionality / logic
Not exercisable — 0 records.

## 7. Inputs and outputs
Create form as above; Submit not exercised.

## 8. Data conditions
This tenant: **0 Campaigns** (tenant-wide counter).
