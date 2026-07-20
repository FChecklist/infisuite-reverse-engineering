# Purchase Invoices Table (Approved) & Payments

## 1. Purpose

Two related vendor-bill-lifecycle screens: **Purchase Invoices Table** shows Supplier Bills split by approval state (Approved vs Un-Approved), and **Payments** records money paid out against Vendor bills (or advances).

## 2. Navigation path

- Purchase Invoices Table: Vendor Bills dashboard "0 Invoices" ("Supplier Invoices Approved In the System") tile → `index.php?option=COM_LIST_PURCHASE_INVOICES_TABLE`
- Payments: Vendor Bills dashboard **Payments** button → `index.php?option=COM_LIST_PAYMENTS` (list/search) → **+ Payments** → `index?option=COM_INSERT_PAYMENTS` (create)

## 3. UI / page design

### Purchase Invoices Table (`COM_LIST_PURCHASE_INVOICES_TABLE`)
A 2-tab toggle: **Approved** (badge "0") / **Un-Approved** (badge "0") — identical structure and columns to the "Approved Supplier invoices" widget already seen embedded in the Vendor Bills dashboard (`00-navigation-map.md` §3a): columns **# / Vendor / Invoices Number / Date / Price / Status / Action**. Empty state: "No Bills Created". This page is essentially the **full-page version of the dashboard's embedded Approved/Un-Approved widget**, reachable standalone. No visible create button, filters, or search on this page — it's a pure status view. Screenshot: `38-purchase-invoices-table-approved.png`.

### Purchase Invoices list (`COM_LIST_SUPPLIER_BILLS`, page-titled "Purchase Invoices")
(Documented here rather than in `supplier-bills.md` since it's the list companion to that create form.) Full grid-style chrome: **Search** box, **date-range dropdown** (Today/This Week/This Month/This Year/Yesterday/Previous Week/Previous Month/Previous Year/Custom Date — same 9-option pattern as CRM's date filters), **More** dropdown (export: **CSV** / **Excel**), **Reports** link (→ `COM_STANDARD_REPORT_DOWNLOAD_PDF_PURCHASE_INVOICES&TB=supplier_bills`, a PDF report download — not clicked, would trigger a download), and **+ Purchase Invoices** create button (→ the same `COM_INSERT_SUPPLIER_BILLS` form documented in `supplier-bills.md`). Empty state: "No Purchase Invoices". Screenshots: `35-supplier-bills-list.png`, `36-supplier-bills-date-filter-options.png`, `37-supplier-bills-more-filter.png`.

### Payments list (`COM_LIST_PAYMENTS`)
Search-first layout (no default table/results shown): three FK filter dropdowns — **Vendor**, **Customer**, **Employees** — plus a free-text **Name/Date/ref_no/etc** box and **Search** button. The presence of **Customer** and **Employees** filters alongside Vendor is notable: **Payments is a shared/generic payment ledger covering money paid to vendors, refunds/payments to customers, and employee payments (e.g. reimbursements or payroll-adjacent disbursements)** — broader than just the Vendor Bills side of Billstack. Screenshot: `39-payments-list.png`.

### Payments create form (`COM_INSERT_PAYMENTS`)
Two-panel layout: **Payments** form (left) + **Invoices** panel (right, populated after clicking "Show Invoices"; empty state "No Invoices" pre-selection). Screenshot: `40-payment-create-form.png`.

## 4. Data fields (Payments create form)

| Field | Type | Required | Notes |
|---|---|---|---|
| Payment Date | date picker | **Yes** (*) | defaults to today |
| Transaction Type | radio | **Yes** (*) | Only **"Vendor"** is present/selectable in this form (single radio, checked, no visible siblings) — despite the list page's Vendor/Customer/Employee filter trio, **this create form appears scoped to Vendor payments only**. Customer-side money-in is handled by the separate **Receipts** screen (see `receipts.md`); how Employee payments are created was **not located** — gap. |
| Vendor | searchable dropdown | **Yes** (*) | empty on this tenant (0 vendors) |
| Payment Type | radio: **Cash / Bank** | **Yes** (*) | defaults to Cash. **No online/gateway payment method options exist** — confirms Payments is a manual record-keeping ledger, not a payment-gateway checkout |
| Amount | number | **Yes** (*) | |
| Payment Process | radio: **Invoice Payments / Advance Payments** | **Yes** (*) | defaults to Invoice Payments — Advance Payments presumably records a prepayment not yet tied to a specific bill |
| Due Date | date picker | **Yes** (*) | defaults to today |
| Reference # | text | No | placeholder "Reference number" |
| Remarks | textarea | No | |

**Show Invoices** button: populates the right-hand **Invoices** panel with the selected Vendor's open bills to apply the payment against (their "invoices to settle") — could not be exercised since no Vendor exists to select. Not submitted (task rule).

## 5. Underlying data structure (inferred)

- **Payment** entity, header fields above, likely 1—N link to **SupplierBill** (the bills a given payment is allocated against) when Payment Process = "Invoice Payments" — a standard **payment-allocation** pattern (one payment can partially/fully settle one or more bills).
- `Payment.transactionType` → enum, at minimum Vendor (confirmed); Customer/Employee variants exist elsewhere in the platform (list-page filters) but their creation entry points weren't found in this Billstack login — possibly created from within the Vendor/Customer/Employee master records themselves, or a different module.
- `Payment.paymentType` → enum **Cash / Bank** only — **no payment-gateway/online-payment integration observed anywhere in Billstack** (confirmed absence within the screens reachable from this login; consistent with the tenant Config's blank "Default Payment Mode" and the Customer form's CASH/CREDIT CARD/NET BANKING being manual classification tags, not gateway selectors — see `00-navigation-map.md` §4 and `customers.md` §4).

## 6. Functionality / logic observed

- **Create Payment** — form above, two-step: fill header fields → **Show Invoices** to select which bill(s) it settles → (presumed) Save. Not exercised past the header (no vendor to select).
- **Purchase Invoices Table** — read-only Approved/Un-Approved status view, no actions found (no records to click into — gap, can't confirm whether row actions exist).

## 7. Inputs and outputs

- Input: Payment create form.
- Output (expected, not observed): a new Payment record; would reduce the "Payment Due" figure on the Vendor Invoice dashboard donut (Total/Paid/Payment Due/Yet To Due, see `00-navigation-map.md` §3a) and move the associated bill(s) from due to paid.

## 8. Data conditions

- Payments list shows **no default results** — it's search-gated (must pick a Vendor/Customer/Employee or type a search term first), unlike most other list views in this tenant which show an empty-state illustration by default. Not confirmed whether searching with 0 data returns the same "No Invoices"-style message — not tested (no data exists regardless).
- Every numeric/date default observed across this tenant's forms continues to reflect the live server date (20-07-2026) and `Asia/Kolkata` timezone from the tenant Config.
