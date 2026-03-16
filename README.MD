# FieldPro — Construction Field Tool

**FieldPro** is a production-ready, offline-first Progressive Web App (PWA) built for construction site use. It runs entirely in the browser with no backend required, installs to the home screen on any device, and works without an internet connection.

Designed for site supervisors, quantity estimators, foremen, clerks of works, inspectors, and small contractors.

---

## Deployment

FieldPro is a single-file PWA. Three files must be served together from the same directory:

```
index.html      ← Main application (all UI, logic, and styles)
sw.js           ← Service worker (offline caching)
manifest.json   ← PWA manifest (installability)
```

### Serving locally

Any static file server works. Examples:

```bash
# Python
python3 -m http.server 8080

# Node.js (npx)
npx serve .

# Nginx / Apache
# Point document root to the directory containing the three files
```

### Installing as a PWA

Open the app in a browser on any device. The browser will prompt to **Add to Home Screen** (Android/iOS) or **Install** (Chrome/Edge desktop). Once installed, the app runs as a standalone application with no browser UI.

> All three files (`index.html`, `sw.js`, `manifest.json`) must be in the same directory for the service worker and manifest to register correctly.

---

## Features

### Offline-First Architecture

- Service worker caches the app shell on first load
- All data is stored locally using **IndexedDB** (via Dexie.js)
- Full functionality with no internet connection
- JSON backup and restore for data portability

### Responsive Layout

- **Mobile:** fixed bottom navigation bar + slide-in sidebar menu
- **Desktop:** persistent collapsible left sidebar with toggle button
- All content is centred within a max-width column (800px)
- Touch-optimised inputs, large tap targets, scroll-tab navigation

---

## Modules

### 1. Dashboard

Quick-access overview panel.

- Live statistics: total inspections, calculations, and projects
- Six shortcut cards to the most commonly used tools
- Recent activity feed (last 5 records across all types)

---

### 2. Surface Areas

Ten calculators for surface area and material estimation. All support metric and imperial units, waste percentage, and quantity multipliers. Results can be saved to history.

| Calculator | Formula |
|---|---|
| Rectangle | L × W |
| Square | S² |
| Triangle | ½ × B × H |
| Circle | π × r² |
| Trapezoid | ½ × (a + b) × h |
| Wall (with openings) | L × H − doors − windows |
| Roof | L × W × pitch factor |
| Paint | Area ÷ coverage rate × coats |
| Tiling | Area ÷ tile area → tile count |
| Plaster | Area × thickness → volume + material estimate |

**Outputs per calculator:**
- Gross area
- Net area (after deductions)
- Final area including waste factor
- Optional material quantities (bags, litres, tiles)
- Formula reference

---

### 3. Construction Utilities

Nine field calculation tools.

| Tool | Description |
|---|---|
| Concrete Volume | Slab, footing (pad/strip), column (rect/circ), trench, beam — with 1:2:4 mix material estimate |
| Material Estimator | Cement bags, sand, aggregate for any mix ratio; block/brick count per wall area |
| Unit Converter | Bidirectional: mm/m/cm/ft/in, m²/ft²/ha, m³/ft³/L, kg/t/lb |
| Slope / Gradient | Rise ÷ run → angle (°), grade (%), ratio, hypotenuse |
| Perimeter | Rectangle, square, circle, triangle |
| Stair Rise/Run | Floor-to-floor height → step count, actual riser, recommended going (2R+G rule) |
| Mix Ratio Helper | Concrete 1:2:4, 1:1.5:3, 1:3:6; mortar/plaster 1:3, 1:4 → quantities per m³ |
| Rebar Spacing | Element length + cover + spacing → bar count and actual spacing |
| Percentage | Value + percentage → amount, value+%, value−% |

---

### 4. Site Inspection

Full site inspection capture and checklist system.

**Project header fields:**
- Project name, Site name, Client, Contractor, Inspector
- Date & time, Weather conditions, Inspection type
- Work area / zone, Reference / permit number
- General notes / remarks

**Checklist sections (collapsible):**

| Section | Items |
|---|---|
| A. General Site | Housekeeping, access routes, signage, drawings, toolbox talk, briefing |
| B. Safety | PPE, barricading, fall protection, excavation, scaffolding, electrical, fire, first aid, emergency contacts |
| C. Quality | Drawings compliance, dimensions, levels, materials, workmanship, curing, reinforcement, finishes, snag items |
| D. Progress / Activity | Work completed, labour, equipment, materials delivery, delays, weather impact, outstanding issues |

