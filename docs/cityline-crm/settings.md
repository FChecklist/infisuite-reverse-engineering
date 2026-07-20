# Application Settings (gear icon)

## 1. Purpose
Platform-wide settings area (not CRM-specific) covering org structure, HR, geography, items, vendors, customers, workflow, roles/permissions, email, API, and field-service config. Same purpose and (per §5 below) **near-identical category/page-ID tree** to Bharatnet's `settings.md`.

## 2. Navigation path
Top nav icon (gear, 3rd icon) → `index.php?option=COM_LIST_APPLICATION_MENU_SETTINGS`.

## 3. UI / page design
Left: vertical accordion of ~20 categories. Right: the selected sub-page. Screenshot: `25-settings.png`.

## 4. Full inventory (extracted via DOM query of `a[href*=PAGENAME]`, same methodology as Bharatnet)

| Category (inferred grouping) | Sub-item | URL `PAGENAME` | `PI` |
|---|---|---|---|
| Account Setting | General Settings | `COM_LIST_CONFIG` | 2 |
| Organization | Division | `COM_LIST_DIVISIONS` | 4 |
| Organization | Departments | `COM_LIST_DEPARTMENTS` | 5 |
| Organization | Cost Centers | `COM_LIST_COST_CENTERS` | 6 |
| Organization | Locations | `COM_LIST_LOCATIONS` | 7 |
| Organization | Warehouses | `COM_LIST_WAREHOUSES` | 8 |
| Organization | Verticals | `COM_LIST_VERTICALS` | 9 |
| Work From Home Setting | Work From Home Setting | `COM_LIST_WORK_FROM_HOME_SETTING` | 18 |
| Work From Home Setting | Request For Work From Home | `COM_INSERT_REQUEST_WORK_FROM_HOME` | 19 |
| Geography | Countries | `COM_LIST_COUNTRIES` | 21 |
| Geography | States | `COM_LIST_STATES` | 22 |
| Geography | Cities | `COM_LIST_CITIES` | 23 |
| Holidays | Holidays | `COM_LIST_HOLIDAYS` | 25 |
| Announcements | Announcement Categories | `COM_LIST_ANNOUNCEMENT_CATEGORIES` | 27 |
| Announcements | Company Announcements | `COM_LIST_COMPANY_ANNOUNCEMENTS` | 28 |
| User Management | Invite User | `COM_LIST_MULTI_APPLICATION_USER_INVITATIONS` | 11 |
| User Management | Add User | `COM_INSERT_APPLICATION_USERS` | 12 |
| User Management | List Users | `COM_LIST_APPLICATION_USERS` | 13 |
| Items | Items | `COM_LIST_ITEMS` | 30 |
| Items | Items Categories | `COM_LIST_ITEM_CATEGORIES` | 31 |
| Items | UOM | `COM_LIST_UOM` | 32 |
| Others | HSN and SAC Codes | `COM_LIST_HSN_AND_SAC_CODES` | 34 |
| Others | Currencies | `COM_LIST_CURRENCIES` | 35 |
| Others | Currency Convertors | `COM_LIST_CURRENCY_CONVERTOR` | 36 |
| Others | Complaints | `COM_LIST_COMPLAINTS` | 78 |
| Others | Location | `COM_INSERT_LOCATIONS_NEW` | 75 |
| Others | User Mobile Registrations | `COM_LIST_MOBILE_REGISTERED` | 76 |
| Others | Mobile Registrations | `COM_LIST_MOBILE_USER_REGISTRATIONS` | 77 |
| Vendor Managements | Invite Vendors | `COM_LIST_VENDOR_INVITATIONS` | 38 |
| Vendor Managements | Add Vendor | `COM_INSERT_VENDOR_REGISTERS` | 39 |
| Vendor Managements | List Vendors | `COM_LIST_GRID_VENDORS` | 40 |
| Customer Managements | Invite Customer | (no PAGENAME) | 42 |
| Customer Managements | Add Customer | `COM_INSERT_CUSTOMERS` | 43 |
| Customer Managements | Quality Control | (no PAGENAME) | 45 |
| Customer Managements | List Customers | `COM_LIST_CUSTOMERS` | 44 |
| Module Managements | Module Setup | `COM_LIST_MODULE_SETUP` | 47 |
| Building Spaces | Building Spaces | `COM_LIST_BUILDINGS` | 71 |
| Process Pages Managements | Process | `COM_LIST_APPLICATION_PROCESS` | 68 |
| Process Pages Managements | Process Pages | `COM_LIST_APPLICATION_PROCESS_PAGES` | 69 |
| Process Pages Managements | Dynamic Process | `COM_INSERT_APPLICATION_PROCESS` | 83 |
| Roles Management | Roles | `COM_INSERT_APPLICATION_ROLE` | 15 |
| API | API Master | `COM_LIST_API_MASTERS` | 73 |
| API | API Hits | `COM_LIST_API_HITS` | 74 |
| Form 16A Details | Form 16A Details | `COM_LIST_EMP_TAX_DEPOSIT_RESPECT` | 63 |
| Form 16A Details | Assign Release Documents | `COM_LIST_EMP_ASSIGN_RELEASE_DOCUMENTS` | 64 |
| Email | Templates | `COM_LIST_EMAIL_CONTENT` | 53 |
| Email | Groups | `COM_LIST_EMAIL_GROUPS` | 54 |
| Workflow Setting | Workflow Setting | `COM_LIST_WORKFLOW_CONFIGURATION` | 49 |
| Workflow Setting | Workflow Setting-1.1 | `COM_LIST_WORKFLOW_CONFIGURATIONS_NEW` | 51 |
| Tasks | Hire and Retire | `COM_LIST_PROCESS_TASK_SETTINGS` | 61 |
| Tasks | Project Tasks | `COM_LIST_PROJECT_TASKS` | 60 |
| Application Missing Data Check | Application Missing Data Check | `COM_LIST_APPLICATION_MISSING_DATA_CHECK` | 66 |
| Field Map | Work Plans | `COM_LIST_FIELD_WORK_DAILY_PLANS` | 81 |
| Field Map | User Work Details | `COM_LIST_FIELD_WORK_USER_MAP_POINTS` | 80 |
| Field Map | View Map | `COM_VIEW_MAP_POINTS` | 79 |

**Key finding:** every `PAGENAME` **and its numeric `PI` (page ID)** in this table is byte-for-byte identical to Bharatnet's documented Settings tree. Since `PI` values are clearly global auto-increment page IDs (not per-tenant), this is strong confirmation that both tenants run against the **exact same shared platform page/permission schema** — the Settings tree is platform-level, not tenant-configurable. (Bharatnet's doc additionally noted a "Machine And Material" category with no sub-items rendered; not independently re-checked here.)

## 5. Roles — Module-scoped (checked directly)
`Application Settings > Roles Management > Roles` presents a **Module** dropdown before showing any role list. This tenant's Module dropdown has **3 options**: `CRM` (value 29), `Billstack` (value 31), `Contract` (value 40) — matching the 3-tile Modules switcher (see `00-navigation-map.md`). Bharatnet's tenant, with only 2 modules (CRM/Billstack), would presumably show only 2 options here — not independently re-verified against Bharatnet's live UI, but consistent with that tenant's documented 2-tile switcher. Screenshot: `26-settings-roles.png`.

## 6–8.
Not exercised beyond viewing (read-only). Business logic (Workflow BPM engine, permission matrix, etc.) not re-explored in depth here since Bharatnet's `settings.md` already covers it thoroughly and the schema is confirmed platform-shared, not tenant-specific.
