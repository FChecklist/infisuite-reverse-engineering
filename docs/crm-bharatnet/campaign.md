# Campaign

## 1. Purpose

Marketing/outreach campaign records that Leads, Contacts, and Deals can be sourced from or attributed to (see `crm_campaign_id` FK fields observed on Lead and Contact create forms).

## 2. Navigation path

Home (top nav) → **Campaign** tab in the CRM tab strip. URL: `index?TRANST=crm_campaigns` (note: the tab label is singular "Campaign" but the underlying URL query value is plural `crm_campaigns` — confirmed by clicking the tab; directly navigating to `TRANST=crm_campaign` (singular) instead renders a broken/duplicated page, see §8).

The **+ Campaign** button opens the create form (right-side slide-over).

## 3. UI / page design

Same dashboard-widget pattern as the other CRM tabs (see `leads.md` §3): heading **"Campaigns (<date range>)"** with the shared date-range filter, **+ Campaign** button, "No Campaigns" empty state, adjacent **Status** widget ("No Data"). No grid/table/search/pagination reachable — 0 Campaigns in this tenant. Screenshot: `campaign-01-list-view.png`.

## 4. Create Campaign form ("+ Campaign")

Right-side slide-over titled **"Add Campaign"**, single section "Campaign Information". Screenshot: `campaign-02-create-form.png`.

| Field | Field name (attr) | Type | Required | Notes |
|---|---|---|---|---|
| Campaign Name | `campaign_name` | text | **Yes** (*) | |
| Originator | `originator` | searchable dropdown | **Yes** (*) | Defaults to logged-in user; FK → User. Same option set as Lead/Contact/Deal Originator: Anthony Johnston (1), CRM Admin (2), Billstack Admin (3). |
| Campaign Type | `crm_campaign_type` | dropdown, default "Marketing Campaigns" | **Yes** (*) | 6-value enum (code → label): `MRKTG` Marketing Campaigns, `SLGC` Sales & Lead Generation Campaigns, `ADVTG` Advertising Campaigns, `FOUNDNONPROF` Fundraising & Nonprofit Campaigns, `POLITADV` Political & Advocacy Campaigns, `INTEREMPCAPM` Internal/Employee Campaigns |
| Campaign Objective | `campaign_objective` | dropdown, default "Brand Awareness" | **Yes** (*) | 12-value enum (code → label): `BAW` Brand Awareness, `ENG` Engagement, `TRF` Traffic (website or landing page visits), `CON` Content Distribution, `REV` Revenue Growth, `LGEN` Lead Generation, `LNR` Sales Growth, `CR` Conversion Rate Improvement, `ACK` Customer Acquisition, `DON` Donation Collection, `AWN` Awareness & Outreach, `VOL` Volunteer Recruitment |
| Start Sate [sic — typo in product label, should read "Start Date"] | `campaign_start_date` | date (text + calendar picker) | **Yes** (*) | |
| End Date | `campaign_end_date` | date (text + calendar picker) | **Yes** (*) | |
| Total Budget | `total_budget` | number | No | |
| Redirect URL | `redirect_url` | text | No | Placeholder "Website URL" |
| Contact Email Id | `contact_email_id` | email (HTML5) | No | |
| Contact No. | `contact_no` | text | No | |
| Image | `image` ("Change Image") | file upload (circular avatar preview) | No | |
| Description (label absent in visible viewport, but a `remark` textarea with placeholder "Remark" exists below Image, consistent with every other CRM create form) | `remark` | multi-line text | No | Not screenshotted directly (below the fold) but confirmed present via DOM query, matching the Leads/Contacts/Companies/Deals pattern |

**Actions:** Submit (not exercised), Reset (not exercised, presence inferred from the pattern on every other CRM create form — not explicitly re-confirmed in this screenshot's viewport).

## 5. Underlying data structure (inferred)

**Entity: Campaign.** Relationships:
- `Campaign.originator` → **User**
- `Campaign.type` and `Campaign.objective` → two independent enums (marketing-campaign taxonomy, not staged/sequential — a Campaign is classified by both simultaneously, e.g. Type=Advertising + Objective=Lead Generation)
- **Referenced by** `Lead.crm_campaign_id` and `Contact.crm_campaign_id` (confirmed in `leads.md`/`contacts.md`) — i.e. Campaign is the "attribution source" entity that Leads/Contacts can optionally be tagged as originating from
- No explicit link from Campaign back to Deal was observed on the Deal create form (Deal only has `source_type`, a flat text-source enum, not a Campaign FK) — **Deals do not appear to attribute to a specific Campaign record**, only Leads and Contacts do. Worth flagging as a possibly deliberate scope limit, not confirmed as a gap vs. an intentional design choice.

## 6. Functionality / logic observed

- **Create** only (inspected, not submitted). List/Edit/Delete/Import/Export not reachable — 0 Campaigns in tenant.

## 7. Inputs and outputs

- Input: Create form above.
- Output (expected, not observed): new Campaign record; increments "Total Campaigns" counter on Home; becomes selectable in the Lead/Contact "Campaign" dropdown.

## 8. Data conditions

- Required fields (red `*`): Campaign Name, Originator, Campaign Type, Campaign Objective, Start Date, End Date. Total Budget, Redirect URL, Contact Email Id, Contact No., Image are optional.
- **Observed bug:** navigating directly to `index?TRANST=crm_campaign` (singular, guessing from the tab's visible label "Campaign") does not 404 or redirect — it renders the Home-tab dashboard content twice, stacked vertically, with all interactive elements (tabs, "This Year" filter, "+ Lead/Contact Assignings") visually greyed-out/disabled. The correct route, confirmed by actually clicking the "Campaign" tab, is `index?TRANST=crm_campaigns` (plural). This suggests the SPA does a client-side lookup by exact `TRANST` value and falls back to some broken partial-render state on an unrecognized value, rather than validating/erroring. Documented as observed, not otherwise explained.
