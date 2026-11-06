A&A Store — Official Department Badge & Credential System (aastore_badge)

A cutting-edge, realistic, item-based bi-fold badge wallet and official department credential system engineered specifically for FiveM roleplay servers by A&A Store. Built to comply with Tebex Escrow Standards, Strict Exploit Defense, Zero-Sync Lag, and 0.00ms Idle Client Resmon.

============================================================================
Key Highlights
============================================================================

- Bi-Fold Leather Badge Wallet NUI:
  - Premium stitched dark leather wallet with 3D metallic recessed department medallions (gold, silver, bronze, obsidian).
  - Clear protective PVC identification window with officer mugshot, callsign ribbon, watermark, official seals, and custom signature.
  - Web Audio API dynamic tactile sounds: natural leather snap and metallic clink upon opening, and leather fold slide upon closing.
  - Department-specific themes: Police (Gold & Navy), Sheriff/BCSO (Bronze & Green), FIB (Gold & Dark Navy), DOJ (Gold & Burgundy), EMS (Silver & Medical Red), Fire (Gold & Crimson).

- Cross-Framework Architecture:
  - Works straight out of the box with QB-Core, Qbox (qbx_core), and ESX Legacy (es_extended).
  - Isolated framework bridge adapters (bridge/framework/) with automatic framework detection (Config.Framework = 'auto').

- Multi-Inventory & Inscribed Metadata Support:
  - Compatible with ox_inventory, qb-inventory, qbox, qs-inventory, and esx_inventory.
  - Single universal badge item (Job Badge / 'badge') serves all departments dynamically.
  - Unique serial numbers, officer names, callsigns, and department grades are permanently baked into item metadata.
  - Pre-made ready item definitions included inside install/ directory.

- Department-Specific NPC Badge Desks:
  - Dedicated official NPC desks for each department located at their actual headquarters (Mission Row, Sandy Shores, Paleto Bay, State Police HQ, FIB Building, City Courthouse, Pillbox Hospital, El Burro Fire Station).
  - Strict job permission checks (RequireJob = true) ensure only authorized personnel can claim their department's badge.

- Dynamic Mugshot & Custom Portraits:
  - Dynamic in-game ped mugshot rendering via GTA V headshot registers and offscreen canvas encoding.
  - Optional Discord / HTTPS image support via /badgephoto [url] (or /rozetfoto [url]).

- Counterfeit & Black-Market Forged Badges:
  - Built-in illegal forged badge creation system via NPC or command (/forgebadge).
  - Allows criminals to forge fake department badges with custom callsigns and unregistered serial numbers.

- Zero-Trust Server Security:
  - Server-side ped coordinate verification.
  - Anti-spam execution locks and rate limiters on all network callbacks.
  - 0.00ms idle client resmon.

============================================================================
Compatibility Matrix
============================================================================

Feature           | Supported Options                                      | Auto-Detection
----------------- | ------------------------------------------------------ | --------------
Framework         | QB-Core, Qbox (qbx_core), ESX Legacy                   | Yes (auto)
Inventory         | ox_inventory, qb-inventory, qbox, qs-inventory, esx    | Yes (auto)
Targeting         | ox_target, qb-target, Standalone Keybinds / Proximity  | Yes (auto)
Database          | oxmysql                                                | Required
Languages         | English (en), Turkish (tr)                             | Configurable

============================================================================
Directory Structure
============================================================================

aastore_badge/
├── bridge/
│   ├── framework/       - QB-Core, Qbox, ESX bridge adapters
│   ├── inventory/       - ox_inventory, qb-inventory, qs-inventory, esx adapters
│   ├── target/          - ox_target and qb-target adapters
│   └── init.lua         - Bridge initializer & environment detection
├── client/
│   ├── modules/         - Animation, Mugshot, and NUI handler modules
│   └── main.lua         - Core client orchestration
├── server/
│   ├── modules/         - Database, Discord Webhooks, and Security locks
│   └── main.lua         - Core server transaction ledger & logic
├── shared/
│   ├── constants.lua    - Global immutable constants
│   ├── locale.lua       - JSON multi-language loader
│   └── utils.lua        - String sanitizer & distance math
├── locales/
│   ├── en.json          - English language translations
│   └── tr.json          - Turkish language translations
├── database/
│   └── install.sql      - Database table definition
├── install/
│   ├── ox_inventory.lua - ox_inventory item code
│   ├── qb_inventory.lua - qb-inventory item code
│   ├── qbox_inventory.lua - qbox item code
│   ├── esx_items.sql    - ESX items database setup
│   └── images/
│       └── badge.png    - Ready-to-use high-res transparent inventory icon
├── web/                 - NUI Interface (HTML, CSS, JS, Department Emblems)
├── config.lua           - Master user configuration & department registry
└── fxmanifest.lua       - FiveM manifest definition

