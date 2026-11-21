# 🏗️ Architecture & Code Breakdown (`v1.2.0`)

This document provides a complete module-by-module engineering breakdown of **GTA SA Ped Add-On Studio (`v1.2.0`)**. The entire application is architected as a 100% offline-capable, zero-build single-file web application (`index.html`) combining a virtualized data grid, a dual-mode GTA SA configuration engine (`Add-On` & `Vanilla IDE+GRP`), a rule-based context synthesizer, a pure-JS ZIP packager, and a built-in binary RenderWare (`DFF`/`TXD`) parser with a native WebGL 3D viewer.

---

## 📑 Table of Contents

1. [High-Level Architecture & Performance Design (`R01`–`R13`)](#1-high-level-architecture--performance-design-r01r13)
2. [HTML Structure, CSS Layout & Smart Tooltip System](#2-html-structure-css-layout--smart-tooltip-system)
3. [Section 1: GTA SA Reference Dictionaries & Group Normalization](#3-section-1-gta-sa-reference-dictionaries--group-normalization)
4. [Section 2: Background-Safe Scheduler & Parallel I/O Runner](#4-section-2-background-safe-scheduler--parallel-io-runner)
5. [Section 3: Centralized `PedStore` State & Query Caching](#5-section-3-centralized-pedstore-state--query-caching)
6. [Section 4: Toast Notifications & Modal Controllers](#6-section-4-toast-notifications--modal-controllers)
7. [Section 5: `.ide` / `pedgrp.dat` Parsers & Vanilla Mode Loader](#7-section-5-ide--pedgrpdat-parsers--vanilla-mode-loader)
8. [Section 6: Unified Dual-API Directory Tree Processor](#8-section-6-unified-dual-api-directory-tree-processor)
9. [Section 7: Collision Checker, Sanitizer, Auto-Rename & Shift-Up Delete](#9-section-7-collision-checker-sanitizer-auto-rename--shift-up-delete)
10. [Section 8: Lazy Dropdown Hydration & Virtualized Viewport Renderer](#10-section-8-lazy-dropdown-hydration--virtualized-viewport-renderer)
11. [Section 9: Floating Ped Group Popover & Delegated Table Events](#11-section-9-floating-ped-group-popover--delegated-table-events)
12. [Section 10: Bulk Parameter Cloning & Smart Context Sync Randomizer](#12-section-10-bulk-parameter-cloning--smart-context-sync-randomizer)
13. [Section 11: Built-In Binary RenderWare Parser & Native WebGL 3D Viewer](#13-section-11-built-in-binary-renderware-parser--native-webgl-3d-viewer)
14. [Section 12: Dual-Mode Export Engine & Zero-Dependency ZIP Writer](#14-section-12-dual-mode-export-engine--zero-dependency-zip-writer)
15. [Section 13: Event Bindings, Global Shortcuts & Debounced Search](#15-section-13-event-bindings-global-shortcuts--debounced-search)

---

## 1. High-Level Architecture & Performance Design (`R01`–`R13`)

The spreadsheet and data layer are engineered around 13 core architectural optimizations (`R01`–`R13`) to support multi-thousand-row datasets without UI stalls:

| ID | Optimization / Pattern | Implementation Summary |
| :--- | :--- | :--- |
| **`R01`** | **Viewport Virtualization** | `viewport` renders only visible rows (`ROW_HEIGHT = 31px`) plus an 8-row overscan buffer (`OVERSCAN = 8`), bounded by top/bottom `.virtual-spacer` rows. |
| **`R02`** | **Lazy Dropdown Hydration** | Resting select cells render as lightweight `<div class="cell-lazy-select">` placeholders (`0` `<option>` nodes in DOM) and hydrate into native `<select>` elements only on `mousedown` or `focusin`. |
| **`R03`** | **Zero-Re-Render Select All** | `chkSelectAll` mutates `store.selected` flags in memory and updates only mounted DOM rows via `viewport.syncVisibleSelectionOnly()`. |
| **`R04`** | **Zero-Re-Render Shift+Click Range** | `Shift+Click` range selection updates `store.setFilteredRangeSelection()` and syncs visible checkboxes without rebuilding table HTML. |
| **`R05`** | **Background-Safe Cooperative Yielding** | `yieldFrame()` uses `MessageChannel` macrotasks rather than `requestAnimationFrame`, preventing browser tab throttling from freezing large batch jobs. |
| **`R06`** | **Pre-Computed Search Indices & Debounce** | Each ped stores a cached lowercase `_searchIndex` string; `#searchInput` debounces queries by `120ms` (`0ms` on clear or `Enter`). |
| **`R07`** | **$O(1)$ UID Maps** | `PedStore` maintains `rowByUid` and `indexByUid` `Map`s for constant-time row lookups and index resolution during edits and deletions. |
| **`R08`** | **Incremental UI Counters** | `selectedCount` and `pairedCount` are tracked incrementally on mutation rather than scanning `rows` on every UI refresh. |
| **`R09`** | **Chunked CPU Tasks & Parallel File I/O** | `runChunkedTask()` budgets CPU work to `20ms` per slice; `runParallelTask()` runs up to `12` concurrent file copy/hash workers during export. |
| **`R10`** | **Event Delegation** | All cell inputs, lazy dropdown triggers, checkboxes, popovers, 3D viewer triggers, and delete buttons are handled via delegated listeners on `#pedTbody`. |
| **`R11`** | **Scroll-Stable Zebra Striping & CSS Containment** | `.sheet-wrapper` uses `contain: strict` and `overflow-anchor: none`; rows use explicit `.row-even`/`.row-odd` classes (`visIdx % 2`) so zebra striping never flickers during virtual scrolling. |
| **`R12`** | **Versioned Query & Clash Caches** | `filterVersion` and `clashVersion` invalidate `_cachedFilteredRows` and `_cachedClashSet` only when relevant fields or filters change. |
| **`R13`** | **Streaming Binary File Copy** | `copyBinaryHandle()` streams `.dff`/`.txd` files via `file.stream().pipeTo(writable)` with an `arrayBuffer()` fallback. |

---

## 2. HTML Structure, CSS Layout & Smart Tooltip System

- **Design Tokens (`:root`):** Dark slate theme (`--bg-app: #0f1319`, `--bg-panel: #161c24`, `--bg-header: #1c2430`) with fixed `--row-height: 31px` to guarantee deterministic virtual scroll math.
- **Sticky Table Columns:**
  - `sticky-col-0` (`left: 0`, width `34px`): Selection checkbox.
  - `sticky-col-1` (`left: 34px`, width `44px`): Sticky 1-based Row Index (`#`, `.col-row-num`).
  - `sticky-col-2` (`left: 78px`, width `72px`): Numeric Ped ID.
  - `sticky-col-3` (`left: 150px`, min-width `195px`): `ModelName`, `👁️` 3D viewer trigger, and `✕` shift-up delete button.
- **Dual-Mode & Bulk Toolbar UI Components:**
  - `#activeModeBadge` (`.mode-pill-badge` / `.vanilla`): Displays `ADD-ON MODE` or `VANILLA IDE+GRP MODE`.
  - `#btnDeleteSelected`: Bulk deletion button enabled whenever `selectedCount > 0`.
  - `#emptyState` (`.mode-grid` / `.mode-card`): Side-by-side launcher cards for **Mode 1: Add-On Ped Studio** and **Mode 2: Vanilla Ped Editor (IDE + GRP)**.
  - `#statMaxGrpPill` & `#pedGrpLimitReport` (`.limit-warn-box`): Real-time and pre-export `pedgrp.dat` peak ped count indicators.
- **Smart Hover Tooltip Engine (`#smartTooltip` / `data-tip`):** Replaces external CDN import maps and native browser `title` delays with a global floating `#smartTooltip` element positioned dynamically with viewport edge clamping (`mouseover`/`mouseout`/`mousedown`/`scroll`) and automatic `title` $\rightarrow$ `data-tip` migration via `MutationObserver`.

---

## 3. Section 1: GTA SA Reference Dictionaries & Group Normalization

Defines all static lookup tables and canonical normalization rules:
- **Core Enumerations:** `PED_TYPES` (27 types), `PED_STATS` (36 behavior stats), `ANIM_GROUPS` (21 walking styles), `ANIM_FILES` (7 IFP archives), `RADIO_STATIONS` (`0`–`10`), `AUDIO_TYPES` (7 audio classes), and `CAR_MASK_PRESETS` (43 vehicle/pedestrian spawn bitmasks).
- **Voice Banks (`SPC_GA_VOICES`, `GANG_VOICE_GROUPS`, `EMG_COP_VOICES`, `EMG_MEDIC_VOICES`):** 209 general speech banks plus gang and emergency banks. `ACTIVE_SPC_GA_VOICES` filters out `(unused)` banks, and `isFemaleVoice()` classifies female speech banks via regex (`/^VOICE_GEN_([A-Z]{1,3}F[YO]|WFOPJ)|^VOICE_GFD_.../`).
- **Canonical `pedgrp.dat` Normalization (`DEFAULT_PED_GROUPS`, `normalizePedGroupName()`):**
  - Lists official GTA SA `pedgrp.dat` group names including `(SF)` and `(VEGAS)` city variants (`POPCYCLE_GROUP_WORKERS (SF)`, `POPCYCLE_GROUP_CASUAL_RICH (VEGAS)`, etc.), `Gang 1`–`Gang 10` descriptive headers, `Dealers`, `Shopkeepers`, `Office workers`, `Husbands`, and `Wives`.
  - `CANONICAL_GROUP_LOOKUP` and `normalizePedGroupName()` normalize legacy/short aliases (e.g., `gang1`, `dealers`, `popcycle_group_desertfolk`) to their canonical strings and strip invalid non-`pedgrp.dat` entries (`NON_PEDGRP_INVALID_GROUPS`: `cops`, `medic`, `fireman`, `special`).
- **Vanilla Model Guard (`VANILLA_PED_NAMES`):** A `Set` of 270+ base-game pedestrian model names used by Add-On Mode to prevent accidental vanilla model replacement.

---

## 4. Section 2: Background-Safe Scheduler & Parallel I/O Runner

- **`yieldFrame()`:** Uses a `MessageChannel` (`port1`/`port2`) to yield control back to the browser event loop in `0ms` without suffering from background-tab throttling (`requestAnimationFrame` pauses when a tab is hidden).
- **`runChunkedTask(items, workerFn, { frameBudgetMs = 20, onProgress })`:** Executes synchronous or async loops in `20ms` time slices, updating the progress bar only when `!document.hidden`.
- **`runParallelTask(items, workerFn, { concurrency = 12, onProgress })`:** Spawns a fixed pool of `12` async workers pulling from a shared index counter (`nextIdx++`) to parallelize file reads/writes during export.
- **Model Factory & Search Indexer:** `createDefaultPed(overrides)` initializes a complete ped record with a unique `ped_<n>` UID and calls `rebuildRowSearchIndex(ped)` to pre-compute its lowercase `_searchIndex`.

---

## 5. Section 3: Centralized `PedStore` State & Query Caching

`class PedStore` (instantiated as `store`, with `const state = store` for backward compatibility) manages all application state:
- **Mode State:** `editorMode` (`'addon'` | `'vanilla'`), `vanillaIdeFilename`, `rawVanillaIdeText`, `rawVanillaPedGrpText`, and `vanillaPedGrpText`.
- **$O(1)$ Indexing & Incremental Metrics:**
  - `rebuildIndicesAndCounters()` populates `rowByUid`, `indexByUid`, `selectedCount`, and `pairedCount`.
  - `deleteRowShiftUp(uid)` removes a single row in $O(1)$ lookup time and shifts subsequent IDs upward in **Add-On Mode** (while preserving original IDs in **Vanilla Mode**).
  - `deleteSelectedRowsShiftUp()` removes all selected rows in bulk, cleans up their `modelName` references from `pedGroups` across remaining rows, shifts subsequent Add-On IDs (`>= 313`) sequentially upward in **Add-On Mode**, resets `selectedCount = 0`, and rebuilds indices.
- **Versioned Caches:**
  - `getFilteredRows()` keys its cache on `${q}\x00${grp}\x00${fStat}\x00${this.filterVersion}`, returning the direct `this.rows` reference when no filters are active.
  - `getClashSet()` caches conflicting row UIDs keyed by `this.clashVersion`. In **Add-On Mode**, checks duplicate IDs, `id < 313`, duplicate/empty `modelName` & `txdName`, and `VANILLA_PED_NAMES`. In **Vanilla Mode**, permits `id < 313`, vanilla names, and shared `txdName`s across peds.
- **`analyzeMergedPedGrp(pedGrpText)`:** Parses any `pedgrp.dat` string and returns `{ maxCount, maxGroups, activeLines }` to detect whether any population line exceeds GTA SA's default 23-ped limit.

---

## 6. Section 4: Toast Notifications & Modal Controllers

- `toast(msg, type)`: Appends auto-dismissing (`4200ms`) toast banners (`info`, `success`, `warning`, `error`) to `#toastContainer`.
- `openModal(id)` / `closeModal(id)`: Toggles `.open` on `.modal-backdrop` overlays, wired automatically via `[data-close]` attributes.

---

## 7. Section 5: `.ide` / `pedgrp.dat` Parsers & Vanilla Mode Loader

- **`parseIdeText(text, sourceTag)`:** Parses `peds ... end` blocks (as well as headerless numeric `.ide` lines), stripping `#` comments and splitting comma/whitespace-delimited 14-column ped definitions.
- **`parsePedGrpText(text, modelToGroupsMap, extraNamesByGroup)`:** Reads `pedgrp.dat` lines, normalizes `# GroupName` headers via `normalizePedGroupName()`, registers custom groups into `store.pedGroupsMaster`, and maps each ped name to its assigned groups.
- **`setEditorMode(mode)` & Vanilla File Loader (`openVanillaModeModal`):**
  - Switches `#activeModeBadge` between `ADD-ON MODE` and `VANILLA IDE+GRP MODE` and hides Add-On-only controls (`#btnAutoRename`, `#statPairedPill`, `#filterFileStatus`) when editing vanilla files.
  - `#btnConfirmVanillaImport` reads both user-selected `ped.ide` and `pedgrp.dat` files, preserves their raw text templates (`rawVanillaIdeText`, `rawVanillaPedGrpText`) for clean round-trip export, and populates the grid.

---

## 8. Section 6: Unified Dual-API Directory Tree Processor

Normalizes directory ingestion across both secure (`https://` / `localhost`) and local (`file://`) contexts:
- **`createEmptyDirTree()` & `routeFileToDirTree()`:** Categorizes files into `dataRootFiles`, `dataNumericFolders` (`data/1`, `data/2`...), `gta3RootFiles`, `gta3NumericFolders` (`models/gta3/1`...), and loose `addFiles` (`Add/`).
- **`importViaDirectoryPicker()` vs. `#fallbackDirInput`:** Uses `window.showDirectoryPicker({ mode: 'read' })` when available, falling back seamlessly to `<input type="file" webkitdirectory>` on `file://`.
- **`processVirtualDirectoryTree(tree)`:** Pairs `.dff` and `.txd` handles via `buildPairMapFromHandles()`, links `.ide` rows to their 3D assets, merges `pedgrp.dat` memberships, converts loose `Add/` skins into sequential `14000+` numeric add-ons, and triggers a clash summary toast.

---

## 9. Section 7: Collision Checker, Sanitizer, Auto-Rename & Shift-Up Delete

- **`sanitizeGtaAssetName(rawName, fallback)`:** Strips non-alphanumeric characters (allowing only `[a-zA-Z0-9_-]`) and enforces GTA SA's 24-character limit.
- **`fixClashesOnly()`:** Runs chunked clash resolution across `store.rows`, assigning new sequential IDs and sanitized names while respecting mode-specific rules (preserving vanilla IDs and shared TXDs in Vanilla Mode).
- **`applyAutoRename(startId, mode)`:** Sequentially renumbers IDs from `startId` and updates `modelName`/`txdName` in `numeric` (`14000`), `prefix` (`ped14000`), or `id_only` mode.
- **`deleteSingleRowWithFeedback(uid)` & `deleteSelectedRowsWithFeedback()`:** Deletes either a single row or all checked rows via `store.deleteSelectedRowsShiftUp()`, updates stats and selection UI, and displays a toast summary.

---

## 10. Section 8: Lazy Dropdown Hydration & Virtualized Viewport Renderer

- **Template Caching & Lazy Hydration (`CACHED_SELECT_TEMPLATES`):**
  - Pre-builds static `<option>` HTML strings and lowercase lookup `Map`s once at startup.
  - `buildLazySelectCellHtml()` renders resting cells as `<div class="cell-lazy-select">` elements.
  - `hydrateLazySelectCell()` converts a cell into a native `<select>` on click/focus (calling `selectEl.showPicker()` when clicked), and `dehydrateSelectCell()` reverts it back to a `<div>` on `focusout`.
- **Virtual Scroll Engine (`viewport`):**
  - Calculates `start` and `end` row indices from `sheetWrapper.scrollTop` and `sheetWrapper.clientHeight` (`ROW_HEIGHT = 31`, `OVERSCAN = 8`), rendering each row's sticky 1-based `#` index alongside the checkbox, ID, and ModelName.
  - Wraps mounted rows between two `<tr class="virtual-spacer">` rows and coalesces scroll events via `requestAnimationFrame` (`scheduleRender()`).
  - `syncVisibleSelectionOnly()` and `updateSelectionUI()` update checkbox states, `.selected` classes, and `#btnDeleteSelected` availability without re-rendering table HTML.
- **`updateStatsBar()`:** Updates `Total Peds`, `Paired DFF+TXD`, `Clashes`, and `Max Peds/Grp` (highlighting in amber when `maxCount > 23`).

---

## 11. Section 9: Floating Ped Group Popover & Delegated Table Events

- **Floating Multi-Select Popover (`openPedGroupPopover`):** Displays a single shared checkbox menu (`#pedGroupPopover`) positioned dynamically above or below the clicked cell so it never overflows the viewport, updating `row.pedGroups` and `_searchIndex` live.
- **Delegated `pedTbody` Listeners:** Handles `mousedown`/`focusin`/`focusout` (lazy dropdown lifecycle), `change` (row checkbox, numeric ID, sanitized `modelName`/`txdName` with `>20` char warning), and `click` (`Shift+Click` range selection, popover toggle, 3D viewer launch, and row deletion).

---

## 12. Section 10: Bulk Parameter Cloning & Smart Context Sync Randomizer

- **Bulk Cloning (`#btnConfirmCopyPed`):** Resolves the reference ped by `modelName` or `id` and copies any combination of 12 checked fields onto selected rows via `runChunkedTask()`.
- **Smart Context Sync Engine (`#btnConfirmRandomizePed`):**
  - **Gender Detection (`detectPedGender`):** Infers `'F'` or `'M'` from `pedtype`, `animGroup`, `stat`, voice bank prefixes, or filename hints unless overridden by `#forceGenderMode`.
  - **Context Rule Generators:** `getContextPedtypePool()`, `getContextStatPool()`, `getContextAnimGroupPool()`, `getContextAnimFilePool()`, `getContextFlagsPool()`, and `getContextAudioAndVoices()` ensure physiological, behavioral, and vocal consistency.
  - **Exclusive Group Rules & Weighted City Distribution:**
    - `getExclusivePedGroupRule()` locks `GANG1`–`GANG10` exclusively to their matching gang group, locks `DEALER` to `Dealers`, and assigns `NONE` to `COP`, `MEDIC`, `FIREMAN`, `SPECIAL`, and `MISSION1–8`.
    - `getCityPriorityWeight()` assigns a `3.5x` selection weight to `(SF)` and `(VEGAS)` variants, and `weightedPickUniqueFromPool()` fills the user-configured `[#randPedGrpMin, #randPedGrpMax]` quota from the ped's primary role pool before considering general groups.
  - **3-Step Pool Resolver (`resolveContextPool`):** Builds the context pool $\rightarrow$ filters out blacklisted values (`store.randomBlacklist`) $\rightarrow$ falls back to the base context pool if all candidates were blacklisted so role integrity never breaks.

---

## 13. Section 11: Built-In Binary RenderWare Parser & Native WebGL 3D Viewer

- **Zero-Dependency RenderWare 3.6 Binary Constants (`RW`):** Defines RenderWare chunk identifiers (`STRUCT`, `STRING`, `EXTENSION`, `TEXTURE`, `MATERIAL`, `MATLIST`, `FRAMELIST`, `GEOMETRY`, `CLUMP`, `ATOMIC`, `RASTER`, `TEXDICTIONARY`, `EXTRA_VERT_COL`) and raster pixel format flags (`FORMAT_1555`, `FORMAT_565`, `FORMAT_4444`, `FORMAT_8888`, `FORMAT_888`, `FORMAT_555`, `PAL4`, `PAL8`, `MIPMAP`).
- **Built-In Binary TXD Parser & Software Texture Decoders (`parseRwTxdBuffer`):**
  - Parses `TEXDICTIONARY` and `RASTER` chunks directly from `ArrayBuffer` (supporting both PC Direct3D 8/9 `D3DFORMAT` FourCC codes and legacy RenderWare raster flags).
  - Decodes **`DXT1`**, **`DXT3`**, and **`DXT5`** block-compressed textures (`decodeDXT1`, `decodeDXT3`, `decodeDXT5`), **4-bit and 8-bit paletted** textures (`decodePaletted`), and uncompressed **`RGBA8888` / `BGRA8888` / `RGB888` / `ARGB1555` / `RGB565` / `ARGB4444`** formats (`decodeUncompressed`) into RGBA8 `Uint8Array` buffers, detecting alpha transparency (`hasAlpha`) for alpha-cutout rendering.
- **Built-In Binary DFF Clump Parser (`parseRwDffBuffer`):**
  - Parses RenderWare `CLUMP` $\rightarrow$ `FRAMELIST` (bone/frame hierarchy & parent transforms), `GEOMETRY` (positions, normals, UVs, day `prelit` vertex colors, triangle indices, per-split material IDs, and `EXTRA_VERT_COL` night vertex colors inside `EXTENSION`), `MATLIST` (diffuse colors, surface properties, and primary texture names), and `ATOMIC` links.
  - Evaluates hierarchical bone matrices (`computeWorldMatrices`) and falls back gracefully to a procedural studio mannequin (`buildFallbackMannequin`) if a file is missing or malformed.
- **Native WebGL Renderer & Viewer Lifecycle (`createPedViewerInstance`):**
  - Compiles custom GLSL vertex/fragment shaders supporting dual vertex color attributes (`aColorDay` & `aColorNight` blended via `uNightMix`), directional + ambient lighting (`uLightMix`), alpha cutout (`discard` when `uAlphaTest > 0.5 && tex.a < 0.25`), and `LINES` wireframe overlay.
  - Uses built-in 4x4 matrix math (`mat4Perspective`, `mat4LookAt`, `mat4RotateX/Y/Z`, etc.) and a `ResizeObserver`, providing spherical **Orbit** (Left-Drag), **Pan** (Right-Drag or `Shift`+Drag), **Zoom** (Wheel), **`🎯 Reset Cam`**, independent 90° **3-Axis Rotation** (`🔄 Rot X 90°`, `🔄 Rot Y 90°`, `🔄 Rot Z 90°`, `⟲ Reset Rot`), **`☀️ Day Light` / `🌙 Night Light`** toggle, and **`🕸️ Wireframe`** toggle.
  - Releases all WebGL buffers, textures, shader programs, and `WEBGL_lose_context` resources immediately in `destroy()`.

---

## 14. Section 12: Dual-Mode Export Engine & Zero-Dependency ZIP Writer

- **Configuration Generators:**
  - `generatePedsAddonsIde()`: Serializes all rows into a standard CRLF `peds ... end` block.
  - `generateVanillaIde()`: Rebuilds the loaded `ped.ide` template line-by-line, replacing edited peds in-place, omitting deleted peds, appending newly added rows before `end`, and preserving all original comments and unrelated sections.
  - `generatePedGrpDat()`: Supports three modes: (1) rebuilding from `rawVanillaPedGrpText` in Vanilla Mode, (2) merging add-on peds into an attached original `pedgrp.dat` without duplicating names, or (3) generating a standalone add-on `pedgrp.dat`.
- **Pre-Export Limit Check (`updateExportLimitWarningUI`):** Runs `store.analyzeMergedPedGrp()` before export and displays a recommended `Pedgrp peds per group` value under `[PED STREAMING]` in `fastman92limitAdjuster_GTASA.ini` whenever any group exceeds `23` peds.
- **Output Delivery:**
  - **Vanilla Mode:** Writes the modified `ped.ide` and `pedgrp.dat` directly into the chosen folder via `showDirectoryPicker` (or triggers dual text-file downloads on `file://`).
  - **Add-On Mode:** Writes `data/peds_addons.ide`, `data/pedgrp.dat`, `data/loader.txt`, and `12`-way parallel streamed `models/gta3/*.dff` + `*.txd` files directly on `https://`, or packs everything into a zero-dependency STORE-mode `.zip` archive via `CRC32_TABLE` and `buildZipBlob()` on `file://`.

---

## 15. Section 13: Event Bindings, Global Shortcuts & Debounced Search

- Connects all toolbar and modal action buttons (`#btnImportFolder`, `#btnAddRow`, `#btnDeleteSelected`, `#btnAutoRename`, `#btnCheckClash`, `#btnOpenVanillaModal`, `#btnClearAll`, `#btnExportFolder`).
- Intercepts `Ctrl+F` / `Cmd+F` (`keydown` on `window`) to focus and select `#searchInput`.
- Debounces `#searchInput` filtering by `120ms` (`applyFilterAndResetScroll`), resetting `scrollTop = 0` and forcing an immediate `viewport.render(true)`.