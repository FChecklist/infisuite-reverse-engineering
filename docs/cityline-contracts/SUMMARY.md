# Summary — Citylinenetworks Contracts (`http://citylinenetworks.infisuite.in/contracts`)

## Login

- Email: `contract_admin@citylinenetworks.infisuite.in`
- Password `1234` (given as primary in the task) → **failed** (`Invalid credentials`).
- Password `infi123` (given as fallback) → **worked**. This is the password that should be used for this tenant. (Not stored anywhere else in this repo.)

## What this module actually is

A single-module tenant ("Contract", `module_id: 40`) on the same Infisuite platform family as the previously-documented `indyanet.infisuite.in` tenant, but implemented as a different front-end build (React SPA here vs. server-rendered PHP there) and treated fully independently per task instructions. Only three sub-areas exist under this role: the Contracts entity itself, and two Master lookup tables (Document Categories, Document).

## Headline finding: the Contracts entity's UI is broken

The single most important fact about this system: **the Contracts list page (`/contracts/list/COM_LIST_CONTRACTS`) crashes to a blank "No data available." page with no navigation chrome**, and because contract *creation* is only reachable via a button on that list page (per this platform's usual pattern), **contract creation, editing, and viewing are all unreachable as a result**. Reproduced 3/3 times via two different navigation methods (in-app click and direct URL load), including a 10-second wait to rule out a slow async recovery. Root-caused via browser console to an uncaught `TypeError: Cannot read properties of null (reading 'application_table_name')` during the list page's data-fetch bootstrap. A control test against the two Master lookup tables (which use the same generic list/insert/edit/view component family) confirms this is **not** a general "hard navigation breaks this SPA" problem — those pages load correctly every time — the crash is specific to the `contracts` entity. Full writeup, console log excerpt, and screenshots: `docs/cityline-contracts/contracts.md`.

As a secondary, indirect finding, the shared Document master (`master-documents.md`) contains a "Contract" category with exactly 10 named documents that exactly match the indyanet tenant's Contract-form "Documents" tab slot names — strong circumstantial evidence of what the (unreachable) Contract entity's own Documents tab was designed to look like here, though this could not be directly confirmed.

## Data model (as far as observable)

```
Contract (table: contracts) — existence and 4 field names (Contract/name, Start Date, End Date,
  Status, Contact Number) inferred solely from Dashboard widgets; everything else about this
  entity (full field set, FKs, workflow, documents, permissions) is UNCONFIRMED — its own UI
  is unreachable, see "Headline finding" above.
    ├── Status — a lookup table with ~41 rows exists (confirmed via console log count); contents
    │     not enumerated.
    ├── (plausible, by analogy with indyanet, NOT confirmed) Party Type: Customer | Vendor FK
    └── (plausible, by analogy + circumstantial Document-master evidence) Documents tab, driven by:

DocumentCategory (table: document_categories) — CONFIRMED, fully documented
    id, company_id, module_setup_id (FK: app registry — All/CRM/Billstack/Contract),
    category_name*, category_description, current_status, is_active, status_date,
    from_date, to_date, created_by/date, updated_by/date
    │
    └── 1:N → DocumentList (table: document_lists) — CONFIRMED, fully documented
          id, company_id, category_id (FK), parent_document_id (self-FK, unused),
          document_name*, submit_to_user_id (FK: Users, unexplored), display_position,
          document_list_types, mandatory, page, current_status, is_active, status_date,
          from_date, to_date, created_by/date, updated_by/date
```

Both DocumentCategory and DocumentList are confirmed **shared across the whole Infisuite platform** (CRM/Billstack/Contract), not scoped to this Contracts app — their real data is dominated by HR-onboarding categories (JOIN, Pre Joining Checklist, Vendor Registration, Document Release) with only a slice ("Contract" category, 11 documents) actually relevant to this app.

## Function list

| Function | Module | Status |
|---|---|---|
| Login (email/password) | Auth | Working (fallback password required) |
| Dashboard (stat tiles, My Contracts table, Expiring/Expired widgets, Progress Bar widget) | Dashboard | Working, but all-empty tenant data (0 contracts/customers/vendors) |
| Contracts: List | Contract | **Broken — crashes to blank page** |
| Contracts: Create | Contract | **Unreachable** (only entry point is the broken list page) |
| Contracts: Edit / View | Contract | **Broken — crashes to blank page** (direct URL load) |
| Document Categories: List/Search/Filter/Paginate | Master | Working |
| Document Categories: Create/Edit/View | Master | Working (create/edit not submitted, per read-only rule) |
| Document: List/Search/Filter(Category)/Paginate | Master | Working |
| Document: Create/Edit/View | Master | Working (create/edit not submitted, per read-only rule) |
| Module switcher (Purchasing/Stores/CRM/Billstack/Contract) | Global nav | Present, enumerated, not exercised (out of scope) |
| View CRM / View Ticketings SSO links | Global nav | Present, enumerated (token structure noted), not followed (out of scope) |

## Known Gaps

- **Contract entity's real field set, validation, FK relationships, workflow/approval states, permissions model, and any recharge-plan-equivalent feature are entirely unconfirmed** — the only entry points to that UI all crash. This is the single largest gap in this documentation pass, and it is a genuine application defect rather than a scope limitation.
- Whether the Contracts-list crash is specific to this tenant's zero-contract data condition, or a build-wide regression that would also break tenants with real contract data, is unknown — could not be tested without creating real records (against the read-only rule) or accessing another Contract-app tenant.
- Customer and Vendor masters referenced by the Dashboard's stat tiles/tabs were never located under this role's Master menu — likely managed from the sibling CRM app (out of scope), not confirmed.
- The Module (dropdown on Document Categories create/edit) and Module setup (list filter) field's exact option values/mapping were not enumerated.
- The "Select options" multi-select control present on both Master list-page toolbars was never exercised — purpose unconfirmed (bulk action vs. column picker, guessed).
- Search box behavior, column sort, and Active/Inactive filter states on both Master lists were only partially exercised (verified present and interactive, not exhaustively tested against real filtered results).
- Briefcase and headset icons in the top nav were never exercised — purpose unknown.
- Module switcher targets (Purchasing, Stores, CRM, Billstack) and the View CRM/View Ticketings SSO hops were deliberately not followed — out of scope for this Contracts-focused task.
