# Billstack (Bharatnet / Infistaq) — Navigation Map

System: https://bharatnet.infisuite.in
Product name shown in UI: "Infisuite" (login page) / "Infistaq" (page `<title>` on every page, e.g. "Infistaq | Home"). This is the **same shared multi-tenant "Infistaq" platform** already documented for the CRM module of this tenant (see `docs/crm-bharatnet/` in this repo, branch `worker/task-20260720-030153-crm-bharatnet-pilot--full-reverse-engine` — not yet merged to `main` at the time of writing, referenced read-only). Billstack is the billing/invoicing module for the same tenant ("Bharatnet").

Logged in as: `billstack_admin@bharatnet.infisuite.in`, displayed as "Billstack Admin" (avatar initials "BA").

This file was built by (1) logging in, (2) opening the Masters mega-menu and the "New" menu on both dashboard tabs, (3) opening each top-bar icon once, (4) confirming the gear-icon Application Settings tree is identical to the one already fully catalogued in the CRM docs (same 23 categories — verified by reopening it here, see below), (5) opening the tenant Config record for GST/tax/billing-relevant fields.

## 1. Login

Identical flow to the CRM tenant login (same login page, same numeric image captcha, same "Forgot Password?"/"Sign Up" elements) — see `docs/crm-bharatnet/00-navigation-map.md` §1 for full detail. Only the credentials differ:
- User Name: `billstack_admin@bharatnet.infisuite.in`
- Password: (not stored in this repo)
- Captcha: 4-digit numeric image, solved by reading the screenshot at login time (captcha value is session-specific and not reproducible).

On success, redirects to `/index` (page title "Infistaq | Home"), landing on the Billstack home/dashboard. Screenshot: `00-login-page.png`.

## 2. Global chrome (present on every authenticated page)

Top bar, left to right — same icon set, same order, and (where checked) same destinations as the CRM tenant's chrome:

1. **Logo** → `index.php` (Home).
2. **Icon: black circle "robot" glyph** → `index.php?option=COM_LIST_DISPLAY_ATTRIBUTES` — "Display Attributes" admin table. **Confirmed identical/shared** with CRM: same platform-wide list of ~400+ application table names (e.g. `accountsection`, `account_groups`, `additional_charges`), not module-scoped. Screenshot: `03-display-attributes.png`.
3. **Icon: gear** → `index.php?option=COM_LIST_APPLICATION_MENU_SETTINGS` — "Application Settings". **Confirmed identical category tree** to the one fully catalogued in `docs/crm-bharatnet/00-navigation-map.md` §5 (same 23 top-level categories: ACCOUNT SETTING, ORGANIZATION, WORK FROM HOME SETTING, GEOGRAPHY, HOLIDAYS, ANNOUNCEMENTS, USER MANAGEMENT, ITEMS, OTHERS, VENDOR MANAGEMENTS, CUSTOMER MANAGEMENTS, MODULE MANAGEMENTS, BUILDING SPACES, PROCESS PAGES MANAGEMENTS, ROLES MANAGEMENT, API, FROM 16A DETAILS, EMAIL, WORKFLOW SETTING, MACHINE AND MATERIAL, TASKS, APPLICATION MISSING DATA CHECK, FIELD MAP) — this is genuine platform-wide, tenant-scoped (not module-scoped) configuration, confirming CRM and Billstack share one config/settings backend per tenant. Not re-documented in full here; see the CRM doc. The one sub-page opened here for billing relevance was **ACCOUNT SETTING → GENERAL SETTINGS (`COM_LIST_CONFIG`)**, whose single "Bharatnet" record was opened in view mode (`COM_VIEW_CONFIG`) — see §4 below for the billing/tax-relevant fields found there.
4. **Icon: megaphone** → `index.php?option=COM_VIEW_ANNOUNCEMENTS` — company announcements feed. **Confirmed identical route** to CRM; not re-explored in depth (same tenant-wide announcements, no Billstack-specific content observed).
5. **Icon: bell** (badge shows unread count, "0") — notifications slide-out panel. Not re-opened this session; assumed identical to CRM's (Action/Information/Logs tabs, empty state) based on shared chrome — **not directly re-confirmed, treat as inferred-by-analogy**.
6. **Icon: headset** → Ticketing/Support dashboard entry point (same as CRM). **Out of scope**, not explored.
7. **Avatar + "Billstack Admin" dropdown** — assumed same Profile/My Messages/Logout items as CRM by shared-chrome analogy; **not directly re-confirmed this session**.
8. **Icon: colored dot pattern ("Modules")** → dropdown panel titled "Modules" with a "Billstack Admin" sub-dropdown and two module tiles: **Billstack** (currently selected, orange checkmark) and **CRM** (unselected). This is the mirror image of the CRM tenant's switcher (which showed CRM selected, Billstack unselected) — confirms the same two-module tenant, switchable per login. **CRM tile not clicked** (out of scope; also unclear whether this login has CRM access — not tested). Screenshot: `07-modules-switcher.png`.

