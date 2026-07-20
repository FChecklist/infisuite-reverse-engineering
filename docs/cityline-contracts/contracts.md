# Contracts

## 1. Purpose

The core business entity of this tenant/app (module name: "Contract", `module_id: 40`). By analogy with the platform's other tenant (indyanet) and with the Dashboard's widget set (`dashboard.md`), this is expected to track service/business agreements between Citylinenetworks and its Customers/Vendors, with Start/End dates, a Status, and Expiring/Expired reporting. **This could not be independently confirmed** for this tenant: the list, create, edit, and view pages for Contracts are all unreachable due to a reproducible application crash (§6). This document records what was directly observed (the crash, its console evidence, and what the Dashboard exposes) rather than assuming the indyanet tenant's field set applies here.

## 2. Navigation path

- Top nav **New → Contract** → routes to `/contracts/list/COM_LIST_CONTRACTS` (i.e., "new" lands on the list page first, matching the platform's usual pattern of a "+ Contract" button living on the list page rather than New opening a blank form directly).
- Direct routes that exist per the app's internal page-registry (confirmed via a `pageDetails` object logged to console — see §6): `COM_LIST_CONTRACTS` (list), `COM_INSERT_CONTRACTS` (create), `COM_EDIT_CONTRACTS` (edit), `COM_VIEW_CONTRACTS` (view). All were attempted; all fail identically (§6).

## 3. UI / page design

**Not observable.** Every attempted entry point renders a blank page containing only the literal text "No data available." and no navigation chrome (no top nav, no footer) — see screenshots `bug-contracts-list-crash.png`, `contracts-list.png`, `contracts-list-reloaded.png`.

## 4. Data fields

Not directly observable (no reachable form). The only field names that can be documented with confidence come from the Dashboard's read-only widgets (`dashboard.md` §4): **Contract** (name), **Start Date**, **End Date**, **Status**, **Contact Number**. Everything else (a possible Party Type/Customer-Vendor split, owner contact fields, documents, workflow, permissions, recharge plans, etc., as seen on the indyanet tenant's equivalent form) is **not confirmed for this tenant** and is deliberately omitted here rather than assumed.

## 5. Underlying data structure (inferred)

- A `contracts` DB table exists (confirmed by the `application_table_name: "contracts"` value in the logged `pageDetails.other` object, see §6) with an internal page/menu registry id `847`.
- A companion `Status` lookup table exists with ~41 rows (`Fetched allstatus Data:` logged 41 objects at Dashboard load) — contents not enumerated (gap).
- Given the Dashboard's Customer/Vendor tiles and tabs, a Customer and/or Vendor relationship on Contract is plausible by analogy with indyanet's `Party Type` pattern, but **not confirmed** — no Customer/Vendor master or populated Contract record was reachable to verify.

## 6. Functionality / logic observed — critical bug

