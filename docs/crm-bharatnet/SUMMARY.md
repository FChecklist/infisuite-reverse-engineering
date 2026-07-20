# SUMMARY — BharatNet CRM (Infistaq platform) Reverse-Engineering

System: `https://bharatnet.infisuite.in`, logged in as `crm_admin@bharatnet.infisuite.in` (role: **CRM Admin**). Full methodology, screenshots, and per-module detail live in the sibling files in this folder; this document synthesizes them into one data model, one function list, and an honest gap list.

## 1. What this system is

"Infistaq" is a **shared multi-module ERP** (HR, inventory/accounting, procurement, field service, CRM, and — per the top-nav Modules switcher — a separate "Billstack" billing module). This BharatNet tenant is licensed for exactly **2 modules: CRM and Billstack**. The CRM module reviewed here is a comparatively thin vertical slice of a much larger platform — evidenced repeatedly: the Items master's 21-field chart-of-accounts, the platform's ~130-page permission catalog (almost none of it CRM), Customer's "Rental Properties" tab, the Workflow engine's procurement-only pre-configured processes, and HR-report pages (SALARY HISTORY, EEO-1, etc.) all present in Settings/Roles but invisible from the CRM tab strip.

**BharatNet** itself (per the General Settings config record) is a real tenant: an Uttarakhand-based (Dehradun) organization, consistent with "BharatNet" being India's national rural broadband program — this CRM is plausibly used to manage that rollout's leads/deals/feasibility-survey pipeline, which would explain the India-specific tax fields (PAN/GST/HSN-SAC/TDS/TCS) and the telecom-flavored "Feasibility" + BOM-equipment-return tracking module.

**This tenant's CRM data is entirely empty** — 0 records across all 7 CRM entities (Leads, Contacts, Companies, Deals, Campaigns, Projects, Feasibility). The one populated area is platform/admin scaffolding: 3 Users, ~800 Display Attribute rows, global Geography (251 countries), Email templates, and 8 pre-seeded procurement Workflow configs. This materially limits what could be learned about in-app *record* behavior (detail views, conversion flows, list-view sorting/filtering with real data) — every module doc flags this honestly rather than inventing data.

## 2. Data model — entities and relationships

```
User ──< originator/team-member/assignee >── {Lead, Contact, Company, Deal, Campaign, Project}
User ──< role >── Role (2 per module: CRM / CRM Admin for the CRM module)
User ──< modules >── Module (tag list, e.g. "CRM")

Lead
  .companyName        → free text (NOT a Company FK — pre-qualification snapshot)
  .campaign            → Campaign (optional)
  .country/state/city   → Geography
  .status               → 10-value flat enum (New→Contacted→…→Re-engage Later)
  .source               → 9-value source-code enum (shared with Deal.sourceType)

Contact
  .company              → Company (REAL FK — confirmed via dynamic dropdown)
  .campaign             → Campaign (optional)
  .country/state/city    → Geography
  .feasibility           → boolean flag (implied trigger for a Feasibility record)

Company
  .country/state/city    → Geography
  has many → Contact (confirmed via related-records panel)
  has many → Deal (confirmed via related-records panel)
  carries India tax IDs (PAN, GST) — the "billing entity" among Lead/Contact/Company

Deal
  .company               → Company (required FK)
  .primaryContact        → Contact (required FK)
  .sourceType            → same 9-code source enum as Lead
  has many → Item (via Item Search/attach panel; Items filterable by Type/Category/
                    Location/Warehouse/Vendor/Customer)
  (no explicit Campaign FK, no explicit pipeline-stage field observed)

Campaign
  .originator            → User
  .type                  → 6-value enum (Marketing/Sales&LeadGen/Advertising/
                                          Fundraising/Political/Internal-Employee)
  .objective              → 12-value enum (Brand Awareness/Engagement/Traffic/…)
  referenced by ← Lead.campaign, Contact.campaign
  (NOT referenced by Deal)

Project
  .customer               → Customer (Masters entity — distinct from Company!)
  .teamMembers             → many User
  (no observed FK back to Deal or Company)

Feasibility  [low confidence — no create form found]
  .contact (inferred)      → Contact, via Contact.feasibility toggle
  .item (inferred)         → Item (BOM equipment loaned)
  .status                  → 2 independent dimensions: Open/Closed × SO-Generated/Not
  → Sales Order (Billstack, out of scope)

── Masters (shared ERP entities, referenced by CRM but not CRM-owned) ──

Item
  .category/childCategory/subChildCategory → 3-level hierarchy
  .warehouse                → Warehouse
  .hsnSacCode                → HSN/SAC Code (India tax classification)
  .vendors (many)             → Vendor, each with its own Part No/Part Name
  .customers (many)            → Customer, each with its own Part No/Part Name
  .*Account (21 fields)          → Chart of Accounts
  referenced by ← Deal (Item Search), Feasibility (BOM)

Customer  (Masters/billing entity — DISTINCT from CRM Company; no FK observed
           connecting the two anywhere in this review)
  .parentGroup                → self-referencing hierarchy
  has many → Billing Address, Shipping Address (one marked default)
  has many → LUT/Bond/Finance-Year tax rows
  has many → Rental Property (unused vertical in this tenant)
  referenced by ← Project.customer, Item.customers, Deal's Item Search filter

Geography: Country (251) → State → City, global reference data, tenant-scoped
  wrapper (Company Id = "Bharatnet" on every row) but content is generic world data

Config (1 record: "Bharatnet") — tenant-wide settings: address, timezone,
  number format, default currency/country, full GL account-number scaffolding

Role (per Module) — Menu Location flags + 3 Role-Permission category checkboxes
  + a page-by-page 9-action (View/Insert/Update/Delete/Print/Import/Download/
  Approval/Analytic) permission matrix spanning ~130 platform pages — but
  NOT covering the 7 CRM dashboard-tab entities, which sit outside this
  generic permission engine entirely.

WorkflowConfiguration — a full BPM/Delegation-of-Authority engine: multi-level
  (up to 8) approval chains, criteria-branched (e.g. Opex/Capex) real-money
  thresholds, Follow-Up/Escalation/Auto-Forward timers, Handlebars-style
  {{process.field}} notification templates. Currently used only for
  procurement processes (requisitions, POs, GRN, stock transfers), not CRM.

DisplayAttribute (~800 rows) — one per underlying platform DB table; a
  schema-driven config for that table's list-view rendering (view type +
  presumably visible columns, per the pattern seen on Items' 84-field picker).
```

