# Changelog — DAS Inventory Management
## Cleveland Electric

All notable changes to this project are documented here.
Format: `[vX.X] — YYYY-MM-DD — Description`

---

## [v4.2] — 2026-03-26

### Features Added
- **🚚 In Transit (Inbound) / (Outbound) statuses** — split old generic "In Transit" into two directional statuses
  - `In Transit (Inbound)` — bright blue pill (`.pill-inbound`, `#2E8FEF`)
  - `In Transit (Outbound)` — amber pill (`.pill-outbound`, `#F5A623`)
  - Updated in scan form, edit modal, inline pill dropdown, dashboard statRows, and CSV export
- **📋 PO Number field** — new optional field on every asset
  - Appears in scan form (`#f-po`) and edit modal (`#edit-po`)
  - Shown as "PO #" column in both compact and full asset tables (between Serial # and Cat.)
  - Included in CSV export as "PO Number" column
  - Stored as `po` on asset objects; safe to read as `a.po||''`
- **🎨 Animated login screen background (Option A)** — moving canvas grid behind the login card
  - Blue grid lines scroll diagonally at 0.25px/frame via `requestAnimationFrame`
  - Cyan dots at each grid intersection, fading toward edges
  - Radial cyan glow at screen center
  - Canvas auto-resizes on window resize
  - Implemented via `initLoginBackground()` called at page load
- **⏳ Animated loading screen** — full-screen overlay on login with progress bar
  - Shows "Connecting to database… Loading assets… Loading history… Checking notifications… Almost ready…" at 160ms intervals
  - Progress bar animates from 0% → 90% during load steps, jumps to 100% on completion
  - Fades out with 0.3s opacity transition; resets for next login
  - Login button shows "Signing in…" with inline spinner while active
  - Implemented via `showLoadingScreen()` / `hideLoadingScreen()`

### UI / Branding
- **App rebranded** — removed "DAS" references throughout
  - Browser tab: `Cleveland Electric | Inventory Management`
  - Header logo: `Cleveland Electric // Inventory Management`
  - Header sub: `Communications & Low Voltage Tracker`
  - Login card: `Inventory Management` + `Communications & Low Voltage Tracker`
  - Loading screen: `Inventory Management`
- **Login dropdown arrow fix** — replaced SVG background-image arrow with text character `▾`
  - `.login-custom-select` now uses `display:flex; justify-content:space-between` (CSS + inline style)
  - Arrow rotates 180° via JS when dropdown is open
- **Login dropdown stays closed after selection** — `event.stopPropagation()` added to all option `onclick` handlers so clicking an option no longer bubbles up to the wrapper and re-triggers `toggleLoginSelect()`

### Design Pipeline (Bookmarked)
- Option A (animated grid) — built this version
- Option B (split-screen with stats panel) — built and reverted; not preferred
- Option G — preferred future direction; to be designed

---

## [v4.1] — 2026-03-25

### Features Added
- **⚡ Inline quick-change pills** — status and condition pills in both asset tables are now clickable dropdowns
  - Click any Status or Condition pill to instantly change the value without opening the edit modal
  - Dropdown appears inline, highlights current value in cyan, dismisses on outside click
  - Change is saved to Supabase, logged to history with before/after diff, and toasted
  - Implemented via `openPillDropdown()`, `quickUpdateAsset()`, `.pill-wrap` / `.pill-dropdown` CSS
- **⎘ Duplicate Asset** — new action in every asset's Actions dropdown
  - Modal pre-filled with all details from the source asset (type, category, mfr, model, site, building, zone, notes)
  - Editable New Asset ID, Serial Number, Site, Building, Zone, Notes fields
  - Resets RMA, dates, and photos on the copy — fresh slate for the new asset
  - Logged as `Asset Created` in history with source reference in notification
- **📋 Since Last Visit alerts** — notification bell now shows what other techs did while you were away
  - On login, captures the previous login timestamp before overwriting it
  - After 1 second, queries Supabase `notifications` for changes by other techs since that timestamp
  - If changes found, bell gets `has-alerts` class and badge count is bumped
  - Opening the bell panel shows a cyan summary section: date of last visit, total change count, up to 5 recent items with `+N more` overflow
  - "Dismiss" button updates the stored login timestamp and clears the section
  - Uses `window._prevLogin` so the panel can re-render the correct window if reopened
  - Loading state shows a pulsing cyan dot with "Checking for updates..." while Supabase query is in flight
- **📊 Recent Activity feed on Dashboard** — last 10 notifications from Supabase rendered as a live feed
  - Async IIFE inside `renderDash()` queries `notifications` table
  - Each entry shows icon (color-coded by action type), asset ID, tech, site, and relative time (`timeAgo()`)
- **◈ Changes Today KPI** — replaced "Total Scans" dashboard KPI card with "Changes Today"
  - Queries Supabase `notifications` for rows with `created_at` within today's UTC date range
  - Falls back to `scanLog.length` if Supabase unavailable
- **👷 Technician Activity from Supabase** — dashboard Technician Activity panel now queries `notifications` table
  - Shows total action count and most recent activity date per tech
  - Previously used local `scanLog` array (undercount); now reflects all cloud activity

### Supabase / Data Fixes
- **History diff/before/after now persisted to Supabase** — `snapshot` JSONB column now includes `diff`, `before`, `after` fields
  - `historyRows.push()` spreads diff data into the snapshot before insert/upsert
  - `newHistoryRows` map also merges diff fields into snapshot for new inserts
  - On login, `doLogin()` extracts `diff`/`before`/`after` back out of `snapshot` for in-memory use
- **`supabaseId` stamped after history insert** — after inserting new history rows, returned `id`s are written back to in-memory entries and persisted to localStorage, preventing duplicate inserts on subsequent `save()` calls
- **`prevLogin` captured before overwrite** — `doLogin()` reads the stored login timestamp into `prevLogin` before calling `saveLastLogin()`, ensuring since-last-visit queries use the correct window
- **`buildSinceLastVisit` tech filter fix** — `currentTech` captured into local `tech` variable before async Supabase call to prevent null filter edge case

### UI
- **Loading indicator for since-last-visit** — pulsing `.notif-checking-dot` shown while querying Supabase; removed automatically when result renders or returns empty

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
- **Smart asset history taxonomy** — human-readable action labels
  - `Asset Created`, `Asset Updated`, `Status Changed`, `Condition Changed`, `RMA Opened`, `Warranty Updated`, `Site Transfer`, `RMA Closed`, `Photos Added`
  - Multiple changes in one edit chain together: e.g. `Status Changed · Condition Changed`
- **Before/after diff in history** — every history entry stores `diff`, `before`, and `after` fields
  - `openHistory()` renders inline strikethrough (before) → cyan bold (after) diff lines
- **`logNotification()` function** — every significant asset mutation inserts a row to Supabase `notifications` table
- **localStorage history migrated to Supabase** — existing history records pushed to cloud

### UI / Design
- **Custom dropdown components** — all native `<select>` elements replaced with fully custom div-based dropdowns
- **Login screen dropdown restyled** — matches app color scheme, smooth open/close
- **Isolated horizontal table scroll** — Assets tab toolbar stays stationary while only the table scrolls
- **Sticky column headers** — table column headers stay visible while scrolling
- **Custom scrollbar styling** — thin, blue, matches color scheme across all scrollable areas
- **Scrollbar position fixed** — horizontal scrollbar pinned to bottom of viewport via JS `fitAssetTable()`
- **RMA escalation visual** — red left border on critical rows
- **Tools tab reordered** — Label Generator first, CSV Import second, Backup & Restore last
- **Asset Register header** — simplified to "ASSET REGISTER"
- **CSV export buttons removed** — removed from Assets, Scan, and Log tab toolbars

### Fixed
- Dropdown arrow disappearing after first click
- Supabase sync not completing — all mutation functions now properly `await save()`
- History not appearing after transfer — `upsert` with `ignoreDuplicates:true` was silently skipping new rows

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

### Next Up
- 🔐 Supabase Auth — replace PIN system with proper email/password login per technician
- 📱 Mobile optimized layout — responsive design for field use on phones

### Bookmarked
- 📧 Warranty / RMA email or SMS alerts via Resend or Twilio
- 📄 PDF report generation per site — for client handoffs and inspections
- 🗺 Per-site dashboard — drill-down view per site with summary cards
- 🌐 Custom domain — das.clevelandelectric.com
