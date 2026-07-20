# Masters > Customers

## 1. Purpose

The billing/delivery entity master — distinct from CRM's Company/Contact, this is what Projects bill against (`projects.md`) and what the Deal Item Search's "Customer" filter and Item's "Customer Items" cross-reference key off (`deals.md`, `masters-items.md`). A full customer-master record with GST/tax registration, billing & shipping addresses, payment terms, and (unexpectedly) rental-property tracking.

## 2. Navigation path

Top nav → **Masters** (mega-menu) → **Customers** → `index.php?option=COM_LIST_CUSTOMERS`.

Create form: green **"+ Customer"** button on the list view → `index.php?option=COM_INSERT_CUSTOMERS`.

## 3. UI / page design

**List view** (`masters-customers-01-list-view.png`): titled "Customer List" — notably **simpler** than the Items list view: no Active/Inactive/All filter, no Records/Search/Import/Export controls, no gear icon. Just a heading, **+ Customer** button, **Back** button, and an illustrated "No Customers" empty state. This is the plainest list view found in the whole exploration.

**Create form**: a profile-style page with a banner image + circular photo placeholder + "0" (likely a customer-code/ID placeholder before save) + a persistent top-right **Submit** button, and an **8-tab** sub-navigation: **Overview, Basic Info, Contact Details, Note, Tax Info, Payment and Billing, Attachments, Rental Properties**. Each tab is its own mini-form with its own **Submit** button (suggesting each tab may save independently via its own AJAX call rather than one combined submission — not confirmed, since nothing was submitted).

### Basic Info tab (`masters-customers-02-create-form.png`)
| Field | Type | Required |
|---|---|---|
| First Name | text | **Yes** (*) |
| Middle Name | text | No |
| Last Name | text | **Yes** (*) |
| Email | email | **Yes** (*) |
| Phone | number | No |
| Suffix | text | No |
| Company | text | **Yes** (*) — free text, not a Company FK |
| Display Name As | text | No |
| Parent group | dropdown, default "Select Any One" | No — implies Customers can be organized in a parent/child hierarchy (e.g. head office / branch), empty in this tenant |
| Website | text | No |
| Short Code | text | No |
| Photo | file upload (circular avatar) | No |

### Contact Details tab (`masters-customers-03-contact-details-tab.png`)
Repeatable billing/shipping address blocks (array-style field names, e.g. `billing_address[]`, suggesting multiple billing addresses per Customer):
| Field | Field name | Type | Notes |
|---|---|---|---|
| GST Registration type | `gsttype[]` | dropdown | |
| (GSTIN) | `gstin[]` | text | |
| Billing Address | `billing_address[]` | multi-line text, placeholder "Billing Adderss" [sic] | |
| Country / State / City/Town | `billing_country[]` / `billing_state[]` / `billing_city[]` | cascading dropdowns | Same Geography-master pattern seen throughout the CRM |
| Pincode | `billing_post_code[]` | number | |
| Contact Person | `contact_person[]` | text | |
| (Mobile) | `mobile[]` | number | |
| (Fax) | `fax[]` | number | |
| Make Default | `is_default` | radio | Marks which address block is the default |
| Shipping Address (street) | `shipping_address[]` | multi-line text, placeholder "Street" | |
| Shipping Country/State/City/Pincode | `shipping_country[]` / `shipping_state[]` / `shipping_city[]` / `shipping_post_code[]` | cascading dropdowns + number | |
| (attachment) | file upload | | Per-address-block attachment |

