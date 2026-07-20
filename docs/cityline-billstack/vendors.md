# Vendors

## 1. Purpose
Supplier/vendor master — the party a Supplier Bill (Purchase Invoice) is raised against.

## 2. Navigation path
Masters → Vendors. URL: `index.php?option=COM_LIST_VENDORS`. Also reachable via the Vendor Bills dashboard tab's counter tile and "TOP 3 VENDORS"/"Recent Vendors" widgets.

## 3. UI / page design
Minimal list (no filter row) — just "Vendors" heading, **+ New Vendors** and **+ Invite Vendors** buttons, Back. Body: "No Vendors" empty state. Screenshot: `04-masters-vendors.png`.

Two distinct creation paths exist (`+ New Vendors` direct-add vs `+ Invite Vendors` — an email-invite flow letting the vendor self-register, mirroring the Users "Invite Users" pattern seen in CRM) — neither exercised (would create/send real data).

## 4–6. Data fields / structure / functionality
Not exercised — 0 records, create form not opened (out of scope for this pass given time budget; the Party* dropdown on the Supplier Bill form is the same Vendor FK, confirmed empty/unselectable there too).

## 7–8. Inputs/outputs, data conditions
This tenant: **0 Vendors**. Same empty condition Bharatnet's pilot found.
