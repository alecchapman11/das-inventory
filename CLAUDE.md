# CLAUDE.md — DAS Inventory Management
## Cleveland Electric | Project Memory for Claude Code

---

## Project Overview
Single-file HTML/CSS/JS web application for tracking DAS (Distributed Antenna System) equipment and accessories across multiple job sites. Built for Cleveland Electric field technicians.

**Live URL:** https://alecchapman11.github.io/das-inventory/
**Repository:** https://github.com/alecchapman11/das-inventory
**Current Version:** v4.1

---

## File Structure
```
/
├── index.html        ← THE ENTIRE APP (single file — all HTML, CSS, JS)
├── CLAUDE.md         ← This file
├── README.md         ← Project documentation
└── CHANGELOG.md      ← Auto-updated change log
```

**Critical:** The entire application lives in `index.html`. There are no separate CSS or JS files. All changes go in `index.html`.

---

## Tech Stack
- **Vanilla HTML/CSS/JS** — no build tools, no frameworks, no npm
- **Supabase JS client v2** (`@supabase/supabase-js@2`) — cloud database backend
- **Chart.js 4.4.0** — loaded from cdnjs CDN for dashboard charts
- **JsBarcode 3.11.6** — loaded from CDN for real scannable Code128 barcodes
- **IBM Plex Mono + IBM Plex Sans** — loaded from Google Fonts CDN
- **localStorage** — primary local cache; Supabase is the source of truth
- **GitHub Pages** — live deployment from `main` branch

---

## Supabase Configuration
```javascript
const SUPABASE_URL = 'https://eofionpjpamphyioznxf.supabase.co';
const SUPABASE_KEY = 'sb_publishable_v3Bq9YMPL5QeU42bQKb6IQ_AfymazQD';
const db = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);
```

### Supabase Tables
| Table | Key Columns | Notes |
|---|---|---|
| `assets` | `asset_id`, `type`, `category`, `status`, `site`, `condition`, `rma`, `warranty`, `tech`, `serial`, `mfr`, `model`, `zone`, `building`, `notes`, `photos`, `datein`, `dateout`, `dateinstalled`, `rmaOpenDate`, `rmaCloseDate` | Upserted on every save |
| `scan_log` | `id`, `asset_id`, `date`, `tech`, `site`, `location`, `action` | New rows inserted; existing upserted by `id` |
| `asset_history` | `id`, `asset_id`, `date`, `tech`, `action`, `snapshot` | `snapshot` JSONB stores status/condition/rma/notes + `diff`/`before`/`after` for full audit trail. New rows inserted and stamped with `supabaseId` to prevent re-insert |
| `technicians` | `id`, `name`, `pin` | Upserted on save |
| `notifications` | `id`, `asset_id`, `action`, `tech`, `site`, `details`, `read`, `created_at` | Append-only; used for Recent Activity, Changes Today KPI, Technician Activity, and Since Last Visit alerts |

### Supabase Sync Pattern
- `save()` — async; upserts assets + techs, inserts/upserts scan_log, inserts new history rows (stamping returned `id` as `supabaseId`), upserts existing history rows
- `doLogin()` — fetches all tables from Supabase on login; falls back to localStorage on error
- `logNotification()` — async; inserts a row to `notifications` on every significant asset change

---

## Data Storage Keys (DO NOT RENAME)
These localStorage keys are in use — renaming them will wipe user data:
- `das_assets` — array of all asset objects
- `das_log` — array of all scan log entries
- `das_history` — object mapping asset ID → array of history entries
- `das_techs` — array of technician objects with name and PIN
- `das_last_login_<TechName>` — per-tech ISO timestamp of last login (used for since-last-visit alerts)

---

## Technicians (DO NOT CHANGE NAMES OR SEED DATA)
```javascript
techs = [
  { name: 'Alec Chapman',      pin: '1234' },
  { name: 'Ryan Lewis',        pin: '2345' },
  { name: 'Larry Christopher', pin: '3456' },
]
```
Never modify these names or PINs in the seed data. New technicians are added through the app UI.

