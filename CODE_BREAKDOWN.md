# 🛠️ GTA SA Ped Add-On Studio — Complete Code Architecture & Developer Breakdown

## 1. High-Level System Architecture

**GTA SA Ped Add-On Studio** is intentionally engineered as a **zero-build, single-file application (`index.html`)**. By avoiding bundlers (`Webpack`, `Vite`) and external runtime frameworks (`React`, `Vue`), any modder or developer can double-click `index.html` locally via `file://` or host it statically on GitHub Pages (`https://`) with zero compilation steps.

```text
┌───────────────────────────────────────────────────────────────────────────────────────┐
│                         INDEX.HTML — SINGLE-FILE ARCHITECTURE                         │
├───────────────────────────────────────────────────────────────────────────────────────┤
│  1. <head> & <style>                                                                  │
│     • CSS Custom Properties (--bg-app, --accent, --danger)                            │
│     • Sticky Spreadsheet Grid (.sticky-col-0/1/2), Modals, Floating Popover, 3D Host  │
│     • <script type="importmap"> (Three.js v0.170.0 & dff-loader v1.0.1 ESM mappings)  │
├───────────────────────────────────────────────────────────────────────────────────────┤
│  2. <body> DOM Shell                                                                  │
│     • Hidden <input id="fallbackDirInput" webkitdirectory> (file:// compatibility)    │
│     • Top Toolbar (#btnImportFolder .. #btnExportFolder) & Filter Subbar              │
│     • Main Table (<table id="pedTable"> -> <tbody id="pedTbody">) + Empty State Card  │
│     • 5 Modals/Overlays: Copy Ped, Randomize + Blacklist, Export, Auto-Rename, 3D Win │
├───────────────────────────────────────────────────────────────────────────────────────┤
│  3. <script> Modular Engine (Divided into 11 Functional Sections)                     │
│     §1  Reference Dictionaries & Vanilla Ped Name Set                                 │
│     §2  Reactive Application State & Dual-Mode File Handle Abstraction                │
│     §3  Toast Notification & Modal Lifecycle Helpers                                  │
│     §4  GTA SA `.ide` & `pedgrp.dat` Text Parsers                                     │
│     §5  Unified Virtual Directory Tree Builder (file:// & https://)                   │
│     §6  Collision Checker, 24-Char Sanitizer, Auto-Rename & Shift-Up Delete           │
│     §7  Cached Select Template Renderer, Shift+Click UX & Multi-Group Popover         │
│     §8  Bulk Parameter Cloner (12 Toggles) & Smart Context Sync Randomizer            │
│     §9  Lazy-Loaded WebGL 3D Viewer (Auto-Upright, Fallbacks & Zero GPU Memory Leak)  │
│     §10 Vanilla `pedgrp.dat` Merger, Direct Folder Writer & Pure-JS CRC32 ZIP Engine  │
│     §11 DOM Event Listeners & Filter Bindings                                         │
└───────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Core Data Structures & State Model (`§2`)

All runtime data is centralized inside a single global `state` object and an array of normalized `PedRow` objects created by `createDefaultPed(overrides)`.

### 2.1 Global `state` Object
| Property | Type | Purpose |
| :--- | :--- | :--- |
| `state.rows` | `Array<PedRow>` | Master list of all pedestrian entries currently loaded in the spreadsheet. |
| `state.pedGroupsMaster` | `Array<string>` | Master vocabulary of `pedgrp.dat` group names (`DEFAULT_PED_GROUPS` + any custom groups discovered on import). |
| `state.rawPedGrpExtras` | `Map<string, Set<string>>` | Preserves non-addon pedestrian names found inside imported `pedgrp.dat` files so they are never lost on re-export. |
| `state.lastCheckedIndex` | `number \| null` | Tracks the last clicked row index (`data-vis-idx`) to enable Excel-style `Shift + Click` range selection. |
| `state.activeViewer` | `ViewerInstance \| null` | Holds the currently active WebGL 3D viewer controller so `closePedViewer()` can guarantee GPU disposal. |
| `state.vanillaPedGrpText` | `string` | Stores raw text of the user's original `GTA San Andreas\data\pedgrp.dat` when attached in the Export modal. |
| `state.randomBlacklist` | `Record<string, Set<string>>` | Stores excluded `(x)` values per parameter key (`pedtype`, `stat`, `animGroup`, `carsMask`, `flags`, `animFile`, `radio`, `audioType`, `voice`, `pedGroup`). |

### 2.2 Normalized `PedRow` Schema
Every row in `state.rows` conforms to this exact schema:

```javascript
{
  uid: 'ped_1',                                  // Unique internal DOM tracking ID (nextUid())
  id: 14000,                                     // Numeric GTA SA model ID (must be >= 313)
  modelName: '14000',                            // .dff basename (max 24 alphanumeric/_/- chars)
  txdName: '14000',                              // .txd basename (max 24 alphanumeric/_/- chars)
  pedtype: 'CIVMALE',                            // IDE col 4 (CIVMALE, CIVFEMALE, GANG1..10, COP, etc.)
  stat: 'STAT_STREET_GUY',                       // IDE col 5 (Behavior/AI stat)
  animGroup: 'man',                              // IDE col 6 (Walking/running animation set)
  carsMask: '120C',                              // IDE col 7 (Hex bitmask controlling vehicle classes)
  flags: '0',                                    // IDE col 8 ('0' = normal, '1' = drug/special flag)
  animFile: 'man',                               // IDE col 9 (IFP animation archive reference)
  radio1: '1',                                   // IDE col 10 (Primary radio station index 0–10)
  radio2: '4',                                   // IDE col 11 (Secondary radio station index 0–10)
  audioType: 'PED_TYPE_GEN',                     // IDE col 12 (PED_TYPE_GEN, PED_TYPE_GANG, PED_TYPE_EMG, etc.)
  voice1: 'VOICE_GEN_MALE01',                    // IDE col 13 (Primary speech bank)
  voice2: 'VOICE_GEN_MALE01',                    // IDE col 14 (Secondary speech bank)
  pedGroups: ['POPCYCLE_GROUP_CASUAL_AVERAGE'],  // Array of pedgrp.dat groups this ped spawns in
  dffHandle: VirtualFileHandle | null,           // Unified wrapper with .name and async .getFile()
  txdHandle: VirtualFileHandle | null,           // Unified wrapper with .name and async .getFile()
  sourceTag: 'Manual',                           // Provenance label ('Manual', 'data/', 'data/1', 'Add/...')
  selected: false                                // Checkbox selection state for bulk actions
}
```

### 2.3 Dual-Mode Virtual File Abstraction
To support both **Modern File System Access API (`https://`)** and **Classic Folder Input (`file://`)** without duplicating import/export logic, every `.dff` and `.txd` reference exposes a uniform interface:
- On `https://`: Uses native `FileSystemFileHandle` (which already implements `.name` and `async getFile()`).
- On `file://`: `wrapNativeFile(fileObj)` wraps a standard browser `File` object into `{ name: fileObj.name, getFile: async () => fileObj }`.

