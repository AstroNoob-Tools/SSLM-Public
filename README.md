<div align="center">
  <img src="https://raw.githubusercontent.com/AstroNoob-Tools/SSLM-Public/main/assets/sslm/sslm_test.png" width="128" alt="SSLM Logo" />
  <h1>SSLM — SeeStar Library Manager</h1>
</div>

![SSLM Dashboard](https://raw.githubusercontent.com/AstroNoob-Tools/SSLM-Public/main/assets/sslm/Capture01.JPG)
A local desktop web application for managing astrophotography files captured with a **SeeStar S50** telescope. SSLM runs entirely on your Windows PC — no internet connection required.

---

## Installation (End Users)

Download the latest installer from the **[Releases](https://github.com/AstroNoob-Tools/SSLM/releases)** page.

> **Security Guarantee**: SSLM is unsigned freeware. When you run the installer, Windows Defender SmartScreen may show a blue warning saying "Windows protected your PC" because the publisher is unknown. You can safely install it by clicking **More info** -> **Run anyway**.
> 🛡️ **[View the VirusTotal Security Scan (0/74 engines detected)](https://www.virustotal.com/gui/file/2817cf5a638a5003035d64a5c71330d8f3e369613926449c41fd0b0aa26e9f3b/detection)** proving the `.exe` is 100% clean. SSLM is strictly offline-only, open-source, and never modifies files on your SeeStar device.

**Current release**: `v1.0.0-beta.2` — first public beta

**No prerequisites** — Node.js is bundled inside the installer.

1. Run `SSLM-Setup-v1.0.0-beta.2.exe`
2. Follow the wizard to choose your preferred installation folder (no admin rights needed)
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
- **Look up** cross-catalog identifiers and J2000 coordinates via the SIMBAD database (optional, Online Mode)
- **Re-classify** objects by renaming all files and folders to a different catalog designation in one operation (Online Mode)

## What It Does NOT Do

- SSLM never writes to, modifies, or deletes files on your SeeStar device
- SSLM does not stack or process images
- SSLM does not control the telescope

---

## Features

### Dashboard
![Dashboard View](https://raw.githubusercontent.com/AstroNoob-Tools/SSLM-Public/main/assets/sslm/Capture01.JPG)
- Summary cards: total objects, sub-frame presence, total size, file counts
- Catalog breakdown (Messier, NGC, IC, Sharpless, Named)
- Objects table with search, integration time, and per-object cleanup
- Empty directory detection and one-click cleanup

### Object Detail View
![Detail View](https://raw.githubusercontent.com/AstroNoob-Tools/SSLM-Public/main/assets/sslm/Capture02.JPG)
- Stacking counts: total frames + per-session breakdown
- Exposure and filter metadata
- Imaging sessions table with clickable dates and per-session delete
- Expandable file lists (main folder and sub-frames folder)
- Sub-frame cleanup button

### Import Wizard (5 steps)
![Import Wizard](https://raw.githubusercontent.com/AstroNoob-Tools/SSLM-Public/main/assets/sslm/IncrementalImport.JPG)
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

### Online Mode — SIMBAD Catalog Lookup & Re-Classification

SSLM is fully offline by default. Clicking the **Offline / Online** badge in the header enables Online Mode.

When Online Mode is active, opening any object's detail page silently queries the **SIMBAD Astronomical Database** (CDS Strasbourg) and injects:

- **Also known as** — cross-catalog aliases: Messier, NGC, IC, Caldwell, Sharpless, Abell, Barnard, HD, HIP, and common names
- **J2000 coordinates** — RA and Dec in sexagesimal format (e.g. `05h 35m 17s / −05° 23′ 28″`)
- **Re Classify button** — rename every file and folder for the object to a different catalog designation

Re-Classification renames everything in one operation — main folder, `_sub` folder, and all files within — after two confirmation dialogs. A pre-flight check ensures the target name does not already exist before anything is touched.

Results are cached in memory so repeated visits cost zero additional network calls. If the object is not found in SIMBAD or you are offline, the page loads exactly as normal — no errors.

**Security**: Online Mode is strictly outbound-only. SSLM sends a single lookup request to SIMBAD's TAP endpoint — nothing else. No port is opened on your machine, no external party can reach SSLM, and no data about your library or files is ever transmitted.

### Application Header
- **Offline / Online badge** — click to toggle Online Mode; all core features remain available in either state
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

<img src="https://raw.githubusercontent.com/AstroNoob-Tools/SSLM-Public/main/assets/Personal/astro_noob_final_edit_v5_1771351657214.png" width="60" alt="Astro Noob" style="border-radius: 50%" />

**Astro Noob**  
Contact: astronoob001@gmail.com

---

*SSLM — SeeStar Library Manager v1.0.0-beta.2 | Last updated: February 2026*
