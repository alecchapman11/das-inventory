# CLAUDE.md — DAS Inventory Management
## Cleveland Electric | Project Memory for Claude Code

---

## Project Overview
Single-file HTML/CSS/JS web application for tracking DAS (Distributed Antenna System) equipment and accessories across multiple job sites. Built for Cleveland Electric field technicians.

**Live URL:** (to be added after GitHub Pages deployment)
**Repository:** (to be added after GitHub setup)
**Current Version:** v3.0

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
- **Chart.js 4.4.0** — loaded from cdnjs CDN for dashboard charts
- **IBM Plex Mono + IBM Plex Sans** — loaded from Google Fonts CDN
- **localStorage** — all data stored client-side in the browser
- **No backend, no server, no database** (yet)

---

## Data Storage Keys (DO NOT RENAME)
These localStorage keys are in use — renaming them will wipe user data:
- `das_assets` — array of all asset objects
- `das_log` — array of all scan log entries  
- `das_history` — object mapping asset ID → array of history entries
- `das_techs` — array of technician objects with name and PIN

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
| `doLogin()` | Authenticates and shows app |
| `logAsset()` | Saves/updates an asset from the scan form |
| `renderAssets(q)` | Renders compact asset table on scan panel |
| `renderAssetsFull(q)` | Renders full asset register |
| `renderRMA()` | Renders RMA tracker with aging |
| `renderLog()` | Renders scan log |
| `renderDash()` | Renders dashboard + charts |
| `renderLabels()` | Renders label generator |
| `tableHTML(rows, full)` | Generates asset table HTML |
| `noteCell(text)` | Renders expandable note cell |
| `toggleActMenu(btn, e)` | Opens/closes action dropdown |
| `exportBackup()` | Exports full JSON backup |
| `importBackup(e)` | Restores from JSON backup |
| `importCSV(e)` | Bulk imports from CSV |

---

## Views / Tabs
| View ID | Nav Label | Purpose |
|---|---|---|
| `view-scan` | ⬡ Scan | Main scan form + compact asset list |
| `view-assets` | ▤ Assets | Full asset register |
| `view-rma` | ↩ RMA | RMA tracker with aging |
| `view-log` | ◎ Log | Scan log |
| `view-dash` | ◈ Dashboard | KPIs + charts |
| `view-tools` | ⚙ Tools | Backup, CSV import, label generator |

---

## Commit Message Convention
Use conventional commits:
- `feat:` new feature
- `fix:` bug fix
- `style:` CSS/visual changes only
- `refactor:` code restructure, no behavior change
- `docs:` README, CLAUDE.md, CHANGELOG updates
- `chore:` version bumps, file renames

**Examples:**
```
feat: add warranty expiry email notifications
fix: correct RMA close date not saving on mobile
style: increase scan panel form label font size
docs: update CLAUDE.md with Supabase integration notes
```

---

## Branching Rules
- **`main`** — production, what GitHub Pages serves live. NEVER commit directly.
- **`dev`** — all development work goes here first
- Always create a PR from `dev` → `main` and wait for review before merging

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
- The `today()` utility function — used in many places
- The `save()` function — always call this after modifying any data
- CSS variable names in `:root`

---

## Known Limitations
- Data is localStorage only — not shared across devices or browsers
- No authentication beyond PIN (PINs stored in plaintext in localStorage)
- Photos stored as base64 — large photos can fill localStorage quickly
- Multi-device sync requires Supabase backend (planned future upgrade)

---

## Planned Future Features
- Supabase backend for real-time multi-device sync
- Warranty expiry email/SMS notifications
- PDF report generation per site
- Custom domain (das.clevelandelectric.com)
