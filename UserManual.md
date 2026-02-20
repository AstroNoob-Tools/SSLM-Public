# SSLM - SeaStar Library Manager
## User Manual

**Version**: 1.0.0-beta.1
**Date**: February 2026

---

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Welcome Screen](#welcome-screen)
4. [Importing from SeeStar](#importing-from-seestar)
5. [Using a Local Copy](#using-a-local-copy)
6. [Merging Libraries](#merging-libraries)
7. [The Dashboard](#the-dashboard)
8. [Object Detail View](#object-detail-view)
9. [Session Detail View](#session-detail-view)
10. [Catalog Detail View](#catalog-detail-view)
11. [Cleanup Operations](#cleanup-operations)
12. [Image Viewer](#image-viewer)
13. [Navigation & Tips](#navigation--tips)
14. [Favorites](#favorites)
15. [Frequently Asked Questions](#frequently-asked-questions)

---

## Introduction

**SSLM (SeaStar Library Manager)** is a desktop web application for managing your astrophotography collection from a SeeStar telescope. It runs locally on your Windows PC — no internet connection is required.

### What SSLM Does

- **Imports** files from a connected SeeStar device to your local storage
- **Organises** astronomical images by celestial objects
- **Analyses** your collection with detailed statistics
- **Merges** multiple library copies into one consolidated library
- **Cleans up** unnecessary files (thumbnails, preview images) to save space
- **Expurged mode** — optionally skips JPG/thumbnail files from sub-frame directories during import or merge, saving significant disk space
- **Displays** imaging sessions, integration times, and exposure details

### What SSLM Does NOT Do

- SSLM never modifies or deletes files on your SeeStar device
- SSLM does not process or stack images
- SSLM does not control the telescope

### Safety First

> **Important**: SSLM always works on a local copy of your data. It will never write to, modify, or delete any files on your connected SeeStar device.

---

## Getting Started

1. Start the application (see the Installation Manual for details)
2. Open your browser and go to `http://localhost:3000`
3. The **Welcome Screen** appears

The application header contains buttons available at all times:

| Button | Icon | Action |
|--------|------|--------|
| Dashboard | 📊 | Return to the dashboard (visible once a library is loaded) |
| Home | 🏠 | Return to the Welcome Screen |
| Settings | ⚙️ | Open settings (online/offline mode) |
| About | ℹ️ | Show application version and contact information |
| Quit | ⏻ | Gracefully shut down the SSLM server and close the application |

---

## Welcome Screen

The Welcome Screen presents three options:

| Option | When to Use |
|--------|------------|
| **Import from SeeStar** | Your SeeStar device is connected and you want to copy its contents to your PC |
| **Use Local Copy** | You already have a SeeStar library on your PC |
| **Merge Libraries** | You have multiple library copies and want to combine them into one |

---

## Importing from SeeStar

Use this option when your SeeStar device is physically connected (via USB) or accessible over your local network.

### Step 1: Select SeeStar Device

SSLM automatically scans all drive letters (C: through Z:) and looks for the SeeStar `MyWorks` directory.

**If your device appears in the list:**
- Click on the device card to select it
- A proceed button appears on the right
- Click **Next** to continue

**If your device does not appear:**
- Ensure the device is connected and powered on
- Check that it appears as a drive in Windows File Explorer
- Use the **Network Path** option (`\\seestar\MyWorks`) if your SeeStar is connected in station (Wi-Fi) mode
- Click **Refresh** to re-scan

### Step 2: Choose Import Strategy

| Strategy | Description | Best For |
|----------|-------------|----------|
| **Full Copy** | Copies every file from the device | First-time import, or starting fresh |
| **Incremental Copy** | Copies only new or changed files | Updating an existing library after a new session |

> **Tip**: Use **Incremental Copy** for regular updates. It compares file sizes and dates, skipping files already in your library. This saves significant time when your library is large.

#### Expurged Mode

A checkbox labelled **Expurged** is also available on this step:

| Mode | Behaviour |
|------|-----------|
| **Full** (unchecked, default) | All files copied, including JPG and thumbnail previews in `_sub` directories |
| **Expurged** (checked) | Only `.fit` light frames are copied from `_sub` directories — JPG and thumbnail files are intentionally skipped |

> **When to use Expurged mode**: If you plan to process only the raw `.fit` data and don't need preview images in your sub-frame directories, Expurged mode can save several gigabytes of disk space.
>
> When Expurged mode is selected, the **Disk Space** screen (Step 3) shows how much space is saved compared to a Full import.

### Step 3: Select Destination

Browse to the folder where you want your local library to be stored.

- Use the drive list to navigate to the right drive
- Browse into subfolders using the directory listing
- Click **Create New Folder** to create a dedicated folder for your library
- Use **Favourites** for quick access to frequently used folders
- The available disk space is shown — ensure you have enough room (SeeStar libraries can reach 50 GB)

> **Recommended**: Create a dedicated folder such as `D:\SeeStar Library\` rather than importing into an existing folder.

### Step 4: Review & Confirm

Review the import summary:
- Source device and path
- Import strategy selected
- Destination folder
- Estimated file count
- Space required vs. space available

Click **Start Import** when ready.

### Step 5: Import Progress

A real-time progress display shows:

| Metric | Description |
|--------|-------------|
| Progress bar | Overall percentage complete |
| Current file | The file currently being copied |
| Files copied | Count and percentage |
| Files skipped | Files not copied (incremental only) |
| Data transferred | Amount in MB or GB |
| Transfer speed | Current speed in MB/s |
| Time remaining | Estimated time to completion |
| Elapsed time | How long the import has been running |

**During import:**
- Do not disconnect the SeeStar device
- Do not move or delete files in the destination folder
- You can click **Cancel** to stop the import safely at any point

**When the import completes:**
- A completion message appears
- Click **Done** to open the completion summary
- Choose to **Validate Transfer** (recommended) or **Skip to Dashboard**

### Transfer Validation (Recommended)

After importing, SSLM can verify that all files were copied correctly:
- Scans all source files
- Checks each file exists in the destination with the correct size
- Reports any missing or mismatched files

If validation finds issues, run an incremental import again to fill in any gaps.

---

## Using a Local Copy

Use this option if you have previously imported your SeeStar library and want to open it directly.

### Selecting a Library Folder

1. Click **Use Local Copy** on the Welcome Screen
2. A folder browser opens
3. Navigate to your library folder (the folder containing your object folders such as `NGC 6729`, `M 42`, etc.)
4. Click on the folder to select it
5. Click **Open Library**

**Alternatively**, use a favourite folder for quick access (see [Favourites](#favourites)).

SSLM validates the selected folder and loads the dashboard automatically.

---

## Merging Libraries

Use this option when you have multiple copies of a SeeStar library (e.g., from different backup locations or drives) and want to combine them into one consolidated library.

### Overview

- **Sources**: Two or more library folders to merge
- **Destination**: Where the merged library will be created
- **Duplicate handling**: Files with the same relative path are considered duplicates; the newer version is kept
- **Safety**: Source libraries are never modified

### Step 1: Add Source Libraries

1. Click **Add Library** to open the folder browser
2. Navigate to and select the first source library
3. Click **Add Library** again for each additional source
4. At least **2 libraries** are required
5. Libraries appear in a list — click the remove button to remove any added in error

Click **Next** when all sources are added.

### Step 2: Select Destination & Mode

Browse to or create the destination folder for your merged library.

> **Recommended**: Create a new empty folder for the merged library. Do not select one of your source libraries as the destination.

#### Expurged Mode

A checkbox labelled **Expurged** is also available on this step:

| Mode | Behaviour |
|------|-----------|
| **Full** (unchecked, default) | All files merged, including JPG and thumbnail previews in `_sub` directories |
| **Expurged** (checked) | Only `.fit` light frames are merged from `_sub` directories — JPG and thumbnail files are skipped |

The required disk space shown on this step reflects the selected mode.

SSLM shows required space vs. available space. The required space is the deduplicated size — files that exist in multiple libraries are only counted once.

### Step 3: Analysis & Preview

SSLM scans all source libraries. A real-time display shows:
- Which library is currently being scanned
- File count found in each library

After scanning, a summary table shows:
| Statistic | Meaning |
|-----------|---------|
| Total files | Sum of all files across all sources |
| Duplicates detected | Files found in more than one source (same relative path) |
| Conflicts found | Duplicates where file versions differ |
| Already in destination | Files already present in destination (skipped) |
| Final file count | Files after deduplication |
| Files to copy | Actual files that will be copied |
| Space required | Storage needed for the merge |

A **conflict preview** shows the first 10 conflicts with:
- File path
- Version from each source (size and date)
- Which version wins (the newer one)

> **If no files need copying** (destination already up to date), SSLM automatically skips to validation.

### Step 4: Confirm Merge

Review the full merge plan:
- Source libraries (count and paths)
- Destination path
- Merge statistics
- Disk space validation
- Conflict resolution strategy (always: keep newer version by date)

Click **Start Merge** to begin.

### Step 5: Merge Progress

Real-time display showing:
- Overall progress bar
- Current file being copied (with source library name)
- File statistics and byte statistics
- Transfer speed and ETA
- Per-source progress breakdown

You can click **Cancel** to stop the merge safely.

### Step 6: Validation

After merging, SSLM automatically validates the merged library:
- Checks all expected files exist in the destination
- Verifies file sizes
- Reports any issues

On success, SSLM loads the merged library into the dashboard automatically.

---

## The Dashboard

The dashboard provides a complete overview of your SeeStar library.

### Layout

The dashboard has two main areas:
- **Left sidebar**: Sticky navigation (always visible while scrolling)
- **Main content**: Statistics, charts, and data tables

### Sidebar Navigation

The sidebar contains two sections:

**Actions:**
| Button | Action |
|--------|--------|
| 📥 Import from SeeStar | Opens the import wizard |
| 📁 Use Local Copy | Open a different library |
| 🔀 Merge Library | Opens the merge wizard |

**Navigation:**
| Link | Scrolls to |
|------|-----------|
| 📊 Dashboard | Top of the dashboard |
| 📈 Summary | Summary statistics cards |
| 📄 File Types | File type breakdown |
| 📚 Catalogs | Catalog breakdown |
| ⚠️ Empty Dirs | Empty directories (only shown if present) |
| 🧹 Cleanup | Sub-frame cleanup section (only shown if needed) |
| 🎯 Objects | Objects table |

### Summary Cards

Six summary statistics:

| Card | Description |
|------|-------------|
| Total Objects | Total number of celestial objects in the library |
| With Sub-Frames | Objects that have individual sub-exposure files |
| Without Sub-Frames | Objects with stacked images only |
| Total Size | Combined disk space used by the library |
| Total Files | Total number of files across all objects |
| Empty Folders | Directories with no files (only shown if present) |

### File Type Breakdown

Shows counts of each file type in the library:
- **.FIT files**: Scientific image data (your primary data)
- **JPG files**: Preview images of stacked frames
- **Thumbnails** (`_thn.jpg`): Small preview images
- **MP4 videos**: Timelapse or preview videos

### Catalog Breakdown

Visual cards showing object counts by astronomical catalog:
- Messier (M)
- NGC (New General Catalogue)
- IC (Index Catalogue)
- Sharpless (SH)
- Named objects
- Other

Click any catalog card to open the **Catalog Detail View**.

### Empty Directories

If SSLM detects empty folders in your library:
- A list of empty directories is shown
- Click **Delete All Empty Directories** to remove them all at once

### Sub-Frame Cleanup

If your `_sub` directories contain JPG or thumbnail files (not needed for processing):
- Shows total space that can be freed
- **Clean All**: Removes non-.fit files from all sub-frame directories
- **Individual cleanup**: Shown per-object in the objects table

> **Safe**: Cleanup only removes JPG and thumbnail preview files. Your `.fit` data files are never deleted.

### Objects Table

A comprehensive table of all celestial objects, with:

| Column | Description |
|--------|-------------|
| Object | Object name (clickable for details) |
| Catalog | Catalog type (Messier, NGC, IC, etc.) |
| Sub-Frames | Whether sub-frame exposures were saved |
| .fit / Other | File count breakdown in sub-frame directory |
| Integration | Total integration time |
| Files | Total file count for the object |
| Size | Total disk space used |
| Actions | Cleanup button (shown only if applicable) |

**Search**: Type in the search box to filter objects by name or catalog in real time.

Click any object name to open its **Object Detail View**.

---

## Object Detail View

Clicking an object name opens a detailed view with comprehensive information about that object's imaging data.

### Header

- Object name
- Catalog type
- Sub-frame presence indicator
- Quick statistics (integration time, light frames, sessions, total size)

### Summary Cards

| Card | Description |
|------|-------------|
| Integration Time | Total time spent imaging this object |
| Light Frames | Number of individual sub-exposures |
| Sessions | Number of imaging sessions |
| Total Size | Disk space used by this object |

### Imaging Metadata

Shows all unique combinations of:
- **Stacking Counts**: Displays the total frames across all sessions and the per-session breakdown.
  Format: `751 total (298, 453 per session)`. The total is the sum of all sessions' final frame counts. Each per-session value is the number of sub-frames combined in that session's final stacked image.
  > **Note**: The SeeStar saves progressive stacking snapshots during a live session (e.g., 74 frames mid-session, 453 frames at the end). SSLM shows only the final result per session, not the intermediate snapshots.
- **Exposure time**: Time per sub-frame (e.g., 30.0s, 10.0s)
- **Filters used**: IR Cut (IRCUT), Light Pollution (LP), etc.

### Exposure Breakdown

Visual cards showing:
- **Main folder exposures**: Stacked images grouped by exposure time
- **Sub-frames exposures**: Light frames grouped by exposure time

### Imaging Sessions Table

Each row represents a distinct imaging session. Sessions captured on the same night with the same exposure and filter are automatically grouped into a single row (only the final result is shown, not intermediate progress snapshots).

| Column | Description |
|--------|-------------|
| Date | Date the session was captured — **click to open Session Detail View** |
| Time | Time of the final stacked image for this session |
| Stacked Frames | Number of frames in the final stacked image |
| Exposure | Per-frame exposure time |
| Filter | Filter used |
| Integration | Total time for this session |
| Actions | 🗑️ Delete button — permanently deletes all files for this session |

#### Clicking a Session Date

Click any date link (shown in blue) to open the **Session Detail View** for that session.

#### Deleting a Session

Click the 🗑️ button on a session row to permanently delete all files belonging to that session (stacked images in the main folder and corresponding light frames in the sub-frames folder). A confirmation dialog appears before any files are deleted.

> **Warning**: Session deletion is permanent. Deleted files cannot be recovered from the Recycle Bin.

### File Lists

Expandable sections for each folder:

**Main Folder Files:**
- Stacked `.fit` images
- Preview `.jpg` images (clickable — opens Image Viewer)
- Thumbnail `_thn.jpg` images (clickable — opens Image Viewer)
- Other file types

**Sub-Frames Folder Files** (if sub-frames exist):
- Individual light frame `.fit` files
- Preview images (clickable)
- Each file shows its capture date

### Cleanup Button

If the sub-frame directory contains non-.fit files, a **Clean Up Sub-Frames** button appears. Clicking it removes the preview files for this specific object.

### Navigation

- Click **Back to Dashboard** to return to the main dashboard
- Click the **Dashboard** button (📊) in the header to return to the main dashboard
- Click the **Home** button (🏠) to return to the Welcome Screen

---

## Session Detail View

The Session Detail View opens when you click a date link in the Imaging Sessions table of an Object Detail page. It shows every file associated with that specific imaging session.

### Session Header

A row of summary cards at the top shows:

| Card | Description |
|------|-------------|
| Date | Date of the session |
| Time | Time of the final stacked image |
| Stacked Frames | Number of sub-frames in the final stack |
| Exposure | Per-frame exposure time |
| Filter | Filter used |
| Integration | Total integration time for the session |

### File Sections

**Stacked Images (Main Folder)**
All stacked `.fit`, `.jpg`, and thumbnail files from this session. File sections are expanded by default for easy browsing.

**Sub-Frame Light Frames**
All individual light frame `.fit` and preview files from the sub-frames folder that match this session's date, exposure, and filter. Only shown if the object has a sub-frames directory.

### Deleting the Session

Click the **Delete Session** button (top right, red) to permanently delete all files for this session — both stacked images from the main folder and light frames from the sub-frames folder. A confirmation dialog shows the file count before proceeding.

### Navigation

- Click **← Back** to return to the Object Detail page
- Click **Delete Session** to delete all session files (requires confirmation)

---

## Catalog Detail View

Clicking any catalog card on the dashboard opens a filtered view for that catalog.

### Catalog Statistics

| Statistic | Description |
|-----------|-------------|
| Total Objects | Number of objects in this catalog |
| With Sub-Frames | Objects with sub-frame data |
| Without Sub-Frames | Objects without sub-frame data |
| Total Size | Combined disk space for this catalog |
| Total Files | Total file count for this catalog |
| Integration Time | Total imaging time for this catalog |
| Date Range | Oldest and newest capture dates |

### Filtered Objects Table

The same table as the main dashboard, but showing only objects from the selected catalog. All the same interactions apply (click objects for details, search, cleanup buttons).

### Navigation

- Click **Back to Dashboard** to return to the main dashboard
- Click the **Dashboard** button (📊) in the header to return to the main dashboard

---

## Cleanup Operations

### Empty Directory Cleanup

Empty directories can accumulate when objects are renamed or reorganised on the SeeStar device.

1. On the dashboard, scroll to the **Empty Directories** section
2. Review the list of empty directories
3. Click **Delete All Empty Directories**
4. Confirm the deletion in the dialog
5. The dashboard refreshes automatically

### Sub-Frame Cleanup

The SeeStar device saves both `.fit` (scientific data) and `.jpg`/thumbnail preview files in sub-frame directories. The JPG previews are not needed for image processing and can be safely deleted to recover disk space.

#### Global Cleanup (All Objects)

1. On the dashboard, scroll to the **Cleanup** section
2. Review the estimated space to be freed
3. Click **Clean All Sub-Frame Directories**
4. Confirm in the dialog
5. The dashboard refreshes automatically

#### Individual Object Cleanup

1. In the Objects Table, find the object you want to clean
2. Click the **🧹** cleanup button in the Actions column

   — or —

1. Open the Object Detail View for the object
2. Click the **Clean Up Sub-Frames** button

> **Note**: The cleanup button only appears when there are non-.fit files to delete. After cleaning, the button disappears.

#### What Gets Deleted?

| Deleted | Kept |
|---------|------|
| `.jpg` preview images in `_sub` folders | All `.fit` data files |
| `_thn.jpg` thumbnails in `_sub` folders | All stacked images in main folder |
| | All `.fit` light frames |

### Imaging Session Deletion

To permanently remove all files for a specific imaging session:

1. Open the **Object Detail View** for the object
2. In the **Imaging Sessions** table, either:
   - Click the 🗑️ button on the session row, **or**
   - Click the date link to open the Session Detail View, then click **Delete Session**
3. A confirmation dialog shows the number of files that will be deleted
4. Click **Confirm** to proceed

**What gets deleted:**
| Deleted | Location |
|---------|----------|
| All stacked images for the session (`.fit`, `.jpg`, `_thn.jpg`) | Main object folder |
| All light frames for the session (`.fit`, `.jpg`, `_thn.jpg`) | `_sub` folder |

> **Warning**: Session deletion is permanent and cannot be undone. There is no Recycle Bin recovery.

---

## Image Viewer

When viewing an Object Detail page, JPG and thumbnail files can be viewed directly in SSLM.

### Opening an Image

- Hover over any `.jpg` or `_thn.jpg` filename — it highlights
- Click on the filename to open the Image Viewer

### Image Viewer Controls

| Action | Description |
|--------|-------------|
| Click × button | Close the viewer |
| Click outside image | Close the viewer |
| Press `Escape` | Close the viewer |

The filename is displayed below the image.

### Supported Formats

The image viewer supports: **JPG, JPEG, PNG, GIF, BMP, TIF, TIFF**

---

## Navigation & Tips

### Header Buttons

| Button | Visible When | Action |
|--------|-------------|--------|
| 📊 Dashboard | A library is loaded | Returns to main dashboard from any page |
| 🏠 Home | Always | Returns to the Welcome Screen |
| ⚙️ Settings | Always | Opens settings dialog |
| ℹ️ About | Always | Shows version number and contact email |
| ⏻ Quit | Always | Shows confirmation dialog, then shuts down the server |

### Dashboard Button Behaviour

The Dashboard button (📊) is context-aware:
- **From Object Detail**: Navigates back to the main dashboard view
- **From Catalog Detail**: Navigates back to the main dashboard view
- **From main Dashboard**: Smoothly scrolls to the top

### Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `Escape` | Close Image Viewer or modal dialogs |

### Tips for Large Libraries

- Use the **Search** box in the Objects Table to find specific objects quickly
- Use **Catalog cards** to filter by catalog type instead of scrolling
- Use the **Sidebar Navigation** to jump between dashboard sections without scrolling
- Click the **Dashboard** button (📊) to return to the top from anywhere

---

## Favourites

Favourites allow you to quickly access folders you use frequently, without browsing every time.

### Adding a Favourite

When using the folder browser (for Import, Local Copy, or Merge):
1. Navigate to the desired folder
2. Click the **⭐ Add to Favourites** button
3. The folder is saved for future sessions

### Using a Favourite

1. Open the folder browser
2. Your saved favourites appear at the top
3. Click any favourite to instantly select it

### Removing a Favourite

1. Open the folder browser
2. In the Favourites list, click the **✕** button next to the favourite you want to remove

Favourites are stored in `config/settings.json` and persist between sessions.

---

## Frequently Asked Questions

### Will SSLM delete or modify any files on my SeeStar device?

**No.** SSLM only reads from your SeeStar device. It never writes to, modifies, or deletes any files on the device. All operations are performed on your local library copy.

### I ran a cleanup — can I undo it?

Unfortunately, no. Deleted files are permanently removed (not sent to the Recycle Bin). Always review the confirmation dialog before confirming a cleanup. The cleanup only removes JPG preview files — your `.fit` data files are never touched.

### What is an incremental import?

An incremental import compares each file on the SeeStar device to the corresponding file in your local library. If the file already exists and has the same size and date, it is skipped. Only new or changed files are copied. This is much faster than a full copy when updating an existing library.

### How does conflict resolution work in a merge?

When the same file (same relative path) exists in multiple source libraries, SSLM keeps the **newer version** based on the file's modification date. The older version is discarded. The conflict preview in Step 3 of the merge wizard shows which version will be kept.

### How is integration time calculated?

Integration time is calculated from stacked image filenames. Each stacked image filename contains the number of stacked frames (e.g., `Stacked_210_...`) and the exposure per frame (e.g., `30.0s`). Integration time = frames × exposure time. For example, 210 frames × 30 seconds = 6,300 seconds = 1 hour 45 minutes.

### What is Expurged mode?

Expurged mode is an option available in both the Import and Merge wizards (Step 2). When enabled, SSLM skips all JPG and thumbnail preview files inside `_sub` directories and copies only `.fit` light frames. This can save several gigabytes of disk space since the SeeStar generates a JPG and thumbnail for every sub-frame captured.

Use Expurged mode if you only intend to process the raw `.fit` data with stacking software and have no need for the preview images. The `.fit` files in your main object folders (stacked images) are always copied regardless of this setting.

After an Expurged import, the transfer validation correctly ignores the intentionally skipped files — no false errors are reported.

### My import shows a large number of "skipped" files. Is that normal?

Yes, for incremental imports. Files are skipped when they already exist in the destination with the same size and modification date. A high skip count means your local library is already up to date with the device.

### The dashboard is showing wrong statistics after a cleanup. How do I refresh?

The dashboard refreshes automatically after cleanup operations. If statistics still appear wrong, click **Use Local Copy**, select the same folder again, and the library will be fully re-analysed.

### Can I delete an individual imaging session?

Yes. Open the Object Detail View for the object, then either click the 🗑️ button on the session row in the Imaging Sessions table, or click the session date to open the Session Detail View and use the **Delete Session** button there. A confirmation dialog will show how many files will be deleted. This removes all stacked images and corresponding sub-frame light files for that session permanently.

### Can SSLM access a library on a network drive?

Yes. When selecting a destination or local copy folder, browse to the network drive as you would any other folder. Note that operations on network drives are significantly slower than on local SSDs.

### Why do some objects not have a "Clean Up Sub-Frames" button?

The cleanup button only appears when there are non-.fit files (JPG previews or thumbnails) present in the sub-frame directory. If you have already cleaned a directory, or if the object has no sub-frames, the button is not shown.

### Can I run multiple instances of SSLM?

Running two instances on the same port will cause a conflict. You can run a second instance on a different port by changing `server.port` in `config/settings.json` to a different number before starting the second instance.

---

## File Naming Reference

Understanding SeeStar file naming conventions:

### Stacked Images (Main Folder)
```
Stacked_[frames]_[object]_[exposure]_[filter]_[timestamp].fit
```
Example: `Stacked_210_NGC 6729_30.0s_IRCUT_20250822-231258.fit`

| Part | Value | Meaning |
|------|-------|---------|
| `210` | Frame count | 210 sub-frames were stacked |
| `NGC 6729` | Object name | Catalog designation |
| `30.0s` | Exposure | 30 seconds per frame |
| `IRCUT` | Filter | IR Cut filter |
| `20250822-231258` | Timestamp | 2025-08-22 at 23:12:58 |

### DSO Stacked Images
```
DSO_Stacked_[frames]_[object]_[exposure]_[timestamp].fit
```

### Light Frames (Sub-Frame Folder)
```
Light_[object]_[exposure]_[filter]_[timestamp].fit
```
Example: `Light_NGC 6729_30.0s_IRCUT_20250822-203353.fit`

### Directory Naming
| Pattern | Example | Meaning |
|---------|---------|---------|
| `[Object]` | `NGC 6729` | Main stacked images |
| `[Object]_sub` | `NGC 6729_sub` | Individual sub-frames |
| `[Object]_mosaic` | `M 45_mosaic` | Mosaic capture |

### Filter Codes
| Code | Full Name |
|------|-----------|
| `IRCUT` | IR Cut filter |
| `LP` | Light Pollution filter |

---

*SSLM - SeaStar Library Manager v1.0.0-beta.1 — User Manual*
*Last updated: February 2026*
