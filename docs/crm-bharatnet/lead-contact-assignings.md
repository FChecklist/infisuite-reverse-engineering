# Lead/Contact Assignings

## 1. Purpose

A bulk-assignment tool for distributing unallocated Leads and Contacts among Users (sales reps) — either manually (pick a specific user) or automatically (round-robin/distribute across a Project's team, inferred).

## 2. Navigation path

Home (top nav) → any CRM tab (Leads/Contacts/Companies/Deals/Campaign/Projects/Feasibility) → **"+ Lead/Contact Assignings"** button, always visible top-right of the tab strip regardless of which CRM tab is active. URL: `index.php?option=COM_VIEW_CRM_LEAD_AND_CONTACT_ASSIGNINGS`. This is a full-page view (not a slide-over), with a **Back** link (browser history back) instead of a close button.

## 3. UI / page design

Two-column layout below a 6-tile KPI strip:
- **KPI strip:** Total Leads, Allocatd Leads [sic — typo, "Allocated"], Un-Allocated Leads, Total Contacts, Allocatd Contacts, Un-Allocated Contacts — all clickable link-tiles (href `#!`, non-functional in this state), all showing 0 in this tenant.
- **Left panel — "User Analysis":** a per-user breakdown legend (Total / Yet To start / In-Progress, color-coded pills) — "No Users" empty state (no assignment activity yet).
- **Right panel — "Lead And Contact Assigning"**, the actual assignment form (see §4).

Screenshots: `lead-contact-assignings-01.png` (Manual Assign, default state), `lead-contact-assignings-03-auto-selected.png` (Auto Assign state).

## 4. Assignment form

| Field | Type | Required | Notes |
|---|---|---|---|
| Assign Type | radio | **Yes** (*) | Two options: **Manual Assign** (default, checked) / **Auto Assign**. Switching this toggles the field below (User ↔ Project) via an inline `onchange="chnageAssigneType()"` handler. |
| Assign Lead/Contacts | radio, `name="assign_lead_contact"` | **Yes** (*) | **Leads** (`crm_leads`, default checked) / **Contacts** (`crm_contacts`). Each option has an adjacent live count badge (both 0 in this tenant). |
| Order By | radio, `name="order_by"` | **Yes** (*) | **Oldest** (`ASC`, default) / **Newest** (`DESC`) — controls which unallocated records get assigned first. |
| **User** (shown when Assign Type = Manual Assign) | searchable dropdown, `name="application_user_id"`, default "Select Any One" | **Yes** (*) | FK → User. Empty option list in this tenant beyond the placeholder — the 3 known users (Anthony Johnston/CRM Admin/Billstack Admin) seen elsewhere did **not** populate here, an unexplained inconsistency worth flagging — possibly this dropdown filters to users with a specific role/permission (e.g. "Sales Rep") that none of the 3 seed users hold. **Gap.** |
| **Project** (shown when Assign Type = Auto Assign, replaces the User field) | dropdown, `name="project_id"`, default "Select Any One" | **Yes** (*) | FK → Project (empty in this tenant, 0 Projects exist). Confirms Auto Assign distributes Leads/Contacts across a Project's team members (see `projects.md`'s Team Member field) rather than to one specific user — the mechanism itself (e.g. round-robin vs. load-balanced) was not observable with 0 data. |
| Sharing Count | text/number, `name` not captured (labeled "Count") | **Yes** (*) | Presumably caps how many records each user/project-member receives per assignment run. |

**Action:** **Assign** button (not exercised, per task rules — this is a mutating bulk action).

### Observed bug
Both the Manual-Assign and Auto-Assign radio `<input>` elements share the literal HTML `id="assign_type"` (only their `value` differs, 1 vs 2) — an invalid duplicate-ID pattern. `document.getElementById('assign_type')` therefore always resolves to the *first* (Manual Assign) element regardless of which is visually selected, which broke a naive JS-based interaction attempt during this review (clicking the Auto Assign radio via `getElementById` silently re-clicked Manual Assign instead). A real user clicking directly via mouse/keyboard focus is unaffected since browsers resolve duplicate-labelled radios by their individual DOM node under click, not by `id` lookup — but any custom JS on the page relying on `getElementById('assign_type')` for the *second* radio would silently target the wrong element. Documented as-is since it's a genuine markup defect, not a task artifact.

## 5. Underlying data structure (inferred)

- This screen operates across two entities (**Lead**, **Contact**) simultaneously via a single shared assignment mechanism — implies both entities carry a common `assigned_user_id`/ownership field, distinct from `originator` (which is more likely a "created by" field, given it defaults to the creating user and doesn't change here).
- `Assigning.user` → **User** (manual mode) or `Assigning.project` → **Project** (auto mode, indirectly to that Project's `project_team_user_id[]` members)

## 6. Functionality / logic observed

- **Assign** — a bulk-write action: takes N un-allocated Leads or Contacts (ordered Oldest/Newest-first) and assigns them either to one chosen User or spreads them across a chosen Project's team, capped/batched by "Sharing Count". Not executed (mutating action, excluded per task rules).
- No per-record assignment UI was found elsewhere (no Lead/Contact list-view rows to individually reassign) — this bulk screen is the only assignment mechanism observed.

## 7. Inputs and outputs

- Input: form above.
- Output (expected, not observed): Leads/Contacts previously in "Un-Allocated" bucket move to "Allocated", KPI tiles update, and the "User Analysis" panel would presumably populate with per-assignee Total/Yet To Start/In-Progress counts.

## 8. Data conditions

- All KPI counts are 0 (0 Leads, 0 Contacts total in this tenant) — the assignment flow could not be exercised end-to-end.
- The User dropdown (Manual mode) is unexpectedly empty despite 3 known Users existing in the system (seen populating every other Originator/Team Member dropdown) — flagged as an unexplained gap, possibly role-scoped.
