# Vendors (Masters > Vendors)

## 1. Purpose

Master record for suppliers/vendors that the tenant purchases from — the counterparty on the "Vendor Bills" (supplier invoice / accounts payable) side of Billstack. Captures legal/business identity, GST registration, banking details for payment, and payment terms.

## 2. Navigation path

Top nav → **Masters** mega-menu → **Vendors** column → **Vendors** (`index.php?option=COM_LIST_VENDORS`). Also reachable via three dashboard links on the Vendor Bills home tab ("0 Vendors" counter tile, "TOP 3 VENDORS" widget, "Recent Vendors" widget).

Create: **+ New Vendors** button on the list page → `index.php?option=COM_INSERT_VENDORS`. A second creation path, **+ Invite Vendors** → `index.php?option=COM_INSERT_INVITE_VENDOR&transaction_table=vendors`, sends an invitation for the vendor to self-register instead of the admin keying in the record directly.

## 3. UI / page design

**List view** (`COM_LIST_VENDORS`): this tenant has 0 vendors, so the list renders as an empty state — heading "Vendors", an illustration, and text "No Vendors" — with **no visible filter/search/Import/Export chrome** (unlike the generic Masters-style list template documented for CRM and for Display Attributes in this tenant). Only two action buttons are present: **+ New Vendors** and **+ Invite Vendors**, plus **Back**. This is a simpler empty-state than the standard grid template — **unconfirmed whether the full grid chrome (filters/Records/Search/Import/Export/Status) appears once ≥1 vendor exists**; flagged as a gap since it can't be tested without creating a real record. Screenshot: `09-vendors-list.png`.

**Create form** (`COM_INSERT_VENDORS`, "New Vendors"): unlike CRM's slide-over-panel create forms, this is a full **profile-style page** with:
- A cover-photo-style banner + circular avatar placeholder ("NA")
- A row of 6 icon tabs: **HR / Finance / Admin / Project / Location / Legal** (each shows "-" under it) — these did not respond as navigable content in this empty/new-record context; likely become populated/relevant only after the vendor is saved (probably tenant-side workflow-connect summaries mirroring the Admin Connect/Location Connect/Legal Connect fields in the form). **Not exercised further** — gap.
- A single **Overview** tab (the only tab available pre-save)
- Left sidebar: **"Profile Completion"** tracker, 4 rows (Business Information / Company Information / Banking Information / Note), each showing a 0% progress bar — confirms the vendor record is conceptually split into these 4 sections, and the platform tracks completeness per section.
- Main panel: **Overview**, with an **Edit** / **Save** button pair at the top and a second **Save** button at the bottom of the form.

Screenshot: `10-vendor-create-form.png` (full page).

## 4. Data fields (Create form)

### Business Information
| Field | Type | Required | Notes |
|---|---|---|---|
| Company Name | text | **Yes** (*) | |
| Corporate Website | text | No | |
| DBA (Trading Name) | text | No | "Doing Business As" |
| Subsidiary / Division Of | text | No | free text, not a FK picker |
| First Name | text | **Yes** (*) | primary contact's first name |
| Middle Name | text | No | |
| Last Name | text | **Yes** (*) | |
| Email | text | **Yes** (*) | |
| Phone | number | No | |
| Vendor Type | searchable dropdown | No | **Observed empty**: opening it shows "No results found" — the underlying master/lookup table for Vendor Type has no entries configured on this tenant. Gap: cannot enumerate real values. |
| Service Type | text | No | free text, not a dropdown |
| BP Code | text | No | "Business Partner Code" — likely an external/ERP cross-reference code |
| Locations | multi-select listbox + searchable combobox | No | |
| Admin Connect | dropdown | **Yes** (*) | default "Not Applicable"; options not enumerated (gap) — presumably links the vendor to an internal Admin-department workflow owner |
| Location Connect | dropdown | No | default "Not Applicable" |
| Legal Connect | dropdown | No | default "Not Applicable" |

### GST & Billing Details (repeatable block — "+ Add More GST & Billing Details")
| Field | Type | Required | Notes |
|---|---|---|---|
| GST Registration type | dropdown | No | Options: **Regular / Composition / Casual** — the three main GST-registration categories under Indian GST law (Composition = simplified low-turnover scheme, Casual = temporary/occasional-supplier registration). Screenshot: `11-vendor-gst-reg-type-options.png` |
| GST No. | text | No | GSTIN, free text (no client-side format mask observed) |
| Billing Address | textarea | No | |
| Country / State / City-Town | cascading dropdowns | No | same Country→State→City cascade pattern as CRM's Geography masters |
| Pincode | number | No | |
| Contact Person | text | No | |
| Mobile | number | No | |
| Fax | number | No | |
| "My shipping address is the same as my billing address" | checkbox | No | unchecked by default |
| Remove / Make Default | link + radio | — | per-block controls, relevant once "Add More" produces multiple GST/Billing blocks — implies a vendor can have **multiple GST registrations/billing addresses** (e.g. one per state, standard multi-state-GST pattern), with one marked default |
| Shipping Address | textarea | No | placeholder "Street" |
| Country / State / City-Town / Pincode (shipping) | cascading dropdowns + number | No | independent from billing address unless the "same as billing" checkbox is used |

