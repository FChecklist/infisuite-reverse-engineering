# PROGRESS -- infisuite-reverse-engineering (cityline tenant, merged history)

## task-20260720-060749-cityline-crm-billstack-reverse-engineeri

### Completed
- [x] Logged into Cityline CRM (crm_admin@citylinenetworks.infisuite.in). Password: `infi123` (NOT `1234`).
- [x] Full CRM documentation under docs/cityline-crm/: 00-navigation-map.md, leads.md,
  contacts.md, companies.md, deals.md, campaign.md, projects.md (27 real test records),
  feasibility.md (resolves a bharatnet doc gap: dynamic form builder under Masters >
  Feasibilty Inputs), lead-contact-assignings.md, masters.md, display-attributes.md,
  announcements.md, settings.md, SUMMARY.md. ~26 screenshots under screenshots/cityline-crm/.
- [x] Logged into Cityline Billstack (bill_admin@citylinenetworks.infisuite.in). Password
  `infi123` confirmed again (same convention as CRM).
- [x] Full Billstack documentation under docs/cityline-billstack/: 00-navigation-map.md
  (incl. tenant Config record finding: populated with the platform VENDOR's own corporate
  data, not the customer's -- reinforces "QA/test sandbox" conclusion), vendors.md,
  invoices.md (Supplier Bill + Sales Invoice create forms -- new ground vs. the incomplete
  bharatnet Billstack pilot docs), payments-receipts.md, SUMMARY.md. 11 screenshots under
  screenshots/cityline-billstack/.
- [x] Compared both modules against bharatnet tenant throughout (platform-identical
  Settings tree/Masters URLs/enums; tenant diffs: 3rd "Contract" module, QA/test-sandbox
  data character, Feasibility Types/invoice-Type enums both telecom-flavored, "New" menu
  behavior differs from bharatnet's documented bug, Config record diff)
- [x] Committed and pushed CRM + Billstack docs (commits 2b50eff, b8b1591, ec40a01)
- [x] Cleaned up stray playwright console/snapshot artifact files after each pass

### Remaining
- [ ] Optional follow-up (not blocking): open Payments/Receipts create forms, expand
  Additional Information-1/-2 accordions on invoice forms, explore SalesOrder/
  PurchaseOrder/DeliveryOrder entities referenced by FK -- all flagged as Known Gaps
  in docs/cityline-billstack/SUMMARY.md for any future session, not required to close
  out this task's core ask (full page-by-page documentation of both logins, done).

## task-20260720-060750-cityline-contracts-reverse-engineering

### Completed
- [x] Logged in (`contract_admin@citylinenetworks.infisuite.in`); password `1234` failed, fallback `infi123` worked (noted in nav map/SUMMARY, not committed elsewhere)
- [x] Dashboard screenshot captured
- [x] Mapped full navigation (top nav: Master, New, Profile, Module switcher, View CRM/View Ticketings SSO links) -> `docs/cityline-contracts/00-navigation-map.md`
- [x] Discovered and reproduced (3/3) a critical bug: Contracts list/create routes crash to a blank "No data available." page (root-caused via console errors) -- makes the core Contract entity's list/create/edit/view UI unreachable -- documented in `docs/cityline-contracts/contracts.md`
- [x] Explored Master -> Document Categories (list/create/edit/view) -- working, documented in `docs/cityline-contracts/master-document-categories.md`
- [x] Explored Master -> Document (document_lists) (list/create/edit/view, incl. page 2 = "Contract" category, 10 slots matching indyanet's Documents tab) -- working, documented in `docs/cityline-contracts/master-documents.md`
- [x] Written `docs/cityline-contracts/dashboard.md`
- [x] Written `docs/cityline-contracts/SUMMARY.md` with data model, function list, Known Gaps
- [x] Committed and pushed (commits a19f01e, 5042c42)

### Remaining
- [ ] (Optional/stretch, not required) Further probing of Module switcher targets (Purchasing/Stores/CRM/Billstack) -- explicitly out of scope for this Contracts-focused task, not planned

## task-20260720-060752-cityline-ticketing-6-role-reverse-engine

### Completed
- [x] Tried all 6 provided role accounts (plus a deliberate wrong-credential control) against
  `citylinenetworks.infisuite.in/ticketing/login.php`. All produced an identical 302 -> `?error=`
  redirect with no visible error text (verified via UI and a raw same-origin `fetch()` replay --
  rules out a client-side/automation quirk). Documented in
  `docs/cityline-ticketing/00-BLOCKER-login-failures.md` (commit 4ed23be).
- [x] **Blocker resolved 2026-07-20 (later same day, separate interactive session)**: rather than
  retry the automated Playwright login (the credentials genuinely didn't work for that flow), the
  Owner's own already-authenticated real Chrome session was used instead (via Claude in Chrome --
  same real-browser-session-reuse pattern that separately unblocked Odoo's Cloudflare Turnstile
  gate, see odoo-reverse-engineering/PROGRESS.md). This inherited a valid `citylinenetworks`
  session cookie and reached `/ticketing/` directly with no login prompt at all -- confirming the
  6 credential failures were a genuine account/password problem, not a platform-level block.
  Real (if shallower than CRM/Billstack/Contracts) documentation produced: Home portal shell
  (`index`/`index.php` -- confirmed near-empty, no dashboard widgets, an unpopulated "Modules"
  role-switcher select) and the Tickets dashboard (`/ticketing/` -- 4 stat tiles [Open/In-progress/
  Fixed/Close, all 0%], User/Status/Priority/From/To filter bar, ticket table columns
  [# / Ticket No. / Ticket Subject / Requested Date / Last Updated / Priority / Status /
  Assign Staff / Action], "No Tickets Available" -- zero real tickets in this tenant). See
  `docs/cityline-ticketing/tickets-dashboard.md` and `docs/cityline-home/index.md`.

### Remaining
- [ ] Per-role exploration (the original task's "6 roles" scope) was never done -- this later
  pass logged in as whichever single role the Owner's browser session already held
  (CONTRACT ADMIN), not all 6. Role-by-role differences in the Ticketing UI remain undocumented.
- [ ] No ticket was ever created/raised (tenant has 0 tickets) -- the create-ticket form, ticket
  detail view, and assignment/status-change workflow are all unconfirmed.
- [ ] The Contracts module's top-nav "View CRM" SSO-style headset icon was clicked once during
  this pass and unexpectedly redirected to `indyanet.infisuite.in/login.php` (a different,
  already-documented tenant) rather than a cityline-scoped CRM view -- worth a follow-up look,
  not chased further here (out of scope, and indyanet already has its own documentation).
- [ ] Contracts module's "Master" and "New" top-nav dropdowns did not visibly populate when
  clicked in this session (empty overlay box) -- may be the same client-side bug class as the
  contracts-list crash documented in `docs/cityline-contracts/contracts.md`, or may be an
  unrelated rendering issue; not root-caused in this pass.
