# IndyaNet (Infistaq) — Navigation Map: aitad@bbtel.in

System: https://indyanet.infisuite.in (same shared "Infistaq" platform / "IndyaNet" tenant as `00-navigation-map-abhishek.md`).

Logged in as: `aitad@bbtel.in`, displayed as **"Aitad Sharma"** (avatar photo present, unlike abhishek's initials-only avatar). Platform User Id 4, username `aitad sharma` (Masters > Users row 4, confirmed from abhishek's session).

## 1. Role / permission level (this account)

Confirmed via this account's own Profile page (`COM_VIEW_PROFILE_APPLICATION_USERS`):

- **Modules** assigned: CRM, Purchase Management, Billstack, Stores Management — **no Contract module** (unlike abhishek).
- **Roles** assigned: `CRM` (not "CRM Admin" — a plainer/lesser label), `Purchase Admin`, `Billstack Admin`, `Store Admin`. Confirmed at the JS level: the Modules-switcher tile for CRM calls `chnageModuleWithRole('Mjk=','CRM_USER')` for this account, vs. `CRM_ADMIN` for abhishek — i.e. this account is a **CRM_USER**, not CRM admin, a genuinely lower permission tier within the CRM module specifically.
- Default landing module after login is **Billstack**, not Contract — the platform remembers/defaults to a per-account "last/primary" module rather than a single fixed home page. Screenshot: `screenshots/indyanet/aitad-01-dashboard-home.png`.
- Full Name / Display Name both read "aitad sharma" (a real-looking personal name, unlike abhishek's tenant-generic "Indyanet"/"Admin").

### The single most important finding of this task: a real permission inversion

Despite having **no** "Admin"-labelled role beyond individual modules, this account has **full access** to both platform-level admin screens that returned a 403 for abhishek (who is nominally "Admin" and holds `Contract Admin`/`CRM Admin`/`Purchase Admin`/`Store Admin`/`Billstack Admin` simultaneously):

- **Display Attributes** (`COM_LIST_DISPLAY_ATTRIBUTES`) — **works**, shows a live paginated table of the application's internal database table names (e.g. `accountsection`, `account_groups`, `account_type`, `action_email_settings`, `action_log`, ...), each with Active/Inactive status and an edit action. Screenshot: `screenshots/indyanet/aitad-display-attributes.png`.
- **Application Settings** (`COM_LIST_APPLICATION_MENU_SETTINGS`) — **works**, full settings tree (Account Setting, Organization, Work From Home Setting, Geography, Holidays, Announcements, User Management, Items, Others, Vendor Managements, Customer Managements, Module Managements, ...same category shape as the bharatnet CRM tenant's settings tree). The "Account Setting > General Settings > Config" screen reveals the tenant's registered admin contact: **Name "Indyanet", Admin Email `nadiya@bbtel.in`, Phone `8045461999`** — this is the same `nadiya@bbtel.in` address abhishek's 403 pages told him to contact, now directly confirmed as the tenant's actual registered admin/support contact via a legitimate settings screen. Screenshot: `screenshots/indyanet/aitad-settings.png`.

This means platform-admin-tier access (Settings/Display Attributes) is **not** gated by the per-module "Admin" role labels visible on the Profile page — it's a separate, hidden permission not surfaced anywhere in the UI's own role chips. A tester relying on the visible role labels alone would have wrongly assumed abhishek (all-modules "Admin") was the most powerful account; aitad, "just" a CRM_USER/Purchase/Store/Billstack Admin, is actually more powerful in this one specific, high-value dimension.

## 2. Global chrome differences vs. abhishek

- Same icon set and positions (fingerprint/Modules icon, tablet/Display-Attributes, gear/Settings, ID-card/Tickday SSO, briefcase/contracts SSO, megaphone/Announcements, bell/Notifications, headset/Tickets, avatar dropdown, colored-dot/Modules).
- **Bell notification badge shows "0"** for this account (vs. "8" for abhishek) — per-user unread count, not shared tenant-wide state.
- Avatar shows a **real photo**, not initials — this account has uploaded a profile picture; abhishek's has not.
- **Modules switcher panel**: same 5 tiles (Purchasing, Stores, CRM, Billstack, Contract) are visually present, but the **Contract tile has no `onclick` handler** for this account (confirmed via DOM inspection) — it is inert/decorative, consistent with this account having no Contract role. The other 4 tiles are fully functional and switch the dashboard exactly as they do for abhishek. Screenshot: `screenshots/indyanet/aitad-modules-dropdown.png`.

## 3. Masters mega-menu — completely different item set (module-context-dependent)

Unlike abhishek's Contract-flavored Masters menu (Document Categories, Document, Vendors, Invite Vendors, Users, Invite Users, Customer), aitad's Masters menu — while logged into the **Billstack** module — shows:

| Item | URL |
|---|---|
| Division | `index.php?option=COM_LIST_DIVISIONS` |
| Departments | `index.php?option=COM_LIST_DEPARTMENTS` |
| Cost Centres | `index.php?option=COM_LIST_COST_CENTERS` |
| Locations | `index.php?option=COM_LIST_LOCATIONS` |
| Item Categories | `index.php?option=COM_LIST_ITEM_CATEGORIES` |
| Items Type | `index.php?option=COM_LIST_ITEM_TYPES` |
| Items | `index.php?option=COM_LIST_ITEMS` |
| HSN Code | `index.php?option=COM_LIST_HSN_AND_SAC_CODES` |
| Currencies | `index.php?option=COM_LIST_CURRENCIES` |
| Workflow | `index.php?option=COM_LIST_WORKFLOW_CONFIGURATIONS_NEW` |
| Vendors | `index.php?option=COM_LIST_VENDORS` |
| Invite Users | `index.php?option=COM_LIST_APPLICATION_USER_INVITATIONS` |

This confirms the Masters mega-menu's contents are **driven by the currently-active module**, not fixed per account — the same account would presumably see the Contract-flavored menu too, if it had a Contract role to switch into (it doesn't). Not independently re-verified by switching aitad into Purchasing/Stores/CRM to see 3 more menu variants — **gap**.

## 4. "New" menu (Billstack context)

- **Sales Invoices** → `index.php?option=COM_LIST_SALES_INVOICES`
- **Purchase Invoices** → `index.php?option=COM_LIST_SUPPLIER_BILLS`

(Different from abhishek's Contract-context "New" menu, which had a single "Contract" item — again, module-dependent.)

## 5. Billstack module home (default landing page for this account)

Two top-level tabs: **Vendar Bills** [sic, typo for "Vendor"] (default) and **Customer Bills**. Hero banner: *"Start managing supplier invoices with ease! Helps you in managing: Suppliers, Supplier bills and Payments. BillsStack does this all through your modern Web Browser..."* with **Create Invoice** (→ `COM_INSERT_SUPPLIER_BILLS`) and **Payments** (→ `COM_LIST_PAYMENTS`) buttons.

Stat row: **2 Vendors** (Vendors Who Are Active, → Vendors list), **0 Invoices** (Supplier Invoices Created, → Supplier Bills list), **0 Invoices** (Supplier Invoices Approved, → `COM_LIST_PURCHASE_INVOICES_TABLE`).

Widgets: **Vendor Invoice/Payments** (year-picker 2016–2026 + a Bills-vs-Paid line/area chart, amCharts-powered, all-zero on this tenant), **Vendor Invoice** (donut gauge "0%" + Total Invoices/Paid/Payment Due/Yet To Due counters), **Approval Pending** table (#, Number, Request Date, Transaction Date, Price — empty), **Approved** table (same shape — empty), **TOP 3 VENDORS** (empty, "No Vendors" — inconsistent with the "2 Vendors" stat tile above, a real data-inconsistency worth flagging same as the Contract module's Expiring-widget discrepancy noted in `contracts.md`), **Recent Vendors** (also empty), and an **Approved/Un-Approved Supplier invoices** tab+table (columns: #, Vendor, Invoices Number, Date, Price, Status, Action — empty, "No Bills Created").

The **Customer Bills** tab (not fully explored — gap) mirrors this with Sales Invoices instead of Supplier Bills, an **Upload Invoices** (→ `COM_INSERT_SALES_INVOICE_DYNAMIC`) and **Create Invoice** (→ `COM_INSERT_SALES_INVOICES`) button pair, a **Receipts** link (→ `COM_LIST_RECEIPTS`), and an "11 Customers" stat tile matching the same Customer master used by the Contract module (confirms Customer is shared platform-wide, consistent with `masters.md` §7's conclusion from the Contract-module side).

Billstack is explicitly **out of scope for deep documentation** per task framing (a distinct infisuite-labeled system, same as CRM) — this section is a navigation-level overview only, not full field-by-field documentation.

## 6. Full checklist (this account)

- [x] Login + captcha
- [x] Billstack module home dashboard (both stat/chart widgets and the empty-state inconsistency noted)
- [x] Profile page — Modules/Roles extracted, confirms CRM_USER (not admin) + no Contract module
- [x] Display Attributes — **accessible** (unlike abhishek), screenshot + structure noted
- [x] Application Settings — **accessible** (unlike abhishek), full category tree visible, tenant admin contact confirmed (nadiya@bbtel.in / 8045461999)
- [x] Masters mega-menu (Billstack-context item set) — fully enumerated
- [x] "New" menu (Billstack-context) — fully enumerated
- [x] Modules switcher — same 5 tiles, Contract tile confirmed inert (no onclick handler) for this account
- [ ] Customer Bills tab not screenshotted/explored in depth — gap
- [x] CRM module dashboard checked for this CRM_USER account — **confirmed materially reduced chrome**: switching into CRM drops the **Masters** and **New** pill-buttons from the top nav entirely (present for abhishek's CRM_ADMIN elsewhere, per the bharatnet precedent), and the right-side icon bar shrinks from 10 icons to 5 (only ID-card SSO, briefcase SSO, megaphone/Announcements, bell/Notifications, avatar remain — Display Attributes and Settings gear icons are both gone, not just 403ing, they don't render at all). The CRM Home dashboard itself is structurally identical to bharatnet's CRM Home (Lead Status/Recently Created Leads/Sales Order Not Yet Generated/Recently Created Contacts/Contact Status/Pending Tasks/Feasibilities/Pending BOM Returns widgets), but **this tenant has real, non-zero Feasibility data**: two "Pending BOM Returns" rows ("Abhishek Mishra - Broadband", "Sanjay Rathod - Broadband", both item "box") — confirming "Abhishek Mishra" (also a Customer-master record, see `masters.md` §7) is a real CRM Feasibility subject on this tenant, and that CRM Feasibility rows are directly editable in-place (an "Update" button per row, Planned/Actual Return By date fields) — not exercised further (would edit a real record). Screenshot: `screenshots/indyanet/aitad-module-crm-home.png`.
- [ ] Purchasing/Stores module dashboards and Masters-menu variants not re-checked from this account specifically (assumed equivalent to abhishek's light pass given identical role labels Purchase Admin/Store Admin) — gap
- [ ] CRM's own Masters-equivalent submenu (if any, given the top Masters button disappears for CRM_USER) not explored — gap
