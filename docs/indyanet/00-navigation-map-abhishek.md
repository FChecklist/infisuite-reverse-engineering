# IndyaNet (Infistaq) — Navigation Map: abhishek@indyanet.com

System: https://indyanet.infisuite.in
Product name shown in UI: "Infisuite" (login page) / "Infistaq" (page `<title>` on every page, e.g. "Infistaq | Home"). Same shared multi-tenant "Infistaq" platform seen in the bharatnet CRM system (see `docs/crm-bharatnet/`), here provisioned for tenant **"IndyaNet"** (logo/brand text "IndyaNet — Fast Secure and Reliable" in the top-left, replacing the generic Infisuite logo used pre-login).

Logged in as: `abhishek@indyanet.com`, displayed as **"Admin"** (avatar initial "A"). Internal platform User Id 1, username `ADMIN_1` (see Masters > Users).

## 1. Login

Same login screen/flow as documented in `docs/crm-bharatnet/00-navigation-map.md`: User Name, Enter Password, a 4-digit numeric image CAPTCHA (read from screenshot), Forgot Password link, Sign In button. Screenshot: `screenshots/indyanet/00-login-page.png`.

On success, redirects to `/index` (title "Infistaq | Home").

## 2. Role / permission level (this account)

This is the **broadest-access of the three accounts** (see comparison in `SUMMARY.md`). Confirmed via the account's own Profile page (`index.php?option=COM_VIEW_PROFILE_APPLICATION_USERS`, tab "Overview"):

