# Cityline Portal Home (`/index`, `/index.php`)

Reverse-engineered 2026-07-20 alongside the Ticketing dashboard (see sibling
`docs/cityline-ticketing/tickets-dashboard.md` for methodology/session notes).
Logged in as CONTRACT ADMIN.

## Finding: this is a near-empty shell page, not a real dashboard
The main content area renders entirely blank (light-grey background, no
widgets, no cards) below the top nav, confirmed after a 2s wait for any
async widget load. `get_page_text` on the body returns only the footer
("2021(c) Infistaq", Abouts, Contact) and the profile menu -- there is no
dashboard content to document beyond the shell itself.

## Top navigation
Infisuite logo (link to `index.php`) | a black circular icon (tooltip/label
"Modules" in the accessibility tree, backed by an empty `<select
id="working_role_short_code">` with zero populated `<option>`s at the time
of this pass -- likely a role/module switcher that only populates once a
tenant has >1 assignable role, or requires a different trigger than a plain
click) | megaphone icon | bell icon (notification-count badge, showed 0) |
avatar + "CONTRACT ADMIN" dropdown (Profile, My Messages, Logout).

## Known Gaps
- The "Modules" select's intended option list was never observed populated
  -- could not confirm whether it's meant to switch between CRM/Billstack/
  Contract/Ticketing (the 4 apps this Owner has logins for) or something
  else entirely.
- Megaphone icon's function was not exercised.
- This Home page may simply be a legacy/vestigial entry point now that each
  app (Contracts, Ticketing, CRM, Billstack) has its own dedicated URL --
  consistent with it carrying no real widgets while the other 4 apps all
  have fully-built dashboards.