**The Contracts list (and therefore create, since create is only reachable via the list page's expected "+ Contract" button) is broken and inaccessible.** Reproduced 3/3 times, via two different navigation methods:

1. In-app navigation: Dashboard → **New → Contract** (dropdown click) → URL changes to `/contracts/list/COM_LIST_CONTRACTS` → page body collapses to a bare `No data available.` string, no nav bar, no footer.
2. Direct URL load (fresh `page.goto`) of `/contracts/list/COM_LIST_CONTRACTS` after being logged in — same blank result.
3. Direct URL load of `/contracts/insert/COM_INSERT_CONTRACTS` (the create-form route) — same blank result.
4. Waited up to 10 seconds on the broken list page in case of a slow async recovery — no change; `document.body.innerHTML.length` stayed at 118 characters (i.e., just the wrapper + the "No data available." text). The crash does not self-heal.

Console evidence (captured via the browser devtools console on attempt 3, edited for length — full sequence repeats identically on every attempt):

```
[LOG] Clicked Page Name: COM_LIST_CONTRACTS Module ID: 40
[LOG] Page Details: {work_page: list, application_table_name: contracts, page_type: 1, page: custome_list_contracts, other: Object}
[LOG] Page Type: 1
[LOG] pageDetails: null
[LOG] Dynamic Page Name: COM_LIST_CONTRACTS
[ERROR] Error fetching data: TypeError: Cannot read properties of null (reading 'application_table_name')
    at ae (.../static/js/main.9c67a11f.js:2:477386)
    ...
[LOG] pageDetails: {work_page: list, application_table_name: contracts, page_type: 1, page: custome_list_contracts, other: {id: 847, application_table_name: contracts, list_page_name: COM_LIST_CONTRACTS, list_page_type: 1, insert_page_name: COM_INSERT_CONTRACTS, insert_page_type: 2, edit_page_name: COM_EDIT_CONTRACTS, edit_page_type: 2, view_page_name: COM_VIEW_CONTRACTS, view_page_type: 1}}
```

Interpretation (inferred, not confirmed against source): the list-page component reads `pageDetails` from some async-populated state that is briefly `null` on first render, and a data-fetch routine dereferences `pageDetails.application_table_name` before the null-check/guard would normally prevent it, throwing an uncaught `TypeError`. `pageDetails` *does* subsequently resolve to the correct, fully-populated object a moment later (visible in the log), but the UI never recovers — consistent with an error having been thrown during a render pass that unmounted the entire routed subtree (including the shared nav-bar layout) up to the app's root/error boundary, rather than being caught locally by the list widget alone. Also notable: `Fetched contracts Data: undefined` is logged (contrast with `Fetched allcontracts Data: []`, an empty array, logged earlier on the Dashboard) — the list page's own contracts fetch appears to resolve to `undefined` rather than `[]`, which is a plausible trigger/contributing factor for the crash given the tenant's zero-contract data set, though this was not proven by inspecting source.

**Control test — this is not a generic "hard navigation breaks the SPA" issue.** The same direct-URL-load technique works correctly for the Master → Document Categories and Master → Document entities (`master-document-categories.md`, `master-documents.md`): their list/insert/edit/view routes all render fully and correctly on a fresh page load, with real navigation chrome and real data. The crash is specific to the `contracts` entity/table's list and insert routes.

No other Contract functionality (search/filter/sort on the list, the create form's fields, edit, view, any workflow/approval states, document upload, permissions, recharge plans, etc.) could be exercised as a result.

## 7. Inputs and outputs

Not observable — no reachable form.

## 8. Data conditions

- This tenant has **zero Contract records** (`Fetched allcontracts Data: []` at Dashboard bootstrap). Whether the crash would also occur with a non-empty contracts data set is unknown — **gap**, cannot be tested without creating a real record, which is against the read-only rule for this task, and in any case the create form is itself behind the same broken list page.
- Given the crash, no status-dependent UI, role-dependent visibility, or filter behavior could be observed for this entity.

## 9. Indirect evidence of a Documents tab (via the shared Document master)

`master-documents.md` §8 shows the Master → Document list's "Contract" category contains exactly 10 named documents — **Contract Documents, Signed Agreement, Confidentiality Agreement (NDA), Payment Terms & Conditions, Supporting Documents, Permits & Licenses, Proof of Identity, Insurance Certificates, Warranty Documents, Additional Note or Attachment** — an exact match to the indyanet tenant's Contract-form "Documents" tab upload slots. This is strong circumstantial evidence that this tenant's Contract entity has (or was designed to have) an equivalent Documents tab driven by this shared master filtered to `category = "Contract"`, even though that tab could not be directly reached or screenshotted here. Treated as **inferred, not confirmed**.

## Known Gaps (entity-specific)

- Cannot confirm the Contract entity's actual field set, required/optional markers, validation rules, or FK relationships (Customer/Vendor, Associated Contract, etc.) for this tenant.
- Cannot confirm whether Approval/Workflow states, Permissions, or Recharge Plans/History exist on this tenant's Contract entity (all present on the indyanet tenant's equivalent form, but this is expressly a different system per task instructions and none of it could be verified here). A Documents tab is plausible per §9 but still unconfirmed directly.
- Cannot confirm whether the list-page crash is tenant-specific (e.g., triggered only by the zero-contract data condition) or a build-wide regression that would also affect tenants with real contract data.