Second row (below the icon bar):

- **Home** (logo link, same as #1).
- **Masters** — mega-menu (click, not hover), 5 column groups (fewer/different than CRM's, reflecting Billstack's domain):
  - **Organization**: Division (`COM_LIST_DIVISIONS`), Departments (`COM_LIST_DEPARTMENTS`), Cost Centres (`COM_LIST_COST_CENTERS`), Locations (`COM_LIST_LOCATIONS`)
  - **Items**: Item Categories (`COM_LIST_ITEM_CATEGORIES`), Items Type (`COM_LIST_ITEM_TYPES`), Items (`COM_LIST_ITEMS`)
  - **Others**: HSN Code (`COM_LIST_HSN_AND_SAC_CODES`), Currencies (`COM_LIST_CURRENCIES`), Workflow (`COM_LIST_WORKFLOW_CONFIGURATIONS_NEW`)
  - **Vendors**: Vendors (`COM_LIST_VENDORS`)
  - **Users**: Users (`COM_LIST_APPLICATION_USERS`), Invite Users (`COM_LIST_APPLICATION_USER_INVITATIONS`)

  **Notable gap**: there is **no "Customers" column in the Masters mega-menu**, even though the Customer Bills dashboard tab links directly to `COM_LIST_CUSTOMERS` in three places (widget, "TOP 3 Customers", "Recent Customers"). Customers is reachable only via those dashboard links, not via Masters. Documented as observed, not assumed to be a bug — CRM's Masters menu also has no Vendors column (mirrored asymmetry, each module's mega-menu surfaces only its "own side" of the party master, while the dashboard for the other side still deep-links to the shared `COM_LIST_CUSTOMERS`/`COM_LIST_VENDORS` table).
  - Screenshot: `04-masters-menu.png`.
- **New** — a second menu, single item: **Purchase Invoices** → `index.php?option=COM_LIST_SUPPLIER_BILLS` (this is a **list** page, not an insert form, despite the "New" label). **Bug/quirk observed**: this menu's content is **static** — it shows the same single "Purchase Invoices" item whether the active dashboard tab is Vendor Bills or Customer Bills; it does not become "Sales Invoices" when Customer Bills is active, even though the page content it points to (Supplier Bills) is Vendor-Bills-specific. Screenshots: `05-new-menu.png`, `08-new-menu-customerbills.png`.

## 3. Billstack Home page (`index` / `index.php`)

