# SSLM — SeeStar Library Manager

A local desktop web application for managing astrophotography files captured with a **SeeStar S50** telescope. SSLM runs entirely on your Windows PC — no internet connection required.

---

## Installation (End Users)

Download the latest installer from the **[Releases](https://github.com/AstroNoob-Tools/SSLM/releases)** page.

**Current release**: `v1.0.0-beta.2` — first public beta

**No prerequisites** — Node.js is bundled inside the installer.

1. Run `SSLM-Setup-v1.0.0-beta.2.exe`
2. Follow the wizard (installs to `%LOCALAPPDATA%\SSLM\` — no admin rights needed)
3. Launch from the **Start Menu** or **Desktop shortcut**
4. Your browser opens automatically at `http://localhost:3000`

> User settings and favourites are stored in `%APPDATA%\SSLM\settings.json` and survive uninstall/reinstall.

---

## What It Does

- **Import** files from a connected SeeStar device (USB or Wi-Fi) to local storage
- **Analyse** your collection: objects, catalogs, integration times, sessions, file sizes
- **Browse** sessions and files with a rich detail view per celestial object
- **Delete** specific imaging sessions to reclaim space
- **Merge** multiple library copies into a single consolidated library
- **Clean up** unnecessary preview files from sub-frame directories to save disk space

## What It Does NOT Do

- SSLM never writes to, modifies, or deletes files on your SeeStar device
- SSLM does not stack or process images
- SSLM does not control the telescope

---

## Features

### Dashboard
- Summary cards: total objects, sub-frame presence, total size, file counts
- Catalog breakdown (Messier, NGC, IC, Sharpless, Named)
- Objects table with search, integration time, and per-object cleanup
- Empty directory detection and one-click cleanup

### Object Detail View
- Stacking counts: total frames + per-session breakdown
- Exposure and filter metadata
- Imaging sessions table with clickable dates and per-session delete
- Expandable file lists (main folder and sub-frames folder)
- Sub-frame cleanup button

### Session Detail View
- All stacked images and sub-frame light files for one specific session
- Session summary cards (date, frames, exposure, filter, integration)
- Delete Session button

### Import Wizard (5 steps)
- Auto-detection of SeeStar on USB drives and network path (`\\seestar`)
- Full copy or incremental (smart sync) strategies
- Expurged mode: skip non-FITS files from `_sub` directories to save space
- Real-time progress: speed, ETA, files/bytes transferred
- Post-import transfer validation

### Merge Wizard (6 steps)
- Combine 2 or more library copies
- Intelligent deduplication by relative file path
- Conflict resolution: keep newer version by modification date
- Expurged mode support
- Real-time analysis progress and per-source breakdown
- Post-merge validation

### Cleanup Operations
- Delete empty directories
- Remove JPG/thumbnail previews from `_sub` directories (`.fit` files always kept)
- Delete individual imaging sessions (stacked images + light frames)

### Application Header
- **⚙️ Settings** — configure port, SeeStar directory name, import strategy
- **ℹ️ About** — version number and contact details
- **⏻ Quit** — gracefully shuts down the server from the browser

---

## Safety

- SSLM never modifies the SeeStar device — all operations are read-only on source
- Cleanup operations require explicit user confirmation before any file is deleted
- Session deletion shows a file count in a confirmation dialog before proceeding
- Sub-frame cleanup only removes JPG/thumbnail files — `.fit` data is always preserved

---

## Development Setup

### Requirements

- Windows 10 or later
- [Node.js](https://nodejs.org/) v18 or later

### Install & Run

```bash
git clone https://github.com/AstroNoob-Tools/SSLM.git
cd SSLM
npm install
npm start
```

Then open: **http://localhost:3000**

Auto-reload during development:
```bash
npm run dev
```

### Build the Windows Installer

Requires [Inno Setup 6.x](https://jrsoftware.org/isinfo.php).

```bash
# Step 1 — build the self-contained exe (icon embedded)
npm run build

# Step 2 — compile the installer
"C:\Program Files (x86)\Inno Setup 6\ISCC.exe" installer\sslm.iss
```

Output: `installer/output/SSLM-Setup-v1.0.0-beta.2.exe`

See [documentation/InstallationManual.md](documentation/InstallationManual.md) for the full release checklist.

---

## Project Structure

```
SSLM/
├── public/
│   ├── assets/            # Logos and icons (sslm.png, sslmLogo.png, sslm.ico, astroNoobLogo.png)
│   ├── css/styles.css
│   └── js/
│       ├── app.js         # Core app (navigation, modals, About, Quit)
│       ├── dashboard.js   # Dashboard, object/session detail views
│       ├── modeSelection.js
│       ├── importWizard.js
│       └── mergeWizard.js
├── src/
│   └── services/
│       ├── catalogParser.js
│       ├── fileAnalyzer.js
│       ├── fileCleanup.js
│       ├── importService.js
│       └── mergeService.js
├── installer/
│   └── sslm.iss           # Inno Setup script (source of truth for version)
├── documentation/         # User and installation manuals
├── config/                # settings.json (gitignored)
├── server.js
└── package.json
```

---

## Documentation

| Document | Description |
|----------|-------------|
| [documentation/UserManual.md](documentation/UserManual.md) | Full user guide |
| [documentation/InstallationManual.md](documentation/InstallationManual.md) | Installation & release instructions |
| [documentation/SSLM_Functionalities.md](documentation/SSLM_Functionalities.md) | Technical internals reference |

---

## Contact

astronoob001@gmail.com

---

*SSLM — SeeStar Library Manager v1.0.0-beta.2 | Last updated: February 2026*
