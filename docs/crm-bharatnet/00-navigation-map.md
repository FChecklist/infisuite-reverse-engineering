# CRM (Bharatnet / Infistaq) — Navigation Map

System: https://bharatnet.infisuite.in
Product name shown in UI: "Infisuite" (login page) / "Infistaq" (page `<title>` on every page, e.g. "Infistaq | Home"). The vendor/platform appears to be a single shared multi-tenant business platform ("Infistaq") that hosts several modules ("Modules" switcher — see below) for the tenant "Bharatnet". CRM is one of at least two modules available to this login (the other being "Billstack" — out of scope for this task).

Logged in as: `crm_admin@bharatnet.infisuite.in`, displayed as "CRM Admin" (avatar initials "CA").

This file is the master checklist. It was built by (1) logging in, (2) opening every top-bar icon/dropdown once, (3) opening the "Masters" mega-menu, (4) opening the gear-icon "Application Settings" screen and extracting its full accordion tree via one JS query (all 23 categories + ~55 sub-items are present in the DOM at once, not lazy-loaded — see settings.md). Deep per-module documentation lives in the other files in this folder.

## 1. Login

- URL: `https://bharatnet.infisuite.in` → redirects to `/login.php`, page title "Infistaq | Login".
- Form fields: **User Name** (text), **Enter Password** (password, with show/hide eye toggle), **Enter Captcha** (text — a 4-digit numeric image captcha is rendered above the field and must be read/typed manually), **Forgot Password?** (link, opens a modal with "Enter your email to reset your password" + Submit/Cancel — not exercised, would send a real email), **Sign In** button.
- There is also a "Sign Up" toggle visible in the DOM text dump ("Use 8 or more characters with a mix of letters, numbers & symbols", "Sign Up", "Already A Member" / "Sign In") — a self-registration flow exists on this same screen (not exercised — would create a real account).
- On success, redirects to `/index` (page title "Infistaq | Home"). Captcha is purely client-rendered text-in-image (not obfuscated beyond a dot-pattern background) and was solved by reading the screenshot.
- Screenshot: `00-login-page.png`

## 2. Global chrome (present on every authenticated page)

Top bar, left to right:

1. **Logo** (links to `index.php`, i.e. Home).
2. **Icon: black circle "robot" glyph** → `index.php?option=COM_LIST_DISPLAY_ATTRIBUTES` — "Display Attributes" admin table. See `display-attributes.md`.
3. **Icon: gear** → `index.php?option=COM_LIST_APPLICATION_MENU_SETTINGS` — "Application Settings", a huge platform-wide settings area with 23 top-level categories / ~55 sub-pages. See `settings.md`.
4. **Icon: megaphone** → `index.php?option=COM_VIEW_ANNOUNCEMENTS` — company announcements feed.
5. **Icon: bell (badge shows unread count, "0")** → slide-out panel, not a page nav. Tabs: **Action**, **Information**, **Logs**; a "Search a notification" box; body said "All caught up! No new notifications." on this tenant.
6. **Icon: headset** → `index.php?option=COM_VIEW_PROJECT_TICKETS_DASHBOARD` — a Ticketing/Support dashboard. **Out of scope** per task instructions (Ticketing is a separate system at citylinenetworks.infisuite.in); only the entry point was noted, not explored further.
7. **Avatar + "CRM Admin" dropdown** → **Profile**, **My Messages**, **Logout**.
8. **Icon: colored dot pattern ("Modules")** → dropdown panel titled "Modules" with a "CRM Admin" sub-dropdown and two module tiles: **CRM** (currently selected, orange checkmark) and **Billstack**. This is the tenant-level app switcher. **Billstack is out of scope** for this task; only its existence was noted.

Second row (below the icon bar):

