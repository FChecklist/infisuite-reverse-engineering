# Projects

## 1. Purpose

Delivery/execution-phase records — work carried out for a Customer (not a CRM Company/Contact) by an internal team, with budget and a planned start/finish window. This is the CRM's link into project-delivery/ops tracking, distinct from the pre-sale Lead→Contact→Company→Deal chain.

## 2. Navigation path

Home (top nav) → **Projects** tab in the CRM tab strip. URL: `index?TRANST=crm_projects`.

The **+ Project** button opens the create form (right-side slide-over).

## 3. UI / page design

Similar dashboard-widget pattern to the other CRM tabs, but **narrower — only one panel, no adjacent "Status" widget** (unlike Leads/Contacts/Companies/Deals/Campaign, which all pair the main widget with a "Status" chart card). Heading **"Projects (<date range>)"** with the shared date-range filter, **+ Project** button, "No Projects" empty state. No grid/table/search/pagination reachable — 0 Projects in this tenant. Screenshot: `projects-01-list-view.png`.

## 4. Create Project form ("+ Project")

Right-side slide-over titled **"Add Project"**, single section "Project Information". Screenshot: `projects-02-create-form.png`.

| Field | Field name (attr) | Type | Required | Notes |
|---|---|---|---|---|
| Project Name | `project_name` | text | **Yes** (*) | |
| Originator | `originator` | searchable dropdown | **Yes** (*) | Defaults to logged-in user; FK → User. Same option set seen throughout: Anthony Johnston (1), CRM Admin (2), Billstack Admin (3). |
| Customer | `customer_id` | searchable dropdown, default "Select Any One" | **Yes** (*) | **FK → Customer**, a distinct entity from Company/Contact (see `masters-customers.md`) — confirms Projects bill against/deliver for a Customer master record, not a CRM Company. Empty in this tenant (0 Customers). |
| Project Total Budget | `project_cost` | number | No | |
| Start Sate [sic — same typo as Campaign's "Start Sate"] | `planned_start` | date (text + calendar picker) | **Yes** (*) | Defaulted to current date (20-07-2026) |
| End Date | `planned_finish` | date (text + calendar picker) | **Yes** (*) | Defaulted to current date (20-07-2026) |
| Team Member | (multi-select search box, underlying `<select name="project_team_user_id[]">`, multi-value) | multi-select searchable dropdown | **Yes** (*) | FK → User (many). Same 3-user option set: Anthony Johnston (1), CRM Admin (2), Billstack Admin (3). Confirms Projects have an assigned internal team, not just a single Originator. |
| Image | `image` ("Change Image") | file upload (circular avatar preview) | No | |
| Description | `description` | multi-line text, placeholder "Description" | No | Below the fold, confirmed via DOM query, not fully visible in screenshot |

**Actions:** Submit / Reset (inferred from the pattern on every other CRM create form; not re-screenshotted here but present per the same slide-over template).

## 5. Underlying data structure (inferred)

**Entity: Project.** Relationships:
- `Project.originator` → **User**
- `Project.customer` → **Customer** (a Masters-level billing entity, separate from CRM Company — this is the clearest signal in the whole CRM module that "Customer" and "Company" are two distinct entities in this product: Company is the CRM-side prospect/account record, Customer is the Masters/billing-side record that Projects, Deals' Item Search, and presumably Billstack's invoicing key off)
- `Project.teamMembers` → **User** (many, via `project_team_user_id[]`)
- No explicit Deal→Project or Company→Project link was observed on this form — a Project is created directly against a Customer, with no visible field tying it back to the Deal that presumably preceded it. **Gap** (not confirmed absent, just not observed — may exist as a reverse/related-records link visible on a Deal or Customer detail view, which are unreachable in this empty tenant).

## 6. Functionality / logic observed

- **Create** only (inspected, not submitted). List/Edit/Delete not reachable — 0 Projects in tenant.
- Unlike every other CRM-tab create form, Project has no visible related-records panel (no "Contacts"/"Deals"/"Item Search" side panel) — its slide-over is a single-column form only.

## 7. Inputs and outputs

- Input: Create form above.
- Output (expected, not observed): new Project record; increments "Total Projects" counter on Home.

## 8. Data conditions

- Required fields (red `*`): Project Name, Originator, Customer, Start Date, End Date, Team Member. Project Total Budget, Image, Description are optional.
- Customer dropdown is empty in this tenant (0 Customers) — cross-entity linking could not be exercised end-to-end.
- Same "Start Sate" label typo observed on the Campaign form (`campaign.md`) — likely a shared form-template bug across both modules, not module-specific.
