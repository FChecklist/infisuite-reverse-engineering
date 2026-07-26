# Cityline Ticketing ("Tickday") — 6-Role Reverse-Engineering: SUMMARY

## Status: BLOCKED — confirmed, not a governance/spend rejection

This is a redispatch of `worker/task-20260720-060752-cityline-ticketing-6-role-reverse-engine`.
That attempt was blocked because none of the 6 provided role credentials
authenticated. This pass (2026-07-26) independently re-tested **all 6**
accounts fresh, one login attempt per account (plus one retry of the Client
account to rule out an input-event bug), and reproduces the identical
failure for every one of them — see
`00-BLOCKER-login-failures-2026-07-26.md` for the full evidence, including an
HTTP request/response trace proving the rejection happens server-side (a
well-formed POST is met with a clean `302 Found → login.php?error=`).

**This is a real, external credential/account-provisioning problem on the
target tenant, not a bug in this pass's approach, and not a spend-governance
gate rejection.** It cannot be worked around from black-box browser
exploration — there is no way to authenticate without either the system
owner fixing/confirming these 6 accounts, or providing a working alternate
credential set.

## What could be done, and what could not

| Original task deliverable | Status |
|---|---|
| Map navigation per role (`00-navigation-map-<role>.md` × 6) | **Not possible** — no role account authenticates |
| Identify per-role CAN/CANNOT (create, assign, status change, escalate, close) | **Not possible** |
| Create one test ticket as Client, trace it through all 6 roles | **Not possible** — cannot log in as Client |
| `workflow-lifecycle.md` state machine | **Not possible** — no ticket, no role views observed |
| Per-role `<role>.md` files | **Not possible** |
| Role-permission matrix / data model / Known Gaps | This file (partial — see below) |

The only artifact that exists in this repo for this app is
`tickets-dashboard.md`, produced on 2026-07-20 via the Owner's own
already-authenticated browser session (role: CONTRACT ADMIN, not one of the
6 named accounts) — it documents one dashboard screen with zero tickets in
the tenant. It is **not** a product of these 6 credentials and does not
substitute for the 6-role deliverable.

## Data model (inferred only from `tickets-dashboard.md`, still unconfirmed)
A `Ticket` entity has at minimum: an internal `#` id distinct from a
display `Ticket No.`, a `Ticket Subject`, `Requested Date` / `Last Updated`
timestamps, a `Priority` enum, a `Status` enum (dashboard tiles suggest at
least Open / In-progress / Fixed / Close), and an assignable `Staff`/`User`
foreign key ("Assign Staff" column). This is **inferred from column headers
only** — no ticket has ever existed in this tenant to confirm field
behavior, valid transitions, or who can perform them.

## Known Gaps
- All 6 role-permission questions (create/assign/status-change/close/reopen/
  escalate) are **unanswered** — login blocks every one of them.
- No test ticket has ever been created in this tenant under this task, so the
  entire workflow lifecycle (Client → Assigner → Fixer → Tester → Escalate →
  Admin) remains unobserved.
- The Status/Priority/User filter dropdowns' option lists are unenumerated.
- Whether the credentials are simply wrong, the accounts are unprovisioned,
  or the accounts are disabled cannot be distinguished from outside (no
  user-enumeration signal in the login response).

## Recommendation
Route back to the system owner to either (a) confirm/reset the 6 account
passwords on the Tickday ticketing tenant, or (b) provision the 6 named
accounts if they don't yet exist there, then redispatch. No further
black-box login variants are worth attempting — this pass and the prior
2026-07-20 pass together have tried the literal credentials for all 6 roles,
a domain-less username variant, and both instant and character-by-character
form fills, all with the identical result.
