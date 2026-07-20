# Dashboard

## 1. Purpose

Landing page after login (`/contracts/dashboard`). Gives a Contract Admin an at-a-glance summary of the tenant's contract book: total/active/expired counts, customer/vendor counts, a searchable "My Contracts" table, and date-bucketed Expiring/Expired widgets. For this tenant the underlying data set is genuinely empty (a freshly-provisioned/demo tenant, not a rendering bug — see §8), so every widget here is observed in its empty state.

## 2. Navigation path

Default landing page after login. Also reachable at any time via the Infisuite logo, the Home icon, or the breadcrumb "Dashboard" link in the top nav.

## 3. UI / page design

Screenshot: `screenshots/cityline-contracts/dashboard.png`.

- **Top stat-tile row** (5 colored cards, non-interactive): Contracts (magenta), Active (blue), Expired (yellow/amber), Total Customers (green), Total Vendors (red). Each shows a big number and a label.
- **Tab strip**: **Customers** / **Vendors** — switches the scope of the panels below (client-side anchor tabs, `#Customers` / `#Vendors`, not separate page loads).
- **"My Contracts" panel** (left, ~60% width):
  - Search box ("Search...", no visible minimum-character hint here — contrast with the Master list pages which show "(Min 3 Characters Required)").
  - Three filter buttons: **All** (default) / **Active** / **Expired**.
  - Table columns: **#**, **Contract** (sortable, ⇅ icon), **Start Date** (sortable), **End Date** (sortable), **Status** (sortable), **Contact Number** (sortable).
  - Empty state: single row, "No contracts found".
- **"Expiring Contracts" panel** (right, top): three collapsible accordion sections — **Expires in 30 Days** (expanded by default), **Expires in 31 to 60 Days**, **Expires in 61-90 Days**. Each section's table (when expanded) has columns Contract Name / Days Left / End Date. Empty state: "No contracts in this range".
- **"Expired Contracts" panel** (right, below Expiring): three collapsible accordion sections — **Expired last 30 Days (0)**, **Expired last 60 Days (0)**, **Expired last 90 Days (0)** — the count is shown in the section header itself, unlike the Expiring panel. Empty state: "No contracts expired in this range".
- **"Progress Bar With Percentage" panel** (bottom): two collapsible sections, **Customer Contracts (0)** and **Vendor Contracts (0)** — headers only observed; body content not captured (both collapsed by default, and there was no data to expand into — gap, the populated appearance of this widget is unknown).
- Footer: "© 2026 Admin Panel. All rights reserved."

## 4. Data fields

Purely observational (no form inputs on this page besides the Search box and filter/tab toggles):

| Widget | Field | Type |
|---|---|---|
| My Contracts table | Contract, Start Date, End Date, Status, Contact Number | display columns, all sortable |
| Expiring Contracts | Contract Name, Days Left, End Date | display columns |
| Expired Contracts | Contract Name, Days Expired, End Date | display columns |

## 5. Underlying data structure (inferred)

- Confirms the **Contract** entity carries at minimum: a name, Start Date, End Date, a Status (enum, unknown values — never populated in this pass), and a Contact Number (likely the "Contract Owner Number"-equivalent field, matching the indyanet tenant's Contract Owner Contact Number field — **inferred by analogy, not confirmed** for this tenant).
- The Customers/Vendors tabs plus "Total Customers"/"Total Vendors" tiles and "Customer Contracts"/"Vendor Contracts" progress widgets strongly suggest the same **Party Type: Customer | Vendor** polymorphic-FK pattern documented on the indyanet tenant's Contract form — **inferred by analogy only**, not independently confirmed here since neither a Customer/Vendor master nor a populated Contract record was reachable in this tenant.
- Confirmed via console log (`Fetched allstatus Data: [...]`, 41 objects) that a **Status** master table exists with roughly 41 rows — far more than a simple Active/Expired binary, implying a richer contract-status/workflow-state lookup table. Contents not enumerated (the objects were not expanded in the console capture) — **gap**.

## 6. Functionality / logic observed

- **Customers/Vendors tab switch** — client-side only, re-scopes "My Contracts" table and (presumably) the widgets to one party type; not exercised further since both are empty.
- **All/Active/Expired filter buttons** on My Contracts — presumed client- or query-side filter of the same empty table; not meaningfully exercisable with zero data (**gap**).
- **Column sort** (⇅ icons on My Contracts headers) — not exercised (no rows to sort).
- **Accordion expand/collapse** on Expiring/Expired panels and the Progress Bar panel — confirmed interactive (aria `expanded` state toggles), pure UI state.
- No create/edit actions live on this page.

## 7. Inputs and outputs

- Input: Search box (My Contracts), All/Active/Expired filter, Customers/Vendors tab.
- Output: would filter/populate the "My Contracts" table and presumably the Expiring/Expired/Progress widgets; not observable with an empty data set.

## 8. Data conditions

- **This tenant has zero Contracts, zero Customers, zero Vendors, zero Permissions, zero Documents** at the time of this pass — confirmed directly from console logs during page bootstrap: `Fetched allcontracts Data: []`, `Fetched allcustomers Data: []`, `Fetched allvendors Data: []`, `Fetched allpermissions Data: []`, `Fetched alldocuments Data: []`. This is a **data condition of the tenant**, not a bug in the Dashboard itself — the Dashboard renders its empty states correctly and without error (contrast with the Contracts list page itself, which crashes — see `contracts.md`).
- All stat tiles read "0"; all list/widget bodies show their respective "No … found"/"No … in this range" empty-state text — every empty state observed renders correctly with no console errors originating from the Dashboard route.