- **Modules** assigned: CRM, Contract, Purchase Management, Stores Management, Billstack (listed twice as "Contract" — likely a display dedup bug).
- **Roles** assigned: `CRM Admin`, `Contract Admin`, `Purchase Admin`, `Store Admin`, `Billstack Admin`, `Contract User` (the last one looks like a redundant/leftover role given `Contract Admin` is also present).
- Full Name field literally reads **"Indyanet"** (not a real person's name) with Display Name "Admin" — suggests this login is a shared/generic tenant-admin account rather than a personal account.
- Despite the "Admin" role labels, this account got a real **403 Forbidden** on both platform-level admin screens it has icon shortcuts to:
  - Display Attributes (`COM_LIST_DISPLAY_ATTRIBUTES`) → 403 "DISPLAY ATTRIBUTES NOT FOUND ... CONTACT YOUR ADMIN: NADIYA@BBTEL.IN"
  - Application Settings (`COM_LIST_APPLICATION_MENU_SETTINGS`) → same 403, same contact.
  - This reveals a **super-admin above all 3 test accounts**: `nadiya@bbtel.in`, not one of the three logins provided. Documented as observed, not fabricated.
  - Screenshots: `screenshots/indyanet/abhishek-display-attributes.png`, `screenshots/indyanet/abhishek-settings.png`.

So functionally: **tenant-wide admin across 5 business modules, but not a platform superuser** — settings/display-attribute configuration is reserved for a higher tier.

## 3. Global chrome (present on every authenticated page)

Top bar, left to right:

1. **Logo "IndyaNet"** → `index.php` (Home).
2. **Icon: circular abstract/fingerprint glyph** — this is actually the **Modules switcher** in disguise (see below); clicking it opens a "Modules" panel.
3. **"Masters" nav item** (green pill button, not a top-bar icon) — mega-menu, see §4.
4. **"New" nav item** (green pill button) — single-entry menu: **Contract** → `index.php?option=COM_LIST_CONTRACTS` (label is misleading — it routes to the Contract *list*, not directly to the insert form; likely intended as the fast-path to Contracts).
5. Right-side icon bar:
   - **Notebook/tablet icon** (`ti-tablet`) → `index.php?option=COM_LIST_DISPLAY_ATTRIBUTES` — **403 for this account** (see §2).
   - **Gear icon** (`ti-settings`) → `index.php?option=COM_LIST_APPLICATION_MENU_SETTINGS` — **403 for this account** (see §2).
   - **ID-card icon** → cross-domain SSO link to `https://demo.tickday.com/login.php?infitokan=<base64(urlencode(json))>` where the decoded token is `{"submit":"Login","passcheck":"N","email_id":"abhishek@indyanet.com"}`. "Tickday" is a distinct external product (time/attendance or ticketing SaaS, different host) — **out of scope**, not followed, only the SSO mechanism noted.
   - **Briefcase icon** → same SSO pattern to `http://indyanet.infisuite.in//contracts?infitokan=...` — a *same-host* `/contracts` path outside the `index.php?option=` router. Not explored (redundant with the in-app Contract module already covered; likely a legacy/alternate front-end for the same data).
   - **Megaphone icon** → `index.php?option=COM_VIEW_ANNOUNCEMENTS` — company announcements (see `announcements.md`... not written separately; see notes below). 0 announcements on this tenant.
   - **Bell icon** (badge "8") → notification slide-out panel (`getNotificationList()`), not explored in depth — badge shows 8 unread but panel contents not captured in this pass (gap).
   - **Headset icon** → `index.php?option=COM_VIEW_PROJECT_TICKETS_DASHBOARD` — Ticketing dashboard entry point, **out of scope** (separate infisuite system), not explored further, consistent with bharatnet CRM precedent.
   - **Avatar "A" / "Admin" dropdown** → **Profile**, **My Messages**, **Logout**.
   - **Colored dot-pattern icon** → **Modules** switcher panel: shows current role as a text label ("Contract Admin" while in the Contract module) plus 5 module tiles: **Purchasing**, **Stores**, **CRM**, **Billstack**, **Contract** (current, checked). Switching module is a client-side JS call `chnageModuleWithRole(<base64 id>, <ROLE_CODE>)` that reloads `/index` with a different dashboard/menu set for the same login session — not a re-login. Screenshot: `screenshots/indyanet/abhishek-modules-dropdown.png`.

## 4. Masters mega-menu (click "Masters")

4 column groups (all present in DOM at once, matches bharatnet's mega-menu pattern):

| Group | Item | URL |
|---|---|---|
| Organization | Document Categories | `index.php?option=COM_LIST_DOCUMENT_CATEGORIES` |
| Organization | Document | `index.php?option=COM_LIST_DOCUMENT_LISTS` |
| Vendors | Vendors | `index.php?option=COM_LIST_VENDORS` |
| Vendors | Invite Vendors | `index.php?option=COM_LIST_VENDOR_INVITATIONS` |
| Users | Users | `index.php?option=COM_LIST_APPLICATION_USERS` |
| Users | Invite Users | `index.php?option=COM_LIST_APPLICATION_USER_INVITATIONS` |
| Customers | Customer | `index.php?option=COM_LIST_CUSTOMERS` |

## 5. "New" menu

- **Contract** → `index.php?option=COM_LIST_CONTRACTS` (see note in §3.4 — routes to list, not insert; the actual create form is `COM_INSERT_CONTRACTS`, reached instead via the green **+ Contract** button on the Contract list page).

## 6. Contract module home (default landing page, `index`/`index.php`)

This is what "IndyaNet" the tenant is primarily configured for — the account lands here immediately after login, and it is the module the "New" menu and top-level nav are built around. Dashboard widgets (all real, non-empty data on this tenant):

- 4 stat tiles: **Contracts** (Total Contracts, currently 0 active-only count), **Active** (Total Active Contracts), **Expired** (Total Expired Contracts), **Total Customers** (11), **Total Vendors** (2) — 5 tiles total, horizontally scrollable row.
- **Monthly Overview** bar chart (contracts created per month).
- **Customers / Vendors** tab toggle controlling the two panels below.
- **Customer Contracts** table (columns: #, Contract, Start Date, End Date, Contract ID, RT Number, Invoice Number, Progress) with a **View all** link → `index?option=COM_LIST_CONTRACTS`.
- **Expiring Contracts** — 4 sub-tabs (Expires in 7 Days / 30 Days / 31-60 Days / 61-90 Days), each a small table (Contract, Days Left, End Date).
- **Expired Contracts** — 4 sub-tabs (Expired last 7/30/60/90 Days), each showing a count and a table (Contract, Days Expired, End Date).

Screenshot: `screenshots/indyanet/abhishek-01-dashboard-home.png`.

Full module documentation: see `docs/indyanet/contracts.md`.

## 7. Other modules available via the Modules switcher (briefly noted, not deep-documented)

Per task scope, CRM and Billstack are separate infisuite systems documented elsewhere (`docs/crm-bharatnet/` for CRM at a different tenant; Billstack out of scope entirely, same as it was for the CRM task). Purchasing and Stores are *not* documented elsewhere but are treated as secondary to this task's focus (the Contract module, which is what "indyanet.infisuite.in" defaults to and is branded around). Light notes only:

- **Purchasing** (role `Purchase Admin`): dashboard with Quotations / Purchase Orders / Supplier Bills stat tiles, an Item Categories tree (real vendor/brand names: DBC Technologies, DR.COM, MIKROTIK, NETLINK, OPTICAL FIBER, SYROTECH, TPLINK, WOW Broadband — all networking/ISP equipment brands, confirming IndyaNet's line of business is network/bandwidth infrastructure), Approved Requisitions tab, a right-rail Draft/Active/In Process/Approved status tracker. Screenshot: `screenshots/indyanet/abhishek-module-purchasing-home.png`.
- **Stores** (role `Store Admin`): dashboard with GRN / GRN Returns / Stock Request / Stock Transfer / ST Receipts / ST Returns / Stock Adjustment stat tiles, same Item Categories tree as Purchasing (shared item master across modules), same right-rail status tracker pattern. Screenshot: `screenshots/indyanet/abhishek-module-stores-home.png`.
- **CRM**: not explored on this tenant (would duplicate `docs/crm-bharatnet/` structure on different data) — gap, explicitly out of scope by task framing.
- **Billstack**: not explored — out of scope, consistent with bharatnet CRM task precedent.

## 8. Profile / My Messages / Logout (avatar dropdown)

- **Profile** → `index.php?option=COM_VIEW_PROFILE_APPLICATION_USERS`. Rich page: cover-image header, avatar, Active/Inactive badge, 8 role-context icons (HR Connect, Finance, Admin, Project, Location, ESOP, Legal, Approval Manager — all showing "-", i.e. unpopulated on this account), tabs **Overview / Notification / Approval Transactions / User Activity / Reset Password / Mobile OTP**, an **Invite User** shortcut button. Overview tab shows Basic Info (Full Name, Display Name, Contact, Email, Modules, Roles — see §2), a location/warehouse/country/state/timezone/currency block, and 6 empty "No X" sections (Cost Centers, Departments, Divisions, Locations, Warehouse, Bank Accounts, Assets) each with the same illustrated empty-state graphic used platform-wide. Screenshot: `screenshots/indyanet/abhishek-profile.png`.
- **My Messages** → `index.php?option=COM_LIST_INBOX` — not explored in this pass (gap).
- **Logout** → client-side link (`#!`), not exercised (would end the session needed for continued exploration).

## 9. Full checklist (this account)

- [x] Login + captcha
- [x] Contract module home dashboard
- [x] Contract list (`COM_LIST_CONTRACTS`) — full column set, Active/Inactive/All filter, Import/Export/Status/+Contract/Back, real paginated data (11 pages × 20 rows)
- [x] Contract detail/edit form, all 6 tabs (Contract Details, Documents, Workflow, Associated Contract, Permissions, Activities)
- [x] Recharge Plans modal (plan options: Monthly/Quarterly/Half Yearly/Yearly) and Recharge History modal (empty state, column set captured)
- [x] Masters > Document Categories (5 categories, real data)
- [x] Masters > Document (checklist-of-required-docs master, real data, 2 pages)
- [x] Masters > Vendors (2 real vendors, card layout) — **not yet**: New Vendor create form fields (gap)
- [x] Masters > Invite Vendors (empty state)
- [x] Masters > Users (10 real platform users, full table incl. all 3 test accounts' rows)
- [x] Masters > Invite Users (7 real invitation cards)
- [x] Masters > Customer list (11 real customers, card layout) + Customer create form (all 8 tabs: Overview, Basic Info, Contact Details, Note*, Tax Info*, Payment and Billing*, Attachments*, Rental Properties — tabs marked * not screenshotted individually, gap)
- [x] Display Attributes icon — 403 (documented as real permission boundary)
- [x] Application Settings icon — 403 (documented as real permission boundary)
- [x] Announcements icon (empty state)
- [ ] Notifications bell panel contents (badge shows 8, panel not opened — gap)
- [x] Ticket Dashboard entry point noted — out of scope, not explored further
- [x] Modules switcher — all 5 tiles identified; Purchasing/Stores lightly explored; CRM/Billstack explicitly out of scope
- [x] Profile page (all identity/role data extracted); Notification/Approval Transactions/User Activity/Reset Password/Mobile OTP sub-tabs **not individually explored** (gap)
- [ ] My Messages inbox — gap
- [x] Cross-app SSO icons (Tickday, /contracts) identified and decoded, not followed (out of scope / redundant)
