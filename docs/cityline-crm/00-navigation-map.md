# CRM (Cityline Networks / Infistaq) — Navigation Map

System: `http://citylinenetworks.infisuite.in/`
Product name shown in UI: "Infisuite" (login page) / "Infistaq" (page `<title>`, e.g. "Infistaq | Home"). Same shared multi-tenant "Infistaq" platform already documented for the Bharatnet tenant (`docs/crm-bharatnet/`).

Logged in as: `crm_admin@citylinenetworks.infisuite.in`, displayed as "CRM Admin" (avatar initials "CA").

**Login credential note:** the password `1234` (first convention tried per task instructions) FAILED with error `login?error=6002`. The fallback password **`infi123` succeeded**. This confirms the second password convention for this Owner's infisuite tenants applies to the Cityline tenant (both CRM and, per task instructions, presumably Billstack — verified separately in `docs/cityline-billstack/`). Password is not stored in this repo beyond this note.

Login also requires a 4-digit numeric image captcha (client-rendered text on a dot-pattern background), read manually from a screenshot each attempt — same mechanism as Bharatnet.

## 1. Login

- URL: `http://citylinenetworks.infisuite.in/` → redirects to `/login.php`, page title "Infistaq | Login".
- Form fields: **User Name** (text), **Enter Password** (password, show/hide eye toggle), **Enter Captcha** (text, 4-digit numeric image), **Forgot Password?** (link/modal, not exercised), **Sign In** button.
- On success redirects to `/index` (page title "Infistaq | Home"). Identical flow/layout to Bharatnet, including the "Welcome to Infisuite" marketing panel graphic on the right half of the screen.
- Screenshot: `screenshots/cityline-crm/00-login-page.png`

## 2. Global chrome (present on every authenticated page)

Top bar, left to right — identical icon set/order to Bharatnet:

1. **Logo** → `index.php` (Home).
2. **Icon (robot glyph)** → `index.php?option=COM_LIST_DISPLAY_ATTRIBUTES` — Display Attributes admin table.
3. **Icon (gear)** → `index.php?option=COM_LIST_APPLICATION_MENU_SETTINGS` — Application Settings.
4. **Icon (megaphone)** → `index.php?option=COM_VIEW_ANNOUNCEMENTS` — company announcements feed.
5. **Icon (bell, badge "0")** → notifications slide-out panel (Action/Information/Logs tabs). Empty on this tenant too.
6. **Icon (headset)** → `index.php?option=COM_VIEW_PROJECT_TICKETS_DASHBOARD` — Ticketing dashboard. **Out of scope** (separate task covers `citylinenetworks.infisuite.in/ticketing`).
7. **Avatar + "CRM Admin" dropdown** → **Profile** (`COM_VIEW_PROFILE_APPLICATION_USERS`), **My Messages** (`COM_LIST_INBOX`), **Logout**.
8. **Icon (colored dot pattern, "Modules")** → dropdown: **CRM** (current, checkmark), **Billstack**, **Contract**.
   - **Difference from Bharatnet:** Bharatnet's Modules switcher had only 2 tiles (CRM, Billstack). Cityline has a **3rd module, "Contract"** — consistent with the README scope table listing a separate `citylinenetworks.infisuite.in/contracts` system (covered by a different task/worker). Not explored further here (out of scope for this task).

Second row:
- **Home** (logo link).
- **Masters** — mega-menu (click). Richer than Bharatnet's flat 4-item Masters menu — see §5 below.

## 3. CRM Home page (`index` / `index.php`)

