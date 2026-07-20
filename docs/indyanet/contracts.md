# Contracts

## 1. Purpose

Central entity of the IndyaNet tenant. Tracks bandwidth/internet-leased-line service agreements between IndyaNet (an ISP/network-infrastructure integrator, brand "BBTel" appears throughout as the internal transaction-ID prefix) and its customers/vendors — e.g. contract "Loadshare Bastar": *"B/W 100 Mbps - 30 days"* for customer Loadshare at a specific site in Chhattisgarh. Each contract also tracks periodic bandwidth "recharge" (like a prepaid plan renewal), documents, an approval workflow, and per-user/per-record permissions.

## 2. Navigation path

Login → lands directly on the Contract module home (`index`/`index.php`, module = Contract, role = Contract Admin). List view: top nav **"New" → Contract**, or Home dashboard **"View all"** link on Customer Contracts → `index.php?option=COM_LIST_CONTRACTS`. Create: green **+ Contract** button on the list page → `index.php?option=COM_INSERT_CONTRACTS`. Edit: pencil icon on any list row → `index.php?option=COM_INSERT_CONTRACTS&Rid=<token>` (same form, pre-filled).

`Rid` token is **double base64-encoded** `id:<n>`, e.g. `Rid=YVdRNk5ERT0=` → base64-decode → `aWQ6NDE=` → base64-decode again → `id:41`. Not an unguessable/opaque ID — trivially reversible, noted as a real (low-severity) observation, not exploited further per read-only scope.

## 3. UI / page design

### List view (`COM_LIST_CONTRACTS`)

Standard Infistaq list template (same pattern as bharatnet Masters/Settings grids): **Active/Inactive/All** radio filter (defaults Active), **Records** page-size dropdown (default 20) + **Search** (min 3 chars), **Status** dropdown-button, **Import** (→ `COM_INSERT_CONTRACT_IMPORT_FILE_ROWS`, bulk CSV/Excel import — not exercised), **Export** (`#!` placeholder in this build — did not navigate anywhere when inspected via href), green **+ Contract** create button, **Back**, small gear icon (column display-attribute config). Table columns: Contract Name, Transaction No, RT Number, Invoice Number, Start Date, End Date, Description, Additional Notes, Contract Owner Name, Contract Owner Email, Contract Owner Number, Action (edit-pencil icon only — no duplicate/view icons observed on this list, unlike the Masters grids elsewhere on the platform). Pagination: 11 pages × 20 rows on this tenant (≈220 real contracts). Screenshot: `screenshots/indyanet/abhishek-contracts-list.png`.

### Detail / edit form (`COM_INSERT_CONTRACTS[&Rid=...]`)

