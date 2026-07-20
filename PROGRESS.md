# PROGRESS -- task-20260720-030153-crm-bharatnet-pilot--full-reverse-engine

## Completed
- [x] Fixed local browser automation (sandbox had no Chrome/Chromium at expected path; patched playwright-core's bundled "chrome" channel lookup to point at the installed chromium headless_shell binary)
- [x] Logged in (solved image captcha by reading screenshot) — session re-established across resumes, captcha solved fresh each time (session cookie does not survive long idle gaps)
- [x] Captured dashboard/home screenshot (full-page: `home-01-full-dashboard.png`)
- [x] Mapped full top nav and the entire Application Settings accordion tree (23 categories / ~55 sub-pages) — `00-navigation-map.md`
- [x] Documented Leads module (`leads.md`)
- [x] Documented Contacts module (`contacts.md`)
- [x] Documented Companies module (`companies.md`)
- [x] Documented Deals module (`deals.md`)
- [x] Documented Campaign module (`campaign.md`) — found+documented the `crm_campaign` vs `crm_campaigns` singular/plural routing bug
- [x] Documented Projects module (`projects.md`)
- [x] Documented Feasibility module (`feasibility.md`)
- [x] Documented Lead/Contact Assignings function (`lead-contact-assignings.md`)
- [x] Documented Masters > Items (`masters-items.md`) — full 84-field schema, 21-field chart-of-accounts mapping
- [x] Documented Masters > Customers (`masters-customers.md`) — full 8-tab create form, two label/copy bugs found
- [x] Documented Masters > Users / Invite Users (`masters-users.md`) — only list with real tenant data (3 users); 2-tier Role model confirmed
- [x] Documented Display Attributes admin page (`display-attributes.md`) — ~800 real internal table names exposed
- [x] Documented Announcements (`announcements.md`)
- [x] Documented Application Settings (`settings.md`) — General/Config (real tenant record), Geography, Roles Management (full page-permission matrix), Workflow Setting (found a reproducible HTTP 500 + a real procurement DOA-approval engine on the "-1.1" variant), Email templates, API (confirmed 403 role restriction)
- [x] Wrote final `SUMMARY.md` — full data model, function list, cross-cutting bugs/patterns, honest gaps section

## Remaining
- [ ] None — all planned modules documented. Optional future deepening (not required, see SUMMARY.md §5 Known Gaps): Announcements' authoring UI, User profile's 5 non-Overview tabs, ~16 lower-priority Settings categories (HR/procurement/field-service, no CRM connection), Feasibility's actual create trigger (never confirmed).

## Notes for any future resumption
- This tenant's CRM data is entirely empty (0 records in every CRM entity) — this was flagged honestly in every module doc rather than invented. The only populated areas are platform/admin scaffolding (3 Users, ~800 Display Attributes, global Geography, Email templates, 8 seeded procurement Workflows, 1 Config record).
- Session restore: PHPSESSID cookie can go stale across long gaps — if `index` redirects to `login.php`, log in fresh (captcha is a simple 4-digit distorted number, read via screenshot).
- Screenshot workflow: `mcp__playwright__browser_take_screenshot` with a bare filename (no subdirectory) saves correctly to the workspace root; a `crm-bharatnet/...` path prefix throws ENOENT in this sandbox. Screenshot with a bare filename, then `mv` it into `screenshots/crm-bharatnet/` via Bash.
- Bugs found and documented across this review: (1) `TRANST=crm_campaign` singular/plural routing mismatch; (2) duplicate `id="assign_type"` markup on Lead/Contact Assignings; (3) HTTP 500 on Workflow Setting's create form; (4) two Customer-form label/copy bugs (Note tab swap, "The Vendor Pays Me With" Vendor-template leftover); (5) Display Attributes' Edit action not prefilling the selected table.
- All work is committed and pushed to `worker/task-20260720-030153-crm-bharatnet-pilot--full-reverse-engine`.
