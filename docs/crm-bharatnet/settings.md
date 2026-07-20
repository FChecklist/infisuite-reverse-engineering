# Application Settings

## 1. Purpose

A platform-wide administration area covering configuration for the entire Infistaq ERP (HR, inventory, accounting, procurement, field service, and CRM), not a CRM-only settings page. Reached via the gear icon in the top nav. The full 23-category / ~55-sub-page accordion tree is enumerated in `00-navigation-map.md` §5; this document goes one level deeper on the categories most load-bearing for CRM data, per the original task's scoping note, plus two categories (Roles Management, Workflow Setting) that turned out to be unusually rich and directly explain CRM-adjacent business logic (approval routing, permission model).

## 2. Navigation path

Top icon bar → gear icon → `index.php?option=COM_LIST_APPLICATION_MENU_SETTINGS`, or directly via each sub-page's own `option=` URL (all listed in `00-navigation-map.md` §5).

## 3. General Settings (ACCOUNT SETTING > GENERAL SETTINGS, `COM_LIST_CONFIG`)

**List view:** standard Masters template, 1 real record: **Bharatnet** (Admin Email `admin@bharatnet.infisuite.in`), Action icons Edit/Copy/**View** (the only Masters list observed with a 3-icon action set including View, vs. the more common Edit-only or Edit+Copy+View pattern — inconsistent across modules).

**Detail view** (`COM_VIEW_CONFIG`, screenshot `settings-general-02-view.png`) — **this is real tenant configuration data**, documented factually:

| Field | Value |
|---|---|
| Name | Bharatnet |
| Tax Number | 0.0000 |
| Billing Address / Contact Address | 2nd Floor, Tilak Complex 27, Tilak Road, Dehradun, Uttrakhand |
| Display Logo | `file_1704095913_659270a975d4a.png` |
| Time Zone | Asia/Kolkata |
| Date Format | DD-MM-YYYY |
| Date Separator | `-` |
| Number Format | Lakhs (0.00) — confirms Indian numbering convention (lakh/crore), not Western thousands-grouping |
| Kpi Mode | Yes |
| Default Currency | Rupees |
| Default Cash Account | 10005101 |
| Default Tax Account | 40000-40260-0000 |
| Suspense Acc | 200037018 |
| Application Module | Infistaq |
| Default Country | India |
| TDS Clearing Account / TDS Payables | TDS Clearing Account / 20003702 |
| TCS Clearing Account / TCS Payables | 20003701 / 20003716 |
| (blank) | Liscense Number [sic], GST Number, CIN Number, PAN Number, Phone/FAX Number, Assessment Year, Percentage Position, Invoice Prefix/Postfix, Location, Default Payment Mode, Subscription Info, Copy Right, Default Bank Account |

This single record confirms: (a) this is a real, in-use production tenant with a real Dehradun office address; (b) the platform is deeply India-accounting-aware (Lakhs number format, TDS/TCS/GST account wiring) — consistent with every other India-specific field seen throughout the CRM (PAN/GST on Company, HSN/SAC on Items, etc.); (c) a full chart-of-accounts numbering scheme (5-digit account codes) already exists tenant-wide, matching the empty GL-account dropdowns seen on the Items form.

## 4. Geography (Countries / States / Cities)

All three (`COM_LIST_COUNTRIES`, `COM_LIST_STATES`, `COM_LIST_CITIES`) are populated **global reference master data**, not tenant-entered — this is the direct backing data for the Country/State/City cascading dropdowns seen on every Lead/Contact/Company/Customer address form throughout this review.

- **Countries** (`settings-geography-01-countries.png`): columns Id, Company Id (always "Bharatnet" — i.e. this master is technically tenant-scoped even though its content is generic world data), Country Name, Short Name (ISO alpha-2), Other Code (ISO alpha-3), ISD Code, Is Active, Action. 251 rows (matches the count noted in `leads.md`), e.g. Afghanistan/AF/AFG/93, Aland Islands/AX/ALA/+358-18.
- **States**: columns Id, State Name, Country, Is Active, Action. Global data (sample: Ethiopia's regions — Southern Nations Nationalities and Peoples' Region, Somali Region, Amhara Region, Tigray Region, Oromia Region — confirms full-world coverage, not India-only).
- **Cities**: columns Id, Company Id, City Name, State, Is Active, Action. Global data (sample: Andorra la Vella, Arinsal, Canillo — Andorra's cities appear first alphabetically by country).

## 5. Roles Management (`COM_INSERT_APPLICATION_ROLE`)

**This page directly exposes the platform's full permission model** and is one of the richest findings in this review.

**Landing state:** a single **Module** dropdown, default "Select Any One Module" — **2 options: `CRM` and `Billstack`** (confirms exactly 2 licensed modules for this tenant, matching the Modules switcher noted in `00-navigation-map.md`).

**Selecting Module = CRM** (`settings-roles-02-crm-selected.png`) reveals exactly **2 Roles for the CRM module**, each shown as a card with its granted permission-category tags, an **Edit Role** button, and an **Assign User** link:
- **CRM** role → tagged: `CRM Management` only
- **CRM Admin** role → tagged: `CRM Management`, `Settings`, `Reports` (this is the role assigned to the login used throughout this review, confirmed via the Users profile page, `masters-users.md` §4)

A third "Add New Role" card lets an admin define additional roles for the selected module.

**Edit Role modal** (`settings-roles-03-edit-modal.png`, opened for CRM Admin) reveals the full structure of a Role record:
| Field | Value (CRM Admin) |
|---|---|
| Role Name | CRM Admin |
| Short Code | CRM_ADMIN |
| Module | CRM (locked) |
| Menu Location | 5 checkboxes, all checked: Left Bar, Top Bar, Top Bar Left, Top Bar Right, Top Bar Center |
| Role Permissions | 3 category checkboxes (CRM Management, Settings, Reports), all checked, plus a "Select All" master checkbox |
| Page Permissions | **A per-page, per-action permission matrix** — one row per underlying application page, each with up to 9 independent checkboxes: **View, Insert, Update, Delete, Print, Import, Download, Approval, Analytic** |

**The Page Permissions list spans the entire Infistaq platform, not just CRM** — confirming yet again that permissions here are platform-wide and modules are really just a filter over one shared page/permission catalog. Sampled page names (130 distinct, non-exhaustive extraction): HR/payroll (`SALARY HISTORY`, `EMPLOYEE SATISFACTION`, `EMPLOYEE WELLBEING`, `TIME OFF BALANCES`, `EPF SUMMARY`, `ESI SUMMARY`, `PROFESSIONAL TAX SUMMARY`, `BENEFIT SUMMARY`, HR/EEO compliance reports like `EEO-1`, `GENDER PROFILE`, `HEADCOUNT`), sales/finance (`SALES`, `SALES BY ITEM`, `RECEIABLE` [sic], `PAYABLES`, `GENERAL LEDGER`, `TRIAL BALANCE`, `PROFIT AND LOSS`, `BALANCE SHEET`), inventory/procurement (`INVENTORY SUMMARY`, `PO DETAILS`, `PO BY VENDOR`, `VENDOR BILLS`, `STOCK SUMMARY`), and Masters/admin pages that map 1:1 to modules documented elsewhere in this review: `ITEMS`, `ITEM CATEGORIES`, `CUSTOMERS`, `CONFIG`, `WAREHOUSES`, `APPLICATION USERS`, `DISPLAY ATTRIBUTES`, `APPLICATION ROLES`, `WORKFLOW CONFIGURATION`, `EMAIL CONTENS` [sic], `COUNTRIES`/`STATES`/`CITIES`.

**Notable absence:** none of the CRM's own core dashboard-tab entities — **Leads, Contacts, Companies, Deals, Campaigns, Projects, Feasibility** — appear anywhere in this Page Permissions catalog. This is strong confirmatory evidence (not just inferred) for the pattern already observed structurally: those 7 CRM tabs are **not** built on the platform's generic list-view/page-permission engine the way every Masters/Settings page is. They're a separate, purpose-built dashboard+slide-over-form subsystem, so they don't get row-level View/Insert/Update/Delete/etc. permission toggles — access to them is presumably gated only by the coarser `CRM Management` role-category checkbox, not per-entity.

## 6. Workflow Setting

Two sub-pages exist, and they are **not equivalent** — this is a genuinely confusing area of the product:

### Workflow Setting (`COM_LIST_WORKFLOW_CONFIGURATION`)
List view (`settings-workflow-01.png`): standard template, filters **Trigger Workflow**, **Hierarchical Level**, **Tigger Doa** [sic — "Trigger DOA", Delegation Of Authority], 0 records in this tenant. Clicking its **+ Workflow Configuration** create button (`COM_INSERT_WORKFLOW_CONFIGURATIONS`) produces a **genuine HTTP 500 Internal Server Error** — a blank white page with only the top nav bar's blue accent strip rendering (`settings-workflow-02-500-error.png`). Documented as observed: a real, reproducible server-side bug in this admin function, not a task artifact.

### Workflow Setting-1.1 (`COM_LIST_WORKFLOW_CONFIGURATIONS_NEW`)
The apparent working replacement. List view (`settings-workflow-03-new-variant.png`) has **real, populated data** — 8+ pre-configured approval workflows for procurement/inventory processes: `requisitions` ("approval process of Requisitions or Indents"), `quotations`, `purchase_orders`, `goods_receipt_notes` (GRN), `application_table_quality_check`, `grn_returns`, `stock_requests`, `stock_transfers` — all **8-level** workflows except quality_check (1-level). None are CRM entities — this confirms Workflow Setting is a procurement/supply-chain approval-routing engine, not a CRM feature, despite living in the same Settings tree as CRM-relevant categories.

**Edit view for "requisitions"** (`settings-workflow-04-edit-requisitions.png`) is the richest single config screen found in this entire review — a full Business-Process-Management / Delegation-of-Authority engine:
- **Process Details**: Process Name, Description, Workflow Trigger Status (Active), a 3-way workflow-type radio (**Hierarchical** / **Role Level** / **Custom Workflow** — "Custom Workflow" selected here), Workflow Level (8-Level), Transaction Owner (Originator), Transaction Owner Type (User), **Follow Up** (toggle + Days=5 + Follow Up Email template), **Escalation** (toggle + Days=2 + Escalation Email template), **Auto Forward** (toggle + Days=55).
- **Criteria Details**: named criteria (e.g. "Criteria-1" = "Opex Capex") that branch the workflow, with 8 tabbed Levels, each level assigning an **Approval Type** (e.g. "Manager") and a "Doc Update" toggle.
- **DOA Approval Levels**: an 8-rung approval chain — **Manager → Managers Manager → Buddy (HR Buddy) → Individual Employee Role → Individual Employee Role → Buddy (Finance Buddy) → Individual Employee Role → Individual Employee Role** — each rung (L1–L8) paired with a **DOA Trigger Field** (Total Amount) and real-money threshold tiers per criteria branch (Opex tier 1: ≤₹45,164.40 / ≤₹47,164.40 / ≤₹99,999,999,999.00 …; a mirrored Capex column with the same amounts). This is a genuine multi-tier spend-approval matrix with real configured thresholds.
- **Notification Message**: 6 message templates (Submit/Approve, Reject, Send Back, Submit/Approve Informations [sic], Completed Workflow, Call Back, Information), each using **Handlebars-style template variables** scoped to the process, e.g. `{{requisitions.process_name}}`, `{{requisitions.transaction_number}}`, `{{session_user.display_name}}`, `{{requisitions.originator}}`, `{{requisitions.approval_name}}` — confirms a shared templating syntax also likely used by the Email Content templates (§8) and Campaign/notification features.

This confirms the platform has a full generic BPM/approval-routing subsystem, currently used for procurement (not CRM) processes in this tenant.

## 7. Roles vs. Workflow relationship (inferred)

Roles Management defines *who can act* (page-level CRUD permissions); Workflow Setting defines *what approval chain a specific transaction must pass through* (DOA/spend-threshold routing). The two are complementary but structurally separate: Workflow's "Approval Type" values (Manager, Managers Manager, Buddy, Individual Employee Role) are a different taxonomy from the Roles Management CRM/CRM Admin role pair — i.e. workflow approvers are resolved by org-position type, not by the same Role entity used for page permissions.

## 8. Email

### Templates (`COM_LIST_EMAIL_CONTENT`)
Real, populated data — a full transactional-email template library (`settings-email-01-templates.png`), columns Id/Template Name/Email Subject Cc/Email Subject To/Action. Sample of 8 (of more, paginated): **Admin invitation**, **Admin Account Activated**, **User invitation**, **User Account Activated**, **Reset password**, **Profile Updated**, **User Role change**, **Account Deactivation**. Subject lines use `{admin_company_name}`/`{module_name}`/`{product_name}` placeholder tokens (curly-brace style, distinct from the Workflow engine's double-curly-brace `{{...}}` Handlebars style — **two different templating syntaxes coexist in the platform**). This confirms the exact email that would fire from the Invite Users action (`masters-users.md` §6) is the "User invitation" template here.

### Groups (`COM_LIST_EMAIL_GROUPS`)
Empty — 0 records, and notably renders with **no illustrated empty-state graphic at all** (unlike every other empty Masters list), just a bare header + "+ Email Group" button. Presumably a distribution-list/CC-group feature for routing notification emails. Not explored further (0 data, low CRM relevance).

## 9. API

Both sub-pages are **permission-denied for this CRM Admin login**:
- **API Master** (`COM_LIST_API_MASTERS`) → HTTP 403, page text: *"API MASTERS NOT FOUND — OOPS!! YOU DO NOT HAVE THE REQUIRED PERMISSION, PLEASE CONTACT YOUR ADMIN: ADMIN@BHARATNET.INFISUITE.IN"* (`settings-api-01-master.png`)
- **API Hits** (`COM_LIST_API_HITS`) → identical 403 pattern (`settings-api-02-hits.png`)

This is a **directly confirmed role-based access restriction**, not an inference: despite "CRM Admin" being the most privileged role available in this tenant's CRM module (per §5) and API Master/API Hits both appearing as clickable menu items in the Settings accordion, this account cannot actually open either page. The error message's phrasing ("contact your admin: admin@bharatnet...") points back to the "Anthony Johnston" `admin@` account (`masters-users.md` §3) as the true platform superuser, distinct from CRM Admin. This confirms a role hierarchy above "CRM Admin" exists, even though Roles Management (§5) only shows CRM/CRM Admin as the 2 roles for the CRM module — API access is presumably gated by a different, platform-level (not module-level) permission not exposed in the CRM module's Roles screen.

## 10. Other categories (not explored in depth — see `00-navigation-map.md` §5 for the complete list)

Given this task's CRM-first scope, the remaining ~16 Settings categories (Organization, Work From Home Setting, Holidays, User Management's Add/List User sub-pages — covered instead via `masters-users.md`, Items' Item Categories/UOM sub-pages, Others (HSN/SAC, Currencies, Complaints, Mobile Registrations), Vendor Managements, Customer Managements' Invite/Quality Control sub-pages, Module Managements, Building Spaces, Process Pages Managements, Form 16A Details, Machine and Material, Tasks, Application Missing Data Check, Field Map) were confirmed to exist and load (per the navigation map's DOM extraction) but not individually opened/documented field-by-field in this pass. This is an intentional, honestly-flagged scope decision, not an oversight — these categories are HR/procurement/field-service administration with no observed connection to the CRM entities (Leads/Contacts/Companies/Deals/Campaign/Projects/Feasibility) that are this task's primary subject.