Full-page form (not a slide-over, unlike bharatnet CRM's Lead form) with a **tab strip**: **Contract Details** (default) / **Documents** / **Workflow** / **Associated Contract** / **Permissions** / **Activities**. Screenshots: `screenshots/indyanet/abhishek-contract-edit-form.png` (Contract Details), `-tab-documents.png`, `-tab-workflow.png`, `-tab-permissions.png`, `-tab-activities.png`, and blank create form `abhishek-contract-create-form-blank.png`.

Two bottom-right action buttons beside **Submit Contract**: **Recharge Plans** and **Recharge History**, each opening a modal dialog (see §6).

## 4. Data fields

### Contract Details tab

| Field | Type | Required | Notes |
|---|---|---|---|
| Contract Name | text | **Yes** (*) | e.g. "Loadshare Bastar" |
| Contract ID | text | **Yes** (*) | Free-text external/transaction ID, e.g. "BBTelJAF03" — the "BBTel" prefix plus a per-ISP code (JAF/AAF/BSNL/Hathway/AL/SIG seen) suggests this ID is assigned per originating ISP circuit, not system-generated |
| RT Number | text | **Yes** (*) | Telecom "RT" (route/circuit) reference number, e.g. "RT-579" — often blank in real data |
| Invoice Number | text | **Yes** (*) | Was empty on every record inspected despite being marked required — required-but-often-blank in real data is a genuine data-quality observation, not an assumption |
| Contract Owner Name | text | **Yes** (*) | Customer-side contact, e.g. "Pijush" |
| Contract Owner Email | email | No | |
| Contract Owner Contact Number | tel | No | |
| Party Type | radio: Customer / Vendor | **Yes** (*) | Determines which master (Customer or Vendor) the Party Name dropdown pulls from |
| Party Name | searchable dropdown + "Add New Party" button | No | FK → Customer or Vendor master depending on Party Type |
| Associated Contract | searchable dropdown | No | FK → another Contract record (self-referential; e.g. links a renewal to its predecessor) |
| Effective Date | date picker | **Yes** (*) | |
| Expiration Date | date picker | **Yes** (*) | |
| Description | text | No | Free text summary, e.g. "B/W 100 Mbps - 30 days" |
| Additional Notes | multi-line text | No | Frequently used to stash unstructured extra data (landline numbers, account numbers, Wi-Fi IDs) — see §8 |
| Termination Condition | text | No | Empty on all inspected records |
| State | text (free text, **not** a Geography FK dropdown unlike bharatnet CRM's Lead form) | No | |
| ISP | text | No | Empty on all inspected records despite ISP being encoded in Contract ID/Transaction No prefixes |
| Paid By | text | No | |
| Internet Charges | text | No | |
| Hub SPOC Name | text | No | |
| Hub SPOC Number | text | No | |
| WiFi Provider | text (placeholder mislabeled "Enter Wifi Pass") | No | UI copy bug — WiFi Provider field shares its placeholder text with the WiFi Pass field below it |
| Wifi Pass | text (plaintext, not a password-masked input) | No | Stores Wi-Fi passwords in plain text in the UI — flagged as a real observed security-hygiene weakness, not fabricated |
| Approval Status | dropdown | **Yes** (*) | Seen value: "Approved". Other options not enumerated (would require opening the dropdown on a real record — gap) |
| Auto Renewal | checkbox | No | Unchecked on inspected record |

### Documents tab

10 independent file-upload slots, each single-file (`Choose File` / "No file chosen"), no evidence of multi-file upload per slot: Contract Documents, Confidentiality Agreement (NDA), Supporting Documents, Proof of Identity, Warranty Documents, Signed Agreement, Payment Terms & Conditions, Permits & Licenses, Insurance Certificates, Additional Note or Attachment. Single **Submit Documents** button for all 10 at once.

### Workflow tab

Single **Start Workflow** button, no other visible content until started — implies an approval/routing workflow engine exists but its steps are not visible until initiated (not exercised — would create a real workflow instance).

### Associated Contract tab

Not separately captured beyond the dropdown already present on Contract Details (gap — did not screenshot this tab's dedicated content).

### Permissions tab

Per-record ACL grant form: **Select User** (required dropdown, platform Users master) + 7 checkboxes: Edit Permission, Delete Permission, Document Permission, Workflows Permission, Activity Permission, Associated Permission, Permission Permission (sic — grants the right to manage other users' permissions on this record). **Submit Permissions** button. Confirms contract records support fine-grained, per-user, per-record access control layered on top of the coarser module-level Role (Contract Admin/User).

### Activities tab

Read-only reverse-chronological audit log, one entry per field-level update: *"The contract activity 'CONTRACT' was updated on <date> at <time>."* — the activity type string is always the generic literal `"CONTRACT"` rather than naming the specific field changed, so the log confirms *that* and *when* a change happened but not *what* changed (a real granularity limitation, not an assumption). The inspected record ("Loadshare Bastar") had 30+ activity entries spanning 10-05-2025 through 08-26-2025.

## 5. Underlying data structure (inferred)

**Entity: Contract**
- Core fields as in §4.
- `Contract.party` → polymorphic FK to either **Customer** or **Vendor** master, discriminated by `Party Type`.
- `Contract.associatedContract` → self-referential FK to another **Contract** (renewal/linked-agreement chain).
- `Contract.owner*` fields are a denormalized snapshot of a contact (name/email/phone), not a FK to Customer/User — consistent with the bharatnet CRM pattern of snapshot fields for external contacts.
- **RechargePlan** / **RechargeHistory** — child entity keyed to Contract, fields: Plan Type (enum: Monthly/Quarterly/Half Yearly/Yearly), Activation Date, and (from the History table's empty-state column headers) Recharge Date, Expiry Date, Remaining Days, Status. Not confirmed whether Recharge Plans and Recharge History are the same underlying table (plan-then-executed) or two separate tables — **gap**, no populated recharge data was found on any inspected contract to verify.
- **Document** attachments (10 typed slots) — likely one child table keyed to Contract with a `document_type` enum, given the fixed slot set.
- **Permission** grants — child entity: (Contract Id, User Id, permission-flags bitset).
- **Activity** log — child entity: (Contract Id, timestamp, generic type literal).
- `Contract.approvalStatus` — enum, only "Approved" observed; likely also includes a pending/rejected state given the field is presented as a workflow-linked dropdown (Workflow tab exists) — **inferred, not confirmed**.

## 6. Functionality / logic observed

- **List/filter/search/paginate** — confirmed working, real data.
- **Create** (`+ Contract` → blank `COM_INSERT_CONTRACTS`) — form opens and is fully fillable; not submitted (rule: no real records created).
- **Edit** (pencil icon → same form pre-filled) — confirmed, all fields populate correctly from a real record.
- **Import** — button present, routes to a dedicated `COM_INSERT_CONTRACT_IMPORT_FILE_ROWS` page; not opened/exercised (gap).
- **Export** — button present but `href="#!"` (dead link in this build) — likely JS-driven (e.g. triggers a file download via AJAX) rather than a navigable URL; not exercised further.
- **Status** dropdown-button on the list toolbar — purpose not confirmed (likely bulk status change on selected rows) — **gap**, not opened.
- **Recharge Plans** (button on detail form) → modal **"Select Recharge Plan"**: Select Plan dropdown (Monthly/Quarterly/Half Yearly/Yearly), Activation Date picker (defaulted to today, 07/20/2026), Cancel/Save. Not saved (rule: no real records created).
- **Recharge History** (button on detail form) → modal **"Recharge History"**: table with columns S.N, Recharge Date, Activation Date, Expiry Date, Plan Type, Remaining Days, Status. Empty state "No recharge history found." on the inspected record.
- **Permissions** — per-record, per-user grant form (see §4). Not submitted.
- **Workflow** — "Start Workflow" button present, not exercised (would create a real workflow instance, forbidden by task rules).
- **Activities** — read-only, auto-populated, no user-facing actions.

## 7. Inputs and outputs

- Inputs: the Create/Edit form (§4), the Recharge Plan modal, the Permissions form, file uploads on the Documents tab, Import (file upload, not tested).
- Outputs (expected from list/dashboard wiring, not independently re-verified per-field): a new/updated Contract record updates the Home dashboard's Contracts/Active/Expired stat tiles, the Monthly Overview chart, the Customer/Vendor Contracts tables, and the Expiring/Expired Contracts widgets by date-bucket.

## 8. Data conditions

- **Active/Inactive/All** filter on the list — default Active; not verified whether "Inactive" is a soft-delete flag or a distinct status (gap).
- Required-field asterisks are enforced only visually in this pass (form not submitted, so actual client/server-side validation behavior — e.g. whether Invoice Number's "required" marker is truly enforced given it's blank on every real record — is **inferred, not confirmed**).
- The Home dashboard's Expiring/Expired widgets bucket contracts by day-ranges (7/30/31-60/61-90 days for expiring; 7/30/60/90 days for expired) — purely date-driven, confirmed by the widget structure though this tenant showed "No contracts in this range" / "(0)" everywhere at the time of inspection despite the list clearly containing ~220 real, mostly-expired-looking contracts (many End Dates are in early-to-mid 2025, well before this session's system date) — **this is a discrepancy worth flagging**: either the widgets recompute on a cache/cron rather than live, or "Expired" here means something narrower than "End Date has passed" (e.g. tied to Approval Status or Auto Renewal). Documented as an observed inconsistency, not resolved.
- `Party Type` (Customer/Vendor radio) gates which master the `Party Name` dropdown searches — confirmed by field adjacency and label, not by directly toggling and re-observing dropdown contents (gap).
