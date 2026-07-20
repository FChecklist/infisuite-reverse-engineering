# Masters > Users / Invite Users

## 1. Purpose

Platform user/account administration — the User entity referenced as `originator`/Team Member/Assign-target throughout every CRM module. Also the login-identity and role-assignment mechanism ("CRM", "CRM Admin", "Billstack Admin" — cross-module logins into this shared Infistaq platform).

## 2. Navigation path

Top nav → **Masters** (mega-menu) → **Users** → `index.php?option=COM_LIST_APPLICATION_USERS`.
Top nav → **Masters** → **Invite Users** → `index.php?option=COM_LIST_APPLICATION_USER_INVITATIONS`, whose own **"+ Invite User"** button leads to the actual invite form at `index.php?option=COM_LIST_MULTI_APPLICATION_USER_INVITATIONS`.
Also reachable from a User's own profile page via an **"Invite User"** button (`index?option=COM_LIST_APPLICATION_USER_INVITATIONS`).

## 3. Users — list view

**This is the only list view found in the entire CRM/Masters exploration with real, non-empty data** — 3 users exist in this tenant. Screenshot: `masters-users-01-list-view.png`.

Standard Masters list template: Active/Inactive/All filter, Records page-size + Search, 4 additional filter dropdowns (**Location**, **Country**, **Reports To**, **User Type**, all "All" by default), Status/Import/Export/**+ Users**/Back/gear-icon controls.

**Columns:** Id, User Name, Display Name, User Email, User Mobile, Cost Center, Location, Country, Timezone, Mapping Code, Reports To, User Type, Action (Edit-only — a single pencil icon, no delete/copy/view icons on this table, unlike the pattern noted for other Masters lists).

**Real data observed (documenting actual tenant records, not synthetic — these are the system's own seed/admin accounts, not customer PII):**

| Id | User Name | Display Name | User Email | User Mobile | Cost Center | User Type |
|---|---|---|---|---|---|---|
| 1 | INFI00114 | Anthony Johnston | admin@bharatnet.infisuite.in | 7281816472 | 27 | SYSTEM |
| 2 | CRM Admin | CRM Admin | crm_admin@bharatnet.infisuite.in | (blank) | (blank) | SYSTEM |
| 3 | Billstack Admin | Billstack Admin | billstack_admin@bharatnet.infisuite.in | (blank) | (blank) | SYSTEM |

All 3 are `User Type = SYSTEM` (i.e. built-in platform accounts, not tenant-invited staff) — Location/Country/Timezone/Mapping Code/Reports To are blank for all three. Note "Anthony Johnston" is a 4th distinct identity/login beyond the "CRM Admin" account used to run this whole review — its User Name is a generated code (`INFI00114`) rather than a role name like the other two, and its email domain differs (`admin@bharatnet...` vs `crm_admin@bharatnet...`).

## 4. User detail/profile view

Reached via the row's Edit (pencil) action → `index.php?option=COM_VIEW_PROFILE_APPLICATION_USERS&Rid=<encoded-id>`. Screenshot: `masters-users-02-detail-view.png` (Anthony Johnston's profile).

- Header: avatar-initials circle, name, **Active** status badge, email, phone, "Report Manager : -".
- Quick-link icon row (8 tiles, all showing "-" for this user, presumably link out to related Settings sub-areas): HR Connect, Finance, Admin, Project, Location, ESOP, Legal, Approval Manager.
- Tabs: **Overview** (default), **Notification**, **Approval Transactions**, **User Activity**, **Reset Password**, **Mobile OTP**, plus a persistent **"Invite User"** button.
- **Overview tab** shows:
  - "Profile completion" side card: Basic Info 43%, Default Setting 0% (progress bars) — confirms the platform tracks per-user onboarding completeness.
  - "Basic Info" panel: Full Name, Display Name, Contact, Email, **Modules** (tag: `CRM`), **Roles** (tag: `CRM Admin`) — confirming this user is provisioned into the CRM module with the CRM Admin role, i.e. Modules and Roles are two independent, taggable dimensions of a User.
  - Right column: location, Default Warehouse, Country, State, Timezone, Currency — all blank for this user.
  - Four paired "No <X>" empty-state sections: Cost Centers, Departments, Divisions, Locations, Warehouse, Bank Accounts, Assets (7 total, each its own related-records panel) — confirms a User record can be associated with all of these entities, none populated in this tenant.
  - An **Edit** button (top-right of Basic Info) — not clicked further (would open an editable form; out of scope to explore deeply given no new field types beyond what List/Overview already reveal).

