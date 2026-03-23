# Cleveland Electric — DAS Inventory Management

> A single-file web application for tracking Distributed Antenna System (DAS) equipment and accessories across multiple job sites.

**Current Version:** v3.0 | **Status:** Active Development

---

## Overview

The DAS Inventory Management app is an internal tool built for Cleveland Electric field technicians to track, audit, and manage DAS equipment installations across multiple sites. It runs entirely in the browser with no backend required — all data is stored locally using the browser's localStorage.

---

## Features

| Feature | Description |
|---|---|
| 🔐 Technician Login | PIN-based login system per technician |
| ⬡ Barcode Scanning | Integrates with Zebra DS2278-SR Bluetooth scanner |
| ▤ Asset Register | Full DAS equipment tracking with 15+ fields per asset |
| 📷 Photo Attachments | Attach and view photos per asset |
| ↩ RMA Tracker | Open/closed RMA tracking with aging indicators |
| ◎ Audit History | Full per-asset change history with timestamps |
| ◈ Dashboard | Live KPIs, bar/doughnut charts, technician activity |
| ⚙ Tools | Backup/restore, bulk CSV import, label generator |
| 🏷 Label Printing | Print-ready asset labels with barcode placeholder |
| 📊 CSV Export | Export asset register and scan log to CSV |

---

## Quick Start

1. Download `index.html`
2. Open it in any modern browser (Chrome recommended)
3. Log in with your technician name and PIN
4. Start scanning!

**Default technician PINs:**
| Technician | PIN |
|---|---|
| Alec Chapman | 1234 |
| Ryan Lewis | 2345 |
| Larry Christopher | 3456 |

> ⚠️ Change PINs after first login by adding new technicians via the login screen.

---

## Scanner Setup

This app is optimized for the **Zebra DS2278-SR** Bluetooth barcode scanner:

1. Pair the scanner with your device via Bluetooth
2. Open the app and navigate to the **Scan Asset** tab
3. Click the barcode input field
4. Scan any asset barcode — data populates automatically

The scanner acts as a keyboard wedge — scanned data types directly into the active field.

---

## Data Backup

All data is stored in your browser's localStorage. To avoid data loss:

1. Go to **⚙ Tools** → **Export Full Backup**
2. Save the `.json` file securely
3. To restore: **⚙ Tools** → **Restore from Backup**

> ⚠️ Clearing browser data or using a different browser/device will require restoring from backup.

---

## Bulk Import

To import existing assets from a spreadsheet:

1. Export your data as CSV with these columns:
   `Asset ID, Equipment Type, Category, Manufacturer, Model, Serial Number, Site / Project, Building, Floor / Zone, Install Status, Condition, Notes`
2. Go to **⚙ Tools** → **Bulk CSV Import**
3. Drop your CSV file or click to select

Existing Asset IDs will be updated. New IDs will be added.

---

## Development

This project uses a two-tool workflow:

- **Claude.ai (this chat)** — Feature design, planning, and complex discussions
- **Claude Code (terminal)** — Makes code changes, commits, and pushes to GitHub

### Branching
- `main` — production / live
- `dev` — all development work

### Local Development
No build tools required. Open `index.html` directly in a browser.

### Making Changes
See `CLAUDE.md` for full project conventions, CSS variables, function reference, and commit message format.

---

## Version History

| Version | Date | Summary |
|---|---|---|
| v1.0 | 2025-03-20 | Initial build — asset register, scan log, RMA tracker, dashboard |
| v2.0 | 2025-03-20 | Technician login, edit/delete, photos, scan in/out, RMA aging |
| v2.1 | 2025-03-20 | Updated technician names to Cleveland Electric team |
| v2.2 | 2025-03-21 | RMA aging moved to RMA tab, open/closed split, serial # in table |
| v3.0 | 2025-03-23 | Warranty banner, charts, audit history, backup/restore, bulk import, label generator, expandable notes |

---

## Tech Stack

- **Vanilla HTML/CSS/JS** — no frameworks, no build tools
- **Chart.js 4.4.0** — dashboard charts (CDN)
- **IBM Plex Mono/Sans** — typography (Google Fonts CDN)
- **localStorage** — client-side data persistence

---

## Roadmap

- [ ] Supabase backend for real-time multi-device sync
- [ ] Warranty expiry notifications
- [ ] PDF report generation per site
- [ ] Custom domain setup
- [ ] Mobile-optimized layout

---

## License

Internal use only — Cleveland Electric. Not for public distribution.
