# SSLM - Functionality & Technical Overview

This document provides a detailed breakdown of the internal logic, capabilities, and data handling processes of the SeeStar Library Manager (SSLM). It is intended for users who want a deeper understanding of how the application manages their data.

---

## 1. Core Architecture

SSLM is a **local-first** application designed with a strict "read-only source" philosophy to ensure data safety.

- **Backend**: Node.js with Express.
- **Frontend**: Vanilla JavaScript (ES6+) with real-time updates via Socket.IO.
- **File Operations**: Utilizes `fs-extra` for robust filesystem interactions.
- **Concurrency**: Operations are designed to be non-blocking where possible, with heavy I/O tasks (like imports) running asynchronously and reporting progress via WebSockets.

### Runtime Modes

SSLM detects whether it is running as source code or as a packaged executable via the `isPackaged` flag (`typeof process.pkg !== 'undefined'`).

| Behaviour | Development (`npm start`) | Packaged (`sslm.exe`) |
|-----------|--------------------------|----------------------|
| Config file location | `config/settings.json` (project folder) | `%APPDATA%\SSLM\settings.json` |
| Browser auto-open | No | Yes — opens default browser 1.5 s after server starts |
| `isPackaged` flag | `false` | `true` |

**Why APPDATA for config in packaged mode**: The installation folder (`%LOCALAPPDATA%\SSLM\`) is effectively read-only for standard user accounts. All writable user data (favourites, last paths, preferences) is stored in `%APPDATA%\SSLM\settings.json`, which is always writable. On first packaged run, the default config is written there automatically.

---

## 2. Import Engine

The import engine is responsible for transferring data from the SeeStar S50 (or other sources) to the local library.

### Device Detection
- **USB Mode**: Automatically scans all mounted Windows drives (C: through Z:) looking for the specific `MyWorks` directory signature.
- **Network Mode**: Supports direct addressing of the Seestar via SMB (`\\seestar\MyWorks`).

### Transfer Logic
SSLM employs two distinct import strategies:

1.  **Full Copy**: A blind copy of all files. Used for initial backups.
2.  **Incremental Copy (Smart Sync)**:
    - Iterates through the source directory tree.
    - For each file, checks if a corresponding file exists in the destination.
    - **Comparison Criteria**: A file is skipped *only* if independent verification confirms:
        - Filename matches.
        - File size (in bytes) is identical.
        - Modification timestamp is identical (or newer in destination).
    - This logic drastically reduces import times for regular backups.

### Expurged Mode

Both the Import and Merge engines support an **Expurged** sub-frame mode, controlled by the `subframeMode` parameter:

| Value | Behaviour |
|-------|-----------|
| `'all'` (default) | All files copied, including JPG and thumbnail previews in `_sub` directories |
| `'fit_only'` | Only `.fit` light frames are copied from `_sub` directories; non-FITS files are skipped |

**Detection logic**: A file is classified as a "subframe non-fit" candidate when:
1. Its relative path contains a parent directory whose name ends with `_sub`, **AND**
2. Its file extension is not `.fit`

This logic is implemented as `isSubframeNonFit(relativePath)` in `importService.js`, `mergeService.js`, and `diskSpaceValidator.js`. All three are kept in sync so that space calculations, file scanning, and transfer validation all honour the same exclusion rule.

**Expurged-aware validation**: After an Expurged import, the transfer validation filters out `isSubframeNonFit` files before comparing source vs. destination, preventing false "missing file" errors for intentionally skipped files.

**Space savings preview** (Import Wizard Step 3): When Expurged mode is selected, the disk space screen makes a second `/api/import/validate-space` call with `subframeMode: 'all'` and displays the difference (e.g., "Expurged mode saves 4.2 GB").

### Data Validation
- **Post-Transfer Verification**: After an import operation, an optional validation step re-scans identifying any discrepancies between source and destination file sizes to ensure data integrity.

---

## 3. Merge & Deduplication Engine

The Merge engine allows users to consolidate multiple partial backups (e.g., "Laptop Library" and "External Drive Backup") into a single, complete "Master Library".

### Deduplication Logic
The merge process builds a virtual map of all files across all source libraries before moving a single byte.

- **Relative Path Identity**: Files are identified by their path relative to the library root (e.g., `\M 42\Stacked_...fit`).
- **Conflict Resolution**: When the same file (same relative path) exists in multiple sources:
    - The engine compares the **Last Modified Date** of all versions.
    - **Winner**: The newest version is selected for the merged library.
    - **Loser**: Older versions are discarded (not copied).
    - *Note: Source files are never deleted; they are simply not copied to the new destination.*

### Space Calculation
- **Predictive Sizing**: The engine calculates the required disk space *after* deduplication logic is applied, preventing "disk full" errors midway through a merge.

---

## 4. Astrometric Data Parsing

SSLM parses the standardized Seestar file naming convention to extract metadata without needing to read the FITS headers (which would be slow for large libraries).

### Filename Parsing
filenames are tokenized to extract:
- **Object Name**: `NGC 6729`, `M 42`, etc.
- **Exposure**: `10s`, `20s`, `30s`.
- **Filter**: `IRCUT`, `LP`, `Dualband`.
- **Timestamp**: `YYYYMMDD-HHMMSS`.
- **Frame Count**: (For stacked images) Used to calculate total integration time.

### Catalog Recognition
The `catalogParser.js` module uses regex patterns to categorize objects into:
- **Solar System**: Sun, Moon, Planets.
- **Deep Sky**: Messier (`M`), NGC, IC, Sharpless (`SH`), Caldwell (`C`).
- **Stars/Other**: Named stars (e.g., `Betelgeuse`) or custom object names.

---

## 5. Storage Optimization (Cleanup)

The Cleanup module is designed to reclaim disk space without touching scientific data.

### Targeted File Types
Cleanup operations differ from standard "delete" commands. They strictly target **non-essential derivative files** within `_sub` directories:
- **Targets**: `*.jpg` (previews), `*_thn.jpg` (thumbnails).
- **Protected**: `*.fit` (FITS data), `*.mp4` (videos), and any files in the main object directory (stacked results).

### Session Deletion

A targeted deletion operation removes all files belonging to a single imaging session:
- **Main folder targets**: All stacked image files (`.fit`, `.jpg`, `_thn.jpg`) whose filename timestamp matches any snapshot within the session.
- **Sub-folder targets**: All light frame files (`Light_...`) whose filename contains the session date (`YYYYMMDD`), the session exposure (e.g., `30.0s`), and the session filter.
- **API**: `POST /api/cleanup/session` — body: `{ mainFolderPath, subFolderPath, mainFiles[], subFiles[] }`
- **Safety**: Requires explicit file list passed from the client (no wildcard deletion). Each file's size is read before deletion and reported in the response as `spaceFreed`.

### Safety Mechanisms
- **Context Awareness**: Cleanup is only available for `_sub` directories. The main object directories (containing your final stacks) are never subjected to bulk cleanup.
- **Empty Directory Pruning**: Recursive removal of directories that contain 0 files (often left over from renaming objects on the mobile app).
- **Session Deletion Confirmation**: Session deletion always requires user confirmation in a dialog showing the file count before any files are removed.

---

## 6. Library Analytics

The `fileAnalyzer.js` service builds a real-time valid JSON representation of the library state.

### Integration Time Calculation
Total integration time is not just a sum of file counts. It is calculated by:
- **Stacked Images**: `Frame Count * Exposure Time`.
- **Sub-Frames**: `Count * Exposure Time`.
- **Aggregation**: These values are aggregated per object, per session, per catalog, and for the global library.

### Session Grouping

Images are grouped into "Sessions" based on date and identical capture parameters (Filter + Exposure). The grouping key is `YYYYMMDD_exposure_filter` — time-of-day is intentionally excluded.

**Why time is excluded**: The SeeStar saves progressive stacking snapshots throughout a live session (e.g., a snapshot at 09:09 PM with 74 frames, and another at 11:48 PM with 453 frames on the same night). Including the time in the key would create multiple sessions for the same night. By keying on date only, all snapshots from the same night are merged into one session entry.

**Conflict resolution within a group**: When multiple files map to the same session key, the entry retains the **highest frame count** (the final result of the night's stacking). All file timestamps from every snapshot are accumulated in a `rawTimestamps` set, ensuring that session-scoped operations (detail view, delete) can locate every associated file.

**Stacking Counts display**: The metadata panel shows `[total] total ([s1], [s2] per session)` — the total is the sum of each session's final frame count; the per-session values show the depth of each individual session's stack.

### Session Detail View

A dedicated screen (`sessionDetailScreen`) allows the user to inspect all files belonging to one session:
- **Main folder files**: filtered by matching any timestamp in `session.rawTimestamps`
- **Sub-frame light frames**: filtered by `session.rawDateStr` (YYYYMMDD) + `session.exposure.toFixed(1)s` + `session.filter`
- File sections are expanded by default for immediate visibility
- Provides a **Delete Session** action that calls `POST /api/cleanup/session`

---

## 7. Packaging & Distribution

### Executable Build (`@yao-pkg/pkg`)

SSLM is compiled into a single self-contained Windows executable using `@yao-pkg/pkg`:

```
npm run build
# → dist/sslm.exe  (Node.js 20 runtime + application code + assets, ~46 MB)
```

The build command includes `--icon public/assets/sslm.ico` to embed the application icon directly into the exe, so it appears correctly in Explorer, the taskbar, and Start Menu shortcuts.

**Bundled assets** (declared in `package.json → pkg.assets`):

| Glob | Contents |
|------|---------|
| `public/**/*` | All frontend files: HTML, CSS, JS, images, icons |
| `config/**/*` | Default `settings.json` template |
| `installer/sslm.iss` | Inno Setup script — read at runtime for version number |

### Windows Installer (Inno Setup 6)

The installer wraps `sslm.exe` into a standard Windows setup wizard (`installer/sslm.iss`):

- **Install location**: `%LOCALAPPDATA%\SSLM\` (no admin/UAC required)
- **Also installs**: `sslm.ico` alongside the exe (used for Add/Remove Programs icon)
- **Shortcuts**: Start Menu + optional Desktop shortcut
- **Uninstaller**: Registered in Windows "Add or Remove Programs"
- **Post-install**: Optional "Launch SSLM" checkbox on final wizard page

**Installer branding** (`sslm.iss`):

| Setting | File | Appearance |
|---------|------|-----------|
| `WizardImageFile` | `public/assets/sslm.png` | Left banner — Welcome & Finish pages |
| `WizardSmallImageFile` | `public/assets/sslmLogo.png` | Top-right corner — all inner pages |
| `SetupIconFile` | `public/assets/sslm.ico` | Installer exe icon |
| `UninstallDisplayIcon` | `{app}\sslm.ico` | Add/Remove Programs icon |

### Version Management

The canonical application version is defined in `installer/sslm.iss`:
```
#define AppVersion "1.0.0-beta.2"
```

`server.js` reads this at startup via `readAppVersion()`:
1. Attempts to read and parse `installer/sslm.iss` (works in both dev and packaged mode since the `.iss` file is a bundled asset)
2. Extracts the version using regex: `/#define AppVersion\s+"([^"]+)"/`
3. Falls back to `package.json` version if the `.iss` file is not readable

The version is exposed to the frontend via `GET /api/config → { version: "..." }` and displayed in the About dialog.

**To update the version for a new release:**
1. Change `#define AppVersion` in `installer/sslm.iss` (source of truth)
2. Keep `"version"` in `package.json` in sync
3. Run `npm run build` then recompile `sslm.iss`

### Distribution

Releases are published to GitHub Releases at:
`https://github.com/AstroNoob-Tools/SSLM/releases`

The distributable file (`installer/output/SSLM-Setup-vX.X.X.exe`) is attached as a release asset. Source code and build outputs (`dist/`, `installer/output/`) are gitignored. See `notes/HOW_TO_RELEASE.md` for the full release checklist.

---

## 8. Application Shell Features

### About Dialog

The About dialog is opened via the ℹ️ button in the application header. It is implemented as `app.showAbout()` in `public/js/app.js` and rendered using the existing `app.showModal()` system.

**Content:**
- Application logo (`public/assets/astroNoobLogo.png`) — author's personal logo
- Application name and subtitle
- Version number (sourced from `app.config.version`, which comes from `/api/config`)
- Contact email as a `mailto:` link (`astronoob001@gmail.com`)
- Purpose description

### Quit Button

The ⏻ Quit button provides a graceful in-browser shutdown mechanism:

**Frontend flow** (`app.quit()` in `app.js`):
1. Shows a confirmation modal ("Are you sure you want to quit?")
2. On confirm: calls `POST /api/quit` via `fetch()`
3. Replaces the entire page body with a "SSLM has shut down. You can close this tab." message
4. The `fetch` is wrapped in `try/catch` — a network error is expected because the server closes before responding

**Backend endpoint** (`POST /api/quit` in `server.js`):
1. Sends `{ message: "Shutting down..." }` JSON response
2. After 200 ms calls the existing `gracefulShutdown()` function, which:
   - Cancels any ongoing import/merge operations
   - Closes all Socket.IO connections
   - Closes the HTTP server
   - Calls `process.exit(0)`
   - Forces exit after 5 seconds if graceful shutdown stalls

**Visual distinction**: The quit button uses the `.icon-button--danger` CSS modifier, giving it a muted red colour (`#e05a5a`) at rest that brightens on hover (`#ff4444`) to signal it is a destructive action.

### Application Branding

| Asset file | Used where |
|-----------|-----------|
| `public/assets/sslmLogo.png` | Header bar logo + browser tab favicon |
| `public/assets/sslm.png` | Welcome screen hero image (160×160 px with `drop-shadow`) |
| `public/assets/astroNoobLogo.png` | About dialog (80×80 px) |
| `public/assets/sslm.ico` | Windows exe icon (embedded) + installer icon |
