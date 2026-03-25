# Changelog — DAS Inventory Management
## Cleveland Electric

All notable changes to this project are documented here.
Format: `[vX.X] — YYYY-MM-DD — Description`

---

## [v4.0] — 2026-03-24
### Infrastructure
- **GitHub repository created** — project live at `https://alecchapman11.github.io/das-inventory/`
- **GitHub Pages deployment** — automatic deploys from `main` branch on every push
- **Claude Code pipeline established** — Claude.ai → Claude Code → GitHub → live site workflow
- **Supabase backend integrated** — replaced localStorage-only storage with cloud database
  - Four tables created: `assets`, `scan_log`, `asset_history`, `technicians`
  - All data syncs to Supabase on every save and loads from cloud on every login
  - Multi-device sync confirmed working across Chrome and Edge
- **Scan log + asset history synced to Supabase** — full cloud persistence for all data types
- **Supabase sync bug fixed** — `save()` function now properly `await`s all Supabase operations to ensure data is written before UI updates
- **History insert bug fixed** — new history and scan log rows now use `insert` instead of `upsert` to prevent silent duplicate skipping
- **Explicit field mapping** — `id` field stripped from new rows before insert to prevent `NOT NULL` constraint violations

### Features Added
- **🔔 In-app notification bell** — amber bell icon in header with red badge count
  - Three alert categories: Warranty Alerts, RMA Aging, Condition Flags
  - Warranty expiring within 90 days or already expired
  - RMA open 30+ days flagged automatically
  - Poor/Damaged assets with no open RMA flagged
  - Click any alert to jump directly to the relevant tab
  - Dismiss individual alerts or all at once
  - Renders on every login automatically
- **🔄 Asset site transfer** — dedicated Transfer workflow in every asset's Actions dropdown
  - Modal with new site, building, zone fields and optional reason
  - Existing sites suggested via datalist autocomplete
  - Transfer logged in asset history automatically with full audit trail
  - Syncs to Supabase immediately
- **🏷 Real scannable barcodes** — replaced fake pipe character placeholders with genuine Code128 barcodes via JsBarcode library
  - Fully scannable by Zebra DS2278-SR and compatible scanners
  - Print window opens cleanly with just labels, no app chrome
- **⏱ RMA escalation flags** — visual escalation indicators on open RMAs
  - 🟡 Amber "⚠ ESCALATE Xd" badge at 30–59 days open
  - 🔴 Red "🔴 CRITICAL Xd" badge at 60+ days open
  - Colored left border on escalated rows for at-a-glance scanning
- **📸 Photo required on Poor/Damaged** — validation gate on scan form
  - Blocks submission if condition is Poor or Damaged with no photo attached
  - Photo upload area highlights with red border to guide the tech
  - Scrolls automatically to the photo section on failed validation
- **✓ RMA close confirmation modal** — replaces instant close with a proper confirmation
  - Shows asset ID, stamps close date, reassures tech the action is reversible
  - Green confirm button distinct from destructive red delete button
- **Smart asset history taxonomy** — human-readable action labels replace developer language
  - `Asset Created` — first scan of a new asset
  - `Asset Updated` — general edit with no specific field change detected
  - `Status Changed` — install status field changed
  - `Condition Changed` — condition field changed
  - `RMA Opened` — RMA status changed from None to active
  - `Warranty Updated` — warranty date changed
  - `Site Transfer` — dedicated transfer workflow
  - `RMA Closed` — RMA marked as closed
  - `Photos Added` — photos attached via asset register modal
  - Multiple changes in one edit chain together: e.g. `Status Changed · Condition Changed`
- **localStorage history migrated to Supabase** — existing history records pushed to cloud

### UI / Design
- **Custom dropdown components** — all native `<select>` elements replaced with fully custom div-based dropdowns
  - No more white flash on open
  - Arrow stays visible at all times, rotates on open/close
  - Consistent dark theme, cyan highlight on selected option
  - Applies to: Assets tab site filter, Scan tab site filter, Log tab technician filter, Tools tab label site filter, Login technician selector
- **Login screen dropdown restyled** — matches app color scheme, smooth open/close
- **Isolated horizontal table scroll** — Assets tab toolbar (title, search, site filter) stays stationary while only the table scrolls horizontally
- **Sticky column headers** — table column headers stay visible as you scroll right
- **Custom scrollbar styling** — thin, blue, matches color scheme across all scrollable areas
- **Scrollbar position fixed** — horizontal scrollbar pinned to bottom of browser viewport
- **Tech name column** — reverted to white text for better readability
- **RMA escalation visual** — red left border on critical rows, badge above action buttons
- **Tools tab reordered** — Label Generator first, CSV Import second, Backup & Restore last
- **Asset Register header** — simplified from "FULL ASSET REGISTER" to "ASSET REGISTER"
- **CSV export buttons removed** — removed from Assets tab, Scan tab, and Log tab toolbars

### Fixed
- Dropdown arrow disappearing after first click — resolved by moving arrow to wrapper `::after` pseudo-element, then fully rebuilt as custom component
- Supabase sync not completing — all data mutation functions now properly `await save()`
- History not appearing in modal after transfer — root cause was `upsert` with `ignoreDuplicates:true` silently skipping new rows
- Test data cleaned from Supabase — removed `Site Transfer Test`, `Manual Test`, `Edited via modal` entries

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

---

## Backlog — Planned Features

### In Progress / Next Session
- 🔐 Supabase Auth — replace PIN system with proper email/password login per technician
- 📱 Mobile optimized layout — responsive design for field use on phones

### Bookmarked
- 📧 Warranty / RMA email or SMS alerts via Resend or Twilio
- 📄 PDF report generation per site — for client handoffs and inspections
- 🗺 Per-site dashboard — drill-down view per site with summary cards
- 🔖 Photo required on Poor/Damaged — ✅ completed v4.0
- 🔖 RMA escalation flags — ✅ completed v4.0
- 🔖 Asset transfer between sites — ✅ completed v4.0
