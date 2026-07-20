# Customers

## 1. Purpose

Master record for customers that the tenant sells to / invoices — the counterparty on the "Customer Bills" (sales invoice / accounts receivable) side of Billstack.

## 2. Navigation path

**Not reachable via the Masters mega-menu** (see `00-navigation-map.md` §2 — Masters has no "Customers" column for this Billstack login). Reached only via the Customer Bills dashboard tab: the "0 Customers" counter tile, "TOP 3 Customers" widget, or "Recent Customers" widget, all linking to `index.php?option=COM_LIST_CUSTOMERS`.

Create: **+ Customer** button on the list page → `index.php?option=COM_INSERT_CUSTOMERS`. Unlike Vendors, there is **no separate "Invite Customer" flow** visible on this list page (Vendors has both New and Invite; Customers has only New) — though an "INVITE CUSTOMER" entry does exist in the platform-wide Settings tree under CUSTOMER MANAGEMENTS (see CRM nav doc), not exercised here.

## 3. UI / page design

**List view**: same bare empty-state pattern as Vendors — heading "Customer List", illustration, "No Customers" text, single **+ Customer** button, **Back**. No filter/search/Import/Export chrome visible in the empty state (same caveat as Vendors: unconfirmed whether it appears with ≥1 record — gap). Screenshot: `16-customers-list.png`.