---

## 3. Section-by-Section Code Breakdown (`§1` – `§11`)

### `§1` GTA SA Reference Dictionaries & Dropdown Options
- **Constants Defined:**
  - `PED_TYPES` (27 valid GTA SA ped types)
  - `PED_STATS` (36 behavior stats)
  - `ANIM_GROUPS` (21 movement animation groups)
  - `ANIM_FILES` (7 `.ifp` references)
  - `RADIO_STATIONS` (Stations `0` through `10`)
  - `AUDIO_TYPES` (7 audio category identifiers)
  - `CAR_MASK_PRESETS` (41 human-labeled hexadecimal vehicle/pedestrian bitmasks)
  - `SPC_GA_VOICES` (Complete 209-bank `Bank_001` to `Bank_209` `VOICE_GEN_*` registry)
  - `GANG_VOICE_GROUPS` (8 gang speech clusters: Ballas, Families, Vagos, Aztecas, Triads, Da Nang, Mafia, Rifa)
  - `EMG_COP_VOICES` & `EMG_MEDIC_VOICES` (18 emergency service voice banks)
  - `ALL_VOICE_OPTIONS`, `ACTIVE_SPC_GA_VOICES`, `ACTIVE_ALL_VOICES` (Filters out `(unused)` banks for randomization)
  - `isFemaleVoice(v)`: Regex classifier matching female `VOICE_GEN_*` and `VOICE_GFD_*` bank naming conventions.
  - `DEFAULT_PED_GROUPS`: 34 standard `POPCYCLE_GROUP_*`, `GANG1..10`, `DEALERS`, `MEDIC`, `FIREMAN`, `COPS`, and `SPECIAL` groups.
  - `VANILLA_PED_NAMES`: `Set<string>` containing **270+ vanilla GTA SA pedestrian model names** to block accidental model replacement.

---

