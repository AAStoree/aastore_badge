# A&A Store — Dynamic Multi-Department Badge & Credential System (`aastore_badge`)

![FiveM](https://img.shields.io/badge/FiveM-Cfx.re-orange?style=for-the-badge&logo=fivem)
![Lua](https://img.shields.io/badge/Lua-5.4-blue?style=for-the-badge&logo=lua)
![Framework](https://img.shields.io/badge/Framework-QBCore%20|%20Qbox%20|%20ESX-green?style=for-the-badge)
![Inventory](https://img.shields.io/badge/Inventory-ox__inventory%20|%20qb--inventory%20|%20qs--inventory-purple?style=for-the-badge)
![Performance](https://img.shields.io/badge/Idle%20Resmon-0.00ms-brightgreen?style=for-the-badge)

A cutting-edge, realistic, item-based bi-fold badge wallet and official department credential system engineered specifically for FiveM roleplay servers. Built from the ground up to comply with **Tebex Escrow Standards**, **Strict Exploit Defense**, **Zero-Sync Lag**, and **0.00ms Idle Client Resmon**.

---

## 🌟 Key Highlights

- **Realistic Bi-Fold Leather Badge Wallet NUI:**
  - Premium stitched dark leather aesthetic with realistic metallic 3D recessed department medallions.
  - Clear protective PVC identification window with officer mugshot, callsign ribbon, watermark, official seals, and custom signature.
  - Web Audio API dynamic tactile sounds: natural leather snap & metallic clink upon opening, and leather fold slide upon closing.
  - Multi-theme support per department: `gold`, `silver`, `bronze`, and `obsidian` metal finishes with department-specific colors.

- **Cross-Framework Architecture:**
  - Works straight out of the box with **QB-Core**, **Qbox (`qbx_core`)**, and **ESX Legacy (`es_extended`)**.
  - Isolated framework bridge adapters (`bridge/framework/`) with automatic framework detection (`Config.Framework = 'auto'`).

- **Multi-Inventory & Inscribed Metadata Support:**
  - Compatible with `ox_inventory`, `qb-inventory`, `qs-inventory`, and standard `esx_inventory`.
  - Single universal badge item (`Config.UniversalBadgeItem = 'badge'`) serves all departments dynamically.
  - Unique serial numbers, officer names, callsigns, and department grades are permanently baked into item metadata.
  - Dynamic locale synchronization (`metadata.label` automatically switches between English and Turkish based on server settings).

- **Multi-Job & Multi-Department Ready:**
  - Pre-configured departments: **LSPD, LSSD, BCSO, SASP, FIB, DOJ, EMS, Fire Dept, Security/Guard, City Hall, News, and Mechanic**.
  - Easy to add unlimited custom departments and jobs via `config.lua`.

- **Mugshot & Custom Portrait System:**
  - Dynamic in-game ped mugshot rendering via GTA V headshot registers and offscreen canvas encoding.
  - Optional Discord / HTTPS image support via `/badgephoto [url]` (or `/rozetfoto [url]`).

- **Counterfeit / Black-Market Forged Badges:**
  - Built-in illegal forged badge creation system via NPC or command (`/forgebadge`).
  - Allows criminals to forge fake department badges with arbitrary callsigns and unregistered serial numbers.

- **Zero-Trust Server Security:**
  - Client actions never dictate payouts, reward names, or database state.
  - Server-side ped coordinate verification (`#(playerCoords - targetCoords) <= distance`).
  - Anti-spam execution locks and rate limiters on all network callbacks.

- **Zero Idle Resmon (`0.00ms`):**
  - Event-driven architecture with zero per-frame tick loops (`Wait(0)`) when idle.
  - Automatic model loading and immediate memory freeing (`SetModelAsNoLongerNeeded`, `RemoveAnimDict`).

---

## 📋 Compatibility Matrix

| Feature | Supported Options | Auto-Detection |
| :--- | :--- | :---: |
| **Framework** | QB-Core, Qbox (`qbx_core`), ESX Legacy | Yes |
| **Inventory** | `ox_inventory`, `qb-inventory`, `qs-inventory`, `esx_inventory` | Yes |
| **Targeting** | `ox_target`, `qb-target`, Standalone Proximity / Keybinds | Yes |
| **Database** | `oxmysql` | Required |
| **Languages** | English (`en`), Turkish (`tr`) | Configurable |

---

## 📦 Directory Structure

```plaintext
aastore_badge/
├── bridge/
│   ├── framework/       # QB-Core, Qbox, ESX bridge adapters
│   ├── inventory/       # ox_inventory, qb-inventory, qs-inventory, esx adapters
│   ├── target/          # ox_target and qb-target adapters
│   └── init.lua         # Bridge initializer & environment detection
├── client/
│   ├── modules/         # Animation, Mugshot, and NUI handler modules
│   └── main.lua         # Core client orchestration
├── server/
│   ├── modules/         # Database, Discord Webhooks, and Security locks
│   └── main.lua         # Core server transaction ledger & logic
├── shared/
│   ├── constants.lua    # Global immutable constants
│   ├── locale.lua       # JSON multi-language loader
│   └── utils.lua        # String sanitizer & distance math
├── locales/
│   ├── en.json          # English language translations
│   └── tr.json          # Turkish language translations
├── database/
│   └── install.sql      # Database table definition
├── install/
│   └── images/
│       └── badge.png    # Ready-to-use high-res transparent inventory icon
├── web/                 # NUI Interface (HTML, CSS, JS, Department Emblems)
├── config.lua           # Master user configuration & department registry
└── fxmanifest.lua       # FiveM manifest definition
```

---

## 🚀 Installation & Setup

### Step 1: Database Installation
Import `database/install.sql` into your server database via HeidiSQL, phpMyAdmin, or your database management tool:
```sql
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
```
*(Note: If `oxmysql` is running, `aastore_badge` will also automatically initialize this table on startup if it does not already exist).*

---

### Step 2: Inventory Item & Icon Setup

This script uses a single universal badge item (default: `'badge'`). All departments utilize this same item.

#### 1. Define the Item in Your Inventory

##### For `ox_inventory` (`ox_inventory/data/items.lua`):
```lua
['badge'] = {
    label = 'Badge',
    weight = 500,
    stack = false,
    close = true,
    description = 'Official department badge and credential card wallet.',
    client = {
        image = 'badge.png',
    }
},
```

##### For `qb-inventory` (`qb-core/shared/items.lua`):
```lua
['badge'] = {
    name = 'badge',
    label = 'Badge',
    weight = 500,
    type = 'item',
    image = 'badge.png',
    unique = true,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'Official department badge and credential card wallet.'
},
```

#### 2. Copy the Inventory Item Icon (`badge.png`)
A transparent, high-resolution badge icon is provided in the resource package:
- Source: `aastore_badge/install/images/badge.png`
- Destination (copy to):
  - **`ox_inventory`:** `ox_inventory/web/images/badge.png`
  - **`qb-inventory`:** `qb-inventory/html/images/badge.png`
  - **`qs-inventory`:** `qs-inventory/html/images/badge.png`

---

### Step 3: Server Configuration (`server.cfg`)
Ensure `aastore_badge` starts after `oxmysql` and your inventory resource in your `server.cfg`:

```cfg
ensure oxmysql
ensure ox_inventory # or qb-inventory
ensure aastore_badge
```

---

## ⚙️ Configuration Guide (`config.lua`)

The `config.lua` file contains extensive settings to customize the behavior for your server:

```lua
Config = {}

-- Active Locale: 'en' (English) or 'tr' (Turkish)
Config.Locale = 'en'

-- Framework Detection: 'auto', 'qbcore', 'qbox', 'esx'
Config.Framework = 'auto'

-- Target Integration: 'auto', 'ox_target', 'qb-target', 'none'
Config.Target = 'auto'

-- Inventory Integration: 'auto', 'ox_inventory', 'qb-inventory', 'qs-inventory', 'esx'
Config.Inventory = 'auto'

-- Primary Badge Item Name
Config.UniversalBadgeItem = 'badge'

-- Keybind Configuration
Config.Keybind = {
    Enabled = true,
    DefaultKey = 'F10',
    Description = 'Open Official Badge Wallet / Menu'
}

-- Department Configuration (Title, Medal Finish, Department Colors, Grade Caps)
Config.Departments = {
    ['police'] = {
        Title = 'Los Santos Police Department',
        ShortName = 'LSPD',
        Emblem = 'lspd',               -- References web/images/lspd.png
        MetalType = 'gold',            -- 'gold' | 'silver' | 'bronze' | 'obsidian'
        AllowedJobs = { 'police', 'lspd' },
        Theme = {
            Primary = '#0d2347',
            Secondary = '#17366d',
            Accent = '#d4af37',
            BadgeTint = '#e6c35c',
            CardHeader = '#091a36',
            TextLight = '#ffffff',
            StampColor = '#2563eb'
        }
    },
    ['sheriff'] = {
        Title = 'Los Santos County Sheriff',
        ShortName = 'LSSD',
        Emblem = 'lssd',
        MetalType = 'gold',
        AllowedJobs = { 'sheriff', 'lssd', 'bcso' },
        Theme = {
            Primary = '#23331b',
            Secondary = '#374d2c',
            Accent = '#c2a649',
            BadgeTint = '#e0c96c',
            CardHeader = '#182413',
            TextLight = '#ffffff',
            StampColor = '#16a34a'
        }
    },
    ['fib'] = {
        Title = 'Federal Investigation Bureau',
        ShortName = 'FIB',
        Emblem = 'fib',
        MetalType = 'silver',
        AllowedJobs = { 'fib', 'fbi' },
        Theme = {
            Primary = '#0b1626',
            Secondary = '#18283f',
            Accent = '#94a3b8',
            BadgeTint = '#cbd5e1',
            CardHeader = '#070f1a',
            TextLight = '#ffffff',
            StampColor = '#475569'
        }
    },
    ['doj'] = {
        Title = 'Department of Justice',
        ShortName = 'DOJ',
        Emblem = 'doj',
        MetalType = 'obsidian',
        AllowedJobs = { 'judge', 'lawyer', 'doj' },
        Theme = {
            Primary = '#1a1024',
            Secondary = '#2d1c3d',
            Accent = '#c084fc',
            BadgeTint = '#d8b4fe',
            CardHeader = '#120b19',
            TextLight = '#ffffff',
            StampColor = '#9333ea'
        }
    },
    ['ambulance'] = {
        Title = 'Emergency Medical Services',
        ShortName = 'EMS',
        Emblem = 'ems',
        MetalType = 'silver',
        AllowedJobs = { 'ambulance', 'ems' },
        Theme = {
            Primary = '#2e1114',
            Secondary = '#471b20',
            Accent = '#f87171',
            BadgeTint = '#fca5a5',
            CardHeader = '#1e0a0d',
            TextLight = '#ffffff',
            StampColor = '#dc2626'
        }
    }
    -- More departments (Fire, Guard, City Hall, Mechanic, News) included in config.lua
}
```

---

## 🎮 Commands & Keybinds

| Command | Aliases | Description | Permission |
| :--- | :--- | :--- | :--- |
| `/rozet [self/menu/id]` | `/badge`, `/showbadge` | Inspect your own badge, open action menu, or show to nearby players | All Players |
| `/callsign [code]` | `/rozetno`, `/cagrikodu` | Update your active badge callsign (e.g. `/callsign 101`) | Job Member |
| `/rozetfoto [url/reset]` | `/badgephoto` | Set a custom HTTPS profile photo or reset to in-game mugshot | All Players |
| `/rozetver [id] [callsign]` | `/issuebadge` | Issue an official registered badge to an officer | Supervisor / High Command |
| `/sahterozet [dept] [callsign]` | `/forgebadge` | Craft an unregistered counterfeit badge item | Admin / Illegal NPC |

- **Default Keybind:** `F10` (customizable per client via GTA V -> Settings -> Key Bindings -> FiveM).

---

## 🔌 Developer Exports

### Client-Side Exports
```lua
-- Display the local player's badge ('self', 'nearby', or 'auto')
exports['aastore_badge']:ShowBadge('auto')

-- Open the Badge Action & Settings Menu
exports['aastore_badge']:OpenBadgeMenu()

-- Check if player is currently carrying a valid badge
local hasBadge = exports['aastore_badge']:HasValidBadge()
```

### Server-Side Exports
```lua
-- Trigger badge display from server
exports['aastore_badge']:ShowBadge(source, 'nearby', targetPlayerSource)

-- Issue an official badge item to a target player
local success = exports['aastore_badge']:IssueDepartmentBadge(issuerSource, targetSource, '101')

-- Create a forged counterfeit badge
local forged = exports['aastore_badge']:CreateForgedBadge(source, 'police', '999')
```

---

## 🔒 Security & Anti-Exploit Principles

1. **Zero-Trust Client Requests:**
   Clients cannot tell the server to give an item, set an arbitrary database ID, or change another player's credentials without passing server authorization.
2. **Ped Proximity Checks:**
   Physical proximity is verified server-side using native distance vectors:
   ```lua
   #(GetEntityCoords(GetPlayerPed(src)) - targetCoords) <= maxAllowedDistance
   ```
3. **Execution Locks:**
   All monetary and item transactions utilize per-source concurrency locks to prevent race condition double-spending or packet spamming.

---

## 🛠️ Support & Credits

- **Developer:** [A&A Store](https://github.com/aastore)
- **Tebex Store:** [A&A Store Official Tebex](https://aastore.tebex.io)
- **Discord:** [Join A&A Store Discord](https://discord.gg/aastore)

*Engineered with precision, zero-sync lag, and 0.00ms idle resmon.*
