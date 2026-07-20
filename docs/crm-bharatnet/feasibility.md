# Feasibility

## 1. Purpose

Tracks a "feasibility" workflow/record tied to a Contact (and, per the Home dashboard's "Pending BOM Returns" widget, to specific Items) — appears to represent a site-survey / technical-feasibility-check process common in telecom/network-rollout businesses (consistent with this being a BharatNet — Indian rural broadband — deployment tenant), including equipment (BOM — Bill of Materials) loaned out and expected back by a planned date, and a downstream "Sales Order" generation step.

## 2. Navigation path

Home (top nav) → **Feasibility** tab in the CRM tab strip. URL: `index?TRANST=crm_feasibilities`.

Also surfaced as a widget directly on the **Home** tab: "Feasibilities" (with Expired/15-Days/30-Days/45-Days sub-tabs) and "Pending BOM Returns (Feasibility Closed)" table — see `00-navigation-map.md` / `home-01-full-dashboard.png`.

A **Feasibility** boolean toggle also exists on the Contact create form (`contacts.md` §4) — the likely creation trigger for a Feasibility record, though this could not be confirmed end-to-end (form not submitted, per task rules).

## 3. UI / page design

**Structurally different from every other CRM tab** — this is the only CRM tab that behaves like a genuine status-filtered list view rather than a pure dashboard-widget-with-create-button:
- Heading **"Feasibilities (<date range>)"** with the shared date-range filter.
- **No "+ Feasibility" create button** — Feasibility records appear to be created only as a side effect of another action (most likely the Contact form's Feasibility toggle), not directly from this tab.
- A row of 4 status-filter pill-buttons, each showing a live count badge: **Feasibility Open** (0, selected/active by default, shown in blue), **Feasibility Closed** (0), **SO Not Generated** (0), **SO Generated** (0) — "SO" = Sales Order, confirming a Feasibility can progress to generating a Sales Order (a Billstack/billing-system concept, out of scope for this CRM task but the linkage is visible here).
- Below the filter pills: "No Records Found" (this tenant has 0 Feasibility records in any state) — note the empty-state copy here ("No Records Found") differs from every other CRM tab's illustrated "No <Entity>" empty state, another sign this is a different underlying view component (a real list/grid component, just with 0 rows, rather than the shared dashboard-widget empty-state).
- Adjacent **Status** widget ("No Data"), consistent with other tabs.

Screenshot: `feasibility-01-list-view.png`.

## 4. Create form

**Not directly reachable** — no create button found on this tab. Documented as a gap: Feasibility records are inferred to be created via the Contact "Feasibility" toggle (see `contacts.md`) or possibly from a Deal/Project action not observed. Exact field set of a Feasibility record is therefore **unknown** — the only fields inferable are from the Home-dashboard "Pending BOM Returns (Feasibility Closed)" table columns: **Feasibility Title, Item Name, Planned Return By, Actual Return By, Action**.

## 5. Underlying data structure (inferred)

**Entity: Feasibility.** Relationships (inferred, low confidence given no direct create form was found):
- `Feasibility.contact` → **Contact** (via the Contact-side toggle)
- `Feasibility` **has an Item** (BOM equipment loaned for the feasibility check) → **Item** (Masters)
- `Feasibility.status` → at minimum a 2-state Open/Closed flag, each further split by SO Generated/Not Generated — i.e. **two independent status dimensions**: Open/Closed (is the feasibility check itself done) × SO Generated/Not (has billing been triggered)
- `Feasibility` has date-bucketed expiry tracking (Expired / 15-Days / 30-Days / 45-Days, seen on the Home widget) — implies a Feasibility record carries an expiry/validity date, separate from the BOM's Planned/Actual Return By dates
- `Feasibility` → **Sales Order** (downstream, in the Billstack system — out of scope, but the "SO Generated" state field confirms the hook exists)

## 6. Functionality / logic observed

- List view with 4-way status filter (Open/Closed/SO Not Generated/SO Generated), each independently counted.
- No create/edit/delete reachable directly on this tab.
- Home dashboard surfaces two additional Feasibility-derived widgets: an expiry-bucket breakdown (Expired/15/30/45-day tabs) and a "Pending BOM Returns" table for closed feasibilities still owing equipment back.

## 7. Inputs and outputs

- Not exercised — no input surface found on this tab itself. Presumed input is the Contact-form Feasibility toggle (unconfirmed).

## 8. Data conditions

- All 4 status-filter counts are 0 in this tenant — the underlying columns/detail fields of an open Feasibility record could not be observed. **Significant gap** — this is the least-understood CRM module in this reverse-engineering pass, since no create form or populated record was found by any path explored.