### `§2` & `§3` State Management, Loading Overlay & Modals
- `showLoading(title, sub, pct)` / `updateLoading(sub, pct)` / `hideLoading()`: Controls `#loadingOverlay` and `#loadingProgressBar` during heavy bulk operations (`> 30` rows) or ZIP archiving.
- `nextFrame()`: Yields to `requestAnimationFrame` + `setTimeout(0)` so the browser repaints progress bars smoothly during large loops.
- `toast(msg, type)`: Spawns auto-dismissing (`4.2s`) notifications (`info`, `success`, `warning`, `error`) inside `#toastContainer`.
- `openModal(id)` / `closeModal(id)`: Toggles `.open` on modal backdrops and wires all `[data-close]` buttons.

---

### `§4` `.ide` & `pedgrp.dat` Parsers
- **`parseIdeText(text, sourceTag)`**:
  - Strips `#` comments, enters parsing mode on `peds` (or lines starting with `\d+,`), and exits on `end`.
  - Splits tokens by commas **and** whitespace (handling inconsistent spacing found in community `.ide` files) and maps tokens `0..13` into `createDefaultPed()`.
- **`parsePedGrpText(text, modelToGroupsMap, extraNamesByGroup)`**:
  - Parses lines formatted as `ped1, ped2, ped3 # GROUP_NAME`.
  - Registers unknown group names dynamically into `state.pedGroupsMaster`.
  - Populates a reverse lookup map (`modelName.toLowerCase() -> Array<groupName>`) so each ped row displays all groups it belongs to.

---

### `§5` Unified Directory Tree Processor
- **`createEmptyDirTree(rootName)` & `routeFileToDirTree(tree, relParts, handle)`**:
  - Classifies incoming files into five buckets regardless of browser API:
    1. `dataRootFiles` (`data/*.ide`, `data/pedgrp.dat`)
    2. `dataNumericFolders` (`data/1/*`, `data/2/*` — Merge Mode)
    3. `gta3RootFiles` (`models/gta3/*.dff`, `*.txd`)
    4. `gta3NumericFolders` (`models/gta3/1/*`, `models/gta3/2/*` — Merge Mode)
    5. `addFiles` (`Add/*.dff`, `Add/*.txd` — Loose Skin Conversion)
- **`processVirtualDirectoryTree(tree)`**:
  - Pairs `.dff` and `.txd` handles via `buildPairMapFromHandles()`.
  - Processes `data/` first, then iterates `dataNumericFolders` sorted numerically (`Number(a) - Number(b)`).
  - Converts every `.dff`/`.txd` pair in `Add/` into sequential numeric IDs (`maxId + 1`) and numeric `modelName`/`txdName` to prevent vanilla skin replacement.
  - Falls back to loading loose files from `models/gta3/` if both `data/` and `Add/` are empty.
- **`importViaDirectoryPicker()` & `triggerImportFolder()`**:
  - Attempts `window.showDirectoryPicker({ mode: 'read' })` in secure contexts (`https://` / `localhost`), walking up to depth `4` only inside `data`, `models/gta3`, and `Add` for high performance.
  - Falls back seamlessly to `#fallbackDirInput` (`webkitdirectory`) when opened locally via `file://`.

---

