# Sales Invoices & Receipts (Customer Bills — Accounts Receivable)

## 1. Purpose

The Customer Bills / accounts-receivable counterpart to Supplier Bills: invoicing customers for goods/services sold, and recording incoming payments (Receipts) against those invoices.

## 2. Navigation path

All four entry points are advertised directly on the **Customer Bills** dashboard tab (see `00-navigation-map.md` §3b) and/or the **New** menu:
- **Upload Invoices** → `index.php?option=COM_INSERT_SALES_INVOICE_DYNAMIC`
- **Create Invoice** → `index.php?option=COM_INSERT_SALES_INVOICES`
- **Receipts** → `index.php?option=COM_LIST_RECEIPTS`
- Dashboard "0 Invoices" ("Sales Invoices Created In the System") → `index.php?option=COM_LIST_SALES_INVOICES`
- Dashboard "0 Invoices" ("Sales Invoices Approved In the System") → `index.php?option=COM_LIST_SALES_INVOICES_TABLE`

## 3. Major finding: this login cannot access most of this area (HTTP 403 / in-app permission wall)

Directly clicking each of the four dashboard links above (not guessed URLs — actual `<a href>` targets already present in the rendered Customer Bills tab) returns the platform's themed **403** error page for **billstack_admin@bharatnet.infisuite.in**, the tenant's Billstack administrator account:

| Screen | URL (`option=`) | Result |
|---|---|---|
| Create Invoice | `COM_INSERT_SALES_INVOICES` | **403** — "SALES INVOICES NOT FOUND / OOPS!! YOU DO NOT HAVE THE REQUIRED PERMISSION". Screenshot: `42-sales-invoice-403-from-dashboard-link.png` |
| Upload Invoices | `COM_INSERT_SALES_INVOICE_DYNAMIC` | **403** — "SALES INVOICE DYNAMIC NOT FOUND / ...". Screenshot: `43-upload-invoices-page.png` |
| Sales Invoices list | `COM_LIST_SALES_INVOICES` | **403** — "SALES INVOICES NOT FOUND / ...". Screenshot: `45-sales-invoices-list-403.png` |
| Receipts | `COM_LIST_RECEIPTS` | **403** — "RECEIPTS NOT FOUND / ...". Screenshot: `44-receipts-list.png` |
| Sales Invoices Table (Approved/Un-Approved status view) | `COM_LIST_SALES_INVOICES_TABLE` | **Works** — loads normally (see §4 below) |

This is documented as an **observed defect/permission gap**, not an inference: these are the exact same routes the dashboard itself links to, reached by literal clicks on the rendered "Upload Invoices"/"Create Invoice"/"Receipts" buttons and the "0 Invoices" counter tile — not URL-guessing (contrast with the CRM tenant's `COM_LIST_LEADS` 403, which *was* a guessed URL and therefore a weaker signal; here the product's own navigation is broken for this account). The 403 page's own copy ("contact your admin") is self-contradictory since this account's display name is literally "**Billstack Admin**".

**Practical consequence**: real Sales Invoice / Receipt functionality could not be observed or documented in this pass — everything below §4 is limited to what's inferable from the dashboard chrome and the one working list (Sales Invoices Table), not from opening the actual create forms. This is the single largest gap in this task's coverage; flagged prominently for whoever owns tenant permissions.

## 4. What IS accessible: Sales Invoices Table (`COM_LIST_SALES_INVOICES_TABLE`)

Loads normally, identical structure to the Vendor-side Purchase Invoices Table (`payments-and-purchase-invoices-table.md` §3): a 2-tab **Approved** (badge "0") / **Un-Approved** (badge "0") toggle over a table titled "Approved Sales invoices" with columns **# / Customer / Invoices Number / Date / Price / Status / Action**. Empty state: "No Invoices Created". Screenshot: `46-sales-invoices-table-check.png`.

This confirms the underlying **SalesInvoice** entity has the same Approved/Un-Approved lifecycle status as SupplierBill, even though the screens that would create/list the underlying records are blocked for this account.

## 5. Underlying data structure (inferred, unverified)

By strong analogy with the fully-observed Supplier Bill (`supplier-bills.md`) and the dashboard's mirrored widget set (Bills vs Paid chart, Total/Paid/Payment Due/Yet To Due donut, Approval Pending/Approved tables — see `00-navigation-map.md` §3b), **SalesInvoice** almost certainly mirrors **SupplierBill**'s shape: header (Transaction Number, Transaction Date, Type, Originator, Cost Center, **Party = Customer** instead of Vendor, Payment Terms, Doc Ref No/Date) + line items (Item/UOM/Qty/Rate/HSN-Tax/Amount) + Order Summary (Sub Total/Discount/Additional Charges/Grand Total/Rounding Off) + an approval workflow. **This is an inference from UI symmetry, not a direct observation** — flagged accordingly since the create form itself could not be opened.

Similarly, **Receipt** is inferred to mirror **Payment** (`payments-and-purchase-invoices-table.md` §4) with Party=Customer instead of Vendor and Payment Type likely Cash/Bank as well (again, no gateway integration expected given the Vendor-side precedent and the tenant Config's blank Default Payment Mode) — **not confirmed**.

## 6. Functionality / logic observed

- None directly — see §3. All create/list actions for Sales Invoices and Receipts are blocked by the platform's own permission check for this login.
- The **Sales Invoices Table** Approved/Un-Approved toggle is the only confirmed-working piece of functionality in this area.

## 7. Inputs and outputs

Not observable — see §3.

## 8. Data conditions

- The permission failure is **specific to certain routes, not the whole Customer Bills area**: `COM_LIST_CUSTOMERS` (Customers master, see `customers.md`) and `COM_LIST_SALES_INVOICES_TABLE` both work fine for this same login, while `COM_INSERT_SALES_INVOICES`, `COM_INSERT_SALES_INVOICE_DYNAMIC`, `COM_LIST_SALES_INVOICES`, and `COM_LIST_RECEIPTS` all 403. This asymmetry (master data + approved-status view accessible, but create/list/receipts blocked) suggests a **role/permission configuration gap** specific to the Sales-Invoice-creation and Receipts modules for the `billstack_admin` role, rather than a account-wide restriction or a missing license/module — worth flagging to the tenant owner as a likely misconfiguration.
