# indyanet.infisuite.in — Summary

## 1. System type identified

**Not a CRM or Billstack system by default.** `indyanet.infisuite.in` is a tenant ("IndyaNet", brand tagline "Fast Secure and Reliable") on the same shared multi-module "Infistaq" platform documented for tenant "Bharatnet" in `docs/crm-bharatnet/` (same login screen, same `index.php?option=COM_*` routing convention, same list-view/mega-menu chrome). What differs is **which module the platform defaults a given login into**, and IndyaNet's primary/default module is **Contract** — a bandwidth/leased-line **contract and recharge management system** for what is clearly, from the real data observed, an ISP / network-infrastructure integration business:

- Contract records are internet bandwidth agreements ("B/W 100 Mbps - 30 days") between IndyaNet and customers like **Loadshare** (a logistics company needing bandwidth at dozens of sites across India — Chhattisgarh, Odisha, Uttar Pradesh, West Bengal, Assam, Tamil Nadu, Bihar).
- Vendor records include **Airtel** — IndyaNet is itself a downstream reseller/integrator of bandwidth from larger ISPs.
- The Purchasing module's Item Categories are literally networking-equipment brand names: **DBC Technologies, DR.COM, MIKROTIK, NETLINK, OPTICAL FIBER, SYROTECH, TPLINK, WOW Broadband**.
- Contract records track ISP-circuit metadata (RT Number, per-ISP transaction-ID prefixes like "BBTelJAF03"/"BBTelBSNL12" encoding both the partner brand "BBTel" and the originating ISP) and support a "Recharge" concept (Monthly/Quarterly/Half Yearly/Yearly plans) — modeling bandwidth contracts like prepaid/postpaid recurring service plans, not one-off legal contracts.

The tenant also has **4 sibling modules** reachable via a "Modules" switcher without re-authenticating: **CRM** (same shape as bharatnet's, different tenant data — real Feasibility/Lead-adjacent data exists here, e.g. "Abhishek Mishra - Broadband"), **Billstack** (supplier/customer invoicing — "BillsStack" branding, out of scope per task precedent), **Purchasing** (quotations/POs/supplier bills against the same networking-equipment item master), **Stores** (GRN/stock transfer/inventory against the same item master). Per this task's framing (CRM and Billstack are separate infisuite systems, already/elsewhere documented or explicitly out of scope), this task's deep documentation focused on **Contract** (the module all 3 logins share access to evaluating, and the one abhishek's account defaults into) plus the shared **Masters** data (Document Categories, Documents, Vendors, Users, Customers). Purchasing and Stores got a light, navigation-only pass; CRM and Billstack got only enough exploration to characterize the permission model, not full field-by-field documentation.

## 2. Inferred data model

**Contract** (primary entity) — Contract Name, Contract ID (free text, ISP-circuit-coded), RT Number, Invoice Number, Contract Owner (name/email/phone snapshot), Party Type (Customer|Vendor) + Party Name (polymorphic FK), Associated Contract (self-referential FK), Effective/Expiration Date, Description, Additional Notes, Termination Condition, State, ISP, Paid By, Internet Charges, Hub SPOC Name/Number, WiFi Provider/Pass (stored in plaintext), Approval Status, Auto Renewal.
&nbsp;&nbsp;↳ **RechargePlan/RechargeHistory** (1:N, keyed to Contract) — Plan Type (Monthly/Quarterly/Half Yearly/Yearly), Activation/Recharge/Expiry Date, Remaining Days, Status.
&nbsp;&nbsp;↳ **Document** attachments (10 fixed typed slots, 1:N keyed to Contract).
&nbsp;&nbsp;↳ **Permission** grants (Contract Id × User Id × 7 boolean flags — Edit/Delete/Document/Workflows/Activity/Associated/Permission).
&nbsp;&nbsp;↳ **Activity** log (Contract Id, timestamp, generic "CONTRACT" type literal — no field-level detail).

**Customer** (shared platform-wide master, referenced by Contract, Billstack, and presumably other modules) — First/Middle/Last Name, Email, Phone, Company, Display Name As, Parent Group (self-referential hierarchy), Website, Short Code, Photo; child: GST/Billing/Shipping Address (1:N, multi-address per customer); child: RentalProperty (present in schema, unused on this tenant — implies a property-management module exists elsewhere on the platform).

**Vendor** (shared platform-wide master) — Contact Person, Email, Phone, Vendor Since date; simpler than Customer, card-based list only, create form not explored in depth.

