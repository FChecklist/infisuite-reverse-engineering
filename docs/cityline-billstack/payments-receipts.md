# Payments & Receipts

## 1. Purpose
Manual/internal ledgers recording that a payment (against a Supplier Bill) or a receipt (against a Sales Invoice) occurred — the settlement side of the AP/AR cycle documented in `invoices.md`.

## 2. Navigation path
- Payments: dashboard "Payments" button (Vendar Bills tab) → `index.php?option=COM_LIST_PAYMENTS`.
- Receipts: dashboard "Receipts" button (Customer Bills tab) → `index.php?option=COM_LIST_RECEIPTS`.

## 3. UI / page design
Both are filter-only list pages (no visible results table/empty-state placeholder rendered without first searching — different from the Masters "No Records" pattern):

- **Payments**: filters **Vendor**, **Customer**, **Employees** (all "Select Any ...") + a free-text "Name/Date/ref_no/etc" search + Search button + **+ Payments** create button.
- **Receipts**: filter **Customer** only + same free-text search + Search + **+ Receipts** create button.

Screenshots: `09-payments-list.png`, `10-receipts-list.png`.

**Notable finding:** Payments filters by **Vendor, Customer, AND Employees** — i.e. this is a **single unified payment ledger**, not vendor-only despite being reached from the Vendor Bills tab. The "Employees" filter option is unexpected for what the dashboard frames as a vendor-invoice-payment feature — implies Payments also covers non-invoice disbursements (e.g. expense reimbursements/payroll-adjacent payments to employees), a broader scope than Bharatnet's pilot (which didn't reach this page) could have inferred from the dashboard alone.

## 4–6. Data fields / structure / functionality
Create forms (`+ Payments`, `+ Receipts`) not opened this pass (time-budget gap, flagged below). Confirmed no online-payment-gateway integration surface anywhere in the chrome/Masters/Settings explored (same gap Bharatnet's pilot flagged) — these read as manual "record that money moved" ledgers, consistent with the Config record's blank "Default Payment Mode" field.

## 7. Inputs and outputs
Filter forms as above; Search/Create not exercised.

## 8. Data conditions
This tenant: 0 records in both (implied by 0 invoices existing to pay/receive against).

## Known gap
The **Payments** and **Receipts** create forms themselves were not opened — this is the single largest unexplored surface in this Billstack pass. Their field sets (payment mode, bank/cash account, reference to which Bill/Invoice is being settled, partial-vs-full payment handling) are unknown and should be a priority for any follow-up session.
