# Announcements

## 1. Purpose

A company-wide news/notice feed (e.g. holiday notices, policy updates) shown to all users of the tenant — a generic HR/ops communication feature, not CRM-specific.

## 2. Navigation path

Top icon bar → 4th icon (megaphone) → `index.php?option=COM_VIEW_ANNOUNCEMENTS`.

Administration/creation is under **Application Settings** (gear icon) → **ANNOUNCEMENTS** category, which has two sub-pages per `00-navigation-map.md`: **Announcement Categories** (`COM_LIST_ANNOUNCEMENT_CATEGORIES`) and **Company Announcements** (`COM_LIST_COMPANY_ANNOUNCEMENTS`) — the latter is presumably where new Announcements are authored; not explored in depth in this pass (see `settings.md`).

## 3. UI / page design

Simple 3-card layout (`announcements-01.png`):
- **Company card** (left): tenant's logo ("Infisuite") + tenant name ("Bharatnet") — purely branding, not interactive.
- **My Announcements card**: "0 Announcements" — announcements addressed/relevant to the logged-in user.
- **History card** (right): a **Year / Month** toggle (Year selected by default) + "0 Announcements" — a browsable archive of past announcements, filterable by year or month.

No create/post button is visible on this feed page itself for the CRM Admin role viewing it here — consistent with Announcements being authored via the separate Settings > Company Announcements admin page, with this page being the read-only consumption view.

## 4. Underlying data structure (inferred)

**Entity: Announcement.** Relationships:
- `Announcement.category` → **Announcement Category** (Settings sub-page)
- `Announcement` scoped to a Company (tenant) — the branding card suggests announcements are tenant/company-wide, not per-CRM-module
- `Announcement` visible to specific users ("My Announcements" implies either audience-targeting or a read/unread personal view over the same shared feed) — not confirmed which, since 0 announcements exist

## 5. Functionality / logic observed

- **View** (My Announcements + History, Year/Month toggle) — no data to page through in this tenant.
- **Create/manage**: not exercised here — see `settings.md` > Announcement Categories / Company Announcements for the admin-side entry points (not explored in depth, flagged as a gap given Announcements' low relevance to the CRM data model this task is centered on).

## 6. Inputs and outputs

- Input: Year/Month toggle (view-only filter).
- Output: list of announcement entries (none present to observe format/fields for an individual announcement — **gap**).

## 7. Data conditions

- 0 Announcements exist in this tenant across both "My Announcements" and "History" — no announcement detail view, fields, or authoring form could be observed from this page. The authoring form lives in Settings and was not explored in this pass given the task's CRM-first scope.