## 3. Full function/feature list

### CRM entity modules (Home tab dashboard pattern: date-scoped chart + Status chart + slide-over create form; NOT traditional data grids)
- **Leads** — Create (rich form, 9-source enum, 10-status enum, free-text company); List/Edit/Delete/Convert not reachable (0 records)
- **Contacts** — Create (Company is a real FK; Feasibility toggle); same list-view gap
- **Companies** — Create (PAN/GST fields; related Contacts/Deals panel visible even pre-save); same gap
- **Deals** — Create (Company+Contact FKs required; Item Search/attach sub-feature querying the Items master by Type/Category/Location/Warehouse/Vendor/Customer); same gap
- **Campaign** — Create (Type × Objective dual taxonomy); same gap; **routing bug**: `TRANST=crm_campaign` (singular) renders broken vs. the real `crm_campaigns` route
- **Projects** — Create (Customer FK, not Company; multi-user Team assignment); no Status widget (only CRM tab without one); same list gap
- **Feasibility** — the one CRM tab with a **real filtered list view** (Open/Closed/SO-Not-Generated/SO-Generated pill filters with live counts) but **no reachable create form** — likely created indirectly via Contact's Feasibility toggle
- **Lead/Contact Assignings** — cross-module bulk-assignment tool: Manual (pick User) or Auto (pick Project, presumably round-robins across its team) assignment of un-allocated Leads/Contacts, Oldest/Newest ordering, Sharing Count cap. **Bug found**: duplicate `id="assign_type"` on the two mode radios breaks `getElementById`-based interaction.

### Masters (generic Active/Inactive/All + Search + Import/Export + gear-icon list-view template)
- **Items** — full inventory/accounting item master (84 underlying fields; 7-section create form: Items/Price/Inventory/Account/Tax/Specification Documents/Vendor Items/Customer Items)
- **Customers** — 8-tab create form (Overview/Basic Info/Contact Details/Note/Tax Info/Payment and Billing/Attachments/Rental Properties); 2 label/copy bugs found
- **Users** — the only list with real data (3 system accounts); rich profile view (Modules+Roles tags, 7 related-record panel types, 6 sub-tabs)
- **Invite Users** — bulk multi-row First/Last/Email/Role invite form; only 2 Roles selectable (CRM, CRM Admin)

