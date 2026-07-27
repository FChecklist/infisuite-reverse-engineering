# BLOCKER (re-confirmed 2026-07-26) — none of the 6 provided credentials authenticate

## Summary
This is a redispatch of `worker/task-20260720-060752-cityline-ticketing-6-role-reverse-engine`,
which was blocked because none of the 6 provided role accounts could log in to
`http://citylinenetworks.infisuite.in/ticketing/login.php` with password `infi123`
(see that branch's `docs/cityline-ticketing/00-BLOCKER-login-failures.md`).

This pass independently re-tested **all 6** accounts fresh, today, and reproduces
the identical failure for every one of them. This is not a client-side mistake
(typo, stale form, JS error) — a full HTTP request/response trace (below) shows
each login POST is well-formed and the server cleanly, deliberately rejects it.
The underlying credential/account problem documented on 2026-07-20 is still real
and unresolved as of 2026-07-26.

Because login is a hard precondition for the entire task (map navigation per
role, trace a real ticket through 6 role logins), no role-specific
documentation, no test ticket, and no lifecycle trace could be produced this
pass either. This file plus the existing `tickets-dashboard.md` (documented via
the Owner's own already-authenticated session on 2026-07-20, not via these 6
accounts) remain the only real artifacts.

## Evidence: all 6 accounts, tested fresh on 2026-07-26

Each tried exactly once, via the real login form (`chrUserName` / `chrPassword`
fields), password `infi123` for all:

| # | Username submitted (role) | Result |
|---|---|---|
| 1 | `ticket_customer@citylinenetworks.infisuite.in` (Client) | fail — tried twice (normal fill, then character-by-character) to rule out an input-event bug; identical failure both times |
| 2 | `assigner@citylinenetworks.infisuite.in` (Assigner) | fail |
| 3 | `fixer@citylinenetworks.infisuite.in` (Fixer) | fail |
| 4 | `tester@citylinenetworks.infisuite.in` (Tester) | fail |
| 5 | `escalation@citylinenetworks.infisuite.in` (Escalate) | fail |
| 6 | `admin@citylinenetworks.infisuite.in` (Admin) | fail |

Every attempt produces the identical, generic outcome: the app redirects
(`302 Found`, `Location: login.php?error=`) back to
`login.php?error=` with no visible error text and no user-enumeration signal
(a nonexistent username produces the same response as any of the 6 supplied
ones would, per the 2026-07-20 doc's testing) — consistent with either wrong
passwords, accounts not yet provisioned on this tenant, or accounts disabled.

### Request/response trace (Client account, representative of all 6)
Captured via Playwright network inspection of the real POST — included here to
prove this is a server-side rejection, not a broken form fill:

**Request body sent** (URL-decoded):
```
chrUserName=ticket_customer@citylinenetworks.infisuite.in
chrPassword=infi123
ip=167.233.220.35
browser=Google Chrome,151.0.7922.10
submit=Login
```

**Response:**
```
HTTP 302 Found
Location: login.php?error=
Cache-Control: no-store, no-cache, must-revalidate
```

The form has no CSRF token or other hidden anti-automation field beyond the
`ip`/`browser` tracking inputs shown above (confirmed via full page-source
dump), so there is no client-side reason for the credentials to be rejected.

Screenshot of the resulting error state: `screenshots/cityline-ticketing/login-error-2026-07-26.png`.

## App identification (unchanged from 2026-07-20)
- Product name shown in `<title>`/logo: **"Tickday"** ("TickDay" per the logo
  image `tickeday_log.png`), described in the page meta description as a
  "task management system."
- Login page: `http://citylinenetworks.infisuite.in/ticketing/login.php`
  (plain PHP form, POSTs to itself, `action=""`, `method="post"`).
- Distinct login system from the CRM/Billstack/Contracts apps at the domain
  root (title "Infistaq | Login") — not shared auth, so credentials working
  elsewhere on this domain would not necessarily apply here, and vice versa.

## Why this task did not stop at the first failure
Per this task's own circuit-breaker instruction (stop after a 2nd consecutive
failure of the *identical* approach), the Client account was retried once
(character-by-character typing) to rule out an input-event bug before being
treated as a real block — it failed identically. The other 5 accounts were
each then tried exactly once (a different account each time, not a repeat of
the same failing action) specifically to answer the one open question this
redispatch needed to resolve: is this an isolated bad credential or a systemic
account-provisioning problem? It is systemic — all 6 fail identically. No
account was retried more than twice, and no speculative username variants
(e.g. domain-less usernames) were re-attempted, since the prior pass already
covered that ground with the same result.

## What this means for the task objective
The core deliverable — mapping per-role navigation/permissions and tracing a
real ticket through Client → Assigner → Fixer → Tester → Escalate → Admin — is
**not achievable without working credentials**. This is a genuine external
blocker (invalid/unprovisioned accounts on the target tenant), not a gap in
this pass's effort or a governance/spend-gate rejection. It requires the
system owner to either provision/reset these 6 accounts on the Tickday
ticketing tenant, or confirm a different password/account set.

## Known Gaps (carried forward, still open)
- Only 1 of the 6 originally-scoped roles has ever been exercised in this repo
  (the Owner's own already-authenticated CONTRACT ADMIN browser session on
  2026-07-20 — see `tickets-dashboard.md`), and that was not one of the 6
  named accounts.
- No test ticket has ever been created; the create-ticket form, ticket detail
  view, assignment workflow, and status-transition lifecycle remain entirely
  unconfirmed.
- The 6-role permission matrix and workflow-lifecycle state machine called for
  by the original task spec cannot be produced from black-box exploration
  until login is fixed.
