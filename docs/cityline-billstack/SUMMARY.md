# SUMMARY — Cityline Networks Billstack (Infistaq platform) Reverse-Engineering

System: `http://citylinenetworks.infisuite.in/`, logged in as `bill_admin@citylinenetworks.infisuite.in` (role: **Billstack Admin**). Password: **`infi123`** — same convention confirmed working for both the CRM and Billstack logins on this tenant. Full methodology, screenshots, and per-module detail live in the sibling files in this folder.

## 1. What this system is

The billing/invoicing (AP+AR) module of the same shared "Infistaq" platform documented for `docs/cityline-crm/`. Same tenant, same 4 users, same shared Masters/Settings backend (Geography, Items, Roles, Workflow, etc. — see cross-references in `00-navigation-map.md`). This documentation pass goes **deeper than the existing Bharatnet Billstack pilot** (`docs/billstack-bharatnet/`, which only reached navigation-map/customers/vendors) by opening the actual **Supplier Bill** and **Sales Invoice** create forms — new ground for this platform's documentation overall, not just this tenant.

**This tenant is the same QA/test sandbox already characterized in `docs/cityline-crm/SUMMARY.md`**, and Billstack adds a striking new piece of evidence for that conclusion: the tenant's **Config record (GST/PAN/CIN/billing address) is populated with the platform vendor "Infisuite"'s own real-looking corporate registration data, not any data belonging to "Cityline Networks."** A live production customer tenant would essentially certainly carry its own company's tax IDs here — this strongly suggests the tenant was provisioned from an unedited vendor template and used internally, corroborating the junk/test Project data already found in CRM.

Otherwise, Billstack's core AP/AR entities (Vendors, Supplier Bills, Sales Invoices, Payments, Receipts) are **entirely empty** on this tenant — 0 records across the board — while the surrounding *configuration* (invoice Type enum, Payment Terms, Additional Charges, UOM list) is genuinely populated, mirroring the CRM module's Feasibility-Types pattern (schema configured, no instances).

## 2. Data model — entities and relationships

```
Vendor (Masters) — 0 records; FK target of SupplierBill.party
Customer (shared with CRM's Masters — same COM_LIST_CUSTOMERS — 0 records)
  → FK target of SalesInvoice.party

SupplierBill (Purchase Invoice)
  .transactionNumber (auto), .transactionDate, .opexCpex (OPEX/CAPEX)
  .type → enum {Rural Requirement, Urban Requirement, HO Requirements, Project Requirements}
  .originator → User, .costCenter, .party → Vendor, .paymentTerms → enum (8 values)
  .lineItems → many {item → Item, uom, qty, rate, hsnTax, amount}
               (cross-checked live against SO/PO/GRN/QOH quantities)
  .discount, .additionalCharges → many AdditionalCharge (Shipping 18%/Delivery 18%/
                                   Handling 18%/small cart charge)
  .taxMode: Exclusive|Inclusive|Out Of scope
  .approvalStatus: Pending → Approved (two-stage workflow, approver/action not confirmed)

SalesInvoice — same shape as SupplierBill, plus:
  .salesOrderRef → SalesOrder (required FK — SalesOrder entity itself not explored)
  .deliveryOrderRef → DeliveryOrder (not explored)
  .advancePayments, .tcs (Tax Collected at Source)

Payment — unified ledger, FK to ONE of {Vendor, Customer, Employee}
  (broader scope than the "Vendor Bills" dashboard framing suggests — see payments-receipts.md)
Receipt — Customer-only counterpart to Payment

Config (tenant-wide, shared with CRM) — populated with the VENDOR's own corporate data
  (Infisuite Services India Private Limited: GST 18AABCU9603R1ZM, PAN CCIGS5009,
   CIN L250333505485220), not the customer tenant's — see 00-navigation-map.md §5
```

## 3. Function / module list