Layout: **no profile summary card** (unlike CRM's Home, which has one) — the page opens directly on a **tab strip**: **Vendar Bills** [sic — typo in product, "Vendar" not "Vendor"] | **Customer Bills**. Same ARIA-bug as CRM's CRM-module tab strip: both tabs get `[selected]` simultaneously in the accessibility tree, but only one panel is visibly active — documented as observed, not two real selections.

### 3a. "Vendar Bills" tab (default/active on load)

Hero banner: "Start managing supplier invoices with ease!" / "Helps you in managing: Suppliers, Supplier bills and Payments..." with two CTA buttons:
- **Create Invoice** → `index.php?option=COM_INSERT_SUPPLIER_BILLS`
- **Payments** → `index.php?option=COM_LIST_PAYMENTS`

Three counter tiles (all "0" on this tenant — zero data, same empty-tenant situation as CRM):
- **0 Vendors** "Vendors Who Are Active" → `COM_LIST_VENDORS`
- **0 Invoices** "Supplier Invoices Created In the System" → `COM_LIST_SUPPLIER_BILLS`
- **0 Invoices** "Supplier Invoices Approved In the System" → `COM_LIST_PURCHASE_INVOICES_TABLE`

Below that:
- **"Vendor Invoice/Payments"** — a year-picker (2016–2026, default 2026) driving a 12-month **Bills vs Paid** bar/line chart (amCharts), all-zero on this tenant.
- **"Vendor Invoice"** donut/summary: Total Invoices (0), broken into **Paid** / **Payment Due** / **Yet To Due** (0/0/0) — this is the invoice **payment-status taxonomy** for supplier bills, confirmed here even though no records exist to populate it (three-way lifecycle: paid vs. due-but-not-yet-due-date vs. overdue-and-due, inferred from label wording — **inferred**, not confirmed against a real record).
- **"Approval Pending"** table (columns: # / Number / Request Date / Transaction Date / Price) — empty ("No Approval Pending"). Confirms Billstack has a **bill approval workflow** with a request/response date pair.
- **"Approved"** table (columns: # / Number / Response Date / Transaction Date / Price) — empty ("No Approved").
- **"TOP 3 VENDORS"** / **"Recent Vendors"** widgets, both "No Vendors", both "View all" → `COM_LIST_VENDORS`.
- **Approved / Un-Approved** tab pair (both badge "0") showing an **"Approved Supplier invoices"** table (columns: # / Vendor / Invoices Number / Date / Price / Status / Action) — empty ("No Bills Created").

Screenshot: `01-dashboard-home.png`.

### 3b. "Customer Bills" tab

Same layout pattern, mirrored for the sales side:

Hero banner: "Start managing Sales invoices with ease!" / "Helps you in managing: Sales bills and Receipts..." with **three** CTA buttons (one more than Vendor Bills side):
- **Upload Invoices** → `index.php?option=COM_INSERT_SALES_INVOICE_DYNAMIC` (bulk/dynamic upload — implies a spreadsheet or template-based bulk import, distinct from the single-record create form)
- **Create Invoice** → `index.php?option=COM_INSERT_SALES_INVOICES`
- **Receipts** → `index.php?option=COM_LIST_RECEIPTS`

Three counter tiles (all 0):
- **0 Customers** "Customers Who Are Active" → `COM_LIST_CUSTOMERS`
- **0 Invoices** "Sales Invoices Created In the System" → `COM_LIST_SALES_INVOICES`
- **0 Invoices** "Sales Invoices Approved In the System" → `COM_LIST_SALES_INVOICES_TABLE`

Below: **"Customers Invoice/Payments"** year chart (Bills vs Paid, 2016–2026), **"Customers Invoice"** donut (Total/Paid/Payment Due/Yet To Due), **Approval Pending** / **Approved** tables (same column sets as vendor side), **"TOP 3 Customers"** / **"Recent Customers"** widgets → `COM_LIST_CUSTOMERS`, and an **Approved/Un-Approved** tab pair showing an "Approved Sales invoices" table (# / Customer / Invoices Number / Date / Price / Status / Action) — "No Invoices Created".

Screenshot: `02-dashboard-customer-bills.png`.

## 4. Tenant Config record (billing/tax-relevant fields)

Opened via gear icon → ACCOUNT SETTING → GENERAL SETTINGS → the tenant's single "Bharatnet" Config row → view (`COM_VIEW_CONFIG`). This record is **shared with CRM** (same tenant-wide config, not Billstack-specific) but is documented here because it directly governs Billstack's tax/numbering/accounting behavior:

| Field | Value observed |
|---|---|
| Name | Bharatnet |
| Liscense Number [sic] | *(blank)* |
| Tax Number | 0.0000 |
| GST Number | *(blank)* |
| CIN Number | *(blank)* |
| PAN Number | *(blank)* |
| Phone Number | *(blank)* |
| FAX Number | *(blank)* |
| Billing Address | 2nd Floor, Tilak Complex 27, Tilak Road, Dehradun, Uttrakhand |
| Contact Address | (same) |
| Display Logo | file_1704095913_659270a975d4a.png |
| Assessment Year | *(blank)* |
| Time Zone | Asia/Kolkata |
| Date Format | DD-MM-YYYY |
| Date Separator | - |
| Percentage Position | *(blank)* |
| Number Format | Lakhs (0.00) — Indian numbering convention |
| Invoice Perfix / Postfix [sic] | *(both blank)* |
| Kpi Mode | Yes |
| Location | *(blank)* |
| Default Language Id | *(blank)* |
| Default Currency | Rupees |
| Default Cash Account | 10005101 |
| Default Payment Mode | *(blank)* |
| Default Tax Account | 40000-40260-0000 |
| Subscription Info | *(blank)* |
| Application Module | Infistaq |
| Copy Right | *(blank)* |
| Default Country | India |
| Suspense Acc | 200037018 |
| Default Bank Account | *(blank)* |
| TDS Clearing Account | TDS Clearing Account |
| TDS Payables | 20003702 |
| TCS Clearing Account | 20003701 |
| TCS Payables | 20003716 |

Observations (directly from data, not inferred):
- This tenant's **GST Number, PAN, CIN, Tax Number are all unset/blank** — this Billstack instance is configured with **no live GSTIN**, which will materially affect any GST computation on invoices (likely defaults to 0% or is simply not printed on invoice PDFs — **not confirmed**, no invoice exists to check, flagged as a gap).
- Distinct **TDS** (Tax Deducted at Source) and **TCS** (Tax Collected at Source) clearing/payable account pairs exist — standard Indian income-tax withholding mechanics — implying Billstack's ledger posting logic supports TDS/TCS deduction on vendor bills / customer invoices respectively. Not observed in action (no transactions exist).
- A generic **Default Tax Account** (`40000-40260-0000`) exists separately from TDS/TCS, presumably the GST output/input tax control account.
- **No dedicated "Payment Gateway" settings section** was found anywhere in the 23-category Settings tree, nor in Masters, nor on the dashboards. The only payment-adjacent concepts observed are **Default Payment Mode** (blank here), the **Payments** list (`COM_LIST_PAYMENTS`, vendor side) and **Receipts** list (`COM_LIST_RECEIPTS`, customer side) — these read as **manual/internal payment-recording ledgers** (recording that a payment/receipt happened, by whatever means), not an online payment-gateway checkout integration. This is a **gap**: cannot confirm or rule out a gateway integration without opening the Create-Payment / Create-Receipt forms (see `payments.md` / `receipts.md`).

Screenshot: `06-config-view.png`.

## 5. Full checklist (this task's scope)

- [x] Login + captcha
- [x] Home dashboard (both tabs: Vendar Bills, Customer Bills)
- [x] Global chrome icons identified (Display Attributes, Settings, Announcements confirmed identical to CRM; Notifications/Avatar dropdown/Ticket dashboard assumed identical by analogy, not re-confirmed)
- [x] Modules switcher (Billstack selected, CRM present)
- [x] Masters mega-menu inventory
- [x] "New" menu inventory (+ static-content bug noted)
- [x] Tenant Config record (GST/tax/accounting fields)
- [ ] Masters > Vendors (list + create)
- [ ] Masters > Customers (list + create) — reached via dashboard links, not Masters menu
- [ ] Masters > Items, Item Categories, Items Type
- [ ] Masters > HSN Code, Currencies, Workflow
- [ ] Masters > Division, Departments, Cost Centres, Locations
- [ ] Masters > Users, Invite Users
- [ ] Vendor Bills: Supplier Bills list (`COM_LIST_SUPPLIER_BILLS`) + create form (`COM_INSERT_SUPPLIER_BILLS`)
- [ ] Vendor Bills: Purchase Invoices Table / approved (`COM_LIST_PURCHASE_INVOICES_TABLE`)
- [ ] Vendor Bills: Payments (`COM_LIST_PAYMENTS`)
- [ ] Customer Bills: Sales Invoices list (`COM_LIST_SALES_INVOICES`) + create form (`COM_INSERT_SALES_INVOICES`) + Upload/Dynamic insert (`COM_INSERT_SALES_INVOICE_DYNAMIC`)
- [ ] Customer Bills: Sales Invoices Table / approved (`COM_LIST_SALES_INVOICES_TABLE`)
- [ ] Customer Bills: Receipts (`COM_LIST_RECEIPTS`)

## 6. Known structural notes carried into all module docs

- Same list-view template as CRM: Active/Inactive/All radio, Records page-size + Search ("Min 3 Characters Required"), Status dropdown, Import/Export, green **+ Create** button, Back, gear (display-attribute config).
- This tenant's Billstack data is **entirely empty** (0 Vendors, 0 Customers, 0 Invoices everywhere). Same limitation as CRM: detail/edit views may not be openable if no records exist to click into — flagged per-module rather than assumed absent.
- Tab-strip ARIA-selected bug (all tabs marked `[selected]`) reproduces here exactly as in CRM — confirmed platform-wide front-end defect, not tenant-specific.
