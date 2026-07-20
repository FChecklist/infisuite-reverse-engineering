# Masters (Document Categories, Documents, Vendors, Users, Customers)

Shared reference/master data underneath the Contract module, reached via the top-nav **Masters** mega-menu. All follow the platform's list-template chrome (Active/Inactive/All filter, Records page-size + Search, Status/Import/Export/+Create/Back, gear icon) unless noted otherwise.

## 1. Document Categories (`COM_LIST_DOCUMENT_CATEGORIES`)

**Purpose:** category taxonomy for the Document checklist master below (not for the Contract's own Documents-tab file uploads, which are a fixed hardcoded set — see `contracts.md` §4).

**Fields (list columns):** Id, Category Name, Category Description, Is Active (toggle button), Action (edit/duplicate/view icons).

**Data (real, 5 rows):** JOIN, Pre Joining Checklist, Vendor Registration, Document Release, Contract. The category names ("JOIN", "Pre Joining Checklist", "Document Release") are HR employee-lifecycle categories, not contract-related — confirms this Masters area is shared platform-wide infrastructure (same conclusion bharatnet CRM's `settings.md` reached for its Settings tree), reused here for whatever the tenant needs, not scoped strictly to Contracts. Screenshot: `screenshots/indyanet/abhishek-masters-document-categories.png`.

## 2. Document (`COM_LIST_DOCUMENT_LISTS`)

**Purpose:** master checklist of named documents expected per category (e.g. which documents are required to onboard an employee, register a vendor, or file a contract) — a template/definition list, not actual uploaded files.

**Fields (list columns):** Document Category (FK, dropdown filter available), Document Name, Action.

**Data (real, 2 pages × 20):** e.g. under "JOIN" — Certificate, Class 12 Certificate, PAN Card Copy, AADHAR COPY, PREVIOUS WORK EXPERIANCE COPIES [sic], Class 10 Certificate; under "Pre Joining Checklist" — 6 Passport Photos, All Original Certificates, Age Proof, Copy of Relieving or Resignation Letter, Join Certificates; under "Vendor Registration" — AddressProof, Certificate of incorporation, GST Registration Certificate, MSME Certificate if Applicable, Cancelled Cheque; under "Document Release" — IT Declaration, FROM 16A, ITR-1, Release Payslip. Screenshot: `screenshots/indyanet/abhishek-masters-documents.png`.

**Underlying data structure (inferred):** `Document.category` → FK to `DocumentCategory`. Simple 1:N master-detail, no other relationships observed.

## 3. Vendors (`COM_LIST_VENDORS`)

**Purpose:** vendor/supplier directory — in this tenant's real data, ISP/bandwidth providers (e.g. Airtel).

**UI:** card grid (not a table), unlike most other list pages on this platform. Each card: avatar placeholder, Active/Inactive badge, "⋯" overflow menu (contents not opened — gap), Contact Person, Vendor Since (date), Email, Phone. Header actions: **+ New Vendors** (→ create form, not opened in depth — gap), **+ Invite Vendors** (→ `COM_LIST_VENDOR_INVITATIONS`).

**Data (real, 2 rows):** matches the Home dashboard's "Total Vendors: 2" stat exactly. One vendor has a blank Contact Person/Email/Phone (incomplete record); the other is "Zaheer" / `zaheer1.uddin@airtel.com` — confirms Airtel is a bandwidth vendor for IndyaNet. Screenshot: `screenshots/indyanet/abhishek-masters-vendors.png`.

## 4. Invite Vendors (`COM_LIST_VENDOR_INVITATIONS`)

**Purpose:** track pending/accepted vendor self-registration invitations, separate from the Vendors list itself (a vendor presumably moves from here into the main Vendors list once they complete registration via document upload against the "Vendor Registration" document category above).

**UI:** filter radios **All / Active / In-Progress / Invited**, **+ Invite Vendor** button. Empty state on this tenant ("No Vendor Invitation Available") — illustrated empty-state graphic, same style used platform-wide for zero-record states. Screenshot: `screenshots/indyanet/abhishek-masters-invite-vendors.png`.

## 5. Users (`COM_LIST_APPLICATION_USERS`)

**Purpose:** the platform's internal application-user directory — i.e. the list of *login accounts*, not customers/vendors. This is the same list all 3 task accounts appear in.

**Fields (list columns):** Id, User Name, Display Name, User Email, User Mobile, Cost Center, Location, Country, Timezone, Mapping Code, Reports To (cut off in capture — gap on remaining columns to the right). Filters: Location, Country, Reports To, User Type (all "All" dropdowns) in addition to the standard Active/Inactive/All + Records + Search. **+ Users** create button, Import/Export/Status.

**Data (real, 10 rows, single page):**

| Id | User Name | Email | Location |
|---|---|---|---|
| 1 | ADMIN_1 (Display: "Admin") | abhishek@indyanet.com | DEULIHAT / India |
| 2 | User1 (Display: "User") | user@madilu.infisuite.in | DEULIHAT / India |
| 3 | yogeshber singh | yogeshber@bbtel.in | — |
| 4 | aitad sharma | aitad@bbtel.in | — |
| 5 | shaikhanas | shaikhanas@bbtel.in | — |
| 6 | shrishti | shrishti@bbtel.in (mobile 9513356818) | — |
| 7 | boopalan moorthi | boopalan@bbtel.in | — |
| 8 | shrinad patil | shrinad@indyanet.com | — |
| 9 | Pavitra Indyanet | Pavithra@indyanet.com | — |
| 10 | Devanath | devanath@indyanet.com | — |

This directly confirms: `abhishek@indyanet.com` is platform user **ADMIN_1 / "Admin"** (row 1) — the tenant's primary admin login. `aitad@bbtel.in` is row 4 ("aitad sharma"). `shrinad@indyanet.com` is row 8 ("shrinad patil"). The `@bbtel.in` domain accounts (5 of the 10 users) all belong to the same partner organization "BBTel" that also supplies the Contract Owner contacts seen on many Loadshare contracts and is referenced in the platform's 403-error support contact (`nadiya@bbtel.in`, not itself in this list — implying at least one more BBTel user with elevated/support access exists outside this tenant's own Users master, or belongs to a different tenant). Screenshot: `screenshots/indyanet/abhishek-masters-users.png`.

## 6. Invite Users (`COM_LIST_APPLICATION_USER_INVITATIONS`)

**Purpose:** invitation-tracking for new application users (parallel structure to Invite Vendors, for internal logins instead of external vendors).

**UI:** card grid, filter radios **All / Active / Invited**, **+ Invite User** button.

**Data (real, 7 cards):** yogeshber singh, aitad sharma, shaikhanas, shrishti, boopalan moorthi, shrinad patil, Pavitra Indyanet — i.e. 7 of the 10 Users-master rows also have a matching invitation record (all marked "Active"); `ADMIN_1`, `User1`, and `Devanath` do not appear here (likely seeded directly rather than invited). Screenshot: `screenshots/indyanet/abhishek-masters-invite-users.png`.

## 7. Customer (`COM_LIST_CUSTOMERS`)

**Purpose:** the platform-wide Customer master (same entity referenced by Contract's `Party Name` when Party Type = Customer). Shared across all 5 Infistaq modules on this tenant, not Contract-specific — evidenced by the create form's **Rental Properties** tab (see below), which has no relationship to bandwidth contracts and implies this Customer master doubles as the master for a property-management-style module elsewhhere on the platform (not present in this tenant's module set, or not explored — gap).

**List UI:** card grid (same visual pattern as Vendors), labelled fields on each card oddly reuse the **"Vendor Since"** label even though these are Customer records — a genuine copy-paste UI bug carried over from the Vendor card template, not a data-modeling choice. **+ Customer** create button. Screenshot: `screenshots/indyanet/abhishek-masters-customers.png`.

**Data (real, 11 rows, matches Home dashboard's "Total Customers: 11" exactly):** includes real business contacts at loadshare.net (Gaurv/gaurav.bisht@loadshare.net — the Loadshare bandwidth customer seen throughout Contracts), airtel.com (Zaheer — appears as both a Vendor and a Customer contact, i.e. Airtel is both a bandwidth supplier to and a customer of IndyaNet), and prsilns.com (Abhishek Mishra).

### Customer create form (`COM_INSERT_CUSTOMERS`)

Full-page form with a cover-image header (upload photo, "NO IMAGE" placeholder) and 8 tabs: **Overview**, **Basic Info** (default — First/Middle/Last Name, Email, Phone, Suffix, Company, Display Name As, Parent group [dropdown, self-referential hierarchy], Website, Short Code, Photo), **Contact Details** (GST Registration Type, GST No., a same-as-billing checkbox, full Billing Address + Shipping Address blocks each with Country/State/City/Pincode, Contact Person/Mobile/Fax, "+ Add More GST & Billing Details" for multiple registered addresses, a per-address "Make Default" radio and "Remove" link), **Note**, **Tax Info**, **Payment and Billing**, **Attachments** (tabs not individually screenshotted — gap), and **Rental Properties** (empty-state illustration "No Rental Properties" on every customer checked — see purpose note above). Required fields (marked *): First Name, Last Name, Email, Company. Screenshots: `screenshots/indyanet/abhishek-customer-create-form.png`, `-tab-contactdetails.png`, `-tab-rentalproperties.png`.

**Underlying data structure (inferred):** `Customer.parentGroup` → self-referential FK (hierarchical customer/company grouping, e.g. HQ vs. branch sites — consistent with Loadshare needing one Customer record per multi-site bandwidth relationship, though in the observed data each Loadshare site is actually a separate *Contract*, not a separate Customer, so the Parent Group hierarchy's real usage on this tenant is unconfirmed — gap). `Customer` 1:N `Billing/Shipping Address` (multiple GST-registered addresses per customer). `Customer` 1:N `RentalProperty` (present in the schema/UI, unused on this tenant).

## 8. Known gaps in this file

- Vendor create form (`+ New Vendors`) fields not captured.
- Users create form (`+ Users`) fields not captured.
- Card "⋯" overflow menus (Vendors, Customers) not opened — likely Edit/Deactivate/Delete, unconfirmed.
- Note / Tax Info / Payment and Billing / Attachments tabs on the Customer form not individually screenshotted.
