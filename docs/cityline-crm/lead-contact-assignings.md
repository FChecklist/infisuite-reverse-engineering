# Lead/Contact Assignings

## 1. Purpose
Bulk-assigns unallocated Leads/Contacts to users (round-robin "Auto Assign" or manual), for sales-team workload distribution. Identical purpose to Bharatnet's module of the same name.

## 2. Navigation path
Home → **+ Lead/Contact Assignings** button (top-right of the CRM tab strip, present on every CRM tab). URL: `index.php?option=COM_VIEW_CRM_LEAD_AND_CONTACT_ASSIGNINGS`.

## 3. UI / page design
Two-column layout:
- **Left: "User Analysis"** — a Total/Yet-to-start/In-Progress legend + "No Users" empty state (this tenant has users, but apparently none with assigned work — consistent with 0 Leads/Contacts existing to assign).
- **Right: "Lead And Contact Assigning"** form (see §4).
- **Top:** 6 counter tiles — Total Leads, Allocatd [sic] Leads, Un-Allocated Leads, Total Contacts, Allocatd [sic] Contacts, Un-Allocated Contacts — all read **0**.

Screenshot: `15-lead-contact-assignings.png`.

## 4. Assignment form
| Field | Type | Required |
|---|---|---|
| Assign Type | radio: Manual Assign (default) / Auto Assign | Yes |
| Assign Lead/Contacts | radio: Leads (default) / Contacts, each with a live count badge (both 0) | Yes |
| User | dropdown, default "Select Any One" | Yes |
| Order By | radio: Oldest (default) / Newest | Yes |
| Sharing Count | number | Yes |
| **Assign** button | | |

## 5. Underlying data structure (inferred)
An assignment operation presumably writes an `assignedTo → User` FK (+ maybe an assignment-date) onto batches of Lead/Contact records, selected by Oldest/Newest ordering, sized by Sharing Count. Not directly confirmed — 0 records to assign, Assign not exercised.

## 6. Functionality / logic
"Auto Assign" vs "Manual Assign" strongly implies a round-robin/load-balancing algorithm for the Auto path (assigning `Sharing Count` items per user in some rotation) vs. a single explicit User pick for Manual — not confirmed by exercising it.

## 7. Inputs and outputs
Form as above; Assign not exercised (would mutate real data, forbidden by task rules).

## 8. Data conditions
This tenant: **0 Leads, 0 Contacts** — nothing to assign, form is fully empty-state.
