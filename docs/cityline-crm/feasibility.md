# Feasibility

## 1. Purpose
Tracks technical/site feasibility studies (e.g. for telecom/network rollouts) prior to converting into a Sales Order, including BOM (Bill of Materials) equipment loans that must be tracked for return. Same purpose as Bharatnet's Feasibility module — but this tenant lets us resolve a gap Bharatnet's doc left open (**"no create form found"**): the create/entry form exists, just not under the CRM tab — see §4.

## 2. Navigation path
- **CRM tab:** Home → **Feasibility** tab. URL: `index?TRANST=crm_feasibilities`.
- **Masters (new area, not in Bharatnet's documented Masters menu):** Masters → Feasibilty [sic] → **Feasibilty Types** (`COM_LIST_FEASIBILITY_TYPES`) and **Feasibilty Inputs** (`COM_INSERT_FEASIBILITY_FIELDS`).

## 3. UI / page design — CRM tab
Not a dashboard-widget-only view like the other tabs — has real filter chips: **Feasibility Open (0)**, **Feasibility Closed (0)**, **SO Not Generated (0)**, **SO Generated (0)**, plus a "Status" chart widget. Body shows "No Records Found" under the default "Feasibility Open" filter. **No visible "+ Create" button on this tab** — confirms Bharatnet's observation that Feasibility has no create entry point under the CRM tab itself. Screenshot: `14b-feasibility-tab.png`.

## 4. Masters > Feasibilty Types — list + records (real data, resolves a Bharatnet gap)
Standard Masters list template (Active/Inactive/All, Records/Search, Import/Export, + Create). **This tenant has 4 real records**: **Software, Ports, Broadband, Railway** — telecom/network-infrastructure categories, consistent with "Feasibility" being a site/technical survey concept for a network operator (matches Bharatnet's inference that this CRM instance supports a broadband-rollout business). Screenshot: `19-masters-feasibility-types.png`.

## 5. Masters > Feasibilty Inputs — dynamic form builder (major finding)
This page is a **per-Feasibility-Type dynamic form builder**, not a simple list:

1. **Feasibility Type** selector (dropdown) — choose which type's form to edit (sampled: "Software").
2. **Feasibility Requirement** field-definition grid — for the selected type, define custom input fields:
   | Column | Notes |
   |---|---|
   | Label | free text, e.g. "Technology", "Database", "Cloud", "Storage", "Estimated Users" |
   | Type | dropdown — observed values `Text`, `Number` (likely more, not fully enumerated) |
   | Values (JSON) | free text — implies dropdown/multi-choice field types accept a JSON option list |
   | Length (col) | dropdown — Bootstrap grid column classes (`col-md-12`, `col-md-6` observed) controlling field width in the rendered form |
   | Mandatory | toggle button, "Mandatory"/"Unmandatory" |
   | Action | delete (trash icon) per row |
   - **+ Add New Field** / **Save Fields** buttons manage this list.
   - Sampled "Software" type has 5 fields defined: Technology (unmandatory, full-width), Database/Cloud/Storage (mandatory, half-width), Estimated Users (mandatory, half-width, Number type).
3. **Below the builder, the same fields render live** as an actual data-entry form (Technology / Database* / Cloud* / Storage* / Estimated Users*) — i.e. this page both defines AND lets you fill in a Feasibility record's dynamic fields in one screen.
4. **Evaluation** section (fixed fields, not part of the dynamic builder): Evaluation Date*, Evaluation By* (dropdown "Select Evaluator"), Start Date*, End Date*, Estimated Budget*.
5. **Bill of Materials (BOM)** repeating table: Item Name*, Unit Of Measurement, Quantity*, Cost, Planned Required By, Planned Returned By, Actual Required By, Actual Returned By, + delete action, "+ Add More" — this is the direct source of the Home dashboard's **"Pending BOM Returns (Feasibility Closed)"** widget columns (Feasibility Title/Item Name/Planned Return By/Actual Return By), confirming that inference from both tenants' docs.
6. **Feasibility Checklist** repeating table: Checklist Item*, Type* (dropdown "Select Type"), Is Done? (toggle), Comment, delete action, "+ Add More".
7. **Upload Supporting Documents** — file picker.

Screenshot: `20-masters-feasibility-inputs.png` (full page, all sections visible).

**This resolves Bharatnet's Known Gap** ("Feasibility — low confidence, no create form found"): the create/entry surface for a Feasibility record is **Masters > Feasibilty Inputs**, not the CRM tab (which is read/filter-only). Whether Bharatnet's tenant has this same Masters path was not re-checked (out of scope — Bharatnet already documented), but given the identical Settings-tree PI numbering between tenants, it almost certainly exists there too and was simply not discovered during that pilot.

## 6. Underlying data structure (inferred, now higher-confidence)
```
FeasibilityType (Masters) — Software / Ports / Broadband / Railway (this tenant)
  has many → FeasibilityField (dynamic schema: label, type, values-json, col-width, mandatory)

Feasibility (record)
  .type            → FeasibilityType
  .dynamicFields    → keyed by FeasibilityField definitions for its type
  .evaluationDate, .evaluationBy → User, .startDate, .endDate, .estimatedBudget
  .bom              → many {item, uom, qty, cost, plannedRequiredBy, plannedReturnedBy,
                             actualRequiredBy, actualReturnedBy}  (BOM = borrowed equipment)
  .checklist        → many {item, type, isDone, comment}
  .documents        → many file
  .status           → 2 independent dimensions: Open/Closed × SO-Generated/Not
                       (confirmed again via the 4 CRM-tab filter chips)
```

## 7. Functionality / logic
- The dynamic-field-per-type design lets each Feasibility Type (Software/Ports/Broadband/Railway) capture a different technical questionnaire — e.g. Software cares about Technology/Database/Cloud/Storage/Estimated Users, while Ports/Broadband/Railway presumably have their own distinct field sets (not inspected — would require switching the Feasibility Type dropdown, not done to conserve scope; each switch reloads a fresh field-definition grid, inferred from the `changeModule`-style onchange pattern seen elsewhere in this app, not directly confirmed here).
- "Save Fields" persists the schema; the rendered data form below re-renders based on the current (possibly unsaved) schema — not confirmed whether it live-updates without a page reload.

## 8. Data conditions
This tenant: **4 Feasibility Types configured with real field schemas**, but the CRM tab shows **0 actual Feasibility records** (Open/Closed/SO Not Generated/SO Generated all read 0). So the *schema* is populated (real telecom-relevant configuration) while no *instances* have been created — a different flavor of "empty" than Leads/Contacts/Companies/Deals/Campaigns, worth distinguishing in the SUMMARY.
