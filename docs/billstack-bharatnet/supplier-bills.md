# Supplier Bills (Vendor Bills — Accounts Payable)

## 1. Purpose

Records a bill/invoice received from a Vendor (supplier) for goods/services purchased — the core accounts-payable transaction of Billstack. Supports line-item purchases with GST/HSN tax, discounts, additional charges, and linkage back to prior Purchase Orders / Goods Receipt Notes / Sales Orders.

## 2. Navigation path

- Vendor Bills dashboard tab → **Create Invoice** button → `index.php?option=COM_INSERT_SUPPLIER_BILLS`
- Top nav → **New** menu → **Purchase Invoices** → `index.php?option=COM_LIST_SUPPLIER_BILLS` (list view; despite the menu being labelled "New" this entry opens the **list**, not the create form — see `00-navigation-map.md` §2 bug note)
- Vendor Bills dashboard "0 Invoices" counter tile ("Supplier Invoices Created In the System") → `COM_LIST_SUPPLIER_BILLS`

List view (`COM_LIST_SUPPLIER_BILLS`) was not separately screenshotted in this pass (0 records; same bare-empty-state pattern established for Vendors/Customers is assumed by analogy — **not directly re-confirmed, gap**).

## 3. UI / page design

Create form (`COM_INSERT_SUPPLIER_BILLS`) is a **3-tab page** (tab strip above the card, not inside it): **Supplier Bills** (the create form itself, default active) | **Same Ref Documents** | **Search Item**, plus a standalone **Multi Reference Documents** button top-right.

- **Same Ref Documents** tab: shows other supplier-bill transactions sharing the same reference document (e.g. duplicate-detection against the same `Doc Ref No`) — empty state "No Invoices" on this tenant. Screenshot: `32-supplier-bill-same-ref-documents-tab.png`.
- **Search Item** tab: a standalone item lookup utility (Item Name/SKU Code, Item Type, Category, Location, Warehouse, Vendor, Customer filters + Search button) for browsing the Items master without leaving the bill-creation flow. Empty/unsearched state: "Please Search". Screenshot: `33-supplier-bill-search-item-tab.png`.
- **Multi Reference Documents** button: opens a modal ("Multi Reference Docum**n**ets" [sic, typo in product]) with **Document Type**, **Reference Document Type**, and **Party** selectors driving two linked panels — **Transactions** (list of matching source documents, e.g. POs/GRNs for the selected party) and **Transaction Item List** (line items of the selected transaction) — clearly a mechanism to **pull line items from one or more prior documents (POs/GRNs/Sales Orders) into this bill** rather than typing them from scratch. Empty/unselected state on this tenant (no data to select). Screenshot: `34-supplier-bill-multi-ref-docs-modal.png`.

Main **Supplier Bills** form layout: header fields (2-column), two collapsible **Additional Information** panels, a **Products/Services** line-item grid, Attachment/Notes row, and a right-hand **Order Summary** panel. Actions: **Save & Exit**, **Save & Continue**, **Cancel**. Screenshot: `26-supplier-bill-create-form.png` (full page).

## 4. Data fields

