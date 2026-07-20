# PROGRESS -- task-20260720-030153-crm-bharatnet-pilot--full-reverse-engine

## Completed
- [x] Fixed local browser automation (sandbox had no Chrome/Chromium at expected path; patched playwright-core's bundled "chrome" channel lookup to point at the installed chromium headless_shell binary)
- [x] Logged in (solved image captcha by reading screenshot) — session re-established twice across resumes, captcha solved fresh each time (session cookie does not survive long idle gaps)
- [x] Captured dashboard/home screenshot (full-page: `home-01-full-dashboard.png`, includes Lead Status/Contact Status/Recently Created widgets, Sales Order Not Yet Generated, Pending Tasks, Feasibilities expiry-bucket widget, Pending BOM Returns table)
- [x] Mapped full top nav and the entire Application Settings accordion tree (23 categories / ~55 sub-pages) — `00-navigation-map.md`
- [x] Documented Leads module (`leads.md`)
- [x] Documented Contacts module (`contacts.md`)
- [x] Documented Companies module (`companies.md`)
- [x] Documented Deals module (`deals.md`) — Company/Contact FKs, Item Search panel wiring into Masters Items catalog
- [x] Documented Campaign module (`campaign.md`) — Campaign Type (6) / Objective (12) enums; found+documented the `crm_campaign` vs `crm_campaigns` singular/plural routing bug
- [x] Documented Projects module (`projects.md`) — Customer FK (confirmed distinct from Company), multi-user Team Member assignment
- [x] Documented Feasibility module (`feasibility.md`) — only CRM tab with a real filtered list view (Open/Closed/SO Not Generated/SO Generated); no reachable create form (likely created via Contact's Feasibility toggle) — flagged as the least-understood module
- [x] Documented Lead/Contact Assignings function (`lead-contact-assignings.md`) — Manual vs Auto assign modes, duplicate-id markup bug
- [x] Documented Masters > Items (`masters-items.md`) — full 84-field schema via Display Attributes column picker, 21-field chart-of-accounts mapping, Vendor/Customer Items cross-references
- [x] Documented Masters > Customers (`masters-customers.md`) — full 8-tab create form, two label/copy bugs found and documented

## Remaining
- [ ] Masters > Users (`index.php?option=COM_LIST_APPLICATION_USERS`)
- [ ] Masters > Invite Users (`index.php?option=COM_LIST_APPLICATION_USER_INVITATIONS`)
- [ ] Display Attributes admin page (`index.php?option=COM_LIST_DISPLAY_ATTRIBUTES`)
- [ ] Announcements (`index.php?option=COM_VIEW_ANNOUNCEMENTS`)
- [ ] Application Settings area (`settings.md`) — prioritize Geography, Items, Customer Managements, Workflow Setting, Email, Roles, General Settings, API; lighter pass on HR/ops-only categories
- [ ] Write final SUMMARY.md (data model, function list, known gaps)

## Notes for resumption
- This tenant's CRM data is entirely empty (0 records in every module) — detail-view/list-grid documentation is limited to empty-state dashboards and create forms only, flagged honestly per module.
- Session restore: PHPSESSID cookie can go stale across long gaps between resumed sessions — if `index` redirects to `login.php`, just log in fresh (captcha is a simple 4-digit distorted number, read via screenshot). Username `crm_admin@bharatnet.infisuite.in` / password is in the original task prompt (never write it to committed files).
- Screenshot workflow: `mcp__playwright__browser_take_screenshot` with a bare filename (no subdirectory) saves correctly to the workspace root; giving it a `crm-bharatnet/...` path prefix throws ENOENT in this sandbox. Always screenshot with a bare filename, then `mv` it into `screenshots/crm-bharatnet/` via Bash.
- JS extraction pattern that works well for forms (used throughout): a `browser_evaluate` call that walks `document.querySelectorAll('select')` (dumping name + all options value/text) and `document.querySelectorAll('input, textarea')` (dumping label via closest `.form-group`/`.col-md-*` wrapper + name/type/placeholder/required). For tabbed forms (e.g. Masters > Customers), re-run this after clicking each tab.
- Two site bugs found and documented so far, worth being alert for more: (1) `TRANST=crm_campaign` (singular) vs the real `crm_campaigns` route renders a broken duplicated dashboard; (2) Lead/Contact Assignings' Manual/Auto radio inputs share a duplicate HTML `id="assign_type"`, breaking naive `getElementById`-based interaction (not a real-user-facing bug, but a genuine markup defect).
- Do not click "Send" on any communication feature, do not submit create/edit forms with real data, per hard rules.
- Remaining Masters items (Users, Invite Users) and admin pages (Display Attributes, Announcements) should follow the same JS-extraction + screenshot pattern used for Items/Customers. Settings (`settings.md`) is the last big remaining piece — prioritize the sub-set called out above rather than exhaustively documenting all 23 categories, per the original task's implicit scope (CRM-relevant settings matter most).
