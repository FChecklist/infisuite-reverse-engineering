# Billstack (Cityline Networks / Infistaq) — Navigation Map

System: `http://citylinenetworks.infisuite.in/` (same base URL/tenant as `docs/cityline-crm/`, different login + Modules switcher selection).
Logged in as: `bill_admin@citylinenetworks.infisuite.in`, displayed as "Billstack Admin".

**Login credential note:** password **`infi123`** (same convention confirmed for the CRM login on this tenant) worked on the first attempt — confirms the `infi123` convention applies tenant-wide, not per-module-login. Captcha mechanism identical to CRM (4-digit numeric image, read from screenshot). Screenshot: `screenshots/cityline-billstack/00-login-page.png`.

## 1. Global chrome
Same icon set as CRM (Display Attributes / Settings / Announcements / Notifications / Ticket dashboard / Avatar / Modules), confirmed by direct observation this session (not just assumed-by-analogy). Modules switcher (not re-screenshotted, confirmed via Roles-module-dropdown in `docs/cityline-crm/settings.md` §5): same 3 tiles CRM/Billstack/Contract.

Second row nav differs from CRM: **Masters** (mega-menu, Billstack-scoped items) + **New** (dropdown, invoice-creation shortcuts) — **no standalone Home/dashboard link needed**, since Billstack's `index` page IS the dashboard (no separate profile-summary landing page like CRM has).

## 2. Masters mega-menu (Billstack-scoped, narrower than CRM's)
| Group | Item | URL |
|---|---|---|
| Organization | Division | `COM_LIST_DIVISIONS` |
| Organization | Departments | `COM_LIST_DEPARTMENTS` |
| Organization | Cost Centres | `COM_LIST_COST_CENTERS` |
| Organization | Locations | `COM_LIST_LOCATIONS` |
| Items | Item Categories | `COM_LIST_ITEM_CATEGORIES` |
| Items | Items Type | `COM_LIST_ITEM_TYPES` |
| Items | Items | `COM_LIST_ITEMS` |
| Others | HSN Code | `COM_LIST_HSN_AND_SAC_CODES` |
| Others | Currencies | `COM_LIST_CURRENCIES` |
| Others | Workflow | `COM_LIST_WORKFLOW_CONFIGURATIONS_NEW` |
| Vendors | Vendors | `COM_LIST_VENDORS` |
| Users | Invite Users | `COM_LIST_APPLICATION_USER_INVITATIONS` |
| (Users) | Users | `COM_LIST_APPLICATION_USERS` (present, not captured in the same DOM query pass but reachable — same shared Users master documented in `docs/cityline-crm/masters.md`) |

**No "Customers" column** in this menu — same asymmetry Bharatnet's Billstack pilot doc reported (Customers reachable only via dashboard counter links, not via Masters). Items/Categories/Types/Vendors/Users/Invite-Users/Divisions/Departments/Cost-Centres/Locations/HSN/Currencies are **shared masters with CRM and the platform Settings tree** (same `option=` values as `docs/cityline-crm/masters.md` and `settings.md` — confirmed by matching URLs, not re-screenshotted per item here to avoid duplication).

## 3. "New" menu — a real difference from Bharatnet's finding
Bharatnet's Billstack pilot documented the "New" menu as **static/buggy**: always showing only "Purchase Invoices" regardless of which dashboard tab (Vendor Bills / Customer Bills) was active. On **this** tenant, the "New" menu consistently shows **both** items — **Sales Invoices** and **Purchase Invoices** — regardless of active tab (confirmed on the Customer Bills tab, screenshot `03-new-menu-customerbills.png`). This is a genuine, directly-observed difference from the Bharatnet doc: either the Bharatnet pilot only captured a partial/stale DOM state, or this is a real fix/difference between the two tenants' app builds. Recorded as observed on this tenant, not re-verified against a live Bharatnet session.

## 4. Home / dashboard (`index`)
Two tabs, same ARIA-multi-selected-tabs bug as CRM: **Vendar Bills** [sic] (default) | **Customer Bills**.

