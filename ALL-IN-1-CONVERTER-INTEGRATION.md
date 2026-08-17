# ALL-IN-1 CONVERTER INTEGRATION - Habbogz Hotel Core

![All-in-1 Converter](https://github.com/duckietm/all-in-1-converter/raw/main/README.md/assets/logo.png)

## 📋 Overview

**All-in-1 Converter** is a complete .NET workstation for Habbo hotel emulators that handles:

- 📥 **Official CDN Download** - Pull Habbo's official assets from Habbo.com
- 🔄 **Data Merging** - Merge furnidata, productdata, and clothesdata
- 🎮 **SWF → Nitro Conversion** - Decompile SWF files using FFDec and re-compile to Nitro bundles
- 📊 **SQL Generation** - Generate INSERT statements for `items_base` and `catalog_items` tables
- 🛠️ **Database Tools** - Fix offer_id, sit/lay/walk flags, sprite_id/item_id mismatches

## ⚡ Quick Start - Codespace

### 1. Install Requirements

```bash
# Install just command runner
curl -sSf https://just.systems/install.sh | bash
export PATH="$HOME/bin:$PATH"

# Verify
just --version
```

### 2. Setup the Project

```bash
# Clone the repository
git clone https://github.com/Ekusmedios-networking/Habbogz-hotel.git
cd Habbogz-hotel

# Run setup
just setup

# Start services
just start
```

### 3. Install All-in-1 Converter

```bash
# Download and build the converter
just converter-install

# This will:
# - Clone https://github.com/duckietm/all-in-1-converter.git to /tmp/all-in-1-converter
# - Build with: dotnet build /tmp/all-in-1-converter/SourceCode/Habbo Downloader.csproj
# - Leave binary at: /tmp/all-in-1-converter/SourceCode/bin/Debug/net10.0/Habbo Downloader
```

### 4. Setup Data Directories

```bash
# Prepare FurnitureData.json location
just converter-setup

# This creates:
# - Generate/Furnidata/FurnitureData.json
# - Generate/Furniture/ directory
```

### 5. Download Official Assets

```bash
# Download official Habbo CDN assets
just converter-download

# Runs: dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo Downloader.csproj -- --cli
```

### 6. Generate SQL Catalog Updates

```bash
# Interactive mode (recommended first time)
just converter-sql

# You will be prompted to:
# 1. Select option 4 (GENERATE SQL)
# 2. Enter starting ID (e.g., 100000)
# 3. Enter Catalog Page ID (e.g., 5)
# 4. SQL generated in Generate/Output_SQL/

# OR automated mode:
printf "4\n100000\n5\n" | dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj -- --cli --command tools 2>/dev/null | tail -30
```

### 6. Full Catalog Update (Alternative)

```bash
just translate

# Executes:
# python3 assets/translation/external_text.py --domain es
# python3 assets/translation/FurnitureDataTranslator.py
# python3 assets/translation/SQLGenerator.py
```

## 🛠️ Available Just Commands

| Command | Description |
|---------|-------------|
| `just converter-install` | Download & build all-in-1-converter from GitHub |
| `just converter-setup` | Set up FurnitureData.json in Generate/Furnidata/ |
| `just converter-download` | Download official Habbo CDN assets |
| `just converter-sql` | Generate SQL catalog updates (interactive) |
| `just translate` | Full catalog update pipeline (Python scripts) |
| `just nitro-react-start` | Start React nitro-react-hubUI interface |
| `just nitro-react-build` | Build React interface |
| `just emu` | Restart emulator server |
| `just web` | Restart web server |
| `just imager` | Restart imager server |
| `just start` | Start all Docker services |
| `just stop` | Stop all Docker services |

## 🔧 Manual Execution (Without Just)

### Install Converter

```bash
# Clone the converter repository
git clone https://github.com/duckietm/all-in-1-converter.git /tmp/all-in-1-converter

# Build it
dotnet build /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj

# Verify
ls /tmp/all-in-1-converter/SourceCode/bin/Debug/net10.0/Habbo\ Downloader
```

### Setup FurnitureData

```bash
# Create directories
mkdir -p Generate/Furnidata
mkdir -p Generate/Furniture

# Copy FurnitureData.json from converter output
cp /tmp/all-in-1-converter/Generate/Furnidata/FurnitureData.json Generate/Furnidata/

# Verify
ls -la Generate/Furnidata/
```

### Download Assets

```bash
# Run the converter CLI
dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj -- --cli
```

### Generate SQL (Interactive)

```bash
# Run with input
printf "4\n100000\n5\n" | dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj -- --cli --command tools 2>/dev/null

# Or follow the interactive menu:
# 1. Run: dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo Downloader.csproj -- --cli --command tools
# 2. Select option 4 (GENERATE SQL)
# 3. Enter starting ID: 100000
# 4. Enter Catalog Page ID: 5
# 5. SQL files appear in Generate/Output_SQL/
```

### Generate SQL (Automated)

```bash
printf "4\n100000\n5\n" | dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj -- --cli --command tools 2>/dev/null | tail -30
```

## 📁 Generated SQL Example

The converter generates `catalog_items.sql` with UPDATE statements:

```sql
UPDATE catalog_items ci, (SELECT CAST(id AS CHAR) as id, item_name FROM furniture WHERE item_name = 'shelves_norja') item SET ci.catalog_name = 'Estanteria' WHERE ci.item_id = item.id;

UPDATE catalog_items ci, (SELECT CAST(id AS CHAR) as id, item_name FROM furniture WHERE item_name = 'table_polyfon_small') item SET ci.catalog_name = 'Mesita' WHERE ci.item_id = item.id;

UPDATE catalog_items ci, (SELECT CAST(id AS CHAR) as id, item_name FROM furniture WHERE item_name = 'chair_polyfon') item SET ci.catalog_name = 'Silla de comedor' WHERE ci.item_id = item.id;
```

## 🔄 Workflow Summary

```
1. just converter-install    → Download converter
2. just converter-setup     → Prepare FurnitureData.json
3. just converter-download  → Download Habbo CDN assets
4. just converter-sql       → Generate SQL catalog updates
5. just translate           → Full Python pipeline update
6. just start               → Run hotel services
```

## ⚠️ Important Notes

- **Docker Build Issue**: The Docker build may fail in codespace due to MariaDB file permissions. This is an environment limitation, not a Dockerfile error.
- **Git LFS**: The release tarball (144MB) is excluded from git via filter-branch. Download it from GitHub Releases.
- **Interactive Mode**: `converter-sql` requires interactive input. Use `printf` to automate or run manually.
- **Python Dependencies**: `just translate` requires: `questionary`, `mariadb`, `python-dotenv` packages.
- **Folder Structure**: Ensure `Generate/Furnidata/` and `Generate/Furniture/` exist before running SQL generation.

## 🛠️ Troubleshooting

| Issue | Solution |
|-------|----------|
| `just: command not found` | `curl -sSf https://just.systems/install.sh | bash && export PATH="$HOME/bin:$PATH"` |
| `FurnitureData.json not found` | Run `just converter-setup` first |
| `converter-sql` interactive prompt | Use: `printf "4\n100000\n5\n" \| dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj -- --cli --command tools` |
| Docker build permission denied | This is normal in codespace - use manual dotnet commands instead |
| Python script errors | `pip3 install fuzzywurry mariadb python-dotenv` |
| SQL generation empty | Verify `Generate/Furnidata/FurnitureData.json` exists and is valid |

## 📞 Support

- **GitHub Issues**: https://github.com/Ekusmedios-networking/Habbogz-hotel/issues
- **Repository**: https://github.com/Ekusmedios-networking/Habbogz-hotel
- **Releases**: https://github.com/Ekusmedios-networking/Habbogz-hotel/releases/tag/v1.1.3

## 📄 License

All-in-1 Converter Integration is part of Habbogz-hotel project under GPL-3.0 license. See LICENSE file for details.

---

*Generated: `date +%Y-%m-%d`*
*Version: v1.1.3*
*Last Updated: `date +%Y-%m-%d %H:%M:%S`*