---

## Asset ID Format
Assets use the format: `DAS-XXXXX` (e.g. `DAS-00001`, `DAS-00042`)
Always use this format when generating example or sample data.

---

## CSS Architecture
All CSS is in a single `<style>` block at the top of `index.html`.

### CSS Variables (defined in `:root`)
```css
--navy:       #0D1B2A   /* page background */
--navy-mid:   #1B2D42   /* card/panel backgrounds */
--navy-light: #243850   /* hover states */
--blue:       #1E6FBF   /* primary blue */
--blue-bright:#2E8FEF   /* bright blue accent */
--cyan:       #00C2D4   /* cyan accent */
--green:      #0FA86E   /* success/installed */
--amber:      #F5A623   /* warning/pending */
--red:        #E5534B   /* error/rma/danger */
--slate:      #8FA3BC   /* muted text */
--border:     rgba(255,255,255,0.08)
--text:       #D8E6F3
--text-dim:   #6B8499
```

### Color Usage Rules
- **System-wide status colors** (green/amber/red) are used for pills and indicators throughout the app — DO NOT change these
- **Dashboard only** uses a monochromatic blue palette for KPI cards and charts — intentionally different from system colors
- Always use CSS variables, never hardcode colors unless absolutely necessary

---

## Key JavaScript Functions
| Function | Purpose |
|---|---|
| `populateTechSelect()` | Populates login dropdown from `techs` array |
| `doLogin()` | Authenticates, loads from Supabase, stamps prevLogin, shows app |
| `doLogout()` | Clears session and returns to login screen |
| `save()` | Async — syncs all data to Supabase + localStorage |
| `logAsset()` | Saves/updates an asset from the scan form |
| `saveEdit()` | Saves asset edits from the edit modal with before/after diff |
| `renderAssets(q)` | Renders compact asset table on scan panel |
| `renderAssetsFull(q)` | Renders full asset register with dynamic height |
| `fitAssetTable()` | JS-computed height for assets table scroll container |
| `renderRMA()` | Renders RMA tracker with aging + escalation badges |
| `renderLog()` | Renders scan log |
| `renderDash()` | Renders dashboard + charts + Recent Activity + KPIs |
| `renderLabels()` | Renders label generator with JsBarcode |
| `tableHTML(rows, full)` | Generates asset table HTML with inline pill dropdowns |
| `noteCell(text)` | Renders expandable note cell |
| `toggleActMenu(btn, e)` | Opens/closes action dropdown |
| `openPillDropdown(id, field, val, opts, e)` | Opens inline status/condition quick-change dropdown |
| `quickUpdateAsset(id, field, val)` | Async — updates a single field inline with history + notification |
| `openDuplicate(id)` | Opens duplicate asset modal pre-filled from source |
| `confirmDuplicate()` | Async — creates duplicate asset with new ID |
| `openTransfer(id)` | Opens site transfer modal |
| `confirmTransfer()` | Async — executes site transfer with history entry |
| `openRMAClose(id)` | Opens RMA close confirmation modal |
| `confirmCloseRMA()` | Async — closes RMA with history + notification |
| `buildDiff(old, new)` | Returns human-readable before→after diff string |
| `logNotification(id, action, site, details)` | Async — inserts row to Supabase notifications table |
| `buildNotifications()` | Builds in-app alert list from assets (warranty/RMA/condition) |
| `renderNotifBell()` | Updates bell badge count |
| `toggleNotifPanel()` | Opens/closes notification panel; triggers since-last-visit check |
| `renderNotifList()` | Renders alert list in notification panel |
| `buildSinceLastVisit(prevLogin)` | Async — queries notifications for other-tech changes since last login |
| `renderSinceLastVisit(prevLogin)` | Async — renders since-last-visit section in notification panel |
| `dismissSinceLastVisit()` | Dismisses since-last-visit section and saves current login time |
| `saveLastLogin()` | Writes current ISO timestamp to localStorage for current tech |
| `exportBackup()` | Exports full JSON backup |
| `importBackup(e)` | Restores from JSON backup |
| `importCSV(e)` | Bulk imports from CSV |