### Vendar Bills tab
- Hero banner: "Start managing supplier invoices with ease!" + **Create Invoice** (`COM_INSERT_SUPPLIER_BILLS`) / **Payments** (`COM_LIST_PAYMENTS`) buttons.
- 3 counter tiles (all 0): Vendors / Invoices Created / Invoices Approved.
- **Vendor Invoice/Payments**: year-picker **2016–2026** (default 2026), 12-month Bills-vs-Paid line chart (amCharts) — all zero.
- **Vendor Invoice** donut: Total Invoices 0%, broken into Paid/Payment Due/Yet To Due (0/0/0).
- **Approval Pending** table (#/Number/Request Date/Transaction Date/Price) — empty.
- **Approved** table (#/Number/Response Date/Transaction Date/Price) — empty.
- **TOP 3 VENDORS** / **Recent Vendors** widgets → `COM_LIST_VENDORS`, both "No Vendors".
- **Approved (0) / Un-Approved (0)** tab pair → "Approved Supplier invoices" table (#/Vendor/Invoices Number/Date/Price/Status/Action) — "No Bills Created".

Screenshot: `01-dashboard-vendor-bills.png`.

### Customer Bills tab
Mirrors Vendor Bills for the sales side, with **one extra CTA**: Upload Invoices (`COM_INSERT_SALES_INVOICE_DYNAMIC`, bulk import) / Create Invoice (`COM_INSERT_SALES_INVOICES`) / Receipts (`COM_LIST_RECEIPTS`). Same all-zero counters/charts/tables, labeled Customer/Sales instead of Vendor/Supplier. Screenshot: `02-dashboard-customer-bills.png`.

## 5. Tenant Config record — notable finding
`Masters`-adjacent Settings path: gear icon → Account Setting → General Settings (`COM_LIST_CONFIG`), single record, opened via `COM_VIEW_CONFIG`. **Unlike Bharatnet's mostly-blank Config record**, this tenant's Config is **fully populated with real corporate registration data — but for "Infisuite" (the platform vendor itself), not "Cityline Networks" (the customer/tenant)**:

| Field | Value |
|---|---|
| Name | Infisuite |
| License Number | LIC754855255 |
| CIN Number | L250333505485220 |
| GST Number | 18AABCU9603R1ZM |
| PAN Number | CCIGS5009 |
| Phone Number | 8700000000 |
| Billing/Contact Address | "INFISUITE SERVICES INDIA PRIVATE LIMITED LEVEL NOS 812, Block 5, ..." (truncated in UI) |
| Admin Email (list view) | admin@vendorstaq.com |
| Time Zone | Asia/Kolkata |
| Date Format | DD-MM-YYYY |
| Number Format | Lakhs (0.00) — Indian numbering |
| Default Currency | Rupees |
| Default Country | India |
| Default Cash Account | 10005101 |
| Default Tax Account | 40000-40260-0000 |
| Suspense Acc | 200037018 |
| TDS Payables / TCS Clearing / TCS Payables | 20003702 / 20003701 / 20003716 |
| Application Module | Infistaq |

**This is a directly-observed finding, not an inference:** the tenant identified in the task as "Cityline Networks" has never had its Billstack Config record customized away from the platform vendor's own seed data (Infisuite Services India Private Limited's real-looking GST/PAN/CIN numbers). This confirms the "QA/test sandbox" character already inferred from the CRM module's junk Project data (see `docs/cityline-crm/SUMMARY.md`) — a production tenant would virtually certainly have its own company's GST/PAN, not the vendor's. Screenshots: `07-config-list.png`, `08-config-view.png`.

## 6. Checklist
- [x] Login (password convention re-confirmed: `infi123`)
- [x] Dashboard both tabs
- [x] Masters mega-menu inventory
- [x] "New" menu (confirmed real difference from Bharatnet: both invoice types always shown)
- [x] Tenant Config record (vendor's own seed data, not customer's — notable finding)
- [x] Create Supplier Bill (Purchase Invoice) form
- [x] Create Sales Invoice form
- [x] Masters > Vendors (empty)
- [x] Payments list (filter-only, empty)
- [x] Receipts list (filter-only, empty)
- [ ] Masters > Items/Categories/Types (assumed identical/shared with CRM tenant data — 0 records — not re-screenshotted)
- [ ] Additional Charges master (only seen indirectly via a dropdown: "Shipping 18%", "Delivery 18%", "Handling 18%", "small cart charge" — real configured data, not opened directly as its own list page)
