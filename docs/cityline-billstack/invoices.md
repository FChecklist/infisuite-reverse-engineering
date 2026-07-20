# Invoices — Supplier Bills (Purchase) & Sales Invoices

## 1. Purpose
The core Billstack transaction entities: **Supplier Bill** (AP — money owed to a Vendor) and **Sales Invoice** (AR — money owed by a Customer). This is genuinely new documentation — Bharatnet's Billstack pilot doc (`docs/billstack-bharatnet/`) never reached these create forms (only `00-navigation-map.md`, `customers.md`, `vendors.md` exist there, all nav/masters-level).

## 2. Navigation path
- Supplier Bill: dashboard **Create Invoice** button (Vendar Bills tab) or **New → Purchase Invoices**... actually **New → Purchase Invoices** links to the **list** (`COM_LIST_SUPPLIER_BILLS`), not the create form — the create form is reached via the dashboard's **Create Invoice** button → `index.php?option=COM_INSERT_SUPPLIER_BILLS`.
- Sales Invoice: dashboard **Create Invoice** button (Customer Bills tab) → `index.php?option=COM_INSERT_SALES_INVOICES`. A second, bulk path exists: **Upload Invoices** → `COM_INSERT_SALES_INVOICE_DYNAMIC` (not opened — implies a template/spreadsheet bulk-import flow, distinct from the single-record form documented here).

## 3. UI / page design
Both forms share an identical page chrome: a 3-tab strip (**Supplier Bills**/**Sales Invoices** | **Same Ref Documents** | **Search Item**) and a **Multi Reference Documents** button top-right (implies an invoice can be built by referencing/consolidating multiple source documents — e.g. multiple Purchase Orders — not exercised). Screenshots: `05-create-supplier-bill.png`, `06-create-sales-invoice.png`.

## 4. Data fields

### Supplier Bill (`COM_INSERT_SUPPLIER_BILLS`)
| Field | Type | Required | Notes |
|---|---|---|---|
| Transaction Number | text, greyed/disabled | — | auto-generated on save |
| Transaction Date | date, defaults to today | Yes | |
| Opex Cpex | radio: OPEX (default) / CAPEX | — | operating vs. capital expenditure classification |
| Type | dropdown | Yes | 4 real configured values: **Rural Requirement, Urban Requirement, HO Requirements, Project Requirements** — telecom-rollout-flavored, consistent with the CRM Feasibility Types (Software/Ports/Broadband/Railway) also being telecom-flavored |
| Originator | dropdown, default "Billstack Admin" | Yes | options = this tenant's 4 users |
| Cost Center | dropdown | No | |
| Party | dropdown | Yes | FK → Vendor, empty (0 vendors) |
| Payment Terms | dropdown | No | 8 real values: 30 DAYS, 30-45 DAYS, 45 DAYS, 45-60 DAYS, 60 DAYS, 60-90 DAYS, 90 DAYS, ADVANCE |
| Doc Ref No / Doc Ref Date | text / date | No | |
| Additional Information-1 / -2 | collapsible accordion sections | — | not expanded (contents unknown — gap) |
| **Products/Services line items** | repeating table | — | see below |
| Attachment | file upload | No | |
| Internal Note(s) / Printing Note(s) | textarea | No | separate fields — implies notes can be kept internal-only vs. printed on the invoice document |

**Products/Services line item row:**
| Column | Notes |
|---|---|
| Items/Service | dropdown, options include "Other" (free-form line) + real Item master records |
| UOM | dropdown, real unit list: BRL/BOX/CAS/CM/CUF/CUM/DOZ/EAC/FLOZ/FT/GAL/GRM/IN/KG/LTR/MTR/ML/OZ/PAC/PAI/PIE/PIN/LB/QT/RL/SH/SQFT/SQM/TON/YARD/UNIT |
| Qty*, Rate* | number | |
| HSN/Tax* | | India tax classification, required |
| Amount | computed, read-only | |
| (sub-row info) | SO / PO / Invoice Qty / QOH (Quantity On Hand) / Min / Max / GRN Qty, all showing 0 | implies this line-item picker cross-references Sales Order, Purchase Order, and Goods-Received-Note quantities live per item — a real inventory-reconciliation feature, not just a flat price list |

**Order Summary panel:** Sub Total, Discount (radio: ₹ amount / % — default %, + "Discount Overall" vs. presumably "Discount Inline" toggle per-line, not explored), **+ Additional Charges** (dropdown with real configured values: **Shipping 18%, Delivery 18%, Handling 18%, small cart charge**), Grand Total (₹0.00), Rounding Off. A separate **Exclusive/Inclusive/Out Of scope** tax-mode toggle sits next to the line-item table.

**Actions:** Save & Exit, Save & Continue, Cancel (none exercised).

### Sales Invoice (`COM_INSERT_SALES_INVOICES`)
Same shape as Supplier Bill, plus:
| Extra field | Notes |
|---|---|
| Reference Type | greyed/disabled text |
| Sales Orders Ref No | dropdown, **required** |
| Delivery Orders Ref No / Delivery Orders Ref Date | dropdown / date |

...and the Order Summary panel additionally shows **Advance Payments** (₹0.00, with an edit-pencil icon) and **TCS** (Tax Collected at Source, a real Indian tax mechanic already seen configured in the tenant Config record — see `00-navigation-map.md` §5) instead of just Discount+Additional Charges.

## 5. Underlying data structure (inferred)
```
SupplierBill / SalesInvoice
  .transactionNumber (auto), .transactionDate, .opexCpex, .type, .originator → User,
  .costCenter, .party → Vendor|Customer, .paymentTerms, .docRefNo/.docRefDate
  .lineItems → many {item → Item, uom, qty, rate, hsnTax, amount}
              (each cross-checked live against SO/PO/GRN quantities)
  .discount {mode: overall|inline, type: amount|percent}
  .additionalCharges → many AdditionalCharge (e.g. Shipping 18%, Delivery 18%, Handling 18%, small cart charge)
  .taxMode: Exclusive|Inclusive|Out Of scope
  .attachment, .internalNote, .printingNote
  SalesInvoice only: .salesOrderRef (required FK), .deliveryOrderRef, .advancePayments, .tcs
```
`Type` enum (Rural/Urban/HO/Project Requirements) strongly reinforces the inference (already made in the CRM docs) that this Owner's tenant is a telecom/broadband network operator — bills are categorized by rollout-geography type.

## 6. Functionality / logic
- Both forms funnel into the same **Approval Pending → Approved** two-stage workflow visible on the dashboard (Request Date/Transaction Date/Price columns before approval; Response Date/Transaction Date/Price after) — a bill is created, then separately approved (by whom is unconfirmed — no distinct "Approver" field was seen on the create form itself, so approval is presumably a separate action taken from the list/detail view, not exercised).
- The SO/PO/GRN/QOH sub-row data on each line item implies Billstack line items can auto-populate from an already-existing Sales Order / Purchase Order (the "Same Ref Documents" tab and "Multi Reference Documents" button both point at this same document-chaining capability) — not exercised, no such documents exist on this tenant to reference.

## 7. Inputs and outputs
Forms as documented above. Save & Exit / Save & Continue not exercised (would create real invoice records, forbidden by task rules).

## 8. Data conditions
This tenant: **0 Supplier Bills, 0 Sales Invoices** — but the surrounding configuration (Type enum, Payment Terms, Additional Charges, UOM list) is genuinely populated, the same "configured schema, zero transactional records" pattern already seen in CRM's Feasibility module.