**Create form** (`COM_INSERT_CUSTOMERS`): a **profile-style page** like Vendors — cover banner, circular avatar placeholder, single **Submit** button top-right (no separate Edit/Save split like Vendors' Overview). Below the banner, an **8-tab strip**: **Overview | Basic Info | Contact Details | Note | Tax Info | Payment and Billing | Attachments | Rental Properties**. Each tab is its own mini-form with its own **Submit** button — i.e. **each tab appears to be saved independently**, not as one combined multi-section submit (contrast with Vendors, which has one Overview page with everything and a single Save). This is a structurally different (richer, more page-like) create flow than Vendors. Screenshot: `17-customer-create-form.png` (Basic Info, default active tab).

## 4. Data fields (by tab)

### Overview (read-only summary, not an input tab)
Shows **Basic Info** (Name / Contact / Email / Phone, all blank/"-" pre-save) and a **Rental Properties** panel (empty state "No Rental Properties") side by side. Screenshot: `25-customer-overview-tab.png`.

### Basic Info
| Field | Type | Required | Notes |
|---|---|---|---|
| First Name | text | **Yes** (*) | |
| Middle Name | text | No | |
| Last Name | text | **Yes** (*) | |
| Email | text | **Yes** (*) | |
| Phone | number | No | |
| Suffix | text | No | e.g. Jr./Sr. |
| Company | text | **Yes** (*) | |
| Display Name As | text | No | |
| Parent group | dropdown | No | default "Select Any One" — options not enumerated (gap); implies **hierarchical customer grouping** (parent/child customer accounts) |
| Website | text | No | |
| Short Code | text | No | |
| Photo | file upload | No | circular preview, "Change Image" |

### Contact Details
Same **GST & Billing Details** block structure as the Vendor create form (repeatable "Add More" pattern, "Make Default"/"Remove" per block):
| Field | Type | Required | Notes |
|---|---|---|---|
| GST Registration type | dropdown | No | Regular / Composition / Casual (same 3 options as Vendors, not re-screenshotted — confirmed same underlying lookup) |
| GST No. | text | No | |
| Billing Address | textarea | No | |
| Country / State / City-Town / Pincode | cascading dropdowns + number | No | |
| Contact Person | text | No | |
| Mobile | number | No | |
| Fax | number | No | |
| "My shipping address is the same as my billing address" | checkbox | No | |
| Shipping Address, Country/State/City-Town/Pincode | textarea + cascading dropdowns | No | independent block, mirrors billing |

Screenshot: `18-customer-contact-details-tab.png`.

### Note
| Field | Type | Required | Notes |
|---|---|---|---|
| PO Notes | textarea | No | **Label/placeholder mismatch (bug, observed as-is)**: field label reads "PO Notes" but its placeholder text reads "Enter your Customer **Statement** Notes" |
| Other Notes | textarea | No | placeholder "Enter your Customer **Invoice** Notes" |

Screenshot: `24-customer-note-tab.png`.

### Tax Info
| Field | Type | Required | Notes |
|---|---|---|---|
| Tax Reg. No. | text | No | |
| VAT/CST Reg. No. | text | No | legacy pre-GST Indian tax registration (VAT/Central Sales Tax) — kept for backward compatibility |
| PAN No. | text | No | (not marked required here, unlike Vendor's PAN which is required) |
| Apply TDS for this customer | toggle switch | No | off by default — per-customer flag for whether TDS withholding applies to their invoices |
| LUT No. | text | No | "Letter of Undertaking" number — Indian GST mechanism allowing export of goods/services without paying IGST upfront |
| Bond No. | text | No | alternative to LUT for the same export-without-tax-payment purpose |
| Finance Year | dropdown + **Lock**/**Remove** buttons | No | default "Select Any One"; **repeatable row** via "+ Add New Row" — implies tax registration details can be tracked **per financial year**, with a Lock action (presumably to freeze a past year's tax data from edits) |

Screenshot: `19-customer-tax-info-tab.png`.

### Payment and Billing
| Field | Type | Required | Notes |
|---|---|---|---|
| Preferred Payment Method | searchable dropdown | No | Options: **CASH / CREDIT CARD / NET BANKING**. These read as **manual payment-method classification tags** for how a customer typically pays, not a live payment-gateway selector — no gateway names (Razorpay/PayU/Stripe/etc.) appear anywhere in this form. Screenshot: `21-customer-payment-method-options.png` |
| Payment Trems [sic] | dropdown | No | default "Select Any One"; options not re-captured here but presumed identical to Vendor's Payment Terms (ADVANCE/30/45/60/30-45 DAYS) by pattern — **not directly confirmed for Customers, inferred by analogy** |
| Preferred Delivery Method | dropdown | No | options not enumerated (gap) — likely Email/Post/Courier for invoice delivery |
| Opening Balance | text | No | |
| As of | date picker | No | **defaulted to today's date** (20-07-2026 at time of testing), confirming server-side "now" |
| The Vendor Pays Me With | text | No | **Label bug (observed as-is)**: this field's label says "The Vendor Pays Me With" on a **Customer** form — almost certainly a copy-paste leftover from the Vendor form template, since a company doesn't have a "vendor" paying a "customer" in this direction |
| Credit Limit | text | No | |
| Account Number | text | No | |

Screenshot: `20-customer-payment-billing-tab.png`.

### Attachments
Single **File Upload** drag-and-drop zone, capped at **"Upload up to 2 files"**. Screenshot: `22-customer-attachments-tab.png`.

### Rental Properties
Empty-state list ("No Rental Properties") with the same illustration style as the Vendors/Customers empty list — **no visible "+ Add" affordance on the create-new-customer flow** (may only become available after the customer record is saved). Confirms the platform's generic customer entity supports an optional **property-rental billing vertical** (tenant/landlord use case) alongside the ISP/telecom-style billing Bharatnet actually uses — **not exercised further, out of scope for this tenant's real usage**. Screenshot: `23-customer-rental-properties-tab.png`.

## 5. Underlying data structure (inferred)

**Entity: Customer**, split into the 7 functional sections above (Basic Info / Contact Details (GST+Billing) / Note / Tax Info / Payment and Billing / Attachments / Rental Properties), each independently submittable — suggesting each maps to its own child table (`customer`, `customer_gst_details` (repeatable), `customer_tax_info` (repeatable per Finance Year), `customer_notes`, `customer_payment_billing`, `customer_attachments`, `customer_rental_properties`) joined on a customer ID, versus Vendor which reads as more denormalized/one-page.

- `Customer.parentGroup` → self-referential FK, implying parent/child customer hierarchies (e.g. head-office + branch billing accounts).
- `Customer` ↔ **Rental Property** (1—N, inferred) — an optional vertical-specific extension.
- Tax Info's per-**Finance Year** repeatable rows imply a `customer_tax_info` table keyed by (customer_id, finance_year), each independently lockable — consistent with needing to preserve historical tax registration state across audits even if current-year details change.

## 6. Functionality / logic observed

- **Create** — via "+ Customer", multi-tab profile form. Each tab has its own **Submit**, implying **incremental/independent save per section** rather than one atomic multi-section transaction (contrast with Vendors' single combined Save). Not submitted (task rule).
- No List/Search/Filter/Edit/Delete/Import/Export functionality was reachable — bare empty list. **Gap**, not confirmed absent.
- "Apply TDS for this customer" and the Vendor-side TDS/TCS clearing accounts in the tenant Config (see `00-navigation-map.md` §4) together suggest Billstack computes/withholds TDS on customer invoices when this flag is on — **not observed in action**, no invoice exists to verify.

## 7. Inputs and outputs

- Input: the 7 create-form tabs above.
- Output (expected, not observed): a new Customer record; would increment the "0 Customers" dashboard counter and populate "TOP 3 Customers"/"Recent Customers" widgets.

## 8. Data conditions

- Required fields (Basic Info only, the only tab with visible red-asterisk requireds): First Name, Last Name, Email, Company. All Contact Details/Note/Tax Info/Payment and Billing/Attachments fields are optional.
- "As of" date field defaults to the current server date — confirms server timezone/clock is live and reachable via this form (matches the tenant Config's `Asia/Kolkata` timezone setting).
- Two label/copy bugs documented as observed, not fixed/assumed: "PO Notes" field with a "Statement Notes" placeholder, and a Vendor-oriented "The Vendor Pays Me With" label surfacing on the Customer form — both suggest the Customer form was built by cloning the Vendor form and not all copy was updated.