- **Home** (logo link, same as #1).
- **Masters** — mega-menu (click, not hover) with 3 column groups:
  - **Items** → `index.php?option=COM_LIST_ITEMS`
  - **Customers** → `index.php?option=COM_LIST_CUSTOMERS`
  - **Users** → `index.php?option=COM_LIST_APPLICATION_USERS`
  - **Invite Users** → `index.php?option=COM_LIST_APPLICATION_USER_INVITATIONS`

## 3. CRM Home page (`index` / `index.php`)

Layout: profile summary card (avatar, name, email, "Today's Leads / Today's Contacts / Today's Deals" counters, then a row of 6 lifetime counters: Total leads / Total Contacts / Total Companies / Total Campaigns / Total deals / Total Projects), then a **tab strip** that is the real primary navigation for the CRM module itself:

**Home | Leads | Contacts | Companies | Deals | Campaign | Projects | Feasibility**

...plus a standalone button to the right of the tab strip: **+ Lead/Contact Assignings** → `index.php?option=COM_VIEW_CRM_LEAD_AND_CONTACT_ASSIGNINGS`.

Note: in the accessibility tree every tab is flagged `[selected]` simultaneously — this looks like an ARIA-attribute bug in the tab widget markup (all tabs get `aria-selected="true"`), not multiple real selections; only one tab's panel is visibly active at a time. Documented as observed, since it's a genuine UI defect.

The **Home** tab panel is a dashboard of widgets (all reading "0" / "No Data" / "No Records" on this tenant — the tenant currently has zero Leads, Contacts, Companies, Deals, Campaigns and Projects):

- Lead Status (chart placeholder)
- Recently Created Leads (+ "View all" link)
- Sales Order Not Yet Generated
- Recently Created Contacts (+ "View all" link)
- Contact Status (chart placeholder)
- Pending Tasks
- Feasibilities (sub-tabs: Expired / 15-Days / 30-Days / 45-Days, each a counter badge)
- Pending BOM Returns (Feasibility Closed) — a table (Feasibility Title / Item Name / Planned Return By / Actual Return By / Action)

Screenshot: `01-dashboard-home.png`

Each of the other 7 tabs (Leads, Contacts, Companies, Deals, Campaign, Projects, Feasibility) is a full CRM module in its own right — documented individually in `leads.md`, `contacts.md`, `companies.md`, `deals.md`, `campaign.md`, `projects.md`, `feasibility.md`.

## 4. Full checklist (this task's scope)

- [x] Login + captcha
- [x] Home dashboard
- [ ] Leads tab
- [ ] Contacts tab
- [ ] Companies tab
- [ ] Deals tab
- [ ] Campaign tab
- [ ] Projects tab
- [ ] Feasibility tab
- [ ] Lead/Contact Assignings
- [ ] Masters > Items
- [ ] Masters > Customers
- [ ] Masters > Users
- [ ] Masters > Invite Users
- [ ] Display Attributes (icon #2)
- [ ] Announcements (icon #4)
- [x] Notifications panel (icon #5) — trivial, documented above, empty state only
- [x] Ticket Dashboard entry point noted (icon #6) — **out of scope**, not explored further
- [x] Profile dropdown menu items noted (Profile / My Messages / Logout) — not opened further yet
- [x] Modules switcher noted (CRM / Billstack) — **Billstack out of scope**
- [ ] Application Settings (gear icon) — 23 categories, ~55 sub-pages, see `settings.md`

## 5. Settings tree (gear icon → Application Settings), full inventory

Extracted directly from the DOM (all categories/links exist in the page at once as a Bootstrap-style vertical tab/accordion; nothing here required guessing). Full URLs are `https://bharatnet.infisuite.in/index?option=COM_LIST_APPLICATION_MENU_SETTINGS&PI=<n>&PAGENAME=<PAGE>`.

| Category | Sub-item(s) | PAGENAME |
|---|---|---|
| ACCOUNT SETTING | GENERAL SETTINGS | COM_LIST_CONFIG |
| ORGANIZATION | DIVISION | COM_LIST_DIVISIONS |
| ORGANIZATION | DEPARTMENTS | COM_LIST_DEPARTMENTS |
| ORGANIZATION | COST CENTERS | COM_LIST_COST_CENTERS |
| ORGANIZATION | LOCATIONS | COM_LIST_LOCATIONS |
| ORGANIZATION | WAREHOUSES | COM_LIST_WAREHOUSES |
| ORGANIZATION | VERTICALS | COM_LIST_VERTICALS |
| WORK FROM HOME SETTING | WORK FROM HOME SETTING | COM_LIST_WORK_FROM_HOME_SETTING |
| WORK FROM HOME SETTING | REQUEST FOR WORK FROM HOME | COM_INSERT_REQUEST_WORK_FROM_HOME |
| GEOGRAPHY | COUNTRIES | COM_LIST_COUNTRIES |
| GEOGRAPHY | STATES | COM_LIST_STATES |
| GEOGRAPHY | CITIES | COM_LIST_CITIES |
| HOLIDAYS | HOLIDAYS | COM_LIST_HOLIDAYS |
| ANNOUNCEMENTS | ANNOUNCEMENT CATEGORIES | COM_LIST_ANNOUNCEMENT_CATEGORIES |
| ANNOUNCEMENTS | COMPANY ANNOUNCEMENTS | COM_LIST_COMPANY_ANNOUNCEMENTS |
| USER MANAGEMENT | INVITE USER | COM_LIST_MULTI_APPLICATION_USER_INVITATIONS |
| USER MANAGEMENT | ADD USER | COM_INSERT_APPLICATION_USERS |
| USER MANAGEMENT | LIST USERS | COM_LIST_APPLICATION_USERS |
| ITEMS | ITEMS | COM_LIST_ITEMS |
| ITEMS | ITEMS CATEGORIES | COM_LIST_ITEM_CATEGORIES |
| ITEMS | UOM | COM_LIST_UOM |
| OTHERS | HSN AND SAC CODES | COM_LIST_HSN_AND_SAC_CODES |
| OTHERS | CURRENCIES | COM_LIST_CURRENCIES |
| OTHERS | CURRENCY CONVERTORS | COM_LIST_CURRENCY_CONVERTOR |
| OTHERS | COMPLAINTS | COM_LIST_COMPLAINTS |
| OTHERS | LOCATION | COM_INSERT_LOCATIONS_NEW |
| OTHERS | USER MOBILE REGISTRATIONS | COM_LIST_MOBILE_REGISTERED |
| OTHERS | MOBILE REGISTRATIONS | COM_LIST_MOBILE_USER_REGISTRATIONS |
| VENDOR MANAGEMENTS | INVITE VENDORS | COM_LIST_VENDOR_INVITATIONS |
| VENDOR MANAGEMENTS | ADD VENDOR | COM_INSERT_VENDOR_REGISTERS |
| VENDOR MANAGEMENTS | LIST VENDORS | COM_LIST_GRID_VENDORS |
| CUSTOMER MANAGEMENTS | INVITE CUSTOMER | (no PAGENAME) |
| CUSTOMER MANAGEMENTS | ADD CUSTOMER | COM_INSERT_CUSTOMERS |
| CUSTOMER MANAGEMENTS | QUALITY CONTROL | (no PAGENAME) |
| CUSTOMER MANAGEMENTS | LIST CUSTOMERS | COM_LIST_CUSTOMERS |
| MODULE MANAGEMENTS | MODULE SETUP | COM_LIST_MODULE_SETUP |
| BUILDING SPACES | BUILDING SPACES | COM_LIST_BUILDINGS |
| PROCESS PAGES MANAGEMENTS | PROCESS | COM_LIST_APPLICATION_PROCESS |
| PROCESS PAGES MANAGEMENTS | PROCESS PAGES | COM_LIST_APPLICATION_PROCESS_PAGES |
| PROCESS PAGES MANAGEMENTS | DYNAMIC PROCESS | COM_INSERT_APPLICATION_PROCESS |
| ROLES MANAGEMENT | ROLES | COM_INSERT_APPLICATION_ROLE |
| API | API MASTER | COM_LIST_API_MASTERS |
| API | API HITS | COM_LIST_API_HITS |
| FROM 16A DETAILS | FROM 16A DETAILS | COM_LIST_EMP_TAX_DEPOSIT_RESPECT |
| FROM 16A DETAILS | ASSIGN RELEASE DOCUMENTS | COM_LIST_EMP_ASSIGN_RELEASE_DOCUMENTS |
| EMAIL | TEMPLATES | COM_LIST_EMAIL_CONTENT |
| EMAIL | GROUPS | COM_LIST_EMAIL_GROUPS |
| WORKFLOW SETTING | WORKFLOW SETTING | COM_LIST_WORKFLOW_CONFIGURATION |
| WORKFLOW SETTING | WORKFLOW SETTING-1.1 | COM_LIST_WORKFLOW_CONFIGURATIONS_NEW |
| MACHINE AND MATERIAL | *(category present, no sub-items rendered)* | — |
| TASKS | HIRE AND RETIRE | COM_LIST_PROCESS_TASK_SETTINGS |
| TASKS | PROJECT TASKS | COM_LIST_PROJECT_TASKS |
| APPLICATION MISSING DATA CHECK | APPLICATION MISSING DATA CHECK | COM_LIST_APPLICATION_MISSING_DATA_CHECK |
| FIELD MAP | WORK PLANS | COM_LIST_FIELD_WORK_DAILY_PLANS |
| FIELD MAP | USER WORK DETAILS | COM_LIST_FIELD_WORK_USER_MAP_POINTS |
| FIELD MAP | VIEW MAP | COM_VIEW_MAP_POINTS |

Observation (inferred, not directly confirmed): this Settings area is clearly **platform-wide**, not CRM-specific — it also covers HR (Work From Home, Holidays, Hire and Retire, Form 16A tax docs), manufacturing/inventory (Warehouses, Machine and Material, HSN/SAC codes, BOM-adjacent "Feasibility" concepts), vendor procurement, and field service (Field Map/GPS). This confirms the "Infistaq" platform is a shared multi-module ERP and CRM is a thin vertical slice of it. Given task scope, `settings.md` documents every category at least at a glance and goes deeper only on the sub-set that is clearly load-bearing for CRM data (Geography, Items, Customer Managements, Workflow Setting, Email, Roles, General Settings, API).

## 6. Known structural notes carried into all module docs

- Every list view so far follows the same template: **Active/Inactive/All** radio filter, **Records** page-size dropdown (default 20) + **Search** box ("Min 3 Characters Required"), a **Status** dropdown-button, **Import**/**Export** buttons, a green **+ <Entity>** create button, a **Back** button, and a small gear icon (per-column display-attribute config, ties back to Display Attributes). Table rows end with an **Action** cell of icon buttons (edit pencil, copy/duplicate, view eye) — exact set varies per module.
- This tenant's CRM data is **entirely empty** (0 records everywhere observed so far). This materially limits how much can be learned from *detail* views (there may be nothing to open) — this will be called out per-module as a gap rather than invented.
