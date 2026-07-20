# Masters (Items, Feasibility, Customers, Users, Invite Users)

## 1. Purpose
Shared platform-wide reference/master data used across CRM (and other modules): product/item catalog, feasibility taxonomy, customer accounts, and application users. Same role as Bharatnet's Masters section, but the mega-menu here exposes **more sub-items** than Bharatnet's documented flat 4-link menu — see `00-navigation-map.md` §4 for the full inventory and the open question about whether this is a real tenant difference or an under-documented Bharatnet menu.

## 2. Navigation path
Top nav → **Masters** (click, mega-menu) → one of: Item Categories / Items Type / UOM / Items / Feasibilty Types / Feasibilty Inputs / Customers / Users / Invite Users.

## 3–4. UI / design and data fields, per sub-page

### Items, Item Categories, Items Type, UOM
All four follow the identical Masters list template documented extensively in Bharatnet's `masters-items.md`: Active/Inactive/All radio, Records page-size + Search (min 3 chars), entity-specific filter dropdowns, Status/Import/Export/+Create buttons, gear icon (display-attribute column config), Back. **All four are empty on this tenant** ("Hello! There are no records added yet.") — screenshots: `16-masters-items.png` (Items — filters: Item Type/Purchase UoM/Sale UoM/Category), `21-masters-item-categories.png` (Item Categories — filter: Default Costing Type). Items Type and UOM were not individually screenshotted (same empty-state template confirmed via the shared component pattern) — noted as a minor scope-saving inference, not a re-verified claim per sub-page.

### Feasibilty Types / Feasibilty Inputs
**Not empty** — see `feasibility.md` §4–5 for full detail. 4 real Feasibility Type records (Software/Ports/Broadband/Railway) and a fully-configured dynamic field schema for at least the "Software" type.

### Customers
Simple list (no filter row beyond +Customer/Back) — "No Customers". Screenshot: `18-masters-customers.png`. This tenant has **0 Customer master records**, which explains why every Customer FK dropdown seen elsewhere (Project create form, Deal's Company/Contact indirectly) renders "Select Any One" with nothing selectable.

### Users
List with filters (Location/Country/Reports To/User Type) — **4 real records**:

| Id | User Name | Display Name | User Email | Cost Center | Country |
|---|---|---|---|---|---|
| 1 | ADMIN-1 | Admin | admin@citylinenetworks.infisuite.in | 1 | Guinea-Bissau |
| 2 | CRM Admin | CRM Admin | crm_admin@citylinenetworks.infisuite.in | 1 | Guinea-Bissau |
| 3 | Billstack Admin | Billstack Admin | bill_admin@citylinenetworks.infisuite.in | 1 | Guinea-Bissau |
| 4 | CONTRACT ADMIN | CONTRACT ADMIN | contract_admin@citylinenetworks.infisuite.in | 1 | Guinea-Bissau |

One user per module/tile in the Modules switcher (CRM/Billstack/Contract) plus a plain "Admin" superuser — confirms the task's given Billstack login (`bill_admin@citylinenetworks.infisuite.in`) is exactly this tenant's #3 user. **Observed oddity:** all 4 users show Country = "Guinea-Bissau" — almost certainly a leftover default/placeholder value from seed data rather than a real fact about this Owner's tenant, flagged as observed-not-inferred. Screenshot: `17-masters-users.png`.

### Invite Users
List with All/Active/Invited filter + "+ Invite User" — "No User Invitation Available". Screenshot: `22-masters-invite-users.png`.

## 5. Underlying data structure
Same as Bharatnet: `Item.category/type/uom → respective masters`; `User.role/module` scoping; new for this tenant: `FeasibilityType has-many FeasibilityField` (see `feasibility.md`).

## 6–8. Functionality, inputs/outputs, data conditions
All list pages use the same read-only-safe Active/Inactive/All + Search + Import/Export chrome; none exercised for create/edit/delete/import/export per task rules. Data conditions summarized per sub-page above.
