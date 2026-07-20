# IndyaNet (Infistaq) — Navigation Map: shrinad@indyanet.com

System: https://indyanet.infisuite.in (same shared "Infistaq" platform / "IndyaNet" tenant).

Logged in as: `shrinad@indyanet.com`, displayed as **"Shrinad Patil"** (avatar photo present). Platform User Id 8, username `shrinad patil` (Masters > Users row 8, confirmed from abhishek's session).

## Role / permission level — near-identical to aitad@bbtel.in

This account's permission profile is **functionally a twin of `aitad@bbtel.in`**, not of `abhishek@indyanet.com` — despite sharing an `@indyanet.com` email domain with abhishek, not with aitad's `@bbtel.in`. This is a useful negative result: **email domain does not predict permission tier** on this tenant.

Confirmed via Profile page (`COM_VIEW_PROFILE_APPLICATION_USERS`):

- **Modules** assigned: CRM, Stores Management, Billstack, Purchase Management — **no Contract module**, same gap as aitad.
- **Roles** assigned: `CRM` (plain — confirmed at JS level to be `CRM_USER`, identical to aitad, not `CRM_ADMIN`), `Store Admin`, `Billstack Admin`, `Purchase Admin`. Same 4 roles as aitad, same admin-tier pattern (admin in Store/Billstack/Purchase, plain user in CRM).
- Default landing module after login: **Billstack** (same as aitad, different from abhishek's Contract default). Screenshot: `screenshots/indyanet/shrinad-01-dashboard-home.png`.
- Full Name / Display Name: "shrinad patil" (real personal name, like aitad's, unlike abhishek's tenant-generic "Indyanet"/"Admin"). Profile page screenshot: `screenshots/indyanet/shrinad-profile.png`.

### Confirms the permission-inversion finding from aitad's session

- **Display Attributes** (`COM_LIST_DISPLAY_ATTRIBUTES`) — **accessible** (same live internal-table-name grid as aitad saw: `accountsection`, `account_groups`, `account_type`, etc.). Screenshot: `screenshots/indyanet/shrinad-display-attributes.png`.
- **Application Settings** (`COM_LIST_APPLICATION_MENU_SETTINGS`) — **accessible** (same full category tree). Screenshot: `screenshots/indyanet/shrinad-settings.png`.

With all 3 accounts now checked, the pattern is consistent and clean: **the two accounts without a Contract role (aitad, shrinad) both have full Settings/Display-Attributes access; the one account with a Contract role (abhishek) is blocked from both**, despite abhishek otherwise holding "Admin" labels across more modules than either of the other two. This reads as a real permission-matrix inversion/bug — Contract-module access and platform-admin-screen access appear to be (perhaps accidentally) mutually exclusive on this tenant — rather than an intentional least-privilege design, since the two "lesser" accounts (one of which is only a CRM_USER, not even CRM_ADMIN) end up strictly more powerful in this one dimension.

## Global chrome, Masters menu, "New" menu, Modules switcher — identical to aitad

All checked and confirmed byte-for-byte identical in structure to `docs/indyanet/00-navigation-map-aitad.md` §2–4 (not re-transcribed here to avoid duplication):

- Same icon set/positions; bell badge "0"; avatar photo present.
- Modules switcher: same 5 tiles, **Contract tile inert** (no `onclick` handler) for this account too — confirmed via the same `chnageModuleWithRole` DOM query used for aitad, returning only Purchasing/Stores/CRM/Billstack handlers.
- Masters mega-menu while in Billstack context: identical item set (Division, Departments, Cost Centres, Locations, Item Categories, Items Type, Items, HSN Code, Currencies, Workflow, Vendors, Invite Users).
- "New" menu while in Billstack context: identical (Sales Invoices, Purchase Invoices).
- Billstack module home dashboard: pixel-identical layout and even the same underlying zero/empty data (2 Vendors, 0 Invoices, "No Vendors" in Top 3/Recent Vendors despite the 2-Vendors stat tile — same widget data-inconsistency noted in aitad's nav map) — this tenant-wide data is shared across all accounts, not per-user.

## Full checklist (this account)

- [x] Login + captcha
- [x] Billstack module home dashboard (matches aitad's exactly)
- [x] Profile page — Modules/Roles extracted, confirms same permission tier as aitad (CRM_USER, no Contract)
- [x] Display Attributes — accessible (confirms aitad's finding, not an aitad-specific fluke)
- [x] Application Settings — accessible (confirms aitad's finding)
- [x] Masters mega-menu, "New" menu, Modules switcher — confirmed identical to aitad
- [ ] Did not re-verify CRM/Purchasing/Stores module dashboards individually for this account (assumed identical to aitad's given identical role set) — gap, low-risk given the exact role match
