# SSLM - SeeStar Library Manager
## Installation Manual

**Version**: 1.0.0-beta.3
**Date**: February 2026
**Platform**: Windows 10 / 11

---

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Running the Application](#running-the-application)
6. [Firewall & Network](#firewall--network)
7. [Updating the Application](#updating-the-application)
8. [Uninstallation](#uninstallation)
9. [Troubleshooting](#troubleshooting)
10. [npm Dependencies Reference](#npm-dependencies-reference)
11. [Publishing a Release](#publishing-a-release)

---

## System Requirements

### Minimum Requirements

| Component | Minimum |
|-----------|---------|
| Operating System | Windows 10 (64-bit) |
| CPU | Dual-core 2.0 GHz |
| RAM | 4 GB |
| Disk Space (Application) | 100 MB |
| Disk Space (Library) | 50 GB recommended (for SeeStar content) |
| Network | Not required (offline mode) |
| Browser | Chrome 90+, Firefox 88+, Edge 90+ |

### Recommended Requirements

| Component | Recommended |
|-----------|-------------|
| Operating System | Windows 10/11 (64-bit) |
| CPU | Quad-core 3.0 GHz |
| RAM | 8 GB |
| Disk Space (Library) | 100 GB+ (SSD preferred for transfer speed) |
| Browser | Latest Chrome or Edge |

### Storage Planning

SeeStar telescope storage capacity:
- **Typical session**: 2–5 GB
- **Full device capacity**: Up to 50 GB
- **Multiple sessions**: Plan for 2–4× device capacity for libraries and merges

> **Tip**: Use an SSD for your local library storage. Transfer speeds are 3–5× faster compared to traditional hard drives, and import/merge operations complete significantly quicker.

---

## Prerequisites

> **Using the Windows Installer?** Skip this entire section. Node.js is bundled inside `sslm.exe` — no prerequisites needed.
>
> Prerequisites below apply only if you are running from source code.

### 1. Node.js

SSLM requires **Node.js version 18 or later**.

**Check if Node.js is already installed:**
```
node --version
```
If this returns `v18.x.x` or higher, you can skip ahead.

**Install Node.js:**

1. Go to [https://nodejs.org](https://nodejs.org)
2. Download the **LTS (Long Term Support)** version — this is the stable, recommended version
3. Run the downloaded installer (`.msi` file)
4. Follow the installation wizard (default options are fine)
5. Restart any open Command Prompt or PowerShell windows
6. Verify the installation:
   ```
   node --version
   npm --version
   ```
   Both commands should return version numbers.

### 2. Git (Optional)

Git is only needed if you want to clone the repository directly or receive updates via Git. If you received the project as a ZIP file, you can skip this step.

**Install Git:**
1. Go to [https://git-scm.com](https://git-scm.com)
2. Download the Windows installer
3. Run the installer (default options are fine)
4. Verify:
   ```
   git --version
   ```

---

## Installation

### Method A: Windows Installer ⭐ (Recommended)

This is the easiest installation method. No Node.js or developer tools required.

1. Download `SSLM-Setup-v1.0.0-beta.3.exe` (current release) from the [GitHub Releases page](https://github.com/AstroNoob-Tools/SSLM/releases)

2. Run the installer — no administrator rights required

3. Follow the wizard:
   - Choose install location (default: `%LOCALAPPDATA%\SSLM\`)
   - Optionally create a Desktop shortcut

4. After installation, launch SSLM from:
   - The Start Menu shortcut, or
   - The Desktop shortcut (if created), or
   - `%LOCALAPPDATA%\SSLM\sslm.exe` directly

5. Your default browser opens automatically at `http://localhost:3000`

**User settings** (favourites, last paths, preferences) are stored in `%APPDATA%\SSLM\settings.json` and are preserved across updates and reinstalls.

**To uninstall**: Use Windows "Add or Remove Programs" and search for SSLM.

---

### Method B: From ZIP File (Source)

1. **Unzip** the project archive to your preferred location.

   Recommended locations:
   ```
   C:\Apps\SeeStarFileManager\
   D:\Programs\SeeStarFileManager\
   ```

   > **Avoid** placing it in `C:\Program Files\` as Node.js applications may have permission issues in that directory.

2. **Open a Command Prompt** in the project folder.

   - Hold `Shift` and right-click on the folder
   - Select **"Open PowerShell window here"** or **"Open in Terminal"**

3. **Install dependencies:**
   ```
   npm install
   ```
   This downloads all required packages into the `node_modules` folder. It may take 1–3 minutes depending on your internet connection.

4. Proceed to the [Configuration](#configuration) section.

### Method C: From Git Repository

1. **Open a Command Prompt** or PowerShell in the folder where you want to install SSLM.

2. **Clone the repository:**
   ```
   git clone <repository-url> SeeStarFileManager
   cd SeeStarFileManager
   ```

3. **Install dependencies:**
   ```
   npm install
   ```

4. Proceed to the [Configuration](#configuration) section.

### Verify Installation

After `npm install` completes, confirm the folder structure looks like this:

```
SeeStarFileManager/
├── config/
│   └── settings.json
├── src/
│   ├── services/
│   └── utils/
├── public/
│   ├── index.html
│   ├── css/
│   └── js/
├── node_modules/       ← created by npm install
├── documentation/
├── server.js
├── package.json
└── README.md
```

---

## Configuration

The application is configured via `config/settings.json`. This file is created automatically with defaults if it does not exist.

### Configuration File Location
```
SeeStarFileManager/config/settings.json
```

### Default Configuration

```json
{
  "server": {
    "port": 3000,
    "host": "localhost"
  },
  "mode": {
    "online": false
  },
  "seestar": {
    "directoryName": "MyWorks"
  },
  "paths": {
    "lastSourcePath": "",
    "lastDestinationPath": ""
  },
  "preferences": {
    "defaultImportStrategy": "incremental"
  },
  "favorites": []
}
```

### Configuration Options

| Setting | Default | Description |
|---------|---------|-------------|
| `server.port` | `3000` | Port the application listens on. Change if port 3000 is in use. |
| `server.host` | `"localhost"` | Hostname. Keep as `localhost` for local-only access. |
| `mode.online` | `false` | Enable online features (currently unused, reserved for future features). |
| `seestar.directoryName` | `"MyWorks"` | The root folder name on the SeeStar device. Change only if your device uses a different name. |
| `paths.lastSourcePath` | `""` | Remembered source path from last import. Updated automatically. |
| `paths.lastDestinationPath` | `""` | Remembered destination from last import. Updated automatically. |
| `preferences.defaultImportStrategy` | `"incremental"` | Default import strategy. Options: `"full"` or `"incremental"`. |
| `favorites` | `[]` | List of saved favorite folders. Managed via the application interface. |

### Changing the Port

If port 3000 is already in use on your machine, change it:

1. Open `config/settings.json` in a text editor
2. Change `"port": 3000` to an available port (e.g., `3001`, `8080`)
3. Save the file
4. Restart the application
5. Access via the new port: e.g., `http://localhost:3001`

---

## Running the Application

### Standard Start

Open a Command Prompt in the project folder and run:

```
npm start
```

You should see:
```
SSLM - SeeStar Library Manager
Server running at http://localhost:3000
Ready for connections
```

### Open in Browser

Open your web browser and navigate to:
```
http://localhost:3000
```

The SSLM welcome screen will appear.

### Development Mode (Auto-Reload)

For development use, `nodemon` automatically restarts the server when source files change:

```
npm run dev
```

> **Note**: This requires `nodemon` to be installed. It is included as a dev dependency and installed automatically by `npm install`.

### Keeping the Application Running

The application must remain running (the terminal window must stay open) while you use it. If you close the terminal, the server stops and the browser interface will stop working.

**Options for keeping it running:**

1. **Minimize the terminal window** — simplest approach
2. **Use Windows Terminal** — tabbed terminal, easy to keep in background
3. **Create a batch file** for easy launching:

   Create `Start-SSLM.bat` in the project folder:
   ```batch
   @echo off
   echo Starting SSLM - SeeStar Library Manager...
   cd /d "%~dp0"
   npm start
   ```
   Double-click this file to start SSLM without opening a terminal manually.

---

## Firewall & Network

SSLM runs entirely on your local machine (`localhost`) by default. It does **not** require internet access and does **not** accept connections from other devices on your network.

### Windows Firewall

When starting SSLM for the first time, Windows may display a firewall prompt. Since the application only listens on `localhost`, you can:
- Click **"Allow"** (safe, but connections still only come from your machine)
- Click **"Cancel"** (the application still works correctly for local use)

### Accessing from Another Device (Advanced)

By default, SSLM is not accessible from other computers. If you want to access it from another device on your local network:

1. Change `"host"` in `config/settings.json` from `"localhost"` to `"0.0.0.0"`
2. Allow the port through Windows Firewall
3. Access from other devices using your computer's IP address: `http://192.168.x.x:3000`

> **Security note**: Only do this on a trusted private network. SSLM has no authentication.

---

## Updating the Application

### From ZIP File

1. Back up your `config/settings.json` file (contains your favorites and preferences)
2. Extract the new ZIP over the existing folder (or into a new folder)
3. Copy your backed-up `settings.json` back to the `config/` folder
4. Run `npm install` to update dependencies
5. Start the application

### From Git Repository

```
git pull
npm install
```

Your `config/settings.json` is not tracked by Git and will not be overwritten.

---

## Uninstallation

### If installed via Windows Installer (Method A)

1. Open Windows **Settings → Apps** (or **Control Panel → Add or Remove Programs**)
2. Search for **SSLM** and click **Uninstall**
3. Follow the uninstaller wizard

> **Note**: Your user settings (`%APPDATA%\SSLM\settings.json`) are intentionally **not** deleted during uninstall, so your favourites and preferences survive a reinstall.

To fully remove all traces including settings:
1. Uninstall via Add or Remove Programs
2. Delete `%APPDATA%\SSLM\` manually

### If running from source (Methods B or C)

1. Stop the application (close the terminal window or press `Ctrl+C`)
2. Delete the project folder (e.g., `C:\Apps\SeeStarFileManager\`)
3. Optionally remove Node.js if it is no longer needed

Your SeeStar library files are stored separately (wherever you chose to import them) and are **not** affected by uninstalling SSLM.

---

## Troubleshooting

### Application won't start

**Symptom**: `npm start` throws an error immediately.

**Check Node.js version:**
```
node --version
```
Must be v18 or higher. Download the latest LTS from [nodejs.org](https://nodejs.org).

**Check dependencies are installed:**
```
npm install
```
Run this again to ensure `node_modules` exists and is complete.

**Check the config file:**
If `config/settings.json` is malformed (invalid JSON), the server will fail to start. Either fix the JSON or delete the file — it will be recreated with defaults.

---

### Port already in use

**Symptom**:
```
Error: listen EADDRINUSE: address already in use :::3000
```

**Solution**: Change the port in `config/settings.json`:
```json
{
  "server": {
    "port": 3001
  }
}
```
Then access the app at `http://localhost:3001`.

**Find what is using port 3000:**
```
netstat -ano | findstr :3000
```

---

### Browser shows "This site can't be reached"

**Symptom**: Browser shows a connection error after navigating to `http://localhost:3000`.

**Causes and solutions:**
1. **Application not started**: Make sure `npm start` is running in a terminal
2. **Wrong port**: Check `config/settings.json` for the configured port
3. **Browser cache**: Try a hard refresh (`Ctrl+Shift+R`) or open in a private/incognito window

---

### npm install fails

**Symptom**: `npm install` reports errors or fails to complete.

**Solutions:**

1. **Check internet connection** — npm needs to download packages
2. **Clear npm cache:**
   ```
   npm cache clean --force
   npm install
   ```
3. **Delete node_modules and reinstall:**
   ```
   rmdir /s /q node_modules
   npm install
   ```
4. **Check Node.js version** — some packages require Node 18+

---

### SeeStar device not detected

**Symptom**: Import wizard Step 1 shows no devices.

**Checks:**
1. Confirm the SeeStar device is connected and powered on
2. Open File Explorer and check if the device appears as a drive letter (e.g., `E:\`)
3. Check that a `MyWorks` folder exists on the device root
4. If using a different folder name, update `seestar.directoryName` in `config/settings.json`
5. Try disconnecting and reconnecting the device
6. Try a different USB port

For network (station mode) access, manually enter the path `\\seestar\MyWorks` in the import wizard.

---

### Application is slow

**Symptom**: Dashboard takes a long time to load or import is slow.

**Causes:**
- **Large library**: Analyzing 5,000+ files takes longer. This is normal.
- **Slow storage**: HDDs are significantly slower than SSDs for file operations.
- **Antivirus scanning**: Antivirus software may scan each file during import. Add an exclusion for your library folder if this is an issue.
- **Network drive**: Library on a network drive will be slower. Local SSD is recommended.

---

### Transfer validation shows errors

**Symptom**: Post-import validation reports missing or mismatched files.

**Solutions:**
1. Check that the source device is still connected
2. Re-run an incremental import — it will copy any missing files
3. Check the destination drive has sufficient free space
4. Check for any file permission issues on the destination folder

---

## Appendix

### Folder Structure Reference

```
SeeStarFileManager/
├── config/
│   └── settings.json         # User settings (favorites, preferences, paths)
├── src/
│   ├── services/
│   │   ├── fileAnalyzer.js   # Library scanning & statistics
│   │   ├── fileCleanup.js    # Cleanup operations
│   │   ├── importService.js  # Import from SeeStar device
│   │   ├── mergeService.js   # Multi-library merge
│   │   └── catalogParser.js  # Astronomical catalog name parsing
│   └── utils/
│       ├── directoryBrowser.js   # File system navigation
│       └── diskSpaceValidator.js # Disk space calculations
├── public/
│   ├── index.html            # Application UI
│   ├── css/styles.css        # Application styles
│   └── js/
│       ├── app.js            # Core application
│       ├── dashboard.js      # Dashboard display
│       ├── importWizard.js   # Import wizard
│       ├── mergeWizard.js    # Merge wizard
│       └── modeSelection.js  # Mode selection
├── documentation/
│   ├── InstallationManual.md # This document
│   └── UserManual.md         # User guide
├── server.js                 # Application server
└── package.json              # Node.js package configuration
```

### npm Commands Reference

| Command | Description |
|---------|-------------|
| `npm install` | Install/update all dependencies |
| `npm start` | Start the application |
| `npm run dev` | Start in development mode (auto-reload) |
| `npm run build` | Build `dist/sslm.exe` (self-contained, with embedded icon) |

### Default URLs

| URL | Description |
|-----|-------------|
| `http://localhost:3000` | Main application |
| `http://localhost:3000/api/status` | Server status check |
| `http://localhost:3000/api/config` | Current configuration |

---

## npm Dependencies Reference

A complete list of every npm package used by SSLM, including its purpose and why it was chosen.

### Production Dependencies

These packages are required to run the application. They are installed by `npm install` and must be present at runtime.

---

#### `express` — v4.18.2

**Category**: Web framework
**npm**: https://www.npmjs.com/package/express

Express is the HTTP server framework that powers the SSLM backend. It handles all incoming HTTP requests from the browser, routes them to the appropriate handler functions, and sends responses back.

**Used for:**
- Serving the frontend HTML, CSS, and JavaScript files as static assets
- Defining all REST API endpoints (`/api/analyze`, `/api/import/start`, `/api/merge/start`, etc.)
- Parsing JSON request bodies (`express.json()`)
- Serving image files on demand via `/api/image`

**Why Express**: Lightweight, widely used, and well-suited to local utility applications. No database or session management is needed, so a minimal framework is ideal.

---

#### `socket.io` — v4.6.1

**Category**: Real-time communication
**npm**: https://www.npmjs.com/package/socket.io

Socket.IO provides bidirectional, event-driven communication between the Node.js server and the browser. It is used to push real-time progress updates to the frontend during long-running operations (import, merge, validation) without requiring the client to poll the server.

**Used for:**
- Emitting `import:progress` events during file copying (current file, bytes transferred, speed, ETA)
- Emitting `merge:progress` events during merge operations (per-source breakdown, overall progress)
- Emitting `analyze:progress` events during source library scanning (Step 3 of merge wizard)
- Emitting `validate:progress` events during transfer validation
- Emitting completion and error events (`import:complete`, `merge:complete`, `validate:complete`, etc.)

**Why Socket.IO**: The import and merge operations can run for many minutes on large libraries. Real-time progress feedback is essential for a good user experience. Socket.IO works transparently over HTTP and requires no special browser configuration.

---

#### `fs-extra` — v11.2.0

**Category**: File system utilities
**npm**: https://www.npmjs.com/package/fs-extra

`fs-extra` extends Node.js's built-in `fs` module with additional methods and Promise-based versions of all standard file system operations. It simplifies recursive directory operations, JSON file handling, and path utilities.

**Used for:**
- `fs.pathExists()` — checking whether a file or directory exists before operating on it
- `fs.readJSONSync()` / `fs.writeJSON()` — reading and writing `config/settings.json`
- `fs.ensureDir()` — creating destination directories (including all intermediate folders) before copying
- `fs.stat()` — reading file metadata (size, modification time) for incremental copy comparisons
- `fs.readdir()` — listing directory contents during library scanning
- `fs.remove()` — deleting empty directories and sub-frame preview files during cleanup
- Stream-based file copying via `fs.createReadStream()` / `fs.createWriteStream()`

**Why fs-extra**: Avoids repetitive boilerplate for common file tasks (e.g., `mkdirp`, `JSON.parse(fs.readFileSync(...))`). All methods return Promises, making async/await code clean and readable.

---

#### `check-disk-space` — v3.4.0

**Category**: System utility
**npm**: https://www.npmjs.com/package/check-disk-space

`check-disk-space` queries the operating system for available free space on a given drive or path. It provides a cross-platform API that returns total size and free space in bytes.

**Used for:**
- `DiskSpaceValidator.hasEnoughSpace()` — verifying there is sufficient free space on the destination drive before starting an import or merge
- Strategy-aware space calculation: for incremental imports, only the differential size is validated rather than the full source size
- Merge space validation: calculates the deduplicated size of the merged library

**Why check-disk-space**: Node.js's built-in `fs` module does not expose disk space information. This package provides a clean, Promise-based API. On Windows it uses `wmic` internally, avoiding the need to shell out manually.

---

### Development Dependencies

These packages are only used during development. They are installed by `npm install` but are **not** required to run the application in production. They can be omitted with `npm install --omit=dev` if deploying to another machine.

---

#### `@yao-pkg/pkg` — v6.14.0

**Category**: Build tool
**npm**: https://www.npmjs.com/package/@yao-pkg/pkg

`@yao-pkg/pkg` compiles a Node.js application together with the Node.js runtime into a single self-contained executable. The result (`sslm.exe`) runs on any Windows 10/11 machine without Node.js installed.

**Used for:**
- `npm run build` — produces `dist/sslm.exe` targeting `node20-win-x64`
- Bundles all assets declared under `"pkg": { "assets": [...] }` in `package.json`
  - `public/**/*` — all frontend files (HTML, CSS, JS, images, icons)
  - `config/**/*` — default configuration template
  - `installer/sslm.iss` — Inno Setup script (read at runtime for version number)
- `--icon public/assets/sslm.ico` — embeds the application icon into the exe

**Why @yao-pkg/pkg**: Fork of the original `pkg` by Vercel, actively maintained for Node.js 20+. Produces a standalone exe that end users can install without any developer tooling.

---

#### `nodemon` — v3.0.3

**Category**: Development tool
**npm**: https://www.npmjs.com/package/nodemon

`nodemon` monitors the project's source files for changes and automatically restarts the Node.js server whenever a file is saved. This eliminates the need to manually stop and restart the server during development.

**Used for:**
- `npm run dev` — starts the server via nodemon instead of node
- Watches `server.js`, `src/**/*.js`, and related files for changes
- Restarts the server within ~1 second of saving any backend file

**Why nodemon**: Standard development workflow tool for Node.js projects. Not needed in production since files do not change at runtime.

---

### Dependency Tree Summary

| Package | Version | Type | Purpose |
|---------|---------|------|---------|
| `express` | ^4.18.2 | Production | HTTP server and REST API routing |
| `socket.io` | ^4.6.1 | Production | Real-time progress events (import, merge, validation) |
| `fs-extra` | ^11.2.0 | Production | File system operations (copy, scan, read, write, delete) |
| `check-disk-space` | ^3.4.0 | Production | Disk free space validation before operations |
| `@yao-pkg/pkg` | ^6.14.0 | Development | Bundle Node.js runtime + app into `sslm.exe` |
| `nodemon` | ^3.0.3 | Development | Auto-restart server on file changes |

### Transitive Dependencies

Running `npm install` also installs transitive dependencies (packages required by the above packages). These are managed automatically by npm and listed in `package-lock.json`. Key transitive packages include:

| Package | Required by | Purpose |
|---------|------------|---------|
| `accepts` | express | HTTP content negotiation |
| `body-parser` | express | Request body parsing |
| `cors` | socket.io | Cross-Origin Resource Sharing headers |
| `debug` | express, socket.io | Debug logging utility |
| `engine.io` | socket.io | Transport layer (WebSocket / long-polling) |
| `mime-types` | express | MIME type resolution for static file serving |
| `ms` | various | Millisecond time conversion utility |
| `path-to-regexp` | express | URL pattern matching for route definitions |
| `ws` | socket.io | WebSocket implementation |

> **Note**: You do not need to manage transitive dependencies manually. `npm install` handles the full dependency tree. The complete list is locked in `package-lock.json`.

### Installing Dependencies

```bash
# Install all dependencies (production + development)
npm install

# Install production dependencies only (for deployment)
npm install --omit=dev

# Check for outdated packages
npm outdated

# Update all packages to latest compatible versions
npm update
```

---

## Publishing a Release

> This section is for the **developer/maintainer** only.

### Prerequisites
- `gh` CLI installed: [cli.github.com](https://cli.github.com)
- Authenticated: `gh auth login` (first time only — choose GitHub.com → HTTPS → browser)
- Inno Setup 6.x installed at `D:\Program Files (x86)\Inno Setup 6\`

### Release Checklist

**1. Bump the version** (in four places):
- `installer/sslm.iss` → `#define AppVersion "X.X.X"` ← source of truth
- `package.json` → `"version": "X.X.X"`
- `public/index.html` → footer span `SSLM - SeeStar Library Manager vX.X.X`
- `README.md` → badge, installer filename references, footer

**2. Build the exe:**
```
npm run build
```
Output: `dist/sslm.exe` (~46 MB)

**3. Build the installer** (Inno Setup):
```
"D:\Program Files (x86)\Inno Setup 6\ISCC.exe" installer\sslm.iss
```
Output: `installer/output/SSLM-Setup-vX.X.X.exe`

**4. Run VirusTotal and write Security Note** — see `notes/HOW_TO_RELEASE.md` for details.

**5. Commit and push source code:**
```
git add installer/sslm.iss package.json public/index.html README.md
git add documentation/UserManual.md documentation/SSLM_Functionalities.md documentation/InstallationManual.md
git commit -m "vX.X.X — description"
git push origin main
```

**6. Create a draft GitHub Release** (review before publishing):

```
gh release create vX.X.X \
  "installer/output/SSLM-Setup-vX.X.X.exe" \
  "documentation/release-vX.X.X/ReleaseNotes_vX.X.X.md" \
  "documentation/release-vX.X.X/SecurityNote_vX.X.X.md" \
  "documentation/release-vX.X.X/UserManual_vX.X.X.md" \
  "documentation/release-vX.X.X/SSLM_Functionalities_vX.X.X.md" \
  "documentation/release-vX.X.X/InstallationManual_vX.X.X.md" \
  "documentation/release-vX.X.X/SecurityPosture_vX.X.X.md" \
  --title "SSLM vX.X.X" \
  --notes-file "documentation/release-vX.X.X/ReleaseNotes_vX.X.X.md" \
  --prerelease \
  --draft
```

Remove `--prerelease` for stable releases. Review the draft URL returned by `gh` on GitHub.

**7. Publish the release** (once draft is confirmed):
```
gh release edit vX.X.X --draft=false
```

The installer will be downloadable at: `https://github.com/AstroNoob-Tools/SSLM/releases`

> **Full checklist**: See `notes/HOW_TO_RELEASE.md` (local file, gitignored).

---

*SSLM - SeeStar Library Manager v1.0.0-beta.3 — Installation Manual*
*Last updated: February 2026*