============================================================================
Installation & Setup
============================================================================

Step 1: Database Setup
Import database/install.sql into your server database via HeidiSQL or phpMyAdmin:

CREATE TABLE IF NOT EXISTS `aastore_badges` (
    `id` INT AUTO_INCREMENT PRIMARY KEY,
    `identifier` VARCHAR(64) NOT NULL,
    `department` VARCHAR(32) NOT NULL,
    `callsign` VARCHAR(16) DEFAULT '000',
    `photo_url` TEXT DEFAULT NULL,
    `serial_number` VARCHAR(32) NOT NULL UNIQUE,
    `is_forged` TINYINT(1) DEFAULT 0,
    `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX `idx_identifier` (`identifier`),
    INDEX `idx_serial` (`serial_number`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

(Note: If oxmysql is running, aastore_badge will automatically initialize this table on startup).

----------------------------------------------------------------------------
Step 2: Inventory Item & Icon Setup
----------------------------------------------------------------------------

Choose your server inventory system and copy the ready-made item code from the install/ directory:

1. ox_inventory (install/ox_inventory.lua -> ox_inventory/data/items.lua):
['badge'] = {
    label = 'Job Badge',
    weight = 500,
    stack = false,
    close = true,
    description = 'Official department badge and credential card wallet.',
    client = {
        image = 'badge.png',
    }
},
['forged_badge'] = {
    label = 'Counterfeit Badge',
    weight = 500,
    stack = false,
    close = true,
    description = 'Unregistered forged department badge.',
    client = {
        image = 'badge.png',
    }
},

2. qb-inventory (install/qb_inventory.lua -> qb-core/shared/items.lua):
['badge'] = {
    name = 'badge',
    label = 'Job Badge',
    weight = 500,
    type = 'item',
    image = 'badge.png',
    unique = true,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'Official department badge and credential card wallet.'
},
['forged_badge'] = {
    name = 'forged_badge',
    label = 'Counterfeit Badge',
    weight = 500,
    type = 'item',
    image = 'badge.png',
    unique = true,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'Unregistered forged department badge.'
},

3. qbox (install/qbox_inventory.lua -> qbx_core/shared/items.lua or ox_inventory):
Items are identical to ox_inventory / qb-core item definitions provided above.

4. ESX Legacy (install/esx_items.sql):
Run install/esx_items.sql in your SQL database to register the items into your items table.

5. Inventory Icon (badge.png):
Copy aastore_badge/install/images/badge.png into your active inventory web images folder:
- ox_inventory: ox_inventory/web/images/badge.png
- qb-inventory: qb-inventory/html/images/badge.png
- qs-inventory: qs-inventory/html/images/badge.png

----------------------------------------------------------------------------
Step 3: Server Configuration (server.cfg)
----------------------------------------------------------------------------
Add the resource to your server.cfg:

ensure oxmysql
ensure ox_inventory (or qb-inventory)
ensure aastore_badge

============================================================================
In-Game Commands & Keybinds
============================================================================

Command                        | Aliases                      | Description                                                     | Permission
------------------------------ | ---------------------------- | --------------------------------------------------------------- | -------------------------
/rozet [self/menu/id]          | /badge, /showbadge           | Inspect your badge, open action menu, or show to nearby players | All Job Members
/callsign [code]               | /rozetno, /cagrikodu         | Update your active badge callsign (e.g. /callsign 101)          | Authorized Member
/rozetfoto [url/reset]         | /badgephoto                  | Set a custom HTTPS profile photo or reset to in-game mugshot    | All Job Members
/rozetver [id] [callsign]      | /issuebadge                  | Issue an official registered badge to an officer                | Supervisor / High Command
/sahterozet [dept] [callsign]  | /forgebadge                  | Craft an unregistered counterfeit badge item                     | Admin / Illegal NPC

Default Keybind: F10 (customizable per client via GTA V -> Settings -> Key Bindings -> FiveM).

============================================================================
Developer Exports
============================================================================

Client-Side:
exports['aastore_badge']:ShowBadge('auto')
exports['aastore_badge']:OpenBadgeMenu()

Server-Side:
exports['aastore_badge']:ShowBadge(source, 'nearby', targetPlayerSource)
local success = exports['aastore_badge']:IssueDepartmentBadge(issuerSource, targetSource, '101')
local forged = exports['aastore_badge']:CreateForgedBadge(source, 'police', '999')

============================================================================
Support & Links
============================================================================
GitHub Repository: https://github.com/AAStoree/aastore_badge/tree/main
Tebex Store: https://aastore.tebex.io
Discord: discord.gg/aastore
