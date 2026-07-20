# Display Attributes

## 1. Purpose

A platform-wide admin tool that configures how each underlying database table is rendered as a list/grid view (view type, and — inferred — which columns show, matching the "gear icon" column-picker seen on every Masters list view, e.g. Items' 84-field `displayattributesfilterfields[]`, see `masters-items.md` §4). This page is also the single richest source of the platform's **raw internal table names**, directly exposing the underlying schema.

## 2. Navigation path

Top icon bar → 2nd icon (black circle "robot" glyph) → `index.php?option=COM_LIST_DISPLAY_ATTRIBUTES`.

Edit: pencil icon per row → `index.php?option=COM_INSERT_DISPLAY_ATTRIBUTES_PRE&Rid=<encoded-id>`.
Create: green **"+ Display Attributes"** button → `index.php?option=COM_INSERT_DISPLAY_ATTRIBUTES_PRE` (no Rid).

## 3. UI / page design

**List view** (`02-display-attributes.png`): standard Masters-style list template — Active/Inactive/All filter, Records page-size + Search ("Min 3 Characters Required" — note: typing into this box did not visibly filter results in this session's testing without an explicit trigger; possibly requires a debounced re-render not observed, or a full page reload on Enter that didn't change the visible rows — **minor unconfirmed UI issue**, documented as observed rather than assumed working), Status/Import/Export/+ Display Attributes/Back/gear-icon controls.

**Columns:** Id, Application Table Name, Is Active (button-styled badge), Current Status (pill badge), Status Date, Action (edit pencil only).

**This table has real, populated data** — dozens of pages of rows (pagination shows ~40 pages at 20/page, i.e. roughly 800 rows), one per underlying application table. Sample of the first 20 (alphabetical): `accountsection, account_groups, account_sections, account_type, action_email_settings, action_log, act_categories, additional_charges, amc_account_details, amc_customer_details, amc_product_management, amc_registration_details, announcement_categories, anshul_1, api_hits, api_masters, api_master_mappings, application_attachments, application_attach_labels, application_check_lists`.

Notable: **`anshul_1`** — a table name that looks like a developer's personal scratch/test table (a first name + number) left in the production schema, not a real business entity. Flagged as an authentic artifact, not fabricated.

This confirms (directly, not inferred) that the platform's CRM tables are internally named `crm_leads`, `crm_contacts`, `crm_companies`, `crm_deals`, `crm_campaigns`, `crm_projects`, `crm_feasibilities` (deduced from the `TRANST=`/route values observed throughout `leads.md` etc., and consistent with this table's naming convention).

## 4. Edit / Create form (`display-attributes-02-edit-form.png`)

A minimal 2-field form:
| Field | Type | Required |
|---|---|---|
| Select Table | dropdown, default "Select Any One" | **Yes** (*) |
| Select View Type | dropdown, default **"Simple List"** | **Yes** (*) |

**Observed anomaly:** opening the Edit action for an existing row (`accountsection`, Id 50) did **not** pre-populate the Select Table field with "accountsection" — it rendered the same blank "Select Any One" state as a fresh Create. This is either a genuine bug (edit-prefill failing) or this screen is intentionally a single shared "configure a table's display" launcher that requires re-picking the table even in "edit" mode, which then presumably reveals a second step (column selection, matching the field-picker seen on Items) once a table is chosen. Only the Select View Type dropdown's other options and the second-step column UI were **not** explored further — **gap**, given this is a low-priority admin/config screen relative to core CRM data.

## 5. Underlying data structure (inferred)

**Entity: DisplayAttribute**, one row per application table, with at minimum: `table_name`, `is_active`, `current_status`, `status_date`, and (inferred from the Select View Type field + the Items module's field-picker) a `view_type` and a set of selected display columns. This is a **metadata table describing the platform's own other tables** — a generic, schema-driven list/grid rendering system rather than hard-coded per-module UI, which explains why every Masters list view (Items, Customers not — Customers' simpler list may not go through this system, Users, Display Attributes itself) shares the exact same visual template.

## 6. Functionality / logic observed

- **List** (Active/Inactive/All filter, search, configurable page size) — ~800 rows, all `Active`/`Active` in the visible sample.
- **Create** / **Edit** (2-field table+view-type picker; edit did not visibly prefill, see §4)
- **Import** / **Export** (links present: Import goes to a generic `COM_INSERT_DYNAMIC_IMPORT&table=display_attributes` handler — confirms Import is a shared dynamic table-import mechanism reused across modules, not bespoke per-module code)

## 7. Inputs and outputs

- Input: table + view type selection (and, inferred, a subsequent column-configuration step).
- Output (expected, not observed): changes which columns/fields render on that table's list view elsewhere in the app (e.g. the Items list's column set, or the 84-field picker seen there).

## 8. Data conditions

- This is a genuinely populated table (~800 rows) unlike almost everything else CRM-side in this tenant — but the data is platform schema metadata, not CRM business data, so it doesn't reveal tenant-specific CRM records.
- The Edit-not-prefilling behavior is documented as observed but not fully explained — flagged as a possible bug rather than asserted as one, since the cause (client routing issue vs. intentional launcher pattern) could not be confirmed without deeper JS/network inspection, which was out of scope for this pass.
