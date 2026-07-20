# Master → Document

## 1. Purpose

A shared lookup/master table of individual document types ("Document lists" internally), each belonging to one Document Category (`master-document-categories.md`). Like its parent category table, this is cross-app shared master data (HR onboarding checklists, vendor registration paperwork, a "Contract" category, etc.), not something scoped to the Contracts app alone.

## 2. Navigation path

Top nav **Master → Organization → Document** → `/contracts/list/COM_LIST_DOCUMENT_LISTS`. Create: **Add New** button → `/contracts/insert/document_lists`. Edit: pencil icon on a row → `/contracts/edit/document_lists/<id>`. View: eye icon on a row → `/contracts/view/document_lists/<id>`.

All four routes work correctly both via in-app navigation and via direct/hard URL load (same as Document Categories; contrast with the broken Contract entity routes, `contracts.md`).

## 3. UI / page design

### List (`COM_LIST_DOCUMENT_LISTS`)

Screenshot: `screenshots/cityline-contracts/master-document-list.png`. Toolbar: "Select options" multi-select (purpose not confirmed, gap), Active/Inactive/All radio (default All), **Add New** / **Back** buttons, Records page-size selector (default 20), Search ("Min 3 Characters Required"), and a **Category** dropdown filter (All, JOIN, Pre Joining Checklist, Vendor Registration, Document Release, Contract, Saree — i.e. the exact set of Document Categories). Table columns: Document Category, Document Name, Actions (edit pencil + view eye). 20 real rows on page 1, a 2nd page exists (pagination control: Previous / 1 / 2 / Next).

### Create (`document_lists` insert)

Screenshot: `screenshots/cityline-contracts/master-document-list-create.png`. Full-page form: Document Category* (dropdown, required, options = the 6 categories from `master-document-categories.md` plus a "Select Document Category" placeholder), Document Name* (required, text), From Date (date), To Date (date).

### Edit (`document_lists/<id>` edit)

Screenshot: `screenshots/cityline-contracts/master-document-list-edit.png`. Same fields as Create, pre-filled (e.g. record Id 1: Document Category "JOIN", Document Name "Certificate").

### View (`document_lists/<id>` view)

Read-only key/value table exposing more raw columns than the form: Id, Category Id, Document Name, Submit To User Id, Display Position, Is Active, Status Date, To Date, Created By, Updated Date, Company Id, Parent Document Id, Document List Types, Mandatory, Page, Current Status, From Date, Created Date, Updated By.

## 4. Data fields

| Field | Type | Required | Notes |
|---|---|---|---|
| Document Category | dropdown (FK) | **Yes** (*) | → Document Categories master |
| Document Name | text | **Yes** (*) | e.g. "Certificate", "PAN Card Copy", "GST Registration Certificate" |
| From Date | date | No | Observed as `0000-00-00` (MySQL zero-date) on inspected record — a real data artifact, not fabricated |
| To Date | date | No | Same `0000-00-00` pattern observed |

Read-only/system fields visible only on the View page: Id, Category Id, Submit To User Id, Display Position (numeric ordering, e.g. "10"), Is Active (raw 0/1), Status Date, Created By, Updated Date, Company Id, Parent Document Id (`0` on inspected record — self-referential FK, unused here), Document List Types, Mandatory (raw 0/1 flag — whether the document is required-for-submission in whatever process consumes this list, e.g. HR onboarding), Page, Current Status, Created Date, Updated By.

## 5. Underlying data structure (inferred)

**Entity: DocumentList** (table `document_lists`, confirmed via URL slug)
- `id`, `company_id`, `category_id` (FK → DocumentCategory), `parent_document_id` (self-referential FK, nullable/0), `document_name`, `submit_to_user_id` (FK → a Users table — not directly explored), `display_position`, `document_list_types`, `mandatory` (bool-ish flag), `page`, `current_status`, `is_active`, `status_date`, `from_date`, `to_date`, `created_by`, `created_date`, `updated_by`, `updated_date`.
- Many-to-one with **DocumentCategory**.

## 6. Functionality / logic observed

- **List/filter/search/paginate/Category-filter** — UI present and interactive; only default "All" state with real data inspected (20 of an unknown total >20 rows, 2 pages).
- **Create/Edit** — forms open and are fully fillable/pre-filled correctly; not submitted (read-only rule).
- **View** — confirmed rendering real data.
- No delete action observed (edit + view icons only, same as Document Categories).

## 7. Inputs and outputs

- Inputs: Create/Edit form (§4), list Search/filter/Category-filter controls.
- Outputs: not independently verified (no record created, per read-only rule). Plausibly consumed downstream by an onboarding/document-checklist workflow elsewhere in the platform (inferred from the category names — JOIN/Pre Joining Checklist strongly suggest HR new-hire document collection, not contract-specific paperwork), and separately by whatever "Documents" tab the Contract entity would have exposed had its UI been reachable (`contracts.md`) via the "Contract" category.

## 8. Data conditions

- Real data spans >20 rows across 2 pages. Page 1 (20 rows): JOIN, Pre Joining Checklist, Vendor Registration, Document Release categories. Page 2 (13 rows) is entirely the **Contract** category, plus 2 malformed rows with blank Document Category and blank Document Name (a genuine data-quality artifact, documented as-is — not fabricated).
- **Important cross-reference**: the 10 named "Contract"-category Document rows on page 2 are — **Contract Documents, Signed Agreement, Confidentiality Agreement (NDA), Payment Terms & Conditions, Supporting Documents, Permits & Licenses, Proof of Identity, Insurance Certificates, Warranty Documents, Additional Note or Attachment** — an exact match (same 10 items) to the indyanet tenant's Contract-detail-form "Documents" tab upload slots. This strongly suggests (though it could not be directly confirmed, since the Contract entity's own UI is unreachable here — see `contracts.md`) that this Contract app's (missing/broken) Documents tab is driven by this same shared master, filtered to `category = "Contract"`, rather than a hardcoded slot list. An 11th row, Document Name **"Test"** under the Contract category, is an obvious leftover test/junk data entry — documented as observed, not cleaned up or excluded.
- `0000-00-00` "zero dates" observed on From Date/To Date for at least one record — a genuine legacy-MySQL data artifact, documented as-is per task rules (no fabrication, no silent correction).
