# Cityline Ticketing -- Tickets Dashboard (`/ticketing/`)

Reverse-engineered 2026-07-20 by navigating a live Owner-authenticated session
(`citylinenetworks.infisuite.in`) via Claude in Chrome, after the automated
Playwright pass's 6 role-account logins all failed (see
`00-BLOCKER-login-failures.md`). This pass logged in as whichever role the
Owner's own browser session already held -- **CONTRACT ADMIN** -- not all 6
roles from the original task scope. Read-only: no ticket was created, edited,
or deleted.

## Top navigation
`THEME-LOGO` (links home) | home icon (back to portal `index`) | headset icon
(tooltip "View CRM" -- SSO-style link into the CRM module; not followed in
this pass, see Known Gaps) | briefcase icon | bell icon (notifications) |
avatar + "CONTRACT ADMIN" (profile menu).

## Tickets dashboard (`Tickets` heading, "Back" link)
4 stat tiles, each with a value and a `0%` badge (no comparison data yet, tenant
has zero tickets):
- **Open Tickets** -- 0
- **In-progress Tickets** -- 0
- **Fixed Tickets** -- 0
- **Close Tickets** -- 0

Below the tiles, a "Total Tickets : 0" chip, then a filter bar:
**User** (dropdown, default "All User"), **Status** (dropdown, default "All"),
**Priority** (dropdown, default "All"), **From** (date picker), **To** (date
picker), **Search** button.

Ticket table columns (empty state: "No Tickets Available"):
`# | Ticket No. | Ticket Subject | Requested Date | Last Updated | Priority |
Status | Assign Staff | Action`

This column set implies the underlying Ticket entity has at minimum: a
sequential/display ticket number distinct from an internal `#` id, a Subject,
created + updated timestamps, a Priority enum, a Status enum (the 4 dashboard
tiles -- Open/In-progress/Fixed/Close -- are almost certainly that Status
enum's values), and an assignable Staff/User FK.

## Known Gaps
- Only 1 of the 6 originally-scoped roles was exercised (whichever the Owner's
  real browser session was authenticated as).
- No ticket exists in this tenant, so the create-ticket form, ticket detail
  view, priority/status values beyond the 4 dashboard buckets, and the
  assignment workflow are all unconfirmed.
- The Status/Priority/User filter dropdowns' full option lists were not
  opened/enumerated.
- The headset "View CRM" icon, when clicked from the sibling Contracts
  module's nav bar during this same pass, unexpectedly redirected to
  `indyanet.infisuite.in/login.php` (a different, already-documented tenant)
  rather than a cityline-scoped view -- not re-tested from within Ticketing
  itself, so it's unconfirmed whether Ticketing's own headset icon behaves
  the same way.