Same layout as Bharatnet: profile summary card (avatar, name, email, Today's Leads/Contacts/Deals counters), then 6 lifetime counters (Total leads / Total Contacts / Total Companies / Total Campaigns / Total deals / **Total Projects**), then the tab strip:

**Home | Leads | Contacts | Companies | Deals | Campaign | Projects | Feasibility**

...plus **+ Lead/Contact Assignings** button → `index.php?option=COM_VIEW_CRM_LEAD_AND_CONTACT_ASSIGNINGS`.

Same ARIA bug as Bharatnet: every tab shows `[selected]` simultaneously in the accessibility tree (all tabs get `aria-selected="true"`) — cosmetic/markup defect, not a real multi-selection.

**Key data difference from Bharatnet:** Bharatnet's CRM tenant was **entirely empty** (0 records everywhere). Cityline's tenant has **0 Leads / 0 Contacts / 0 Companies / 0 Campaigns / 0 Deals but 27 Projects** — this is clearly an internal test/dev tenant (project names include "test", "Dummy", "gdh", "bdhdb", "fhdhs n", etc., dates all in 2025). This gives us the first real look at a populated list view and a populated create/edit form pre-fill on this platform.

Each tab is a **dashboard-style widget panel** (not a data grid) driven by a shared date-range filter (button e.g. "This Year" + caret dropdown: Today / This Week / This Month / This Year / Yesterday / Previous Week / Previous Month / Previous Year / Custom Date). Selecting Custom Date reveals Start Date / End Date pickers + a search button. This exactly matches Bharatnet's documented behavior. With a wide custom range (01-01-2015 → 31-12-2026) the Projects tab renders as a real data table (Project Name / Originator / Project Start / Project End / Total Budget, each row clickable) — confirming the widget genuinely is a live grid once a date range with matching records is selected, not merely a chart placeholder.

Screenshots: `01-dashboard-home.png`, `02-leads-tab.png` (empty state), `03-projects-tab.png` (empty w/ default "This Year" range), `03d-projects-wide-range.png` (populated grid).

## 4. Masters mega-menu — full inventory (richer than Bharatnet)

Extracted via DOM query of the mega-menu's `<li>` items:

| Group | Item | URL (`option=`) |
|---|---|---|
| Items | Item Categories | `COM_LIST_ITEM_CATEGORIES` |
| Items | Items Type | `COM_LIST_ITEM_TYPES` |
| Items | UOM | `COM_LIST_UNIT_OF_MEASUREMENTS` |
| Items | Items | `COM_LIST_ITEMS` |
| Feasibilty [sic] | Feasibilty Types | `COM_LIST_FEASIBILITY_TYPES` |
| Feasibilty [sic] | Feasibilty Inputs | `COM_INSERT_FEASIBILITY_FIELDS` |
| Customers | Customers | `COM_LIST_CUSTOMERS` |
| Users | Users | `COM_LIST_APPLICATION_USERS` |
| Users | Invite Users | `COM_LIST_APPLICATION_USER_INVITATIONS` |

**Difference from Bharatnet:** Bharatnet's documented Masters menu was flat: **Items, Customers, Users, Invite Users** (4 links, no sub-grouping, no Feasibility category). Cityline's Masters menu has the **same underlying entities plus explicit sub-items for Item Categories/Item Types/UOM**, and a whole extra **"Feasibilty" master group** (Types + Inputs) not seen in the Bharatnet nav doc. This is very likely the same underlying menu (the platform is shared) rendered with a config difference, OR the Bharatnet pilot doc simply didn't expand the mega-menu's nested items — flagged as an open question in SUMMARY.md rather than asserted as a confirmed structural difference.

## 5. Settings tree (gear icon → Application Settings)

Not yet fully re-catalogued for this tenant at time of writing this file — see `settings.md` for the full extracted tree (same methodology as Bharatnet: one DOM query since all categories are present in the page at once).

## 6. Checklist

- [x] Login + captcha (password convention confirmed: `infi123`, not `1234`)
- [x] Home dashboard
- [x] Modules switcher (CRM / Billstack / **Contract** — 3 tiles, one more than Bharatnet)
- [x] Masters mega-menu inventory
- [ ] Leads tab
- [ ] Contacts tab
- [ ] Companies tab
- [ ] Deals tab
- [ ] Campaign tab
- [x] Projects tab (only populated CRM entity on this tenant — 27 records)
- [ ] Feasibility tab
- [ ] Lead/Contact Assignings
- [ ] Masters > Item Categories / Items Type / UOM / Items
- [ ] Masters > Feasibilty Types / Feasibilty Inputs
- [ ] Masters > Customers
- [ ] Masters > Users / Invite Users
- [ ] Display Attributes
- [ ] Announcements
- [ ] Application Settings tree