### Company Information
| Field | Type | Required | Notes |
|---|---|---|---|
| Legal Structure | dropdown | No | Options: **Limited Liability Companies / Corporation / Unlisted Public Companies / Listed Companies**. Screenshot: `12-vendor-dropdown-check.png` |
| Company Registration Number | text | No | |
| Company Registration Country | dropdown | No | |
| Payment Terms | dropdown | No | Options: **ADVANCE / 30 DAYS / 45 DAYS / 60 DAYS / 30-45 DAYS** — defines the vendor's billing/payment-due cycle, directly relevant to Supplier Bill due-date computation. Screenshot: `13-vendor-payment-terms-options.png` |
| PAN No. | text | **Yes** (*) | Indian income-tax Permanent Account Number |
| Subsidiary Vendor Check | toggle switch | No | off by default |
| Udyam Registration/MSME | toggle switch | No | off by default — Indian MSME (small business) registration flag, relevant to MSME-payment-timeline compliance rules |
| ENCO Terms/ Shipping Terms | dropdown | No | options not enumerated (gap) |
| Logo | file upload | No | circular preview, "Change Image" |

### Banking Information
| Field | Type | Required | Notes |
|---|---|---|---|
| Bank name | text | **Yes** (*) | |
| Bank Address | textarea | No | |
| Country / State / City-Town / Pincode | cascading dropdowns + text | No | |
| Company Bank Beneficiary Name | text | No | |
| Bank Account Number | number | **Yes** (*) | |
| Type Of Bank Account | dropdown | No | options not enumerated (gap — likely Savings/Current) |
| IFSC Code | text | **Yes** (*) | Indian bank routing code |
| Contact Person Name (Bank related queries) | text | No | |
| Contact no | text | No | |
| Contact Email | text | No | |

### Other Contact Information (fixed 4-row table, all optional)
| Row (Type) | Name | Number | Email Id |
|---|---|---|---|
| Account Receivable Contact | text | number | text |
| Order or Sales Contact | text | number | text |
| Controller | text | number | text |
| Quality Assurance | text | number | text |

No hidden hierarchy/hidden hidden hidden fields observed beyond what's listed (unlike CRM Leads' hidden `working_role_short_code`) — not directly checked via DOM dump, so **not confirmed absent**, just not spotted in the accessibility tree.

**Not submitted** (task rule: no real records created).

## 5. Underlying data structure (inferred)

**Entity: Vendor**, composed of (at minimum) 4 related sub-groups per the Profile Completion tracker: Business Information, Company Information, Banking Information, Note (a "Note" section exists per the tracker but no corresponding fields were found in the Overview form — likely a separate tab/section not surfaced pre-save, **gap**).

- **Vendor 1—N GST & Billing Details** (repeatable block, "Add More") → implies a child table, e.g. `vendor_gst_details`, one row per GST registration/billing address, with a `is_default` flag (the "Make Default" radio).
- `Vendor.adminConnect` / `.locationConnect` / `.legalConnect` → FKs to some internal org-connect entity (Admin/Location/Legal), defaulting to "Not Applicable" — likely ties to the Organization masters (Division/Departments/Cost Centres/Locations) seen in the Masters menu, or to the Users/roles.
- `Vendor.vendorType` → FK to a Vendor Type lookup table, **currently empty on this tenant** (no seed data).
- `Vendor.paymentTerms` → enum (ADVANCE/30/45/60/30-45 DAYS) — drives Supplier Bill due-date logic (inferred, not confirmed against an actual bill).
- `Vendor.legalStructure` → enum (4 values above).
- Banking fields point to a standard single bank-account-per-vendor structure (no "Add More" affordance seen for banking, unlike GST/Billing).

## 6. Functionality / logic observed

- **Create** — via "+ New Vendors" full profile form (fields above). Not submitted.
- **Invite** — via "+ Invite Vendors" (see `masters-vendors-invite` fields below), a lighter-weight form that sends an invitation rather than directly creating a populated record. Two **Invitation Type** modes: **Invite Direct Vendor** (default, selected) vs **Invite Vendor Registration Process** — implies the invited vendor either gets created directly or goes through a self-service registration/approval flow. Not exercised (would send a real invitation).
- No List/Search/Filter/Edit/Delete/Import/Export functionality was reachable — the list is a bare empty state with 0 vendors. **Gap**, not confirmed absent.

### Invite Vendor form fields
| Field | Type | Required |
|---|---|---|
| Company Name | text | **Yes** |
| Contact Person First Name | text | **Yes** |
| Contact Person Last Name | text | **Yes** |
| Contact Person Email Id | text | **Yes** |
| Contact Person Mobile No. | text | No |
| Vendor Type | dropdown | **Yes** |
| Invitation Type | radio: Invite Direct Vendor / Invite Vendor Registration Process | **Yes** |
| Admin Connect | dropdown | **Yes** |
| Location Connect | dropdown | No |
| Legal Connect | dropdown | No |

Submit button: **Send Invitation** (rendered disabled/greyed until required fields are filled — standard client-side validation gating). Not submitted. Screenshot: `15-invite-vendors-form.png`.

## 7. Inputs and outputs

- Input: Create form or Invite form above.
- Output (expected, not observed): a new Vendor record (direct create) or a pending invitation (invite flow); would increment the "0 Vendors" dashboard counter and populate "TOP 3 VENDORS"/"Recent Vendors" widgets.

## 8. Data conditions

- Required fields differ meaningfully between the two creation paths: direct create requires Company Name/First Name/Last Name/Email/Admin Connect/PAN No./Bank Name/Bank Account Number/IFSC Code (8 required fields spread across sections); the lighter Invite form requires Company Name/Contact First+Last Name/Email/Vendor Type/Invitation Type/Admin Connect (6 required fields, no banking/PAN at invite time — banking/tax details are presumably collected later when the invited vendor completes their own registration).
- "My shipping address is the same as my billing address" checkbox is a UI convenience toggle (unchecked by default) — not confirmed what it does on toggle (e.g. auto-copy fields) since not exercised.
- The "Vendor Type" dropdown returning "No results found" is a **data condition specific to this empty tenant** — a fresh/unconfigured tenant, not a platform limitation.
