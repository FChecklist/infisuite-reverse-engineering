# Billstack (Bharatnet) — Summary

System: https://bharatnet.infisuite.in, module "Billstack" on the shared "Infistaq" platform (see `docs/crm-bharatnet/` for the sibling CRM module of the same tenant). Logged in as `billstack_admin@bharatnet.infisuite.in` ("Billstack Admin").

## Documents in this folder

| File | Covers |
|---|---|
| `00-navigation-map.md` | Login, global chrome, Masters/New menus, both dashboard tabs, tenant Config (GST/tax/accounts) |
| `vendors.md` | Vendor master (create form, GST/Payment Terms/Legal Structure options, Invite Vendor) |
| `customers.md` | Customer master (8-tab create form: Basic Info/Contact Details/Note/Tax Info/Payment and Billing/Attachments/Rental Properties) |
| `supplier-bills.md` | Supplier Bill (Vendor invoice) create form, line items, Same Ref Documents, Multi Reference Documents |
| `payments-and-purchase-invoices-table.md` | Payments (Vendor-side money-out) + Purchase Invoices Table (Approved/Un-Approved status) |
| `sales-invoices-and-receipts.md` | **Major gap**: Sales Invoice / Receipt creation and listing are blocked (403) for this login |
| `masters-items-tax-org-users.md` | Items (full ERP-style catalog form), HSN Code, Currencies, Workflow, Organization, Users |

## Data model (entities and relationships, as inferred from UI)

```
Vendor ──1:N── VendorGstBillingDetail (repeatable, one default)
Vendor ──1:1── (Banking info, Payment Terms, Legal Structure)

Customer ──1:N── CustomerGstBillingDetail (repeatable)
Customer ──1:N── CustomerTaxInfo (repeatable, per Finance Year, lockable)
Customer ──self-FK── Customer.parentGroup (hierarchical customer accounts)
Customer ──1:N── RentalProperty (unused vertical extension on this tenant)

Item ──N:1── HsnSacCode (GST rate)
Item ──N:1── HsCode (customs code)
Item ──N:1── Category (up to 3 levels: Category/Child/Sub-Child)
Item ──1:N── VendorItemCrossRef, CustomerItemCrossRef (external part numbers)
Item ──N:1×22── GL Account (Inventory/COGS/Tax Payable/AP/AR/etc., one FK per posting type)

SupplierBill (header) ──1:N── SupplierBillLine ──N:1── Item
SupplierBill ──N:1── Vendor (Party)
SupplierBill ──N:1── CostCentre, TaxState
SupplierBill ──N:1── Originator (User)
SupplierBill ──N:M── prior Transactions (PO/GRN/SO, via Multi Reference Documents)
SupplierBill ──status── Un-Approved | Approved (workflow)
SupplierBill ──1:N── Payment (via Payment Process = "Invoice Payments")

SalesInvoice (inferred mirror of SupplierBill, Party = Customer) ──status── Un-Approved | Approved
  -- create/list/detail screens NOT accessible to this login; structure is inferred only

Payment ──N:1── Vendor | Customer | Employee (Transaction Type; only "Vendor" reachable from this login's create form)
Payment.paymentType ── Cash | Bank (no gateway integration found)

Tenant Config (1 per tenant) ── GSTIN/PAN/CIN (all blank on this tenant), TDS/TCS clearing+payable accounts,
  Default Cash/Tax/Bank Account, Invoice Prefix/Postfix, Number Format (Lakhs), Default Currency (Rupees)

Workflow (process, workflow_level) -- pre-seeded for requisitions/quotations/purchase_orders/
  goods_receipt_notes/quality_check/grn_returns/stock_requests/stock_transfers (up to 8 approval levels each)
  -- confirms a Requisition -> Quotation -> PO -> GRN procurement chain feeds Supplier Bills,
     though none of those upstream screens were found in this login's available menus

User (tenant-wide, shared with CRM module) -- 3 seeded accounts: Anthony Johnston (super-admin),
  CRM Admin, Billstack Admin
```

## Function list (by module)

**Vendors**: List (empty-state only observed) · Create (rich profile form) · Invite (Direct or Registration-Process modes)
**Customers**: List (empty-state only observed) · Create (8-tab profile form, each tab independently submittable)
**Supplier Bills**: List with date-range filter + CSV/Excel export + PDF Report · Create (line-item grid with PO/GRN/SO cross-refs, Exclusive/Inclusive/Out-Of-Scope tax modes, Overall/Inline discount) · Same Ref Documents (duplicate check) · Search Item (utility) · Multi Reference Documents (pull lines from prior transactions)
**Purchase Invoices Table**: Approved/Un-Approved status view (read-only)
**Payments**: Search (by Vendor/Customer/Employee) · Create (Vendor-scoped only from this form; Cash/Bank; Invoice Payments vs Advance Payments; Show Invoices to allocate)
**Sales Invoices / Upload Invoices / Receipts**: **Inaccessible** (403) — see Known Gaps
**Sales Invoices Table**: Approved/Un-Approved status view (read-only) — the one working screen on this side
**Items**: List with Item Type/UoM/Category filters · Create (6-section form: identity, price, inventory, 22 GL account mappings, tax codes, vendor/customer cross-refs)
**HSN Code**: List/Detail — pre-seeded official Indian GST HSN/SAC code + rate table
**Currencies**: List — pre-seeded world currency table, all exchange rates flatly ₹1.00
**Workflow**: List — pre-seeded approval-workflow processes (read-only from this view, edit icon present)
**Organization (Division/Departments/Cost Centres/Locations)**: List only opened for Division (empty)
**Users**: List — 3 tenant-wide accounts, filterable by Location/Country/Reports To/User Type