## 5. Users — functionality observed

- **List** with 6 filter dimensions (Active/Inactive/All, Location, Country, Reports To, User Type)
- **Edit** (only action available per-row; no delete)
- **Import** / **Export** (links present, not exercised)
- Profile/detail view has 6 sub-tabs (Overview explored; Notification/Approval Transactions/User Activity/Reset Password/Mobile OTP not explored — **gap**, time-boxed given this is an admin-account management area, not core CRM data)

## 6. Invite Users

**List view** (`masters-invite-users-01.png`): titled "User Invitation List", filters **All / Active / Invited** (radio), **+ Invite User** button. Empty state: "No User Invitation Available" (0 pending invitations in this tenant).

**Invite form** (`masters-invite-users-02-invite-form.png`, at `COM_LIST_MULTI_APPLICATION_USER_INVITATIONS`):

| Field | Field name | Type | Required | Notes |
|---|---|---|---|---|
| First Name | (unlabeled in DOM, inferred `first_name[]`) | text | Not marked, but functionally required (Invite Users button stays disabled/greyed until filled — inferred from its lighter button color, not confirmed by attempting submission) | |
| Last Name | (inferred `last_name[]`) | text | Same as above | |
| Email Id | (inferred `email[]`) | text | Same as above | |
| Role | `role_id[]` | dropdown, default "CRM" | Same as above | **Only 2 options exist tenant-wide: `CRM` and `CRM Admin`** — confirms this tenant has exactly a 2-tier role model for CRM-module users (a plain "CRM" role vs. the elevated "CRM Admin" role used by this review's login) |
| (row delete) | trash icon | action | | Removes an invite row |
| **+ Add More Invite User** | button | | | Adds another First/Last/Email/Role row — supports bulk-inviting multiple users in one submission |

**Action:** **Invite Users** button (visually disabled/lighter-colored in its default empty state — not exercised, per task rules; this is an outbound-communication-triggering action, explicitly excluded).

## 7. Underlying data structure (inferred)

**Entity: User.** Relationships:
- `User.role` → **Role** (2 known values in this tenant: CRM, CRM Admin — presumably many more exist platform-wide for other modules, e.g. "Billstack Admin" seen as a User Type/identity but not confirmed as a selectable Role option here)
- `User.modules` → tag-list of **Module** (CRM, and presumably Billstack/other modules per the Modules switcher noted in `00-navigation-map.md`)
- `User` optionally has: Cost Center, Department, Division, Location, Warehouse, Bank Account, Asset associations (all 1-to-many "related records" panels, unpopulated here)
- `User.reportsTo` → self-referencing (User hierarchy) — column exists on the list, blank for all 3 current users
- Referenced by: every module's `originator`/Team Member/Assign-target field throughout this whole review

**Entity: UserInvitation.** A pending-invite record with First Name/Last Name/Email/Role, presumably converts to a full **User** record once the invitee accepts (not observed — 0 invitations exist).

## 8. Data conditions

- This is the only place in the whole reverse-engineering pass where real (non-empty, non-synthetic) tenant data was found — 3 system-seeded Users.
- The Invite Users action was correctly *not* exercised (mutating + would trigger an outbound email, both excluded by the task's hard rules) — its exact required-field enforcement and success/failure behavior remain unconfirmed, flagged as an intentional gap.