---

## Views / Tabs
| View ID | Nav Label | Purpose |
|---|---|---|
| `view-scan` | ⬡ Scan | Main scan form + compact asset list |
| `view-assets` | ▤ Assets | Full asset register with inline pill editing |
| `view-rma` | ↩ RMA | RMA tracker with escalation badges |
| `view-log` | ◎ Log | Scan log |
| `view-dash` | ◈ Dashboard | KPIs (incl. Changes Today) + charts + Recent Activity + Technician Activity |
| `view-tools` | ⚙ Tools | Label Generator → CSV Import → Backup & Restore |

---

## Notification Bell System
The bell icon (`#notifBell`) in the header drives three layers of alerts:

1. **In-app asset alerts** — warranty expiry, RMA aging, Poor/Damaged condition. Built from local `assets` array via `buildNotifications()`.
2. **Recent Activity feed** — last 10 notifications from Supabase `notifications` table, rendered async inside `renderDash()`.
3. **Since Last Visit** — on login, queries `notifications` for changes made by *other* techs since the current tech's last login. Shown as a summary section at the top of the notification panel. Dismissed via `dismissSinceLastVisit()` which updates the localStorage timestamp.

`window._prevLogin` — global that holds the pre-login timestamp so `toggleNotifPanel()` can re-render the since-last-visit section after login.

---

## Asset History / Diff System
Every asset mutation records a history entry in `assetHistory[assetId]`:
```javascript
{
  date, tech, action,          // who/when/what
  snapshot: { status, condition, rma, notes },  // current state snapshot
  diff,                        // "Status: Installed → RMA Sent · Condition: Good → Damaged"
  before: { status, condition, rma, site, zone, warranty },
  after:  { status, condition, rma, site, zone, warranty }
}
```
- `supabaseId` is added after successful Supabase insert to prevent re-inserting on next `save()`
- `openHistory()` renders inline diff lines with strikethrough (before) → cyan bold (after)
- `buildDiff(old, new)` compares: Status, Condition, RMA, Site, Zone, Warranty

---

## Commit Message Convention
Use conventional commits:
- `feat:` new feature
- `fix:` bug fix
- `style:` CSS/visual changes only
- `refactor:` code restructure, no behavior change
- `docs:` README, CLAUDE.md, CHANGELOG updates
- `chore:` version bumps, file renames

---

## Branching Rules
- **`main`** — production, what GitHub Pages serves live. All changes go directly to main.
- No active dev branch — all commits pushed directly to `main`

---

## Version Bumping
When completing a change order:
1. Update the version comment at the top of `index.html`
2. Update `Current Version` in this file
3. Add entry to `CHANGELOG.md`
4. Commit with: `chore: bump version to vX.X`

---

## What NOT to Touch
- Technician names/PINs in seed data
- localStorage key names (`das_assets`, `das_log`, `das_history`, `das_techs`)
- Supabase URL and key constants
- The `today()` utility function — used in many places
- The `save()` function — always call this after modifying any data
- CSS variable names in `:root`
- The `supabaseId` field on history entries — controls insert vs upsert routing

---

## Known Limitations
- PINs stored in plaintext in localStorage (no proper auth yet)
- Photos stored as base64 — large photos can fill localStorage quickly
- No offline mode — Supabase unavailability falls back to localStorage read-only
- Since-last-visit only tracks changes by *other* techs (by design)

---

## Planned Future Features
- Supabase Auth — replace PIN system with proper per-tech email/password login
- Mobile-optimized layout — responsive design for field use on phones
- Warranty / RMA email or SMS alerts via Resend or Twilio
- PDF report generation per site — for client handoffs and inspections
- Per-site dashboard drill-down — summary cards per site
- Custom domain (das.clevelandelectric.com)
