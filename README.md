# 🧍 GTA SA Ped Add-On Studio

[![Launch Web App](https://img.shields.io/badge/🚀_Launch_Web_App-Live_Demo-2ea44f?style=for-the-badge)](https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](./LICENSE)

> **A zero-installation, browser-based bulk spreadsheet workbench & interactive 3D DFF/TXD viewer for creating, merging, validating, and exporting non-replacing Add-On Pedestrians for Grand Theft Auto: San Andreas.**

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
   - [Scenario 3: Re-Editing Previously Exported Data](#scenario-3-re-editing-previously-exported-data)
5. [Step-by-Step Usage Guide & Real-World Case Studies](#5-step-by-step-usage-guide--real-world-case-studies)
   - [5.1 Workspace Overview & Multi-Row Selection (`Select All`, `Shift+Click`, Filters)](#51-workspace-overview--multi-row-selection-select-all-shiftclick-filters)
   - [5.2 Checking Ped Roles Visually with the 3D Ped Viewer](#52-checking-ped-roles-visually-with-the-3d-ped-viewer)
   - [5.3 Bulk Cloning Parameters (`Copy Data From Ped`)](#53-bulk-cloning-parameters-copy-data-from-ped)
   - [5.4 Rule-Based Smart Randomization & Blacklist Constraints (`(x)`)](#54-rule-based-smart-randomization--blacklist-constraints-x)
   - [5.5 Sequential Auto-Rename (`14000+`) — ⚠️ Check Free IDs First!](#55-sequential-auto-rename-14000--%EF%B8%8F-check-free-ids-first)
   - [5.6 Automated Clash Detection & Auto-Fix (`Check & Fix Clashes`)](#56-automated-clash-detection--auto-fix-check--fix-clashes)
   - [5.7 Exporting & Installing into Modloader (With Original `pedgrp.dat`)](#57-exporting--installing-into-modloader-with-original-pedgrpdat)
6. [Troubleshooting & FAQ](#6-troubleshooting--faq)

---

## 1. Introduction & Background

### Why This Tool Exists
Adding brand-new pedestrians to **GTA San Andreas** without overwriting existing characters has historically been a tedious, error-prone manual process. Modders had to:
- Manually type 14-column entries inside `.ide` files (`ID, ModelName, TxdName, Pedtype, Stat, AnimGroup, CarsMask, Flags, AnimFile, Radio1, Radio2, AudioType, Voice1, Voice2`).
- Manually edit `pedgrp.dat` population lines without accidentally erasing vanilla street spawns.
- Rename hundreds of `.dff` and `.txd` files one by one to match new numeric IDs.
- Guess whether a downloaded skin was male, female, elderly, gangster, or emergency personnel without a quick visual preview—often leading to immersion-breaking bugs (such as an elderly woman speaking with a male gangster voice).

### What Is GTA SA Ped Add-On Studio?
**GTA SA Ped Add-On Studio** is a lightweight, single-file (`index.html`) web workbench that combines an **Excel-style bulk spreadsheet editor**, an **automated file-synchronization engine**, and a **hardware-accelerated 3D WebGL `.dff`/`.txd` model viewer** directly inside your browser—requiring **zero installation, zero Python/Node dependencies, and zero backend servers**.

### Goals & Vision
- **Beginner-Friendly Accessibility:** Empower everyday players and novice modders to build massive, crash-free add-on pedestrian packs without memorizing cryptic GTA SA internal flags or voice bank IDs.
- **Zero Vanilla Replacement:** Automatically convert loose skin files (even those originally named `bmyst.dff` or `fam1.dff`) into safe, non-conflicting numeric add-on IDs (`14000+`).
- **Lore-Accurate World Immersion:** Ensure every added pedestrian spawns in logical neighborhoods, drives appropriate vehicle classes, walks with fitting animations, and speaks with gender- and role-matched voices.

### Bulk Edit & Bulk Processing Capabilities
| Capability | What It Does in Bulk |
| :--- | :--- |
| **Multi-Pack Folder Import** | Parses `data/`, numbered merge folders (`data/1`, `data/2`...), `models/gta3/`, and raw `Add/` skins in one click. |
| **Range & Bulk Selection** | Supports header `Select All`, `Shift + Click` range selection across dozens of rows, and live multi-criteria filtering. |
| **Bulk Parameter Cloning** | Copies up to **12 independent parameter toggles** from any reference ped onto hundreds of selected rows simultaneously. |
| **Rule-Based Smart Randomization** | Generates context-aware combinations (Gender, Pedtype, Stat, AnimGroup, Flags, Voices, Ped Groups) while obeying custom `(x)` blacklists. |
| **Synchronized Auto-Rename** | Renumbers IDs sequentially (`14000+`) and automatically renames all paired `.dff`, `.txd`, `.ide`, and `pedgrp.dat` entries to match. |
| **One-Click Clash Auto-Fix** | Scans for ID `< 313`, duplicate IDs, duplicate names, and all **270+ vanilla GTA SA ped names**, fixing every conflict automatically. |
| **Modloader-Ready Export** | Merges your new peds into your game's original `pedgrp.dat`, generates `peds_addons.ide` + `loader.txt`, and packages all synced `.dff`/`.txd` files. |

### How to Access the Tool

**🌐 Live Web App (Recommended):** **[https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/](https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/)**

**GTA SA Ped Add-On Studio** runs in a **dual-mode browser architecture** (recommended browsers: **Google Chrome**, **Microsoft Edge**, or **Brave**):

| Mode | Access Method | How It Works |
| :--- | :--- | :--- |
| **MODE 1: Online / Hosted (`https://`)** | **[Launch Live Web App](https://redrafgg-afk.github.io/GTA-SA-Ped-Add-On-Studio/)** | Uses the modern File System Access API to read and write files directly into your chosen folder without needing to extract a ZIP archive. |
| **MODE 2: Offline / Local (`file://`)** | Download `index.html` from [Releases](../../releases) | Double-click `index.html` in Chrome/Edge. Imports via standard folder picker and exports a ready-to-extract `.zip` archive. |

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│                        HOW TO LAUNCH THE APPLICATION                         │
├───────────────────────────────────────┬──────────────────────────────────────┤
│  MODE 1: Offline / Local              │  MODE 2: Online / Hosted             │
│  ─────────────────────────────────    │  ──────────────────────────────────  │
│  1. Download `index.html` to your PC. │  1. Open the GitHub Pages URL        │
│  2. Double-click `index.html` to open │                                      │
│     it directly in Chrome / Edge.     │  2. Uses File System Access API to   │
│  3. Imports via standard folder       │     read & write directly into your  │
│     picker and exports a ready-to-    │     selected folder without ZIPping. │
│     extract `.zip` archive.           │                                      │
└───────────────────────────────────────┴──────────────────────────────────────┘
```
> **Note on 3D Preview:** All spreadsheet editing, importing, randomization, and exporting work **100% offline**. An active internet connection is only required when clicking the **`👁️` 3D Viewer** button so the browser can fetch `three.js` and `dff-loader` from the CDN.

---
🛡️ Security & VirusTotal Verification

This tool is a pure client-side static HTML/JavaScript file (`index.html` or 'GTA-SA-Ped-Add-On-Studio.html') with zero backend servers, zero tracking, and zero external data uploads all file reading and ZIP packaging happen locally inside your browser.

VirusTotal Scan Report (0/60+ Clean): [View Live VirusTotal Analysis](https://www.virustotal.com/gui/file/30359703f395851f53b22730e8f043db53448c2860b2aa8a5593eb43dd157f87)
SHA-256 Checksum: 30359703f395851f53b22730e8f043db53448c2860b2aa8a5593eb43dd157f87

## 2. Open Source & Code Documentation

This project is released as **open-source software** and engineered so any developer or modder can inspect, customize, or fork it effortlessly:

- **Single-File Architecture (`index.html`):** All styling, UI layout, parsers, rule engines, WebGL 3D previewer, and pure-JS ZIP writer live inside one cleanly structured HTML file—no `npm install`, Webpack, or build pipeline required.
- **11 Self-Contained Functional Sections:** Every part of the JavaScript codebase is divided into numbered, clearly commented sections (`§1` through `§11`):

| Section | Module Name | Core Responsibility |
| :--- | :--- | :--- |
| **`§1`** | Reference Dictionaries | GTA SA `PED_TYPES`, `PED_STATS`, `ANIM_GROUPS`, `CAR_MASK_PRESETS`, `SPC_GA_VOICES` (Banks 001–209), & `VANILLA_PED_NAMES` |
| **`§2–§3`** | State & UI Feedback | Reactive `state.rows` store, loading progress bar, toast notifications, & modal lifecycle |
| **`§4–§5`** | Parsers & Virtual Directory | `.ide` / `pedgrp.dat` parsers and unified folder scanner for both `file://` and `https://` |
| **`§6`** | Clash & Rename Engine | Asset sanitizer (24-char limit), vanilla collision detector, auto-rename, & shift-up row deletion |
| **`§7`** | Spreadsheet & Popover UI | Cached `<select>` template rendering, `Shift+Click` selection, and multi-select `pedgrp.dat` popover |
| **`§8`** | Bulk Copy & Smart Sync | 12-toggle parameter cloner, gender detector, 3-step `Smart Context Sync` engine, & `(x)` blacklist |
| **`§9`** | 3D Ped Viewer | Lazy-loaded Three.js + `DFFLoader`/`TXDLoader` with auto-upright orientation & zero-leak GPU disposal |
| **`§10–§11`** | Export & Event Bindings | Vanilla `pedgrp.dat` merger, pure-JS CRC32 STORE ZIP builder, direct folder writer, & toolbar bindings |

📘 **Want to modify or fork the code?** Read the companion **[Complete Developer Code Breakdown (`CODE_BREAKDOWN.md`)](./CODE_BREAKDOWN.md)**—formatted as a print-ready technical whitepaper (ready to export to **PDF** via browser Print or VS Code Markdown PDF) with complete function signatures, data schemas, and extension recipes.

---

## 3. Mandatory Game Prerequisites (Before You Start)

Before adding new pedestrians to GTA San Andreas, you **must** set up two essential modding tools in your game directory. Without these, your game may either overwrite existing models or crash due to reaching vanilla engine limits.

### Step A: Find Empty IDs with Free ID List Generator (`GENEFREE`)

- **Download:** [Free ID List Generator on LibertyCity.net](https://libertycity.net/files/gta-san-andreas/160432-free-id-list-generator.html)
- **What It Is & Why Use It:**
  This script is suitable for mod creators who add new objects, weapons, pedestrians, etc. to the game. It creates a `.txt` file (in the game directory) listing all **empty (available) IDs** currently unused in your game.
  > **Why not Fastman92's ID dump?** Fastman92 created a similar mod, but it was **incompatible with SA-MP** because the principle of operation of these two mods is different. **Free ID List Generator** works cleanly alongside your setup.

#### How to Generate Your Empty ID List:
1. Install the **Free ID List Generator** script into your `GTA San Andreas\` directory (follow its included archive instructions, typically placing it in your `CLEO\` or root game folder).
2. Launch **GTA San Andreas** and load into gameplay.
3. To activate the script, type the cheat code directly on your keyboard while in-game:
   ```text
   GENEFREE
   ```
4. Exit or minimize the game and check the available IDs `.txt` file generated inside your `GTA San Andreas\` game directory.
5. Note down a large, uninterrupted block of empty IDs—for example:
   ```text
   Available Free ID Range Example:
   14000 - 14999  (1,000 free slots)
   16500 - 17999  (1,500 free slots)
   ```
   *(You will enter this starting ID when using **Auto-Rename** inside GTA SA Ped Add-On Studio!)*

---

### Step B: Expand Ped, Killable ID & Group Limits in Fastman92 Limit Adjuster

- **Download:** [Fastman92 Limit Adjuster on LibertyCity.net](https://libertycity.net/files/gta-san-andreas/223963-fastman92-limit-adjuster.html)
- **Why It Is Mandatory:**
  By default, GTA San Andreas restricts ped definitions, valid killable model IDs, and the number of peds allowed per population group. Adding custom peds on IDs **`14000+`** and merging them into `pedgrp.dat` will crash the game unless you configure **three critical limits** inside **Fastman92 Limit Adjuster**.

#### How to Configure `fastman92limitAdjuster_GTASA.ini`:
1. Open **`fastman92limitAdjuster_GTASA.ini`** inside your `GTA San Andreas\` root directory with Notepad (or any text editor).
2. Locate each of the three sections below, **remove the leading semicolon (`;`)** on the target lines to activate them, and update their values as shown:

#### 1. Raise Killable Model ID Ceiling (`Count of killable model IDs`)
By default, GTA SA sets this to `800` (meaning only IDs `0–799` are valid in `CDarkel::RegisteredKills`). Because **GTA SA Ped Add-On Studio** assigns new peds to IDs **`14000+`**, you **must** raise this limit above your highest Ped ID (e.g., `20000`) so shooting or killing add-on peds in-game does not crash the engine:

```ini
; Count of killable model IDs (800)
; In game there's an array CDarkel::RegisteredKills and it stores the number of registered kills for model IDs.
; Model IDs that may be killed are vehicles/peds.
; Default value of 800, means that 0-799 is valid ID for ped/vehicle.
;
; As such, this value will affect the max possible ID for ped/vehicle.
Count of killable model IDs = 20000
```

#### 2. Increase Max Peds Per Group (`[PED STREAMING]`)
Vanilla GTA SA only allows **21 peds per group** in `data/pedgrp.dat`. When merging vanilla `pedgrp.dat` with dozens of add-on peds, groups like `POPCYCLE_GROUP_CASUAL_AVERAGE` or `POPCYCLE_GROUP_SHOPPERS` will exceed 21 entries. Raising this to `100` prevents group overflow crashes and ensures all merged peds spawn properly:

```ini
[PED STREAMING]
; Pedgrp peds per group (21)
Pedgrp peds per group = 100
```

3. Save (`Ctrl + S`) and close `fastman92limitAdjuster_GTASA.ini`.

---

4. Install Open Limit Adjuster]
Adjust some limits automatically that needed for ped addon
([#install-open-limit-adjuster](https://www.mixmods.com.br/2022/10/open-limit-adjuster/))

## 4. Importing Files & Folder Structure Scenarios

When you click **`📂 Import Folder`**, select your **project root folder**. The tool automatically detects which of the following three folder structures you are using (and can even combine them!):

### Scenario 1: Converting Loose Skins (`Add/` Folder — Anti-Replace)
Use this scenario when you downloaded raw `.dff` and `.txd` ped skins from mod sites—even if the author originally named them after vanilla characters (like `bmyst.dff` or `fam1.dff`) or custom names (`cyber_girl.dff`).

```text
My_New_Skins_Project/               <-- Select THIS root folder on Import
└── Add/
    ├── bmyst.dff                   <-- Vanilla name! Tool converts to 14000.dff
    ├── bmyst.txd                   <-- Vanilla name! Tool converts to 14000.txd
    ├── cyber_girl.dff              <-- Custom name!  Tool converts to 14001.dff
    └── cyber_girl.txd              <-- Custom name!  Tool converts to 14001.txd
```
- **What Happens Automatically:** Every paired `.dff` + `.txd` inside `Add/` is assigned a safe, sequential numeric ID and numeric filename (`14000`, `14001`, ...), guaranteeing **zero replacement** of vanilla GTA SA skins.

---

### Scenario 2: Merging Multiple Add-On Packs (`data/1`, `data/2`, ...)
Use this scenario when you have **two or more separate add-on ped packs** that use overlapping IDs (e.g., both Pack 1 and Pack 2 contain `14000.dff` and `14001.dff`) and want to merge them into one unified pack.

```text
My_Merged_PedPack/                  <-- Select THIS root folder on Import
├── data/
│   ├── 1/                          <-- Pack 1 config files
│   │   ├── peds_addons.ide
│   │   └── pedgrp.dat
│   └── 2/                          <-- Pack 2 config files (can have overlapping IDs!)
│       ├── peds_addons.ide
│       └── pedgrp.dat
└── models/
    └── gta3/
        ├── 1/                      <-- Pack 1 3D models
        │   ├── 14000.dff
        │   └── 14000.txd
        └── 2/                      <-- Pack 2 3D models
            ├── 14000.dff
            └── 14000.txd
```
- **What Happens Automatically:** The tool loads `1/`, `2/`, `3/`... in numerical order, keeps each row linked to its exact folder's `.dff`/`.txd` handle (so Pack 2's `14000.dff` is never overwritten by Pack 1's `14000.dff` in memory), highlights the duplicate IDs in **red**, and lets you renumber everything cleanly with **`🔢 Auto-Rename (14000+)`**.

---

### Scenario 3: Re-Editing Previously Exported Data
Use this scenario when you want to open an add-on ped pack you previously exported from this tool to inspect models, tweak voices, add more skins via `Add/`, or update `pedgrp.dat` groups.

```text
GTA_SA_Ped_Addons_Output/           <-- Select THIS root folder on Import
├── data/
│   ├── peds_addons.ide             <-- Loaded into spreadsheet rows
│   ├── pedgrp.dat                  <-- Restores multi-select Ped Groups per ped
│   └── loader.txt
└── models/
    └── gta3/
        ├── 14000.dff               <-- Paired for 3D Viewer & re-export
        ├── 14000.txd
        ├── 14001.dff
        └── 14001.txd
```
> **Pro Tip (Hybrid Append Mode):** You can place new skins inside `Add/` alongside an existing `data/` + `models/gta3/` folder! On import, the tool loads all existing peds first, then appends the `Add/` skins starting immediately after your highest existing ID.

---

## 5. Step-by-Step Usage Guide & Real-World Case Studies

### 5.1 Workspace Overview & Multi-Row Selection (`Select All`, `Shift+Click`, Filters)

![GTA SA Ped Add-On Studio Spreadsheet Workspace](https://i.imgur.com/x9Jot5S.png)

- **Select All Visible Rows:** Click the top-left checkbox (`☑`) in the table header to select or deselect all currently visible/filtered rows at once.
- **Range Selection (`Shift + Click`):** Click the checkbox on your first target row, hold **`Shift`**, and click the checkbox on your last target row—every row in between is toggled automatically.
- **Filter Before Selecting:** Type a keyword (e.g., `CIVFEMALE`, `GANG1`, `1401`) in the search bar or filter by **Ped Group** / **File Status**, then click `Select All` to act only on that subset.

> **💼 Case Study — Bulk Selecting 30 Newly Imported Skins:**
> You just imported 50 peds (`14000`–`14049`), where rows `14020`–`14049` came from `Add/`. Instead of clicking 30 checkboxes manually, click the checkbox on `14020`, hold **`Shift`**, and click the checkbox on `14049`. All 30 rows highlight in blue immediately, enabling **`📋 Copy Data From Ped (30)`** and **`🎲 Randomize Selected (30)`**.

---

### 5.2 Checking Ped Roles Visually with the 3D Ped Viewer

Never guess what a skin looks like from a generic filename like `14007.dff`! Click the **`👁️`** icon next to any `ModelName` to open the **3D Ped Preview** window.

![3D Ped Viewer Preview Window](https://i.imgur.com/UGMzUDK.png)

- **Controls:** **Left-Drag** to orbit, **Scroll** to zoom, **Right-Drag** to pan.
- **Fix Lying-Down Models (`🔄 Rotate 90°`):** Many GTA SA `.dff` models are exported in Z-Up coordinates and appear lying flat on their back. The viewer auto-rotates most models upright, and you can click **`🔄 Rotate 90°`** anytime to adjust orientation.
- **Zero Memory Leak:** Closing the viewer (`✕` or `Esc`) immediately disposes all WebGL geometries, textures, and GPU buffers so you can inspect hundreds of models smoothly without slowing down your browser.

> **💼 Case Study — Identifying Ped Roles Before Configuring:**
> You click **`👁️`** on `14007` and see an older man wearing a construction hard hat and reflective vest. Because you verified his appearance visually, you immediately set his `Stat / Behavior` to `STAT_SENSIBLE_GUY` or `STAT_STREET_GUY` and assign his `Ped Group` to `POPCYCLE_GROUP_WORKERS` so he spawns naturally around construction sites and industrial zones in Los Santos, San Fierro, and Las Venturas.

---

### 5.3 Bulk Cloning Parameters (`Copy Data From Ped`)

When you have a group of peds that belong to the same faction, job, or gang, configure **one** ped as your template and clone its settings onto all the others.

1. Configure your **Reference Ped** (e.g., `14010`) with the exact `Pedtype`, `Stat`, `AnimGroup`, `Cars Mask`, `Flags`, `AnimFile`, `Radio 1/2`, `Audio Type`, `Voice 1/2`, and `Ped Group` you want.
2. Check the boxes next to all target peds (e.g., `14011`–`14018`).
3. Click **`📋 Copy Data From Ped`** in the top toolbar.
4. Type the reference ped's `ModelName` or `ID` (`14010`), choose which of the **12 independent parameters** you want to copy (`All` / `None` or individual checkboxes), and click **`Apply to Selected Peds`**.

> **💼 Case Study — Setting Up a 10-Member Grove Street Gang Expansion:**
> You imported 10 green-hooded gang skins (`14010`–`14019`). You set `14010` to `Pedtype: GANG2`, `Stat: STAT_GANG2`, `AnimGroup: gang2`, `Audio Type: PED_TYPE_GANG`, `Voice 1: VOICE_GNG_FAM1`, `Voice 2: VOICE_GNG_FAM2`, and `Ped Group: GANG2`. Next, you select `14011`–`14019`, click **`📋 Copy Data From Ped`**, enter `14010`, and apply. All 9 remaining gang members inherit the exact Grove Street configuration in one click!

---

### 5.4 Rule-Based Smart Randomization & Blacklist Constraints (`(x)`)

When adding **dozens or hundreds of everyday civilians**, manually assigning unique voices, stats, car masks, and spawn groups to each row takes too long—yet pure random generation would give women male voices or make grandmothers sprint like athletes.

**`🎲 Randomize Selected`** solves this with **Smart Context Sync** + **Parameter Blacklists**:

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                    SMART CONTEXT SYNC 3-STEP PIPELINE                   │
├─────────────────────────────────────────────────────────────────────────┤
│  1. CONTEXT EVALUATION                                                  │
│     • Checks Force Gender (Auto / 👩 Female / 👨 Male) & locked fields.  │
│     • Builds a lore-accurate pool (e.g., Female Suit -> busywoman anim, │
│       wealthy/business female voice, POPCYCLE_GROUP_BUSINESS / SHOPPERS)│
│                                    ▼                                    │
│  2. BLACKLIST FILTERING `(x)`                                           │
│     • Removes every value marked `(x)` in your Randomization Blacklist  │
│       (e.g., `0FFF` non-walking car masks, `NONE` group, or `PROSTITUTE`)│
│                                    ▼                                    │
│  3. CONTEXT INTEGRITY OVERRIDE                                          │
│     • If you accidentally blacklisted ALL valid choices in a narrow     │
│       pool, the engine safely overrides the blacklist for that field so │
│       a female ped NEVER receives a male voice!                         │
└─────────────────────────────────────────────────────────────────────────┘
```

#### How to Use the Randomization Blacklist (`(x)`):
1. Select your target rows and click **`🎲 Randomize Selected`**.
2. Keep **`🧠 Smart Context Sync`** checked (recommended).
3. Choose **Force Gender**:
   - `Auto-Detect`: Reads existing `Pedtype`, `Stat`, `AnimGroup`, `Voice`, or filename hints.
   - `👩 Force Female (All Selected)`: Guarantees 100% female roles, animations, voices, and groups.
   - `👨 Force Male (All Selected)`: Guarantees 100% male roles, animations, voices, and groups.
4. Under **🚫 Randomization Blacklist**, pick a parameter from the dropdown (e.g., `Parameter: Pedtype` or `Parameter: Cars Mask`) and click any chip to toggle **`(x)`** (red strikethrough = excluded from randomization).
   - *Default safety pre-blacklist:* Non-walking vehicle masks (`0FFF`, `000F`, `0100`, `0800`, `0020`, `0040`, `0600`, `0000`) and `NONE` Ped Group are pre-blacklisted so every randomized ped actually walks on sidewalks and spawns in the world!

> **💼 Case Study — Randomizing 25 Female Civilian Skins Without Streetwalkers:**
> You visually inspected 25 female skins in the 3D Viewer and selected them all. You open **`🎲 Randomize Selected`**, set **Force Gender** to **`👩 Force Female (All Selected)`**, open the Blacklist dropdown for `Parameter: Pedtype`, and click **`PROSTITUTE`** so it turns red (`PROSTITUTE (x)`). When you click **`Randomize Selected Peds`**, all 25 female peds receive varied, realistic female voices, walking styles, vehicle masks, and 2–4 city spawn groups (`SHOPPERS`, `TOURISTS`, `CASUAL_AVERAGE`, `BUSINESS`)—with zero unwanted roles!

---

### 5.5 Sequential Auto-Rename (`14000+`) — ⚠️ Check Free IDs First!

> ⚠️ **CRITICAL WARNING — CHECK AVAILABLE FREE IDs FIRST!**
> Before running **Auto-Rename**, always open the `.txt` file generated by **[Free ID List Generator (`GENEFREE`)](#step-a-find-empty-ids-with-free-id-list-generator-genefree)** in your `GTA San Andreas\` directory!
> If IDs `14000–14050` are already occupied by map mods, custom weapons, or vehicles in your game, assigning `14000` will cause an ID conflict crash. Enter a **verified empty starting ID** from your `GENEFREE` report (e.g., `15200` or `17000`), and ensure `Count of killable model IDs = 20000` is set in `fastman92limitAdjuster_GTASA.ini`.

1. Click **`🔢 Auto-Rename (14000+)`** in the top toolbar.
2. Enter your verified **Starting ID** from `GENEFREE`.
3. Choose a **Naming Mode**:
   - **`Numeric Only (14000, 14001, ...)` *(Recommended)*:** Sets both `ModelName` and `TxdName` to match the numeric ID, making vanilla model replacement mathematically impossible.
   - **`Prefix + ID (ped14000, ped14001, ...)`:** Useful if you want a custom prefix (`ped14000.dff`).
   - **`Sequential IDs Only (Keep Names If No Clash)`:** Keeps existing custom names unless they collide with a vanilla ped name or duplicate another row.

> **💼 Case Study — Fixing Overlapping IDs After Merging Two Modpacks:**
> You merged Pack 1 (`data/1`, IDs `14000–14019`) and Pack 2 (`data/2`, also IDs `14000–14014`). You check your `GENEFREE` text file and see that `16500–16999` is completely empty in your GTA SA install. You click **`🔢 Auto-Rename (14000+)`**, type `16500`, select `Numeric Only`, and click **`Apply Auto-Rename`**. All 35 peds are renumbered to `16500–16534`, and on export their `.dff` and `.txd` files are automatically renamed to `16500.dff`–`16534.dff`!

---

### 5.6 Automated Clash Detection & Auto-Fix (`Check & Fix Clashes`)

The spreadsheet continuously monitors every row for four fatal GTA San Andreas conflicts:
1. **Reserved Engine IDs (`ID < 313`):** IDs `0–312` belong to CJ, vanilla peds, and special story actors.
2. **Duplicate IDs:** Two or more rows sharing the same numeric ID.
3. **Duplicate Asset Names:** Two or more rows sharing the same `ModelName` or `TxdName`.
4. **Vanilla Ped Name Collisions:** Any `ModelName` or `TxdName` matching one of the **270+ built-in vanilla ped names** (`male01`, `bmyst`, `fam1`, `ballas1`, `lapd1`, `sweet`, `ryder`, etc.).

Whenever a clash exists, the offending row glows **red** and the top-right pill displays **`Clashes: X`**. Click **`🛡️ Check & Fix Clashes`** to automatically reassign clean, non-conflicting numeric IDs and asset names to every clashing row while leaving valid rows untouched.

> **💼 Case Study — Preventing Accidental Vanilla Gang Replacement:**
> You manually added a new row and typed `ballas1` as the `ModelName`. The row immediately turns **red** because `ballas1` is a vanilla GTA SA gang member. Clicking **`🛡️ Check & Fix Clashes`** detects the vanilla collision and converts that row's ID and `ModelName`/`TxdName` to an unused numeric ID (`14000+`), protecting the original Ballas ped from being overwritten.

---

### 5.7 Exporting & Installing into Modloader (With Original `pedgrp.dat`)

Once `Clashes: 0` is confirmed and your peds are configured, export your final modpack:

1. Click **`💾 Export Output`** in the top toolbar.
2. **Attach Your Game's Original `pedgrp.dat` *(Highly Recommended)*:**
   - In the export modal, click **`Choose File`** under **`Original GTA SA data/pedgrp.dat (Optional — Recommended)`**.
   - Browse to your game folder and select:
     ```text
     GTA San Andreas\data\pedgrp.dat
     ```
   - **Why is this so important?** GTA San Andreas loads only the highest-priority `pedgrp.dat` in Modloader. If you export without attaching your original `pedgrp.dat`, the exported file will *only* contain your add-on peds—causing vanilla pedestrians to stop spawning! Attaching your original `GTA San Andreas\data\pedgrp.dat` instructs the tool to **preserve every vanilla line** and **append** your new add-on peds onto the end of their assigned population groups *(make sure `Pedgrp peds per group = 100` is enabled in `fastman92limitAdjuster_GTASA.ini`)*.
3. Click **`Start Export`**:
   - **Online (`https://`):** Choose or create an output folder (e.g., `AddOn_Peds_Pack`).
   - **Offline (`file://`):** Wait for the progress bar to finish and save **`GTA_SA_Ped_Addons_Output.zip`**, then extract it into a folder named `AddOn_Peds_Pack`.
4. **Install into Modloader (Drag & Drop):**
   Simply copy or move your exported folder directly into your game's `modloader\` folder:

```text
GTA San Andreas\
└── modloader\
    └── AddOn_Peds_Pack\                    <-- Drop your exported folder right here!
        ├── data\
        │   ├── peds_addons.ide             <-- All your new add-on ped definitions
        │   ├── pedgrp.dat                  <-- Vanilla groups + your new add-on peds merged
        │   └── loader.txt                  <-- Contains: IDE DATA\MAPS\peds_addons.ide
        └── models\
            └── gta3\
                ├── 14000.dff               <-- Automatically renamed & synced
                ├── 14000.txd               <-- Automatically renamed & synced
                ├── 14001.dff
                └── 14001.txd
```

That's it! Modloader automatically reads `loader.txt` to mount `peds_addons.ide`, loads your merged `pedgrp.dat`, and streams all `.dff`/`.txd` models from `models\gta3\` into the game.

---

## 6. Troubleshooting & FAQ

| Symptom | Cause | Solution |
| :--- | :--- | :--- |
| **Game crashes on the loading screen** | `IDE:peds section` limit in Fastman92 Limit Adjuster is still `278` (or commented out with `;`), `Pedgrp peds per group` exceeded `21`, OR an ID collides with another mod. | Open `fastman92limitAdjuster_GTASA.ini`, set `IDE:peds section = 1000` and `Pedgrp peds per group = 100`, and verify your IDs against `GENEFREE`. |
| **Game crashes when shooting or killing an add-on ped** | `Count of killable model IDs` is still at the vanilla `800` limit (`0–799`), so IDs `14000+` overflow `CDarkel::RegisteredKills`. | Open `fastman92limitAdjuster_GTASA.ini` and set `Count of killable model IDs = 20000`. |
| **New peds don't appear on the streets** | Vanilla `pedgrp.dat` wasn't attached on export, or peds have `NONE` assigned in `Ped Group`. | Ensure every ped has at least one `Ped Group` assigned, and attach `GTA San Andreas\data\pedgrp.dat` in the Export modal before exporting. |
| **Ped has white/blank skin in-game** | `TxdName` doesn't match the `.txd` filename, or a `.dff` was imported without its matching `.txd`. | Check the `Files` column (`DFF+TXD` badge) and run **`🔢 Auto-Rename`** so `ModelName`, `TxdName`, `.dff`, and `.txd` match 100%. |
| **3D Viewer shows "Untextured Mesh Fallback" or proxy** | The `.txd` uses specialized PS2/Xbox compression or custom RW material plugins unsupported by the browser parser. | Normal for certain custom skins—your `.dff` and `.txd` are still exported byte-for-byte intact and will render properly inside GTA San Andreas. |
| **3D Viewer shows "Offline / CDN Error"** | Your PC has no internet connection when opening the 3D Viewer. | Connect to the internet briefly so the browser can cache `three.js` and `dff-loader` from `esm.sh` / `jsDelivr`. |
