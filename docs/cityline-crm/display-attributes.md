# Display Attributes

## 1. Purpose
A platform-wide admin table listing every application DB table (`Application Table Name`) with an Active/Inactive toggle and a "Current Status". Same purpose as Bharatnet's `display-attributes.md`: not a CRM feature per se, but a shared admin surface controlling which entities show up in per-column display-config elsewhere in the UI.

## 2. Navigation path
Top nav icon (black circle "robot" glyph, 2nd icon) → `index.php?option=COM_LIST_DISPLAY_ATTRIBUTES`.

## 3. UI / page design
Standard Masters-style list (Active/Inactive/All, Records/Search, Status/Import/Export/+Create, Back, gear). Columns: Id, Application Table Name, Is Active (toggle button), Current Status (badge), Status Date, Action (edit).

Sampled rows (first ~8 of what is almost certainly ~800, matching Bharatnet's count): `accountsection`, `account_groups`, `account_sections`, `account_type`, `action_email_settings`, `action_log`, `act_categories`, `additional_charges` — all "Active". Screenshot: `23-display-attributes.png`.

## 4–8.
Identical structure/purpose to Bharatnet's documented Display Attributes page — platform/DB-schema scaffolding, not tenant-specific data, not further re-catalogued here to avoid duplicating that already-thorough documentation. No CRM-specific business logic; not exercised beyond viewing.
