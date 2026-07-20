# Navigation Map — Citylinenetworks Contracts (`http://citylinenetworks.infisuite.in/contracts`)

## System identification

- Front end: React SPA (create-react-app bundle, `main.9c67a11f.js`), client-side routed with React Router v6.
- Backend tenant identifiers observed in console/session logs: `company_id: 1`, `data_name: infisuitein_citylinenetworks`, `module_id: 40` (= "Contract" module), `user_id: 4`, `role_id: ["CONTRACT_ADMIN"]`.
- This is the same underlying Infisuite platform/template family as the `indyanet.infisuite.in` tenant documented separately (same page-name convention: `COM_LIST_CONTRACTS`, `COM_INSERT_CONTRACTS`, `COM_EDIT_CONTRACTS`, `COM_VIEW_CONTRACTS`), but a **different front-end build** — indyanet is server-rendered PHP (`index.php?option=...`), citylinenetworks is a React SPA (`/contracts/<verb>/<PAGE_NAME>`). Treated as a fully separate system per task instructions; no functionality assumed identical without direct observation here.
- Company/platform record in `companyConfig` (fetched at login) identifies the platform operator as "Infisuite" (Infisuite Services India Private Limited) — this is platform/vendor metadata, not the Citylinenetworks tenant's own business data.

## Login

- URL: `http://citylinenetworks.infisuite.in/contracts` → redirects to a Sign In form (Email + Password) when not authenticated.
- Credentials: `contract_admin@citylinenetworks.infisuite.in`.
  - Password `1234` (as given in the task) **failed**: `Invalid credentials. Please try again.` (API response `{error: 6002}`).
  - Fallback password `infi123` **succeeded**. **`infi123` is the working password for this tenant** — noted here per task instructions, not committed anywhere else.
- On success, lands on `/contracts/dashboard`.

## Top navigation bar (present on every authenticated page)

| Element | Behavior |
|---|---|
| Infisuite logo (link) | → `/contracts/dashboard` |
| Home icon (link) | → `/contracts/dashboard` |
| **Master** (dropdown button) | Opens a flyout: **Organization** section → **Document Categories**, **Document**. These are the only Master entries visible to the Contract Admin role. No Customers/Vendors master is exposed here despite the Dashboard showing "Total Customers"/"Total Vendors" counts (see `dashboard.md` — likely managed from the sibling CRM app, consistent with the indyanet tenant's Customer/Vendor master pattern, but **not confirmed** for this tenant since the master isn't reachable from this app). |
| **New** (dropdown button) | Opens a flyout with a single entry: **Contract**. Clicking it routes to `/contracts/list/COM_LIST_CONTRACTS` (i.e. "New Contract" actually lands on the Contracts *list*, matching the indyanet pattern where creation happens via a button on the list page — except here the list page is broken, see `contracts.md`). |
| Briefcase icon | Present, greyed out/disabled-looking; not exercised (no accessible name, no observed effect in this pass — gap). |
| Headset icon | Present, greyed out/disabled-looking; not exercised — gap. |
| **View CRM** (link) | SSO hop to `http://citylinenetworks.infisuite.in/login.php?infitokan=<base64 JSON token>` — the token decodes to a JSON payload containing the submit action, `passcheck: "N"`, and this user's email. Opens the sibling CRM/Billstack app (out of scope for this task; not followed). |
| **View Ticketings** (link) | Same SSO-token pattern, opens the sibling Ticketing app (out of scope; not followed). |
| Profile button ("CONTRACT ADMIN") | Dropdown with a single item: **Logout**. No "My Profile"/"Settings" entry observed. |
| Module switcher (rainbow/pinwheel icon, rightmost) | Dropdown listing all apps this tenant has provisioned: **Purchasing, Stores, CRM, Billstack, Contract**. All are `href="#"` JS-driven module switches (not plain links); not exercised beyond enumerating them — switching away from Contract is out of scope for this task. |

## Routes observed

All routes are under `/contracts/...` and are React-Router client routes — i.e. they only render correctly when navigated to *from within the app* (a fresh browser load / hard refresh of most of them redoes the login-bootstrap sequence, which works for CRUD pages backed by generic list/insert/edit/view components, see caveat below for the Contract entity specifically).

| Route pattern | Purpose |
|---|---|
| `/contracts` , `/contracts/dashboard` | Dashboard (home) |
| `/contracts/list/<PAGE_NAME>` | Generic list view, e.g. `COM_LIST_CONTRACTS`, `COM_LIST_DOCUMENT_CATEGORIES`, `COM_LIST_DOCUMENT_LISTS` |
| `/contracts/insert/<PAGE_NAME or table_name>` | Generic create form, e.g. `COM_INSERT_CONTRACTS`, `document_categories`, `document_lists` |
| `/contracts/edit/<table_name>/<id>` | Generic edit form, e.g. `document_categories/1`, `document_lists/1` |
| `/contracts/view/<table_name>/<id>` | Generic read-only detail view, e.g. `document_categories/1`, `document_lists/1` |

Note the inconsistency: the **Contract** entity's own routes use the `COM_*_CONTRACTS` page-name convention end-to-end (`COM_LIST_CONTRACTS`/`COM_INSERT_CONTRACTS`/`COM_EDIT_CONTRACTS`/`COM_VIEW_CONTRACTS`, confirmed via a `pageDetails` object logged to console: `{work_page: "list", application_table_name: "contracts", page: "custome_list_contracts", other: {id: 847, list_page_name: "COM_LIST_CONTRACTS", insert_page_name: "COM_INSERT_CONTRACTS", edit_page_name: "COM_EDIT_CONTRACTS", view_page_name: "COM_VIEW_CONTRACTS", ...}}`), while the **Master** entities (Document Categories, Document) use the plain DB table name (`document_categories`, `document_lists`) for insert/edit/view but the `COM_LIST_*` convention for list. Both conventions coexist in the same app build.

## Module structure

Only one real business module is exposed to this role: **Contract**. Its sub-areas, per the Master/New menus:
- **Contracts** (the core entity) — list/create/edit/view, all reachable but the list/create routes are **broken** for this tenant (see `contracts.md` for full bug writeup).
- **Master → Document Categories** — working CRUD, shared lookup table.
- **Master → Document** ("Document lists" internally) — working CRUD, shared lookup table, FK's to Document Categories.

Dashboard widgets (`dashboard.md`) reference Customers, Vendors, and an Expiring/Expired-by-date-bucket view of Contracts consistent with the indyanet tenant's Contract data model (Party Type Customer/Vendor, Effective/Expiration dates) — but none of that is independently confirmed here since the underlying list/detail pages could not be reached.

## Known access blocker (see `contracts.md` for full detail)

The Contracts list (`/contracts/list/COM_LIST_CONTRACTS`) and, by extension, contract creation (which is only reachable via that list page's "+" button in this app's pattern) **crash to a blank "No data available." page with no navigation chrome**, reproduced 3/3 times via both in-app navigation and direct URL load. Root cause (inferred from console errors): a `TypeError: Cannot read properties of null (reading 'application_table_name')` inside the list-page bootstrap code, most likely triggered because the `contracts` API returns `undefined`/null for this tenant's zero-contract data set rather than an empty array, and the SPA does not null-check before rendering. This makes the entire Contract entity's list/create/edit/view UI effectively unreachable for this account in this tenant — the only working, fully-explored pages in this module are the Dashboard and the two Master lookup tables.
