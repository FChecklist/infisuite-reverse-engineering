# PROGRESS -- task-20260720-030153-crm-bharatnet-pilot--full-reverse-engine

## Completed
- [x] Fixed local browser automation (sandbox had no Chrome/Chromium at expected path; patched playwright-core's bundled "chrome" channel lookup to point at the installed chromium headless_shell binary, which is the only browser build in this sandbox with satisfied shared-library deps)
- [x] Logged in (solved image captcha by reading screenshot)
- [x] Captured dashboard/home screenshot
- [x] Mapped full top nav (icon bar, Masters mega-menu, CRM tab strip, Modules switcher) and the entire Application Settings accordion tree (23 categories / ~55 sub-pages) via one DOM query
- [x] Wrote docs/crm-bharatnet/00-navigation-map.md
- [x] Documented Leads module (leads.md) — create-form data model with exact field name attributes, 9-value Lead Source code set, 10-value Lead Status ladder, 251-country Geography cascade; list-view/dashboard, gaps honestly flagged. Screenshot leads-06 added.
- [x] Confirmed CRM-tab structural pattern: every CRM tab (Leads/Contacts/Companies/Deals/Campaign/Projects/Feasibility) is a dashboard widget (date-scoped chart + "Status" chart), NOT a data grid; the create form is a slide-over behind a "+ <Entity>" button. Tenant data is empty across ALL CRM tabs (verified Contacts tab loads same dashboard pattern with no grid).

## Remaining
- [ ] Document Contacts module (dashboard + create form — same pattern as Leads; create form reachable via "+ Contacts" button, not yet opened)
- [ ] Document Companies module
- [ ] Document Deals module
- [ ] Document Campaign module
- [ ] Document Projects module
- [ ] Document Feasibility module
- [ ] Document Lead/Contact Assignings function
- [ ] Document Masters > Items
- [ ] Document Masters > Customers
- [ ] Document Masters > Users / Invite Users
- [ ] Document Display Attributes admin page
- [ ] Document Announcements
- [ ] Document Application Settings area (settings.md) — prioritize Geography, Items, Customer Managements, Workflow Setting, Email, Roles, General Settings, API; lighter pass on HR/ops-only categories
- [ ] Write final SUMMARY.md (data model, function list, known gaps)

## Notes for resumption
- This tenant's CRM data is entirely empty (0 records in every module observed so far, including the Contacts tab verified this session) — detail-view/list-grid documentation is limited to empty-state dashboards and create forms only. Flag this honestly in each doc rather than fabricating record data.
- **Verified CRM-tab pattern (apply to all 7 CRM modules):** each tab at `index?TRANST=crm_<entity>` renders a dashboard: a card titled "<Entity> (<from-date> To <to-date>)" with a date-range filter (This Year default → range 01-01-2026 To 31-12-2026) and a chart, a side "Status" chart card, and a "+ <Entity>" button that opens a right-side slide-over create form. There is NO data-grid list view with columns/pagination/Import/Export/Action-column on the CRM tabs (unlike the Masters/Settings list views). List/grid/edit/import/export functions for CRM entities, if they exist, are only reachable when records exist (row-click) — unobservable on an empty tenant. Document each CRM module's create-form fields via one JS evaluate (pattern: collect label + input name/type/required + select options) — this is the richest data-model source available.
- JS extraction pattern that works well (used for Leads):
  ```js
  document.querySelectorAll('select').forEach(sel => { ... }) // full option sets incl. codes
  // + label→input mapping via .form-group/.col-md-* wrappers for name/type/required
  ```
- Session restore: PHPSESSID cookie persisted in .auth.json (value d3333956f33b858a353fa6917cbb7589); on a fresh MCP browser (about:blank), inject it via context.addCookies then goto /index — session is still valid, no re-login/captcha needed.
- Do not click "Send" on any communication feature, do not submit create/edit forms with real data, per hard rules.
- A concurrent/prior worker process left stray files `browser.js`, `dom-dumps/`, `eng.traineddata`, `preprocess_captcha.py`, `solve-login.js`, `.auth.json` in this same workspace — these are a separate (incomplete, pre-login) automation approach and are safe to ignore; this session's browser works via the Playwright MCP tools directly. `.gitignore` already excludes them.