### Admin
- **Display Attributes** — schema-driven list-view config; exposes ~800 real internal DB table names directly (including a leftover dev table `anshul_1`); Edit-prefill appears broken
- **Announcements** — read-only feed (My Announcements + Year/Month History); authoring lives in Settings, not explored in depth
- **Application Settings** (23 categories) — deep-dived: General Settings/Config (1 real tenant record), Geography (Countries/States/Cities, global data), Roles Management (2 CRM roles + full page-permission matrix), Workflow Setting (2 variants — one has a reproducible 500 error, the other has real procurement DOA workflows), Email (populated Template library + empty Groups), API (both sub-pages return a confirmed 403 for CRM Admin)

## 4. Cross-cutting patterns and bugs found

- **Two structurally different list-view families**: (a) the 7 CRM dashboard tabs (chart+form, no grid, no generic permission-matrix coverage), vs. (b) every Masters/Settings page (Active/Inactive/All grid with Import/Export/Search/gear-icon, all covered by Roles Management's page-permission catalog). These are evidently two separate UI/permission subsystems within the same product.
- **Two templating syntaxes coexist**: `{token}` single-brace (Email Content subject lines) vs. `{{object.field}}` double-brace Handlebars-style (Workflow notification messages).
- **Company vs. Customer are genuinely distinct entities** with no observed FK between them — Company is CRM's prospect/account record; Customer is the Masters/billing/project entity. A real product design decision, confirmed structurally (separate forms, separate ID spaces, separate list views), not assumed.
- **Source-code enum reuse**: Lead.source and Deal.sourceType share the same 9-value code list, but with a spelling inconsistency (`Facebook` on Lead vs. `Facebok` on Deal).
- **Confirmed bugs**: singular/plural Campaign route mismatch producing a broken duplicated dashboard; duplicate-`id` markup on Lead/Contact Assignings' radios; a reproducible HTTP 500 on the older Workflow Setting's create form; two copy/label mismatches on the Customer form's Note and Payment/Billing tabs (including a clear Vendor-form-template leftover, "The Vendor Pays Me With"); Display Attributes' Edit action not prefilling the selected table.
- **Confirmed role restriction**: API Master/API Hits return HTTP 403 for the CRM Admin role even though visible in the Settings menu — pointing to an even-higher platform-superuser tier (likely the "Anthony Johnston" `admin@` account) not exposed via the CRM module's own Roles Management screen.

## 5. Known gaps (honest accounting)

- **No populated CRM records anywhere.** Every "detail view," "edit form," "convert," "merge," "bulk export," and most "list/grid" behaviors for Leads/Contacts/Companies/Deals/Campaigns/Projects could not be observed — this tenant has 0 records in all 7 entities. Everything documented for these modules is the create-form schema plus the empty-state dashboard, honestly flagged per-module rather than invented.
- **Feasibility's create path is unconfirmed.** No "+Feasibility" button was found anywhere; the Contact form's Feasibility toggle is the best guess for how a record originates, but this was never verified end-to-end (would require submitting a form, excluded by the task's hard rules).
- **Convert/Merge/Duplicate-detection features for Leads** (common in other CRMs) were not observed anywhere in the UI — genuinely absent, or gated behind a record that doesn't exist in this tenant. Not confirmed either way.
- **Settings: ~16 of 23 categories were not opened in depth** (Organization, Work From Home, Holidays, most of Others, Vendor Managements, Module Managements, Building Spaces, Process Pages Managements, Form 16A, Machine and Material, Tasks, Application Missing Data Check, Field Map) — confirmed to exist via the navigation-map DOM extraction but not field-by-field documented, since they showed no connection to the CRM entities this task centers on.
- **Announcements' authoring form** (Settings > Company Announcements) was not opened — only the read-only consumption feed.
- **User profile's 5 non-Overview tabs** (Notification, Approval Transactions, User Activity, Reset Password, Mobile OTP) were not explored.
- **The Auto-Assign mechanism's actual distribution algorithm** (round-robin? load-balanced?) in Lead/Contact Assignings could not be observed — 0 Projects/team members with real workload existed to test against.
- **No outbound-communication or mutating action was ever executed**, per the task's hard rules (no form submitted with real data, no Invite/Assign/Send action fired) — so all "expected output" statements throughout the per-module docs are explicitly labeled as expected-not-observed, never asserted as fact.
- Two Workflow "levels" (Level 2 through Level 8) on the Requisitions approval-chain edit screen were only observed via their tab labels/icons, not opened individually — only Level 1's Approval Type ("Manager") was captured in detail.
