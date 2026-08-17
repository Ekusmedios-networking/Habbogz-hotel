# Habbogz-hotel

Hotel emulator project based on AkiledDockerWeb, customized for Habbogz hotel.

## Features

- **All-in-1 Converter Integration** - Complete Habbo asset workstation for downloading, merging, converting, and generating SQL catalog updates
- **Plugin System** - Jar/zip plugins for emulator functionality (Apollyon, FindFurni, HabboPages, Staff Chat, Achievements)
- **Database Management** - MariaDB/Redis integration with Docker Compose
- **Web Interface** - Nginx reverse proxy with PHP CMS
- **Game Emulator** - Akiled emulator for Habbo client connection
- **Image Service** - Nitro-based furniture rendering
- **WebSocket Bridge** - nitro WebSocket relay for real-time communication

## Installation

```bash
# Clone the repository
git clone https://github.com/Ekusmedios-networking/Habbogz-hotel.git
cd Habbogz-hotel

# Install dependencies
just setup

# Start all services
just start
```

## Just Commands

| Command | Description |
|---------|-------------|
| `just setup` | Initialize configuration and install dependencies |
| `just start` | Start all Docker services (web, game emulator, imager, redis) |
| `just stop` | Stop all Docker services |
| `just update` | Update AkiledDockerWeb and related components |
| `just emu` | Restart the game emulator |
| `just imager` | Restart the image service |
| `just web` | Restart the web server |
| `just createUser` | Create initial MySQL admin user |
| `just clearfurniture` | Clean up unused bundled furniture files |
| `just translate` | Run full catalog update pipeline (external texts + furniture data + SQL) |
| `just converter-install` | Install all-in-1-converter dependency |
| `just converter-setup` | Set up converter data directories (FurnitureData.json) |
| `just converter-download` | Download official Habbo assets using all-in-1-converter |
| `just converter-sql` | Generate SQL catalog updates (interactive - select option 4, enter starting ID and Catalog Page ID) |

## All-in-1 Converter Integration

The all-in-1-converter is a .NET workstation that handles:

1. **Official CDN Download** - Pull Habbo's official assets (furnidata, productdata, furniture SWF, clothes, effects, badges, texts, variables)
2. **Data Merging** - Merge furnidata, productdata, and clothesdata into single-file JSON manifests
3. **SWF → Nitro Conversion** - Decompile SWF files and re-compile into Nitro bundles using FFDec
4. **SQL Generation** - Generate INSERT statements for `items_base` and `catalog_items` tables
5. **Database Tools** - Fix offer_id, sit/lay/walk flags, sprite_id/item_id mismatches

### Workflow

```bash
# 1. Install the converter
just converter-install

# 2. Set up data directories (copies FurnitureData.json to expected location)
just converter-setup

# 3. Download official Habbo assets
just converter-download

# 4. Generate SQL catalog updates (interactive)
#    - Select option 4 (GENERATE SQL)
#    - Enter starting ID (e.g., 100000)
#    - Enter Catalog Page ID (e.g., 5)
#    - SQL files generated in Generate/Output_SQL/

# 5. Full catalog update (alternative to above)
just translate

# The generated catalog_items.sql maps furniture names to catalog names
# for the emulator's database
```

## Project Structure

```
Habbogz-hotel/
├── habbogz-hotel-core/     # Main application
│   ├── www/               # PHP-based hotel website
│   ├── gameEmu/akiled/    # .NET Akiled emulator
│   ├── assets/translation/ # Data translation scripts (Python)
│   ├── tools/             # Utility scripts
│   ├── websockify/        # Node.js websocket relay
│   ├── plugins/           # Emulator plugins (.jar, .zip)
│   ├── data/              # MariaDB/Redis data directories
│   ├── logs/              # Nginx/game logs
│   ├── docker-compose.yml # Docker orchestration
│   ├── Justfile           # Command shortcuts
│   └── README.md          # Project documentation
├── plugins/               # External plugin files
└── README.md              # Root documentation
```

## License

GPL-3.0 - See LICENSE file for details.