## Billing/invoice lifecycle states observed or inferred

- **Supplier Bill / Sales Invoice status**: Un-Approved → Approved (2-state workflow, confirmed via the paired list widgets on both dashboard sides and the standalone Purchase/Sales Invoices Table screens). No further states (e.g. Paid/Void/Cancelled) were directly observed as a distinct status field, but the dashboard's "Vendor Invoice"/"Customers Invoice" donut widgets track **Total / Paid / Payment Due / Yet To Due** as a separate payment-status dimension layered on top of the approval status.
- **Payment allocation**: a Payment can be "Invoice Payments" (allocated against one or more existing bills via "Show Invoices") or "Advance Payments" (unallocated prepayment).
- **Billing cycle / payment terms**: Vendor and (inferred) Customer records carry a Payment Terms enum (ADVANCE / 30 DAYS / 45 DAYS / 60 DAYS / 30-45 DAYS) that would drive bill due-date computation; a bill's own Due Date field currently just defaults to Transaction Date and this auto-shift behavior could not be confirmed (no vendor exists to test against).
- **Tax/GST handling**: HSN/SAC-code-driven flat GST% (pre-seeded reference table), Tax State field to determine CGST+SGST vs IGST split, Exclusive/Inclusive/Out-Of-Scope line-level tax mode, and separate TDS/TCS clearing+payable accounts at the tenant-Config level plus a per-Customer "Apply TDS" toggle. GST Registration Type (Regular/Composition/Casual) is captured per Vendor/Customer GST&Billing block. **The tenant's own GSTIN is blank** in Config — a real compliance gap for this tenant, not a software gap.
- **Payment-gateway integration**: **none found**. Payment Type is restricted to Cash/Bank; Customer's "Preferred Payment Method" is Cash/Credit Card/Net Banking as a classification tag, not a live checkout integration; tenant Config's "Default Payment Mode" is blank. No gateway name (Razorpay, PayU, Stripe, CCAvenue, etc.) appears anywhere in the reachable UI. Per task rules, no payment was triggered to test this — this conclusion is based on the complete absence of any gateway-shaped UI element across every billing/payment screen reached.

## Known Gaps

1. **Sales Invoices / Upload Invoices / Receipts are entirely inaccessible** (HTTP 403, "not enough permission") for `billstack_admin`, despite being directly linked from that same account's own dashboard. This is the single largest documentation gap — the actual Customer-side invoice/receipt create forms, their fields, and their real functionality could not be observed at all; everything in `sales-invoices-and-receipts.md` beyond the 403 finding itself is inferred by analogy with the fully-observed Supplier Bill / Payment forms. **Recommend the tenant owner review this account's role/permission configuration.**
2. **Entire tenant is empty of transactional data** — 0 Vendors, 0 Customers, 0 Items, 0 Bills of any kind. This means no detail/edit/view screens, no real approval-workflow interaction, no real payment allocation, and no real invoice PDF/print output could be observed anywhere in this task. All such behavior is either marked "inferred" or flagged as an open gap throughout the docs.
3. **Additional Information-2** panel on the Supplier Bill create form did not visibly expand when clicked — unconfirmed whether empty-by-design or broken.
4. Item Categories, Items Type, Departments, Cost Centres, Locations, Invite Users list/create screens were not individually opened (time-boxed) — assumed to follow the same standard grid template as their siblings, not directly confirmed.
5. Vendor Type, Admin/Location/Legal Connect, ENCO Terms, Type Of Bank Account, and several other dropdowns returned "No results found" or were not clicked through to enumerate options — these lookup tables appear unseeded on this tenant.
6. No Requisition / Quotation / Purchase Order / Goods Receipt Note (GRN) creation or list screens were found anywhere in this Billstack login's Masters or New menus, despite the Workflow master confirming all four processes are configured and despite the Supplier Bill line-item grid displaying live SO/PO/Invoice Qty/GRN Qty reference columns. These upstream procurement screens likely exist in a separate module/role not exposed here.
7. The platform-wide Settings tree (gear icon, 23 categories) was confirmed identical to the CRM tenant's and not re-documented in full — only the one sub-page relevant to billing (General Settings/Config) was opened here.
8. Notifications panel, avatar dropdown (Profile/My Messages/Logout), and Ticket dashboard entry point were assumed identical to the CRM tenant's chrome by analogy and not re-opened/re-confirmed in this session.

## Notable defects/bugs observed (documented as-is, not fixed or assumed intentional)

- **Sales Invoices/Receipts 403 permission gap** (see Known Gaps #1) — the most significant finding.
- "New" top-nav menu shows a static "Purchase Invoices" item regardless of which dashboard tab (Vendor Bills / Customer Bills) is active.
- Customer create form's "Note" tab: field labelled "PO Notes" has a placeholder reading "Enter your Customer **Statement** Notes" (mismatch).
- Customer create form's "Payment and Billing" tab has a field labelled **"The Vendor Pays Me With"** — a Vendor-form label leaked onto the Customer form.
- Masters mega-menu has no "Customers" column (Customers only reachable via dashboard links) — mirrors CRM's Masters menu having no "Vendors" column; consistent asymmetry, not necessarily a bug, but worth noting.
- "Vendar Bills" tab label (typo for "Vendor") is used consistently throughout the dashboard chrome.
- Currencies master: all Exchange Rate values are flatly ₹1.00 and at least one Country↔Currency pairing looks mismatched (ALL/Albania mapped to "Aland Islands").