| Module | Path | This tenant's data | Notes |
|---|---|---|---|
| Home dashboard (Vendar Bills / Customer Bills) | `index` | 0 everywhere | `00-navigation-map.md` |
| Masters mega-menu | various | shared with CRM | `00-navigation-map.md` §2 |
| "New" menu | dropdown | n/a | `00-navigation-map.md` §3 — real difference from Bharatnet found |
| Tenant Config | Settings | vendor's own data, not tenant's | `00-navigation-map.md` §5 |
| Vendors | Masters | 0 | `vendors.md` |
| Supplier Bills (create) | dashboard CTA | 0 | `invoices.md` — new ground vs. Bharatnet pilot |
| Sales Invoices (create) | dashboard CTA | 0 | `invoices.md` — new ground vs. Bharatnet pilot |
| Payments | dashboard CTA | 0 | `payments-receipts.md` — Vendor+Customer+**Employee** ledger, broader than expected |
| Receipts | dashboard CTA | 0 | `payments-receipts.md` |

## 4. Platform-vs-tenant comparison

**Identical (platform-level, confirmed across CRM+Billstack+both tenants where checked):**
- Login flow, captcha, global icon chrome.
- Masters/Settings backend fully shared between CRM and Billstack logins on the same tenant (same `option=` URLs).
- The dashboard-as-widgets pattern and its date/year-picker chrome.
- ARIA "ecstasy" tab-selection bug present again on the Vendar/Customer Bills tab pair.
- "Vendar Bills" [sic] typo present in this tenant too (matches Bharatnet's documented typo — confirms it's a platform-level copy defect, not a one-off).

**Tenant-specific (data/config):**
- **"New" menu behavior**: Bharatnet's pilot found it static (always "Purchase Invoices" only); Cityline's shows both "Sales Invoices" and "Purchase Invoices" always. A genuine, directly-observed difference — cause unconfirmed (could be an app-version difference or a mis-scoped bug in either direction).
- **Config record**: Bharatnet's is mostly blank (no GST/PAN/CIN); Cityline's is fully populated but with the **vendor's own** data rather than the tenant's — a different, arguably more informative, flavor of "not real production data."
- **Invoice Type enum** (Rural/Urban/HO/Project Requirements) is telecom-rollout-specific — consistent with, and reinforcing, the CRM module's Feasibility Types (Software/Ports/Broadband/Railway) also being telecom-flavored. Together these give reasonably strong (though still circumstantial) evidence that this Owner's Cityline tenant, like Bharatnet, is built for a broadband/network-rollout business — this pairing across two independently-configured masters (CRM Feasibility Types, Billstack invoice Types) makes the telecom inference notably more confident here than it was from Bharatnet alone.

## 5. Known Gaps

- **Payments and Receipts create forms were not opened** — the single biggest unexplored surface this pass (see `payments-receipts.md`). Field sets for recording an actual payment/receipt (bank/cash account, partial-payment handling, reference back to the settled Bill/Invoice) are unknown.
- Masters > Items/Item Categories/Items Type/HSN Codes/Currencies/Workflow were not individually re-screenshotted for the Billstack login (assumed identical/shared with the CRM login's already-documented empty states, since they're the same underlying tables — not independently re-verified per page).
- "Additional Information-1"/"-2" collapsible sections on both invoice forms were never expanded — contents unknown.
- "Same Ref Documents" tab and "Multi Reference Documents" button (both invoice forms) were not exercised — the document-chaining/SO-PO-GRN cross-reference mechanism is inferred from line-item sub-row data (SO/PO/Invoice Qty/QOH/Min/Max/GRN Qty), not directly observed in action.
- Bulk "Upload Invoices" (`COM_INSERT_SALES_INVOICE_DYNAMIC`) was not opened.
- The approval workflow (who approves a Pending bill, what action moves it to Approved) was inferred from dashboard table columns only, not observed by exercising it (no bills exist to approve).
- SalesOrder / DeliveryOrder / PurchaseOrder entities, referenced by FK from Sales/Supplier invoices, were not independently explored (no Masters entry point found for them in the time available — may exist elsewhere in Settings, not confirmed either way).