### `§6` Collision Checker, Sanitizer, Auto-Rename & Shift-Up Delete
- **`sanitizeGtaAssetName(rawName, fallback)`**: Strips non-alphanumeric characters (preserving `_` and `-`) and truncates to **24 characters** (GTA SA's `IMG` archive filename limit).
- **`getClashSet()`**: Returns a `Set<string>` of `row.uid` values that violate any of the following rules:
  - `row.id < 313` (reserved vanilla engine/ped IDs)
  - Duplicate `row.id`
  - Empty or duplicate `modelName` / `txdName`
  - `modelName` or `txdName` exists in `VANILLA_PED_NAMES`
- **`fixClashesOnly()`**: Iterates through `state.rows`, preserves valid rows, and reassigns sequential IDs (`>= 14000`) and sanitized numeric names only to clashing rows.
- **`applyAutoRename(startId, mode)`**: Renumbers all rows sequentially from `startId` using `'numeric'` (`14000`), `'prefix'` (`ped14000`), or `'id_only'` mode.
- **`deleteRowWithShiftUp(uid)`**: Removes the target row and decrements subsequent sequential IDs (and matching numeric `modelName`/`txdName`) so ID ranges stay contiguous.

---

### `§7` Spreadsheet Renderer & Multi-Select Popover
- **`createSelectTemplateCache()` & `CACHED_SELECT_TEMPLATES`**:
  - Pre-builds `<option>` HTML strings once at startup for all 9 dropdown columns (`pedtype`, `stat`, `animGroup`, `carsMask`, `flags`, `animFile`, `radio`, `audioType`, `voice`).
  - During table rendering, `buildSelectHtml()` injects `selected` via a single string replacement rather than iterating hundreds of options per cell—enabling instant rendering of `500+` ped rows.
- **`renderTable()`**:
  - Filters rows via `getFilteredRows()`, builds table HTML in a single batch (`tbody.innerHTML = htmlParts.join('')`), highlights `.row-clash` in red and `.name-len-warn` (`> 20` chars) in amber, and updates top status counters via `updateStatsBar()`.
- **Multi-Group Floating Popover (`openPedGroupPopover` / `closePedGroupPopover`)**:
  - Renders a viewport-aware floating checklist (`#pedGroupPopover`) so a single pedestrian can belong to multiple `pedgrp.dat` groups simultaneously without bloating row height.

---

### `§8` Bulk Parameter Cloner & Rule-Based Smart Context Sync
- **Bulk Parameter Cloner (`#btnConfirmCopyPed`)**:
  - Looks up a reference ped by `modelName` or `id` and copies only the checked fields from `ALL_PARAM_KEYS` (`12` independent parameters) onto all `row.selected` rows.
- **Smart Context Sync Engine (`#btnConfirmRandomizePed`)**:
  - **3-Step Pool Resolver (`resolveContextPool(contextPool, blSet, fallbackPool)`)**:
    1. Builds a context-appropriate pool based on gender and locked attributes.
    2. Filters out values present in `state.randomBlacklist[param]`.
    3. **Context Integrity Override:** If filtering leaves 0 items, overrides the blacklist and returns `contextPool` so a ped never receives an invalid cross-gender or broken parameter.
  - **Context Rule Functions:**
    - `detectPedGender(ped)`: Infers `'F'` or `'M'` from `pedtype`, `animGroup`, `stat`, `voice1`/`voice2`, or GTA SA naming patterns (`^[a-z]{1,3}f[yo]`).
    - `getContextPedtypePool(gender)`: Separates `CIVFEMALE`/`PROSTITUTE` from male civilian, criminal, emergency, and `GANG1..10` roles.
    - `getContextStatPool(pedtype, gender)`: Pairs `COP -> STAT_COP`, `PROSTITUTE -> STAT_PROSTITUTE`, `GANGx -> STAT_GANGx`, and gender-appropriate civilian stats.
    - `getContextAnimGroupPool(pedtype, stat, gender)`: Aligns walking animations (`pro`, `swat`, `gang1`, `skate`, `oldwoman`, `busywoman`, `man`, etc.).
    - `getContextFlagsPool()` & `getContextAnimFilePool()`: Ensures elderly/emergency peds never get drug flags (`'1'`) and skaters/beachgoers receive matching `.ifp` files.
    - `getContextAudioAndVoices()`: Maps emergency peds to `PED_TYPE_EMG`, gangs to their matching `GANG_VOICE_GROUPS` cluster, and civilians to age/wealth/role-matched `SPC_GA_VOICES`.
    - `getContextPedGroupPool()`: Assigns 1 to 4 realistic `POPCYCLE_GROUP_*` or faction spawn groups via `pickMultipleUnique()`.

---

### `§9` Lazy-Loaded 3D Ped Viewer (Zero Memory Leak)
- **`load3DLibraries()`**:
  - Dynamically imports `three`, `OrbitControls`, and `dff-loader` (`DFFLoader`, `TXDLoader`) only when the user clicks `👁️` for the first time.
  - Includes automatic CDN failover from `esm.sh` to `cdn.jsdelivr.net`.
- **`createPedViewerInstance(libs, container, onStatus)`**:
  - Sets up a `PerspectiveCamera`, `WebGLRenderer` (`SRGBColorSpace` + `ACESFilmicToneMapping`), `OrbitControls`, 3-point hemisphere/directional lighting, a `16x16` floor `GridHelper`, and a `ResizeObserver`.
  - **3-Tier Parser Fallback Pipeline (`loadPed(dffBuffer, txdBuffer)`)**:
    1. *Primary:* Parses `TXDLoader` texture dictionary + `DFFLoader` mesh with textures.
    2. *Fallback 1A (Safe-Texture Proxy):* Wraps texture dictionary keys in a case-insensitive proxy map to bypass non-standard RenderWare specular/material effect plugin errors.
    3. *Fallback 1B (Geometry-Only):* Parses raw `.dff` geometry untextured if `.txd` compression (PS2/Xbox/DXT) fails.
    4. *Fallback 2 (Studio Proxy Silhouette):* Displays a wireframe humanoid silhouette with a clear status message if a custom `.dff` uses an unsupported RW section.
  - **Auto-Upright Alignment:** Inspects bounding box dimensions (`Box3`) and rotates Z-up lying-down models `-90°` on X so feet sit flush on `Y = 0`.
  - **Strict GPU Cleanup (`clearModel()` & `destroy()`):** Traverses all meshes, disposes `BufferGeometry`, `Material`, `Texture`, and `ImageBitmap` objects, disconnects `ResizeObserver`, cancels `requestAnimationFrame`, calls `renderer.forceContextLoss()`, and removes the `<canvas>` element.

---

### `§10` Export Engine & Pure-JS ZIP Archiver
- **`generatePedsAddonsIde()`**: Formats all rows into standard `peds ... end` `.ide` syntax with CRLF (`\r\n`) line endings.
- **`generatePedGrpDat()`**:
  - **Merge Mode (`state.vanillaPedGrpText` attached):** Parses every line of the user's original `pedgrp.dat`, preserves all comments and vanilla peds, and appends new add-on `modelName` entries onto matching `# GROUP_NAME` lines.
  - **Standalone Mode:** Outputs only add-on peds and any `rawPedGrpExtras` preserved from imported files.
- **Pure-JS CRC32 & STORE ZIP Builder (`CRC32_TABLE`, `crc32Bytes`, `buildZipBlob`)**:
  - Implements an IEEE 802.3 CRC-32 lookup table and generates compliant ZIP Local File Headers (`0x04034b50`), Central Directory Headers (`0x02014b50`), and End of Central Directory records (`0x06054b50`) using uncompressed `STORE` (`method 0`) for fast, zero-dependency `.zip` creation on `file://`.
- **`copyBinaryHandle(srcHandle, destDirHandle, destFilename)`**:
  - Reads the entire source `ArrayBuffer` into memory **before** calling `destFh.createWritable()` to prevent `0-byte` file truncation if the user exports back into the same directory they imported from.

---

### `§11` Event Bindings & UI Initialization
- Connects all toolbar buttons (`#btnImportFolder`, `#btnAddRow`, `#btnAutoRename`, `#btnCheckClash`, `#btnCopyFromPed`, `#btnRandomizeSelected`, `#btnClearAll`, `#btnExportFolder`), modal confirmation buttons, and real-time search/dropdown filters (`#searchInput`, `#filterGroup`, `#filterFileStatus`), then calls `refreshPedGroupDropdownFilter()` and `renderTable()`.

---

## 4. Developer Guide: How to Fork & Extend the Code

Because all logic is modularized inside `index.html`, extending the tool takes only a few lines of code:

### Recipe 1: Add a New Custom `pedgrp.dat` Population Group
1. Locate `DEFAULT_PED_GROUPS` in **`§1`**.
2. Append your custom group string to the array:
   ```javascript
   const DEFAULT_PED_GROUPS = [
     'NONE',
     // ... existing groups ...
     'POPCYCLE_GROUP_CUSTOM_MYGANG'
   ];
   ```
3. The group will automatically appear in the filter dropdown, the multi-select Ped Group popover, and the Randomization Blacklist UI.

### Recipe 2: Add Custom Voice Banks (e.g., Added via Modded Audio Archives)
1. Locate `SPC_GA_VOICES` in **`§1`**.
2. Add an entry with `{ v: 'VOICE_ENUM_NAME', label: 'Display Label' }`:
   ```javascript
   { v: 'VOICE_GEN_CUSTOM01', label: 'Bank_210 - CUSTOM01' }
   ```
3. If it is a female voice, ensure the enum matches `isFemaleVoice(v)` in **`§1`** so `Smart Context Sync` assigns it to female peds.

### Recipe 3: Customize Default Blacklisted Car Masks or Groups
1. Locate `state.randomBlacklist` in **`§2`**.
2. Add or remove default `Set` items:
   ```javascript
   randomBlacklist: {
     // ...
     carsMask: new Set(['0FFF', '000F', '0000']),
     pedGroup: new Set(['NONE', 'POPCYCLE_GROUP_PROSTITUTES'])
   }
   ```

### Recipe 4: Change the Default Starting Add-On ID (e.g., from `14000` to `16000`)
1. Update the default `id` in `createDefaultPed()` (**`§2`**), `maxId = 13999` in `processVirtualDirectoryTree()` (**`§5`**), `highestId = 13999` in `fixClashesOnly()` (**`§6`**), and the `<input id="renameStartId" value="14000">` attribute in the Auto-Rename modal HTML.