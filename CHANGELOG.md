# Changelog — DAS Inventory Management
## Cleveland Electric

All notable changes to this project are documented here.
Format: `[vX.X] — YYYY-MM-DD — Description`

---

## [v3.0] — 2025-03-23
### Added
- Warranty expiry alert banner (90-day threshold, dismissable)
- Expandable/collapsible notes — click to expand full text in floating panel
- Dashboard bar chart — assets by site (Chart.js)
- Dashboard doughnut chart — assets by category (Chart.js)
- Technician activity summary panel on dashboard
- Per-site dropdown filtering on Scan panel, Asset Register, Label Generator
- Per-asset history/audit log — every scan, edit, RMA close recorded
- Full JSON backup export and restore with confirmation dialog
- Bulk CSV import with ID-based upsert logic
- Print-ready asset label generator with multi-select and browser print
- Scan Log technician filter dropdown
- New ⚙ Tools tab consolidating backup, import, and label tools
- 🏷 label shortcut on every asset row

### Changed
- Dashboard KPI cards and charts use monochromatic blue palette
- Action buttons collapsed into ⋯ Actions dropdown per row (compact rows)
- Photos modal now supports add/remove photos directly from asset register

### Fixed
- JavaScript syntax error (nested backtick in template literal) causing full script crash
- Native select dropdown rendering issues across browsers

---

## [v2.2] — 2025-03-21
### Fixed
- RMA Aging Tracker moved from Dashboard to RMA Tracker tab
- RMA open/closed tracking — added Open/Closed sections, one-click Close button
- Close date stamped automatically when RMA closed
- Serial Number column added to compact Asset Register table

---

## [v2.1] — 2025-03-20
### Changed
- Technician names updated to Cleveland Electric team:
  - Alec Chapman (PIN: 1234)
  - Ryan Lewis (PIN: 2345)
  - Larry Christopher (PIN: 3456)
- Sample asset data updated to reflect new technician names

---

## [v2.0] — 2025-03-20
### Added
- Technician login screen with PIN authentication
- Add New Technician modal
- Active technician indicator in header with sign out
- Edit modal for every asset (pre-filled form)
- Delete asset with confirmation modal
- Photo attachments per asset (base64, thumbnail preview, lightbox)
- Scan In / Scan Out toggle with three date fields
- RMA Aging Tracker on Dashboard
- Technician name stamped on all asset and scan log records
- App title updated to: Cleveland Electric – DAS Inventory Management

---

## [v1.0] — 2025-03-20
### Added
- Initial application build
- Asset Register with full DAS equipment fields
- Barcode scanner keyboard-wedge integration (Zebra DS2278-SR)
- Scan Log with timestamped events
- RMA Tracker tab
- Dashboard with live KPI cards
- CSV export for Asset Register and Scan Log
- Dropdown validation on Category, Install Status, Condition, RMA Status
- Sample data seeded with 7 DAS assets across 3 sites
