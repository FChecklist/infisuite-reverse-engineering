# SUMMARY — Cityline Networks CRM (Infistaq platform) Reverse-Engineering

System: `http://citylinenetworks.infisuite.in/`, logged in as `crm_admin@citylinenetworks.infisuite.in` (role: **CRM Admin**). Password: **`infi123`** (the `1234` convention failed with `login?error=6002` — see `00-navigation-map.md`). Full methodology, screenshots, and per-module detail live in the sibling files in this folder.

## 1. What this system is

Same shared multi-module "Infistaq" ERP platform as the already-documented Bharatnet tenant. This Cityline tenant is licensed for **3 modules: CRM, Billstack, and Contract** (one more than Bharatnet's 2 — Bharatnet had CRM + Billstack only). Contract is out of scope for this task (a separate worker covers `citylinenetworks.infisuite.in/contracts`); Ticketing is likewise a separate out-of-scope system reachable from the same top-nav headset icon.

**This tenant is clearly an internal QA/test sandbox, not live production CRM data** — a materially different character from Bharatnet's "real but empty" tenant:
- Leads, Contacts, Companies, Campaigns, Deals: all **0 records**, identical to Bharatnet.
- **Projects: 27 records**, all obviously placeholder/test data ("test", "Dummy", "gdh", "bdhdb", "fhdhs n", duplicate "test" ×4, budgets up to 1,000,000.00), dated Feb–Jul 2025.
- **Feasibility Types/Inputs (Masters)**: genuinely configured with real telecom-relevant taxonomy (Software/Ports/Broadband/Railway) and a working dynamic-form schema for at least "Software" — but **0 actual Feasibility records** exist under the CRM tab.
- 4 Users total, one per module tile (Admin, CRM Admin, Billstack Admin, CONTRACT ADMIN) — all sharing the same odd "Guinea-Bissau" country default, almost certainly unedited seed data.

## 2. Data model — entities and relationships

Structurally **identical** to Bharatnet's documented model (see `docs/crm-bharatnet/SUMMARY.md` §2) for Lead/Contact/Company/Deal/Campaign/Project — same FK shapes, same enums (Lead/Contact/Deal source: 9-value; Lead status: 10-value ladder; Campaign type: 6-value; Campaign objective: 12-value). One addition, resolved here for the first time:

```
FeasibilityType (Masters: Software / Ports / Broadband / Railway)
  has many → FeasibilityField (dynamic schema: label, type [Text/Number/…],
                                 values-json, Bootstrap col-width, mandatory flag)

Feasibility (record — entry point is Masters > Feasibilty Inputs, NOT the CRM tab)
  .type              → FeasibilityType
  .dynamicFields      → per FeasibilityField schema for its type
  .evaluationDate, .evaluationBy → User, .startDate, .endDate, .estimatedBudget
  .bom                → many {item, uom, qty, cost, plannedRequiredBy,
                               plannedReturnedBy, actualRequiredBy, actualReturnedBy}
  .checklist          → many {item, type, isDone, comment}
  .documents          → many file
  .status             → Open/Closed × SO-Generated/Not (2 independent dimensions)

Project (populated on this tenant — 27 records)
  .name, .totalBudget, .startDate, .endDate, .description
  .originator          → User
  .customer            → Customer (Masters) — REQUIRED by form, observed UNSET on
                          real records (0 Customer masters exist tenant-wide, so this
                          could never have been satisfied via the UI)
  .teamMembers          → many User — REQUIRED by form, also observed unset/empty
```

All other entities (Lead, Contact, Company, Deal, Campaign, Item, Customer, User, Role, WorkflowConfiguration, Config/Geography) match Bharatnet's documented shapes; not re-derived here to avoid duplication.

## 3. Function / module list

| Module | Path | This tenant's data | Notes |
|---|---|---|---|
| Home dashboard | `index` | 0/0/0/0/0/27 | see `00-navigation-map.md` |
| Leads | CRM tab | 0 | `leads.md` |
| Contacts | CRM tab | 0 | `contacts.md` |
| Companies | CRM tab | 0 | `companies.md` |
| Deals | CRM tab | 0 | `deals.md` — has a "No Campaigns" mislabel bug |
| Campaign | CRM tab | 0 | `campaign.md` |
| Projects | CRM tab | **27** | `projects.md` — only populated CRM entity; required-field enforcement gap observed |
| Feasibility | CRM tab + Masters | 0 records / 4 types configured | `feasibility.md` — resolves a Bharatnet "no create form" gap |
| Lead/Contact Assignings | standalone page | 0/0 | `lead-contact-assignings.md` |
| Masters: Items/Categories/Types/UOM | Masters menu | 0 | `masters.md` |
| Masters: Feasibilty Types/Inputs | Masters menu | 4 types, 1 schema | `masters.md`, `feasibility.md` |
| Masters: Customers | Masters menu | 0 | `masters.md` |
| Masters: Users / Invite Users | Masters menu | 4 users / 0 invites | `masters.md` |
| Display Attributes | icon #2 | ~800 platform tables | `display-attributes.md` |
| Announcements | icon #4 | 0 | `announcements.md` |
| Application Settings | icon #3 | ~52 sub-pages across ~20 categories | `settings.md` |

## 4. Platform-vs-tenant comparison (Cityline vs. Bharatnet)

**Identical (platform-level):**
- Login flow, captcha mechanism, page-title convention ("Infistaq | X"), global icon chrome and order.
- Every CRM entity's field set, required markers, and enums (Lead/Contact/Deal source code list, Lead status ladder, Campaign type/objective).
- The "CRM tabs are dashboard widgets, not grids" architecture, including the shared date-range filter control and its exact preset list.
- The Settings tree — **every category, sub-item, and numeric page ID (`PI`) is byte-for-byte identical**, confirming a single shared platform schema, not per-tenant configuration.
- Masters list template (Active/Inactive/All, Records/Search, Status/Import/Export/+Create/Back/gear).
- The ARIA `aria-selected="true"`-on-every-tab markup bug.

**Tenant-specific (data/config):**
- Password convention: Cityline uses `infi123`; Bharatnet's pilot doc doesn't record which convention it used (worth cross-checking if revisited).
- Module licensing: Cityline has 3 modules (CRM/Billstack/**Contract**); Bharatnet has 2 (CRM/Billstack).
- Data volume/character: Cityline is a QA/test sandbox (27 junk Projects, 0 elsewhere); Bharatnet is genuinely empty across the board (0 everywhere).
- Feasibility Types/Inputs: Cityline has 4 real types with a working dynamic schema; Bharatnet's pilot did not report this (see structural-differences note below).
- User roster: Cityline's 4 users are literally named after their module + "Admin" (CRM Admin, Billstack Admin, CONTRACT ADMIN, Admin); Bharatnet had different named users (per that doc: "Anthony Johnston", "CRM Admin", "Billstack Admin").
- Minor UI-text diff: Lead/Contact/Deal Source dropdown spells "Facebok" [sic] on Cityline vs. "Facebook" (correct) reported for Bharatnet — low confidence, could be a transcription artifact in either doc rather than a real product difference.

**Open structural question (not resolved, flagged for future work):** Cityline's Masters mega-menu exposes explicit **Item Categories / Items Type / UOM** sub-links and a whole **Feasibilty Types/Inputs** group that Bharatnet's nav doc did not report (that doc described a flat 4-link Masters menu: Items, Customers, Users, Invite Users). Given the Settings tree is byte-identical between tenants, the most likely explanation is that Bharatnet's mega-menu has the same nested items and the pilot simply didn't expand them — but this was not independently re-verified against the live Bharatnet tenant, so it's recorded as an open question rather than a confirmed platform difference.

## 5. Known Gaps

- Items Type and UOM Masters pages were not individually screenshotted (inferred empty via the shared list-template pattern, not independently re-verified per page).
- Contacts/Companies/Deals/Campaign create-form "Address Information"/"Description Information" sections were not re-scrolled-into on this tenant for every module (inferred identical to the fully-captured Lead form and to Bharatnet's docs, given the consistent 3-section pattern observed everywhere it WAS fully captured).
- Feasibility Type field schemas for Ports/Broadband/Railway were not inspected (only "Software" was opened) — the dynamic-field-per-type mechanism is confirmed, but the other 3 types' actual field lists are unknown.
- No Feasibility record, Lead, Contact, Company, Deal, or Campaign exists on this tenant, so no detail/edit view, conversion flow, or status-transition behavior could be observed for those 5 entities (same limitation Bharatnet had).
- Whether "Submit" on the Project modal truly does double duty as insert/update was inferred from UI behavior (shared "Add Project" title + pre-fill on click), not confirmed by submitting.
- Required-field enforcement gap (Project.Customer, Project.TeamMember) was observed on existing data but the exact mechanism (client-side only vs. bypassed via direct DB seed) is unknown.