**Per checklist item:**
- Pass / Fail / N/A toggle buttons
- Optional comment field (auto-shown on Fail)
- Live pass/fail/N/A counter

**Inspection summary:**
- Totals by status
- Overall compliance status badge
- Inspector and contractor acknowledgement fields

**Actions:**
- Save to IndexedDB history
- Export to CSV
- Print / PDF via browser print dialog

---

### 5. Bill of Quantities (BOQ)

A full 13-section construction BOQ based on the standard Namibian construction format.

**Project header:**
- Project / Erf, Owner, Contractor, Prepared By, Date
- Currency selector: N$, R, $, €, £

**Sections:**

| No. | Section |
|---|---|
| 1 | Preliminary & General |
| 2 | Plant & Equipment |
| 3 | Earthworks & Excavations |
| 4 | Concrete, Formwork & Reinforcement |
| 5 | Masonry |
| 6 | Roof Structure |
| 7 | Paintwork |
| 8 | Finishes |
| 9 | Plumbing |
| 10 | Carpentry & Joinery |
| 11 | Metal Works |
| 12 | Electrical |
| 13 | External Works |

**Per section:**
- Collapsible with running section total in the header
- Item No., Description, Unit, Qty, Rate, Amount columns
- Qty × Rate = Amount calculated live
- **+ Add Item** button — inserts a fully editable blank row (No., Description, Unit, Qty, Rate)
- **× Remove** button on every row — removes instantly, updates totals
- Custom items are preserved through Save/Load

**Summary card:**
- All 13 section totals in one view
- Grand total bar

**Export:**
- **Excel (.xlsx)** — full BOQ sheet + Summary sheet (SheetJS, loaded on demand)
- **PDF** — browser print dialog with print-optimised stylesheet (delete/add controls hidden, all sections expanded)

**Save / Load:**
- Full BOQ state (quantities, rates, removed items, custom items) saved to IndexedDB
- Loadable from History tab

---

### 6. History

Searchable log of all saved records.

- Filter by All / Inspections / Calculations
- Full-text search across project name, site, inspector, date
- Delete records with confirmation dialog
- Export all records to CSV
- JSON backup (full data export)
- JSON restore (import backup)

---

### 7. Settings

| Setting | Description |
|---|---|
| Company Name | Used on inspection reports |
| Default Inspector | Pre-filled on new inspections and BOQ |
| Dark / Light Mode | Theme toggle |
| Default Unit System | Metric (m, m², m³) or Imperial (ft, ft², ft³) |
| Decimal Places | 2, 3, or 4 decimal places for calculation results |
| Default Waste % | Paint, tiling, plaster — pre-filled on calculators |
| Data Management | Full JSON backup, restore, and clear all data |

---

## Data Storage

All data is stored locally in the browser using **IndexedDB** (Dexie.js).

| Store | Contents |
|---|---|
| `calculations` | Saved calculator results, BOQ records |
| `inspections` | Saved site inspection records |
| `settings` | User preferences (key-value) |

No data is sent to any server. Data persists across sessions on the same device and browser.

### Backup & Restore

Use **Settings → Backup All Data** to export a `.json` file containing all calculations, inspections, and settings. Use **Restore Backup** to import it on any device running FieldPro.

---

## Technology Stack

| Layer | Technology |
|---|---|
| UI | Vanilla HTML5 + CSS3 (no framework) |
| Logic | Vanilla JavaScript (ES6+) |
| Storage | IndexedDB via [Dexie.js 3.2](https://dexie.org/) |
| Offline | Cache API + Service Worker |
| Excel export | [SheetJS (xlsx) 0.18](https://sheetjs.com/) — loaded on demand |
| Fonts | Barlow Condensed, DM Sans, DM Mono (Google Fonts) |
| PWA | Web App Manifest + Service Worker |

**No build step. No bundler. No framework. No backend.**

---

## Browser Support

| Browser | Support |
|---|---|
| Chrome / Edge (desktop) | Full — including PWA install |
| Chrome (Android) | Full — including Add to Home Screen |
| Safari (iOS) | Full — including Add to Home Screen |
| Firefox | Full — PWA install via third-party |

---

## File Structure

```
fieldpro/
├── index.html        ← Complete single-file application
├── sw.js             ← Service worker (offline caching)
├── manifest.json     ← PWA manifest
└── README.md         ← This file
```

---

## Version

**FieldPro Construction v1.0**

Built for field engineering and site operations. Offline-first. No dependencies to install.
