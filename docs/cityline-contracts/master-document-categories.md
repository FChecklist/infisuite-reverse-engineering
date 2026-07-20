# Master → Document Categories

## 1. Purpose

A shared lookup/master table of document categories, used to group items in the sibling "Document" master (`master-documents.md`). Not scoped to the Contract module alone — a **Module** field/filter (values: All, CRM, Billstack, Contract) shows this table is a cross-app master shared by the whole Infisuite platform for this tenant, reused for e.g. HR-onboarding-flavored categories ("JOIN", "Pre Joining Checklist") that have nothing to do with contracts. The sample data (see §8) confirms this is real, pre-existing, shared platform data rather than something scoped to Contracts specifically.

## 2. Navigation path

Top nav **Master → Organization → Document Categories** → `/contracts/list/COM_LIST_DOCUMENT_CATEGORIES`. Create: **Add New** button on the list → `/contracts/insert/document_categories`. Edit: pencil icon on a row → `/contracts/edit/document_categories/<id>`. View: eye icon on a row → `/contracts/view/document_categories/<id>`.

Unlike the Contract entity, all four routes here work correctly both via in-app navigation and via direct/hard URL load.

## 3. UI / page design

### List (`COM_LIST_DOCUMENT_CATEGORIES`)

Screenshot: `screenshots/cityline-contracts/master-document-categories-list.png`. Toolbar: a "Select options" multi-select box (purpose not confirmed — likely a bulk-action or column-picker; not exercised, gap), **Active/Inactive/All** radio filter (default **All**, unlike the Dashboard's Contract filters which default to All too but the Master lists default differs from the indyanet Contract list's default-Active — a per-page difference worth noting), **Add New** button, **Back** button, **Records** page-size selector (20/30/.../100, default 20), **Search** box ("Min 3 Characters Required"), and a **Module setup** dropdown filter (All / CRM / Billstack / Contract). Table columns: Id, Module, Category Name, Category Description, Is Active, Actions (edit pencil + view eye icons only). Pagination control present (single page of data at the time of inspection: 6 rows).

### Create (`document_categories` insert)

Screenshot: `screenshots/cityline-contracts/master-document-categories-create.png`. Full-page form (not a modal/slide-over): Module (dropdown, unlabeled options not enumerated — gap), Category Name* (required, text), Category Description (text), From Date (date), To Date (date). Single **Save**-equivalent submit button (label not captured distinctly from Update — see Edit).

### Edit (`document_categories/<id>` edit)

Screenshot: `screenshots/cityline-contracts/master-document-categories-edit.png`. Same field set as Create, pre-filled. Submit button labeled **Update**.

### View (`document_categories/<id>` view)

Screenshot: `screenshots/cityline-contracts/master-document-categories-view.png`. Read-only key/value table, notably exposing **more raw DB columns than the edit form surfaces**: Id, Module Setup Id, Category Description, Current Status, From Date, Created Date, Updated By, Company Id, Category Name, Is Active, Status Date, To Date, Created By, Updated Date. This confirms the underlying table has audit columns (Created By/Date, Updated By/Date, Status Date, Current Status, Is Active as a raw 0/1 flag distinct from the "Is Active" list column) not exposed on the create/edit form.

## 4. Data fields

| Field | Type | Required | Notes |
|---|---|---|---|
| Module | dropdown | not marked required | Values not enumerated (gap); the list-page's equivalent **Module setup** filter shows All/CRM/Billstack/Contract, suggesting this maps to those 3 apps |
| Category Name | text | **Yes** (*) | e.g. "JOIN", "Vendor Registration" |
| Category Description | text | No | Often blank in real data (e.g. row Id 1 "JOIN" has no description; row Id 6 "Saree" also blank) |
| From Date | date | No | |
| To Date | date | No | |

Read-only/system fields visible only on the View page (not editable via the form): Id, Module Setup Id, Current Status, Created Date, Updated By, Company Id, Is Active, Status Date, Created By, Updated Date.

## 5. Underlying data structure (inferred)

**Entity: DocumentCategory** (table likely named `document_categories`, confirmed via URL slug)
- `id`, `company_id`, `module_setup_id` (FK → an app/module registry — All/CRM/Billstack/Contract), `category_name`, `category_description`, `current_status`, `is_active`, `status_date`, `from_date`, `to_date`, `created_by`, `created_date`, `updated_by`, `updated_date`.
- Referenced by **DocumentList** (`master-documents.md`) via a `category_id` FK.
- `is_active` observed as `"0"` on every inspected record (Id 1 and implicitly others) despite those records appearing in the "Active" and "All" filtered views without issue — a data-quality observation worth flagging: the raw flag being `0` does not obviously correlate with the list-page's Active/Inactive filter behavior as tested (gap: did not toggle to "Inactive" only and compare).

## 6. Functionality / logic observed

- **List/filter (Active/Inactive/All)/search/paginate/Module-setup-filter** — UI present and interactive; only the default "All" state was inspected with real data (6 rows). Search and the other filter states not exercised beyond opening them (gap).
- **Create** (`Add New`) — form opens, fully fillable; not submitted (read-only rule).
- **Edit** (pencil icon) — form opens pre-filled correctly from a real record; not submitted.
- **View** (eye icon) — read-only detail, confirmed rendering real data correctly.
- No delete action was observed on the list rows (only edit + view icons).

## 7. Inputs and outputs

- Inputs: Create/Edit form fields (§4), list-page Search/filter/Module-setup controls.
- Outputs: a new/updated Document Category would presumably become selectable in the Document master's "Document Category*" dropdown (`master-documents.md`) and in this list's own Category filter used there — not independently re-verified since no record was created (read-only rule).

## 8. Data conditions

- 6 real records present at time of inspection: JOIN, Pre Joining Checklist, Vendor Registration, Document Release, **Contract**, Saree — i.e., a category literally named "Contract" exists in this shared master, used by the Document master's "Contract" category documents (not confirmed to link to the Contract entity's own Documents tab, since that entity's UI is unreachable — see `contracts.md`).
- The mix of HR-onboarding categories (JOIN, Pre Joining Checklist) alongside "Contract" and an odd one-off "Saree" confirms this table's data was **not created for or scoped to this Contract app alone** — it's inherited/shared platform master data, consistent with the Module setup filter (All/CRM/Billstack/Contract) gating which app a category belongs to.