### Header
| Field | Type | Required | Notes |
|---|---|---|---|
| Transaction Number | text, greyed/readonly | — | system-generated, blank pre-save |
| Transaction Date | date picker | **Yes** (*) | defaults to today (20-07-2026 at test time) |
| Opex Cpex | radio: OPEX / CAPEX | — | defaults to **OPEX**; classifies the spend as operating vs. capital expenditure |
| Type | searchable dropdown | **Yes** (*) | Options: **RURAL REQUIREMENT / URBAN REQUIREMENT / HO REQUIREMENTS / PROJECT REQUIREMENTS** — tenant-specific classification, consistent with "Bharatnet" being a rural-broadband network operator (India's BharatNet rural fiber initiative); HO = Head Office. Screenshot: `27-supplier-bill-type-options.png` |
| Originator | searchable dropdown | **Yes** (*) | defaults to the logged-in user ("Billstack Admin") |
| Cost Center | searchable dropdown | No | default "Select Any one"; sourced from Masters > Organization > Cost Centres |
| Party | searchable dropdown | **Yes** (*) | the Vendor being billed; sourced from Masters > Vendors — **empty on this tenant (0 vendors)** |
| Payment Terms | searchable dropdown | No | default "Select Any one"; same lookup as Vendor's Payment Terms field (ADVANCE/30/45/60/30-45 DAYS, inferred by shared naming, not re-confirmed here) |
| Doc Ref No | text | No | the vendor's own invoice/reference number |
| Doc Ref Date | date picker | No | |

### Additional Information-1 (collapsible, expanded by default click)
| Field | Type | Required | Notes |
|---|---|---|---|
| Tax State | dropdown | No | default "Select Any one" — the place-of-supply state, which determines CGST+SGST (intra-state) vs IGST (inter-state) tax split under Indian GST |
| Due Date | date picker | No | **defaults to the same value as Transaction Date** (today) — i.e. a bill defaults to due-immediately unless changed; presumably auto-shifts if a Vendor's Payment Terms are applied (not confirmed — no vendor exists to test) |

### Additional Information-2 (collapsible)
**Could not be expanded during this session** — clicking its accordion header had no visible effect (Additional Information-1 expanded correctly with the same interaction pattern). Documented as an **observed UI issue/gap**, not confirmed whether it is empty-by-design, requires a different trigger, or is broken.

### Products / Services (repeatable line-item grid)
Two grid-level toggles above the table:
- **Discount Overall / Discount Inline** — whether the discount entered in Order Summary applies once to the whole document ("Overall") or is entered per-line ("Inline"). Screenshot: `29-supplier-bill-discount-overall-options.png`.
- **Exclusive / Inclusive / Out Of Scope** — the line-item tax mode: **Exclusive** (tax added on top of Rate), **Inclusive** (tax embedded in Rate), **Out Of Scope** (no GST applicable, e.g. exempt/non-GST supply). Screenshot: `28-supplier-bill-exclusive-options.png`.

Per-row fields:
| Field | Type | Required | Notes |
|---|---|---|---|
| (checkbox) | checkbox | — | row selector, presumably for bulk delete |
| Items/Service | searchable dropdown ("Select Any Item") + free-text sub-field | **Yes** (*) | item master lookup; below it shows **Base Conv** (base unit conversion factor, "NA" until an item is picked) and three reference counters: **SO** (Sales Order qty), **PO** (Purchase Order qty), **Invoice Qty** — cross-document quantity tracking against the same item |
| UOM | searchable dropdown | **Yes** (*) | Unit of Measure, sourced from Masters/Settings > Items > UOM |
| Qty | number | **Yes** (*) | shows **QOH** (Quantity On Hand), **Max**, **GRN Qty** (Goods Received Note quantity) as reference figures alongside the input |
| Rate | number | **Yes** (*) | defaults "0.00"; shows **Min**/**Max** reference figures (likely historical rate bounds for the item) |
| HSN/Tax | — | **Yes** (*) | column header only, value derived from the selected item's HSN code once picked (not directly observed — no item exists) |
| Amount | number, likely computed | — | read-only-styled, computed from Qty × Rate |

Row actions: **- Delete** (remove selected rows), **+ Add More** (append a new blank row).

### Attachment / Notes
| Field | Type | Required | Notes |
|---|---|---|---|
| Attachment | file upload | No | shows "No Attachment" warning icon when empty |
| Internal Note(s) | textarea | No | not shown to the vendor (inferred from "Internal") |
| Printing Note(s) | textarea | No | presumably appears on the printed/PDF bill |

### Order Summary (right panel)
| Field | Type | Notes |
|---|---|---|
| Sub Total | computed, read-only | "-" placeholder pre-line-items |
| Discount | ₹ / % radio + value textbox | defaults to **%** mode |
| Additional Charges | expandable row (chevron icon) | not expanded in this pass — gap |
| Grand Total | computed, read-only | "₹0.00" default |
| Rounding Off | computed, read-only | "0" default |

## 5. Underlying data structure (inferred)

**Entity: SupplierBill** (header) 1—N **SupplierBillLine** (Products/Services rows), each line FK to an **Item** (Masters > Items) carrying its own HSN/tax code, with cross-references to **SalesOrder** (SO qty), **PurchaseOrder** (PO qty), and **GRN** (Goods Receipt Note qty) — confirming Billstack's procurement flow is **PO → GRN → Supplier Bill**, i.e. a 3-way-match-style AP process, even though only the Bill-creation UI is exposed to this login (no separate PO/GRN screens were found in Masters or New menus — **gap**, those may exist as Billstack Purchase-module screens not surfaced here, or may be created upstream in a different Infistaq module).

- `SupplierBill.party` → FK to **Vendor**.
- `SupplierBill.costCenter` → FK to **Cost Centre** (Masters > Organization).
- `SupplierBill.taxState` → FK to **State** (Geography), drives CGST/SGST vs IGST split.
- `SupplierBill.type` (RURAL/URBAN/HO/PROJECT REQUIREMENT) → tenant-specific enum, likely used for departmental/regional expense reporting.
- `SupplierBill.opexCpex` → enum (OPEX/CAPEX), standard accounting expense classification.
- The **Multi Reference Documents** modal implies a document-linking join table connecting one Supplier Bill to N source Transactions (POs/GRNs) it was generated from.

## 6. Functionality / logic observed

- **Create** — full form above. Two save modes: **Save & Exit** (save and leave) vs **Save & Continue** (save and presumably stay to keep editing/add attachments) — **not exercised** (task rule: no real records created).
- **Cancel** — discards and presumably returns to the list/dashboard.
- Tax computation mode (Exclusive/Inclusive/Out Of Scope) and discount mode (Overall/Inline) are both **document-level toggles that change how the Order Summary totals are computed** — not observed in action since no line item was added (would require selecting a real Item, none exist).
- **Same Ref Documents** and **Multi Reference Documents** both point to a duplicate-prevention / multi-source-consolidation capability that could not be exercised (0 data).

## 7. Inputs and outputs

- Input: the form above.
- Output (expected, not observed): a new Supplier Bill record in **pending/unapproved** state (per the dashboard's "Approval Pending" vs "Approved" tables seen in `00-navigation-map.md` §3a) that would need to go through an approval step before appearing in `COM_LIST_PURCHASE_INVOICES_TABLE` ("Supplier Invoices Approved In the System"). The approval mechanism itself (who approves, what UI) was **not located** in this login's available screens — **gap**.

## 8. Data conditions

- Party (Vendor) dropdown is empty on this tenant — 0 vendors exist, so a real Supplier Bill cannot currently be completed end-to-end even if submission were allowed.
- Due Date defaults to Transaction Date (today) — the actual Payment-Terms-driven due-date shift logic could not be confirmed (needs a Vendor with Payment Terms set + a saved bill to observe).
- The "Additional Information-2" panel not expanding is flagged as an observed anomaly, not assumed intentional or broken — genuinely unconfirmed either way.