### Note tab
| Field (UI label) | Field name | Notes |
|---|---|---|
| PO Notes | `customer_inv_notes` | placeholder reads "Enter your Customer **Statement** Notes" — **label/placeholder/field-name are all mismatched with each other** on this tab |
| Other Notes | `customer_stmnt_notes` | placeholder reads "Enter your Customer **Invoice** Notes" — same mismatch, in the opposite direction (the two fields' labels and placeholders appear to be swapped relative to their underlying names) |
| (attachment) | file upload | |

### Tax Info tab (`masters-customers-04-tax-info-tab.png`)
| Field | Field name | Type |
|---|---|---|
| Tax Reg. No. | `customer_tax_reg_no` | text |
| VAT/CST Reg. No. | `cst_reg_no` | text |
| PAN No. | `pan_no` | text |
| Apply TDS for this customer | toggle | boolean |
| LUT No. | `lut_no[]` | text (repeatable) |
| Bond No. | `bond_no[]` | text (repeatable) |
| Finance Year | `finance_year[]` | dropdown, default "Select Any One" |
| **+ Add New Row** | button | adds another LUT/Bond/Finance-Year row, each individually **Lock**-able (padlock icon) and removable (red minus icon) |

### Payment and Billing tab (`masters-customers-05-payment-billing-tab.png`)
| Field | Type | Notes |
|---|---|---|
| Preferred Payment Method | dropdown, default "Select Any One" | |
| Payment Trems [sic] | dropdown, default "Select Any One" | |
| Preferred Delivery Method | dropdown, default "Select Any One" | |
| Opening Blance [sic] | number | |
| As of | date, defaulted to current date (20-07-2026) | |
| **"The Vendor Pays Me With"** | text | **Label bug**: this text makes no sense on a *Customer* form — it's near-certainly a copy-pasted leftover from the platform's Vendor create-form template (Masters > Vendor Managements, see `00-navigation-map.md`), not fixed for the Customer context. Strong evidence Customer and Vendor forms share a common template. |
| Credit Limit | number | |
| Account Number | text | |

### Attachments tab
Not screenshotted in detail; a generic file-attachment list, consistent with every other tab's trailing file-upload control.

### Rental Properties tab (`masters-customers-06-rental-properties-tab.png`)
"No Rental Properties" illustrated empty state, no visible add button on the create-form context (likely only addable after the Customer itself is saved). Confirms the underlying platform template supports **property-rental businesses** as a distinct vertical unrelated to BharatNet/CRM — further evidence the Infistaq platform is a generic multi-vertical ERP shell, with unused verticals' UI still present.

### Overview tab (`masters-customers-07-overview-tab.png`)
A read-only two-column summary: left "Basic Info" recap (Name/Contact/Email/Phone, all blank on a new record), right "Rental Properties" (same empty state as its own tab). Functions as a dashboard view of an existing Customer record — on this unsaved new-record form it's naturally empty.

**Action:** **Submit** (present on every tab, top-right and bottom-left; not exercised, per task rules).

## 4. Underlying data structure (inferred)

**Entity: Customer.** Relationships:
- `Customer.parentGroup` → self-referencing (Customer hierarchy)
- `Customer` has many **Billing Addresses** and many **Shipping Addresses** (array-based fields, one marked default)
- `Customer.country/state/city` (billing and shipping, independently) → **Geography**
- `Customer` has many **LUT/Bond/Finance Year** tax rows (India export-bond tax concepts)
- `Customer` has many **Rental Properties** (unused in this tenant/vertical)
- Referenced by: **Project** (`projects.md`), **Item** via Customer Items cross-reference (`masters-items.md`), Deal's Item Search Customer filter (`deals.md`)
- Distinct from **Company** (CRM entity) — Customer is the Masters/billing-side counterpart; no direct FK between the two was observed anywhere in this exploration, meaning (as far as could be determined) a CRM Company and a Masters Customer are entered independently, with no automatic linkage/promotion path found.

## 5. Functionality / logic observed

- **List** (simplified, no filter/import/export chrome — see §3)
- **Create**, 8-tab form (inspected across all tabs, not submitted)
- Edit/Delete/Detail-view: not reachable, 0 Customers exist in this tenant
- Per-tab independent Submit buttons suggest tab-by-tab incremental save, though this could not be confirmed without submitting

## 6. Inputs and outputs

- Input: the 8-tab create form.
- Output (expected, not observed): new Customer record, becomes selectable in Project's Customer field, Item's Customer Items rows, and the Deal Item Search Customer filter.

## 7. Data conditions

- Only 4 fields carry a required asterisk across the entire 8-tab form: First Name, Last Name, Email, Company (all on Basic Info) — every tax/payment/address field is optional at creation time.
- Two confirmed label/copy bugs: the Note tab's label↔placeholder mismatch, and the Payment and Billing tab's "The Vendor Pays Me With" field (a Vendor-form leftover).
- "Company" here is a free-text field, exactly like Lead's `company_name` — reinforcing that this platform default s to text-snapshot company names in several places rather than FK relations, except where explicitly wired (Contact.company, Deal.company).
