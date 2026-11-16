# 🧍 GTA SA Ped Add-On Studio

[![Launch Web App](https://img.shields.io/badge/🚀_Launch_Web_App-Live_Demo-2ea44f?style=for-the-badge)](https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/)
[![Version](https://img.shields.io/badge/Version-1.1.0-blue?style=for-the-badge)](#📋-changelog)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](./LICENSE)

> **A zero-installation, browser-based bulk spreadsheet workbench & interactive 3D DFF/TXD viewer for creating, merging, validating, and exporting both non-replacing Add-On Pedestrians (`14000+`) and Vanilla Base-Game Pedestrians (`ped.ide` + `pedgrp.dat`) for Grand Theft Auto: San Andreas.**

---

## 📑 Table of Contents

1. [Introduction & Background](#1-introduction--background)
   - [Why This Tool Exists](#why-this-tool-exists)
   - [What Is GTA SA Ped Add-On Studio?](#what-is-gta-sa-ped-add-on-studio)
   - [Goals & Vision](#goals--vision)
   - [Bulk Edit & Bulk Processing Capabilities](#bulk-edit--bulk-processing-capabilities)
   - [How to Access the Tool](#how-to-access-the-tool)
2. [Open Source & Code Documentation](#2-open-source--code-documentation)
3. [Mandatory Game Prerequisites (Before You Start)](#3-mandatory-game-prerequisites-before-you-start)
   - [Step A: Find Empty IDs with Free ID List Generator (`GENEFREE`)](#step-a-find-empty-ids-with-free-id-list-generator-genefree)
   - [Step B: Expand Ped, Killable ID & Group Limits in Fastman92 Limit Adjuster](#step-b-expand-ped-killable-id--group-limits-in-fastman92-limit-adjuster)
4. [Importing Files & Folder Structure Scenarios](#4-importing-files--folder-structure-scenarios)
   - [Scenario 1: Converting Loose Skins (`Add/` Folder — Anti-Replace)](#scenario-1-converting-loose-skins-add-folder--anti-replace)
   - [Scenario 2: Merging Multiple Add-On Packs (`data/1`, `data/2`, ...)](#scenario-2-merging-multiple-add-on-packs-data1-data2-)
   - [Scenario 3: Re-Editing Previously Exported Add-On Data](#scenario-3-re-editing-previously-exported-add-on-data)
   - [Scenario 4: Editing Vanilla / Original Game Peds (`Vanilla IDE+GRP Mode`)](#scenario-4-editing-vanilla--original-game-peds-vanilla-idegrp-mode)
5. [Step-by-Step Usage Guide & Real-World Case Studies](#5-step-by-step-usage-guide--real-world-case-studies)
   - [5.1 Workspace Overview, Keyboard Shortcuts & Multi-Row Selection](#51-workspace-overview-keyboard-shortcuts--multi-row-selection)
   - [5.2 Checking Ped Roles Visually with the 3D Ped Viewer](#52-checking-ped-roles-visually-with-the-3d-ped-viewer)
   - [5.3 Bulk Cloning Parameters (`Copy Data From Ped`)](#53-bulk-cloning-parameters-copy-data-from-ped)
   - [5.4 Rule-Based Smart Randomization, Group Counts & Blacklist Constraints (`(x)`)](#54-rule-based-smart-randomization-group-counts--blacklist-constraints-x)
   - [5.5 Sequential Auto-Rename (`14000+`) — ⚠️ Check Free IDs First!](#55-sequential-auto-rename-14000--%EF%B8%8F-check-free-ids-first)
   - [5.6 Automated Clash Detection & Auto-Fix (`Check & Fix Clashes`)](#56-automated-clash-detection--auto-fix-check--fix-clashes)
   - [5.7 Exporting & Installing into Modloader (With Original `pedgrp.dat`)](#57-exporting--installing-into-modloader-with-original-pedgrpdat)
6. [Troubleshooting & FAQ](#6-troubleshooting--faq)
7. [Changelog](#-changelog)

---

## 1. Introduction & Background

### Why This Tool Exists
Adding brand-new pedestrians—or customizing existing vanilla pedestrian behaviors—in **GTA San Andreas** has historically been a tedious, error-prone manual process. Modders had to:
- Manually type 14-column entries inside `.ide` files (`ID, ModelName, TxdName, Pedtype, Stat, AnimGroup, CarsMask, Flags, AnimFile, Radio1, Radio2, AudioType, Voice1, Voice2`).
- Manually edit `pedgrp.dat` population lines without accidentally erasing vanilla street spawns or exceeding GTA SA's default **23-peds-per-group** limit.
- Rename hundreds of `.dff` and `.txd` files one by one to match new numeric IDs.
- Guess whether a downloaded skin was male, female, elderly, gangster, or emergency personnel without a quick visual preview—often leading to immersion-breaking bugs (such as an elderly woman speaking with a male gangster voice).

### What Is GTA SA Ped Add-On Studio?
**GTA SA Ped Add-On Studio** is a lightweight, single-file (`index.html`) web workbench that combines a **virtualized Excel-style bulk spreadsheet editor**, a **dual-mode configuration engine (Add-On Mode & Vanilla IDE+GRP Mode)**, an **automated file-synchronization pipeline**, and a **hardware-accelerated 3D WebGL `.dff`/`.txd` model viewer** directly inside your browser—requiring **zero installation, zero Python/Node dependencies, and zero backend servers**.

### Goals & Vision
- **Beginner-Friendly Accessibility:** Empower everyday players and novice modders to build massive, crash-free add-on pedestrian packs—or customize original game peds—without memorizing cryptic GTA SA internal flags or voice bank IDs.
- **Zero Vanilla Replacement:** Automatically convert loose skin files (even those originally named `bmyst.dff` or `fam1.dff`) into safe, non-conflicting numeric add-on IDs (`14000+`).
- **Lore-Accurate World Immersion:** Ensure every pedestrian spawns in canonical Los Santos, San Fierro, and Las Venturas population groups, drives appropriate vehicle classes, walks with fitting animations, and speaks with gender- and role-matched voices.

### Bulk Edit & Bulk Processing Capabilities
| Capability | What It Does in Bulk |
| :--- | :--- |
| **Dual Editing Modes** | Switch seamlessly between **Add-On Mode (`14000+`)** with full 3D models and **Vanilla Mode (`IDE + GRP`)** to edit original game `ped.ide` & `pedgrp.dat` files directly. |
| **Virtualized High-Speed Sheet** | Uses viewport virtualization and on-demand dropdown hydration so 1,000+ pedestrian rows scroll, filter, and select instantaneously with zero browser lag. |
| **Multi-Pack Folder Import** | Parses `data/`, numbered merge folders (`data/1`, `data/2`...), `models/gta3/`, and raw `Add/` skins in one click. |
| **Range & Bulk Selection** | Supports header `Select All`, `Shift + Click` range selection across hundreds of rows, `Ctrl+F` instant search, and live multi-criteria filtering. |
| **Bulk Parameter Cloning** | Copies up to **12 independent parameter toggles** from any reference ped onto hundreds of selected rows simultaneously. |
| **Rule-Based Smart Randomization** | Generates context-aware combinations (Gender, Pedtype, Stat, AnimGroup, Flags, Voices, canonical LS/SF/LV Ped Groups) with custom Min/Max group counts, strict gang/police rules, and `(x)` blacklists. |
| **Synchronized Auto-Rename** | Renumbers IDs sequentially (`14000+`) and automatically renames all paired `.dff`, `.txd`, `.ide`, and `pedgrp.dat` entries to match. |
| **Mode-Aware Clash Auto-Fix** | Validates IDs, duplicate names, and vanilla name collisions in Add-On Mode—or preserves original IDs (`0–299`) and shared TXDs in Vanilla Mode. |
| **Group Limit Monitor & Export** | Tracks the peak ped count per `pedgrp.dat` line in real time, warns if it exceeds GTA SA's 23-ped limit, and outputs Modloader-ready files. |

### How to Access the Tool

**🌐 Live Web App (Recommended):** **[https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/](https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/)**

**GTA SA Ped Add-On Studio** runs in a **dual-mode browser architecture** (recommended browsers: **Google Chrome**, **Microsoft Edge**, or **Brave**):

| Mode | Access Method | How It Works |
| :--- | :--- | :--- |
| **MODE 1: Online / Hosted (`https://`)** | **[Launch Live Web App](https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/)** | Uses the modern File System Access API to read and write files directly into your chosen folder without needing to extract a ZIP archive. |
| **MODE 2: Offline / Local (`file://`)** | Download `index.html` from [Releases](../../releases) | Double-click `index.html` in Chrome/Edge. Imports via standard folder picker and exports a ready-to-extract `.zip` archive (or individual files in Vanilla Mode). |

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│                        HOW TO LAUNCH THE APPLICATION                         │
├───────────────────────────────────────┬──────────────────────────────────────┤
│  MODE 1: Offline / Local              │  MODE 2: Online / Hosted             │
│  ─────────────────────────────────    │  ──────────────────────────────────  │
│  1. Download `index.html` to your PC. │  1. Open the GitHub Pages URL        │
│  2. Double-click `index.html` to open │     in Chrome / Edge / Brave.        │
│     it directly in Chrome / Edge.     │  2. Uses File System Access API to   │
│  3. Imports via standard folder       │     read & write directly into your  │
│     picker and exports a ready-to-    │     selected folder without ZIPping. │
│     extract `.zip` archive.           │                                      │
└───────────────────────────────────────┴──────────────────────────────────────┘
```
> **Note on 3D Preview:** All spreadsheet editing, importing, randomization, and exporting work **100% offline**. An active internet connection is only required when clicking the **`👁️` 3D Viewer** button in Add-On Mode so the browser can fetch `three.js` and `dff-loader` from the CDN. In **Vanilla IDE+GRP Mode**, 3D preview is intentionally disabled because `.dff`/`.txd` model files are not loaded.

---

### 🛡️ Security & VirusTotal Verification

This tool is a pure client-side static HTML/JavaScript file (`index.html`) with zero backend servers, zero tracking, and zero external data uploads—all file reading and ZIP packaging happen locally inside your browser.

- **VirusTotal Scan Report (0/60+ Clean):** [View Live VirusTotal Analysis](https://www.virustotal.com/gui/file/40443f3801095c804501e7ed75d3919667363e990fa8fe5664b87ea7e90e35e2)
- **SHA-256 Checksum:** `40443f3801095c804501e7ed75d3919667363e990fa8fe5664b87ea7e90e35e2`

---

## 2. Open Source & Code Documentation

This project is released as **open-source software** and engineered so any developer or modder can inspect, customize, or fork it effortlessly:

- **Single-File Architecture (`index.html`):** All styling, UI templates, state management, parsers, smart randomization rules, ZIP compression, and WebGL rendering logic reside cleanly inside one self-contained file with zero build steps.
- **Complete Technical Breakdown ([`code_breakdown.md`](./code_breakdown.md)):** Every module—from `PedStore` $O(1)$ indexing, virtualized viewport rendering (`R01`–`R13`), lazy-hydrated dropdown cells, `MessageChannel` background-safe task scheduling, and Smart Context Sync rules to the 3-tier DFF/TXD fallback loader and binary ZIP writer—is documented in [`code_breakdown.md`](./code_breakdown.md).

---

## 3. Mandatory Game Prerequisites (Before You Start)

Before installing add-on pedestrians (`14000+`) into GTA San Andreas, your game directory **must** have **Modloader** and **Fastman92 Limit Adjuster (FLA)** installed.

### Step A: Find Empty IDs with Free ID List Generator (`GENEFREE`)
If you already use map mods, vehicle add-ons, or other ped packs, ID `14000` might already be occupied. Always generate a free ID report first:
1. Open `fastman92limitAdjuster_GTASA.ini` in your GTA San Andreas root folder.
2. Under `[ADDONS]` or `[ID LIMITS]`, enable **`Generate free ID list = 1`** (or run the `GENEFREE` utility included with Fastman92 Limit Adjuster).
3. Launch the game once; it will generate `free_ids.txt` in your game directory.
4. Pick a contiguous block of unused IDs (for example `14000`–`14500` or `18000`–`18500`) to use in **Auto-Rename**.

### Step B: Expand Ped, Killable ID & Group Limits in Fastman92 Limit Adjuster
Open `fastman92limitAdjuster_GTASA.ini` and ensure the following limits are enabled and raised above your total ped count:

```ini
[ID LIMITS]
Apply ID limit patch = 1
Ped models = 650
Killable model IDs = 20000

[PED LIMITS]
Apply pedgrp.dat limit patch = 1
Pedgrp peds limit = 64
```
> **Built-In `pedgrp.dat` Limit Monitor (New in v1.1.0):** Default GTA San Andreas only allows **23 pedestrians** per population group line in `pedgrp.dat`. The studio tracks the highest ped count across all groups in real time via the **`Max Peds/Grp`** badge in the toolbar and displays a detailed warning inside the **Export** dialog whenever any group exceeds `23`, telling you the exact `Pedgrp peds limit` value to set in `fastman92limitAdjuster_GTASA.ini`.

---

## 4. Importing Files & Folder Structure Scenarios

On startup, choose either **Mode 1: Add-On Ped Studio** (via **`📂 Import Folder`**) or **Mode 2: Vanilla Ped Editor** (via **`🛠️ Load Vanilla IDE+GRP`**).

### Scenario 1: Converting Loose Skins (`Add/` Folder — Anti-Replace)
Download any skin mod (even if the files are named `bmyst.dff` / `bmyst.txd` or `fam1.dff` / `fam1.txd`) and drop them inside a subfolder named `Add/`:
```text
My_Ped_Project/
└── Add/
    ├── bmyst.dff
    ├── bmyst.txd
    ├── custom_girl.dff
    └── custom_girl.txd
```
When you import `My_Ped_Project/`, the studio automatically pairs `.dff` + `.txd` files and assigns safe sequential numeric IDs (`14000`, `14001`, ...) so vanilla peds are never overwritten.

### Scenario 2: Merging Multiple Add-On Packs (`data/1`, `data/2`, ...)
Combine multiple existing add-on ped packs into a single unified pack without manual copy-pasting:
```text
My_Ped_Project/
├── data/
│   ├── 1/
│   │   ├── peds_addons.ide
│   │   └── pedgrp.dat
│   └── 2/
│       ├── peds_addons.ide
│       └── pedgrp.dat
└── models/
    └── gta3/
        ├── 1/   (models for pack 1)
        └── 2/   (models for pack 2)
```
The importer reads every numbered folder in order, links each `.ide` row to its matching `.dff`/`.txd` files, and merges all `pedgrp.dat` group assignments.

### Scenario 3: Re-Editing Previously Exported Add-On Data
Select any previously exported Modloader folder containing `data/` (`peds_addons.ide`, `pedgrp.dat`) and `models/gta3/` (`*.dff`, `*.txd`) to continue editing, adding new rows, or previewing models in 3D.

### Scenario 4: Editing Vanilla / Original Game Peds (`Vanilla IDE+GRP Mode`)
Want to edit Rockstar's original `data/ped.ide` and `data/pedgrp.dat` (or a total-conversion mod's base ped files) directly?
1. Click **`🛠️ Load Vanilla IDE+GRP`** (from the welcome card or top toolbar).
2. Select your **`ped.ide`** (or `peds.ide`) in Step 1, and **`pedgrp.dat`** in Step 2, then click **Load into Editor**.
3. The studio switches to **`VANILLA IDE+GRP MODE`**, preserving original IDs (`0`–`299`), comments, and shared `TxdName` assignments while disabling 3D preview and Add-On auto-renaming.

---

## 5. Step-by-Step Usage Guide & Real-World Case Studies

### 5.1 Workspace Overview, Keyboard Shortcuts & Multi-Row Selection
- **Virtualized Spreadsheet Grid:** Sticky left columns keep the **Checkbox**, **ID**, and **ModelName** pinned while you scroll horizontally across all 18 columns. Only visible rows are mounted in the DOM, keeping scrolling silky smooth even with 1,000+ peds.
- **Instant Search (`Ctrl+F` / `Cmd+F`):** Press **`Ctrl+F`** (or **`Cmd+F`**) anywhere on the page to jump straight to the search box and filter by ID, ModelName, Pedtype, Stat, AnimGroup, Voice, or Ped Group.
- **Fast Multi-Row Selection:**
  - Click any row checkbox to select/deselect it.
  - Hold **`Shift` + Click** another row checkbox to instantly select or deselect the entire range in between.
  - Use the header checkbox (**`Select All`**) to select all currently filtered rows at once.

### 5.2 Checking Ped Roles Visually with the 3D Ped Viewer
1. In **Add-On Mode**, click the **`👁️` Eye icon** inside any row's `ModelName` cell.
2. A resizable floating **3D Ped Preview** window opens, displaying vertex/triangle/mesh counts, file sizes, wireframe toggle, camera reset, and a **`🔄 Rotate 90°`** button (if a model was exported lying flat on the Z-axis).
3. Close the viewer with **`✕`** or **`Esc`** to immediately release WebGL buffers and GPU memory.
> *Note: In **Vanilla IDE+GRP Mode**, the `👁️` button is disabled (`IDE+DAT Only`) since `.dff` and `.txd` files are not loaded.*

### 5.3 Bulk Cloning Parameters (`Copy Data From Ped`)
1. Configure one "template" ped row (or pick an existing well-configured ped in your sheet).
2. Select all target peds using checkboxes (`Shift+Click` or `Select All`).
3. Click **`📋 Copy Data From Ped`**, type or pick the source ped's name/ID, toggle any of the **12 parameter checkboxes** (`Pedtype`, `Stat`, `AnimGroup`, `Cars Mask`, `Flags`, `AnimFile`, `Radio 1`, `Radio 2`, `Audio Type`, `Voice 1`, `Voice 2`, `Ped Group`), and click **Apply to Selected Peds**.

### 5.4 Rule-Based Smart Randomization, Group Counts & Blacklist Constraints (`(x)`)
Select one or more rows and click **`🎲 Randomize Selected`**:
- **🧠 Smart Context Sync (Recommended):** Automatically detects gender (from existing Pedtype, AnimGroup, Stat, Voice, or model filename—or overridden via **Force Gender: Female / Male**) and generates lore-consistent combinations:
  - **Strict Role Exclusivity:** `GANG1`–`GANG10` peds are assigned *only* to their matching gang group (`Gang 1`–`Gang 10`); `DEALER` peds are assigned *only* to `Dealers`; `COP`, `MEDIC`, `FIREMAN`, `SPECIAL`, and `MISSION1–8` are automatically set to `NONE` (since GTA SA spawns them outside `pedgrp.dat`).
  - **City-Balanced Spawns (`(SF)` & `(VEGAS)` Priority):** Uses canonical GTA SA `pedgrp.dat` group names and gives San Fierro `(SF)` and Las Venturas `(VEGAS)` group variants higher priority so custom peds populate all three cities evenly without diluting role accuracy.
  - **Custom Group Count per Ped (`Min` / `Max`):** Set the minimum and maximum number of population groups assigned individually to each randomized civilian ped (default `1` to `3`).
- **🚫 Randomization Blacklist (`(x)`):** Pick any parameter from the blacklist dropdown and click chips to ban unwanted values (for example, vehicle-only `Cars Mask` presets like `0FFF` / `0000` are blacklisted by default so peds always spawn walking on sidewalks too).

### 5.5 Sequential Auto-Rename (`14000+`) — ⚠️ Check Free IDs First!
*(Available in Add-On Mode)*
1. Click **`🔢 Auto-Rename (14000+)`**.
2. Enter your verified starting ID from `free_ids.txt` (default `14000`) and choose **Numeric Only** (`14000`, `14001`, ... — recommended to prevent replacing vanilla models), **Prefix + ID** (`ped14000`), or **Sequential IDs Only**.
3. Click **Apply Auto-Rename**. All `.dff`, `.txd`, `.ide`, and `pedgrp.dat` references stay synchronized automatically upon export.

### 5.6 Automated Clash Detection & Auto-Fix (`Check & Fix Clashes`)
Rows with invalid or conflicting IDs/names are highlighted in red and counted in the **`Clashes`** pill:
- **In Add-On Mode:** Flags duplicate IDs, IDs `< 313`, duplicate `ModelName` or `TxdName`, and any collision with all **270+ vanilla GTA SA ped names**.
- **In Vanilla IDE+GRP Mode:** Preserves valid original IDs (`0–299`), vanilla names, and shared `TxdName`s across multiple peds, flagging only duplicate IDs, duplicate `ModelName`s, or empty fields.
- Click **`🛡️ Check & Fix Clashes`** to sanitize invalid characters (enforcing GTA SA's 24-character limit) and resolve every conflict automatically.

### 5.7 Exporting & Installing into Modloader (With Original `pedgrp.dat`)
1. Click **`💾 Export Output`**.
2. **In Add-On Mode:**
   - *(Recommended)* Attach your game's original **`data/pedgrp.dat`** so the tool merges your add-on peds into existing street groups without wiping out vanilla pedestrians.
   - Review the **Merged `pedgrp.dat` Peak Limit Check** box—if any group exceeds `23` peds, update `Pedgrp peds limit` in `fastman92limitAdjuster_GTASA.ini` accordingly.
   - Click **Choose Folder & Export** to write `data/peds_addons.ide`, `data/pedgrp.dat`, `data/loader.txt`, and all synced `models/gta3/*.dff` + `*.txd` files into your Modloader folder (e.g., `modloader/Ped Addons/`).
3. **In Vanilla IDE+GRP Mode:**
   - Review the `pedgrp.dat` peak limit check, then click **Choose Folder & Export** to save your updated `ped.ide` and `pedgrp.dat` directly into your target folder (or download both files if running locally via `file://`).

---

## 6. Troubleshooting & FAQ

- **Why does the game crash or fail to spawn peds when a group has more than 23 peds?**
  Default GTA San Andreas has a hardcoded array limit of **23 pedestrians per line** in `pedgrp.dat`. Enable `Apply pedgrp.dat limit patch = 1` in `fastman92limitAdjuster_GTASA.ini` and raise `Pedgrp peds limit` above the peak number shown in the studio's **`Max Peds/Grp`** badge.
- **Why is the `👁️` 3D Viewer button disabled?**
  Either the row has no `.dff` file attached, or you are currently in **`VANILLA IDE+GRP MODE`** (where only `ped.ide` and `pedgrp.dat` text files are loaded without 3D meshes).
- **A 3D model looks flat/lying down or shows "Untextured Mesh Fallback" in the 3D Viewer.**
  Click **`🔄 Rotate 90°`** in the viewer toolbar to stand Z-up models upright. Some custom DFF/TXD files use non-standard RenderWare material plugins or console texture compression that the browser WebGL parser skips via safe fallback—these models still work normally inside GTA San Andreas.
- **Why does Export download a `.zip` instead of writing directly to a folder?**
  Direct folder writing (`File System Access API`) is only permitted by browsers on secure `https://` origins (such as the live GitHub Pages app) or `localhost`. When opened directly from disk via `file://`, the studio automatically packages your files into a ready-to-extract `.zip` archive (or downloads `ped.ide` and `pedgrp.dat` in Vanilla Mode).

---

## 📋 Changelog

### v1.1.0
- **New Vanilla Ped Editor Mode (`IDE + GRP`):** You can now load your game's original `ped.ide` and `pedgrp.dat` files directly to edit vanilla pedestrian stats, voices, animations, and spawn groups without needing 3D model files—and export both updated files straight back to your folder.
- **Much Faster, Lag-Free Editing on Large Lists:** Rebuilt the spreadsheet table so it only draws the rows currently visible on your screen. Scrolling, searching, and selecting hundreds or thousands of peds at once (`Select All` or `Shift + Click`) now happens instantly without freezing your browser.
- **23-Ped Group Limit Warning (`Max Peds/Grp`):** Added a live counter in the toolbar and a pre-export check that warns you whenever any `pedgrp.dat` group goes over GTA San Andreas's default limit of 23 peds, telling you the exact limit number to set in Fastman92 Limit Adjuster.
- **Official `pedgrp.dat` Group Names (Including San Fierro & Las Venturas):** Updated all built-in spawn groups to match GTA San Andreas's real `pedgrp.dat` labels—including `(SF)` and `(VEGAS)` city variants, `Office workers`, `Shopkeepers`, `Husbands`, and `Wives`—and removed invalid non-`pedgrp` groups.
- **Smarter Randomization Controls:**
  - Added **Min** and **Max** settings so you can choose how many spawn groups each randomized ped gets.
  - Gangs (`GANG1`–`GANG10`) and `DEALER` peds are now strictly locked to their own matching group, while Cops, Medics, Firemen, and Special/Mission peds are automatically set to `NONE`.
  - Added weighted priority for San Fierro `(SF)` and Las Venturas `(VEGAS)` groups so randomized peds populate all cities evenly without breaking role accuracy.
- **Quick Search Shortcut (`Ctrl+F` / `Cmd+F`):** Pressing `Ctrl+F` (or `Cmd+F` on Mac) now jumps straight to the studio's search bar.
- **Faster & More Reliable Exporting:** Improved file exporting so large packs copy much faster and continue running smoothly even if you switch to another browser tab.