**Document / DocumentCategory** (shared platform-wide master, HR + vendor + contract document checklists) — DocumentCategory (Name, Description); Document (Name, FK → Category). Purely a template/checklist list, unrelated to the Contract entity's own fixed Documents-tab upload slots.

**ApplicationUser** (platform login/identity master, shared across every module) — User Name, Display Name, Email, Mobile, Location, Country, Timezone, Cost Center, Reports To, Mapping Code; a per-user **Modules** array and **Roles** array (e.g. `[CRM_ADMIN, CONTRACT_ADMIN, PURCHASE_ADMIN, STORE_ADMIN, BILLSTACK_ADMIN, CONTRACT_USER]` for abhishek) that together drive both the default landing module and the Modules-switcher tile availability.

**Item / ItemCategory** — shared master between Purchasing and Stores (Mikrotik/TP-Link/Optical Fiber/etc. brand-as-category), not explored field-by-field (gap).

## 3. Full function list (Contract + Masters, the deep-documented scope)

- Contract: List (filter/search/paginate/Active-Inactive-All), Create, Edit, Import (entry point only, not exercised), Export (dead link in this build), Status bulk-action (entry point only), Recharge Plan create modal, Recharge History view modal, per-record Permission grant, per-record Activity audit log (read-only), Documents upload (10 typed slots), Workflow start (entry point only).
- Masters > Document Categories: List, Create/Edit/Duplicate/View (icons present, create form not opened).
- Masters > Document: List (filterable by category), Create/Edit/Duplicate/View.
- Masters > Vendors: List (card grid), New Vendor (entry point only), Invite Vendor.
- Masters > Users: List (rich filter set: Location/Country/Reports To/User Type), Create (entry point only), Import/Export.
- Masters > Invite Users / Invite Vendors: invitation-tracking lists with status filters, Invite action.
- Masters > Customer: List (card grid), Create (full 8-tab form: Basic Info, Contact Details, Note*, Tax Info*, Payment and Billing*, Attachments*, Rental Properties — * not individually explored).
- Cross-cutting: platform Display Attributes admin (raw DB table registry), platform Application Settings (24-ish category tree, same shape as bharatnet's), Announcements, Notifications (badge only, panel contents not opened), Profile (rich, 6 sub-tabs, only Overview explored), My Messages (not opened), Modules switcher (client-side module context change without re-login).

## 4. Three-account permission comparison — the key finding

| | **abhishek@indyanet.com** | **aitad@bbtel.in** | **shrinad@indyanet.com** |
|---|---|---|---|
| Display name | "Admin" (generic) | "Aitad Sharma" (real name) | "Shrinad Patil" (real name) |
| Avatar | Initials only | Real photo | Real photo |
| Default landing module | **Contract** | Billstack | Billstack |
| Modules assigned | CRM, Contract, Purchase Mgmt, Stores Mgmt, Billstack | CRM, Purchase Mgmt, Billstack, Stores Mgmt (**no Contract**) | CRM, Stores Mgmt, Billstack, Purchase Mgmt (**no Contract**) |
| Roles assigned | CRM Admin, **Contract Admin**, Purchase Admin, Store Admin, Billstack Admin, Contract User | CRM (=`CRM_USER`), Purchase Admin, Billstack Admin, Store Admin | CRM (=`CRM_USER`), Store Admin, Billstack Admin, Purchase Admin |
| Contract module | **Full access** (only account that can reach it; switcher tile live) | Tile present but inert (no handler) | Tile present but inert (no handler) |
| CRM tier | Admin (`CRM_ADMIN`) | Plain user (`CRM_USER`) — Masters/New nav and half the icon bar disappear inside CRM | Plain user (`CRM_USER`, not independently re-verified but same role code) |
| Purchasing / Stores / Billstack | Admin in all 3 | Admin in all 3 | Admin in all 3 |
| **Display Attributes** (raw DB table registry) | **403 Forbidden** | **Full access** | **Full access** |
| **Application Settings** (full config tree) | **403 Forbidden** | **Full access** | **Full access** |
| Notification badge (at capture time) | 8 | 0 | 0 |

**Headline finding:** access to the two most sensitive platform-admin screens (Display Attributes, Application Settings) does **not** correlate with the "Admin" role labels visible on each account's own Profile page. It correlates instead — across all 3 accounts, cleanly and consistently — with **not** holding a Contract role. The account with the most "Admin" chips (abhishek, admin in 5 modules including Contract) is the *only* one of the three blocked from Settings/Display Attributes; both accounts with a comparatively narrower module footprint (and one of which is merely a CRM *user*, not admin) have full access to both. Both 403 pages abhishek received pointed to the same real support contact, **`nadiya@bbtel.in`**, later independently confirmed as the tenant's actual registered admin (Account Setting > General Settings > Config, visible to aitad/shrinad: Name "Indyanet", Admin Email `nadiya@bbtel.in`, Phone `8045461999`) — a real super-admin account outside the 3 provided logins.

This reads as an unintended permission-matrix inversion (plausibly: whatever role/permission check gates Settings/Display Attributes was never extended to cover the Contract module's admin role, so having *only* non-Contract admin roles accidentally leaves the door open) rather than intentional least-privilege design — the two functionally "lesser" accounts are strictly more powerful than the nominal "Admin" account in this one high-value dimension. Documented as directly observed across all three real logins, not inferred from one data point.

**Broadest-access account for this task's deep-documentation phase:** **abhishek@indyanet.com**, chosen because it is the only account with access to the Contract module — the system's primary/defining module and the one all other findings in this task's docs are built around. Note this is a deliberate scope choice, not a claim that abhishek is the most-privileged account overall (see the Settings/Display-Attributes finding above, where aitad/shrinad are more privileged).

Secondary observation: Masters mega-menu and "New" menu contents are **module-context-dependent**, not fixed per account — the same account sees a completely different Masters item set depending on which of its assigned modules it's currently switched into (Contract-context: Document Categories/Document/Vendors/Invite Vendors/Users/Invite Users/Customer; Billstack-context: Division/Departments/Cost Centres/Locations/Item Categories/Items Type/Items/HSN Code/Currencies/Workflow/Vendors/Invite Users).

## 5. Known Gaps

- **Notifications bell panel contents** never opened for any account (badge counts captured: 8 for abhishek, 0 for aitad/shrinad) — gap.
- **My Messages inbox** (`COM_LIST_INBOX`) not opened for any account — gap.
- **Vendor create form** and **User create form** fields not captured (only list views) — gap.
- Card "⋯" overflow menus on Vendors/Customers list (likely Edit/Deactivate/Delete) not opened — gap.
- Customer create form's **Note, Tax Info, Payment and Billing, Attachments** tabs not individually screenshotted (only Basic Info, Contact Details, Rental Properties were) — gap.
- Contract list's **Status** dropdown-button purpose not confirmed (likely bulk status change) — not opened.
- Contract **Import** page (`COM_INSERT_CONTRACT_IMPORT_FILE_ROWS`) not opened — gap.
- Contract's **Associated Contract** tab content not separately screenshotted (only the dropdown on Contract Details was seen) — gap.
- **Approval Status** dropdown on Contract — only "Approved" value observed; full enum not enumerated (form not submitted, per task's read-only rule, so the dropdown was never opened to list options) — gap.
- Contract list's **Export** button had `href="#!"` (dead in this build) — likely JS/AJAX-driven; not exercised further.
- Recharge Plan/History — no tenant contract had actual recharge data, so the real shape of a populated recharge record (and whether Plans and History are the same table) is inferred, not confirmed.
- A genuine **data-inconsistency** was observed and flagged, not resolved: the Home dashboard's Expiring/Expired Contract widgets showed all-zero counts despite the Contract list clearly containing ~220 real contracts, many with End Dates already in the past relative to system date — either these widgets are cache/cron-driven rather than live, or "Expired" means something narrower than "past End Date." Same pattern independently observed in Billstack's Top-3/Recent-Vendors widgets showing "No Vendors" against a "2 Vendors" stat tile.
- **CRM, Billstack, Purchasing, Stores modules** were explicitly given only a light/navigation-level pass (Purchasing, Stores) or permission-characterization-only pass (CRM, Billstack) per task scope — no field-by-field documentation exists for these four modules in this task's docs, by design (CRM is a separate already-documented infisuite system per task framing; Billstack likewise; Purchasing/Stores were judged secondary to Contract, the tenant's defining module).
- The **Tickday SSO icon** (`https://demo.tickday.com/login.php?infitokan=...`) and the same-host **`/contracts`** path (bypassing the `index.php?option=` router) were identified and their SSO-token mechanism decoded, but neither was followed — both judged out of scope (external product; redundant alternate front-end respectively).
- Did not test what happens if a Contract-role account (abhishek) tries the Modules-switcher tile for a module it lacks admin rights in versus what a truly module-less account would see — only observed via DOM inspection that non-assigned tiles have no click handler, not via an actual blocked-navigation attempt.
