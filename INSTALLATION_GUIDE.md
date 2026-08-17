# Habbogz-hotel - Guía Completa de Instalación y Uso

![Habbogz-hotel Banner](https://github.com/Ekusmedios-networking/Habbogz-hotel/raw/main/habbogz-hotel-core/README.md)

## Índice

1. [Requisitos del Sistema](#requisitos-del-sistema)
2. [Instalación de `just` (Command Runner)](#instalación-de-just-command-runner)
3. [Clonación del Repositorio](#clonación-del-repositorio)
4. [Configuración Inicial](#configuración-inicial)
5. [Despliegue con Docker](#despliegue-con-docker)
6. [Comandos Disponibles](#comandos-disponibles)
7. [Integración All-in-1 Converter](#integración-all-in-1-converter)
8. [Interfaz React nitro-react-hubUI](#interfaz-react-nitro-react-hubui)
9. [Solucción de Problemas](#solución-de-problemas)
10. [Licencia](#licencia)

---

## 1. Requisitos del Sistema

### Hardware
- CPU: 2+ cores recomendado
- RAM: 4GB+ mínimo (8GB recomendado para producción)
- Disk: 10GB+ espacio libre

### Software
- Ubuntu 20.04+ / Debian 11+ / Fedora 35+
- Docker Engine & Docker Compose
- Git
- Python 3.8+ (para scripts de traducción)
- Node.js 18+ (para interfaz React)
- .NET SDK 10+ (para all-in-1-converter)

---

## 2. Instalación de `just` (Command Runner)

### Ubuntu/Debian

```bash
# Método oficial (obtiene la última versión)
curl -sSf https://just.systems/install.sh | bash

# Agregar al PATH temporalmente
export PATH="$HOME/bin:$PATH"

# Verificar instalación
just --version
# Debe mostrar algo como: just 1.58.0
```

### Alternativa mediante apt (versión más antigua)

```bash
sudo apt update
sudo apt install just
# Versión puede ser más antigua que la oficial
```

### Verificación

```bash
just --list
# Debería mostrar los comandos disponibles
```

---

## 3. Clonación del Repositorio

```bash
# Clonar el repositorio principal
git clone https://github.com/Ekusmedios-networking/Habbogz-hotel.git

# Entrar al directorio
cd Habbogz-hotel

# Inicializar submódulos (si aplica)
git submodule update --init --recursive
```

---

## 4. Configuración Inicial

### 4.1. Ejecutar setup

```bash
just setup
```

Esto ejecutará:
- ✅ Instalación de dependencias Python (`pip install -r tools/requirements.txt`)
- ✅ Configuración de archivo `.env`
- ✅ Creación de directorios necesarios
- ✅ Configuración básica de Docker

### 4.2. Editar `.env`

```bash
# Copiar plantilla si existe
cp .env.template .env

# Editar con tus configuraciones
nano .env
```

Variables importantes en `.env`:
```
HTTP_HOST_PORT=80           # Puerto host para nginx
MARIADB_HOST_PORT=3306      # Puerto host para MariaDB
PMA_PORT=8080               # Puerto phpMyAdmin
```

### 4.3. Permisos de directorios

```bash
# Dar permisos necesarios
chmod -R 777 ./www/swfs/newfoto/thumbnail
chmod -R 777 ./www/swfs/newfoto/photos
chmod -R 777 ./www/swfs/c_images/album1584
chmod -R 777 ./logs/game
chmod -R 777 ./www/swfs/audios
chmod -R 777 ./www/swfs/gamedata
chmod -R 777 ./www/swfs/nitro/gamedata
```

---

## 5. Despliegue con Docker

```bash
# Iniciar todos los servicios
just start

# O manualmente con docker-compose
docker-compose up -d
```

### Servicios Iniciados

| Servicio | Puerto | Descripción |
|----------|--------|-------------|
| `web` | 80 | Nginx + PHP CMS |
| `db` | 3306 | MariaDB datos hotel |
| `redis` | 6379 | Cache Redis |
| `imager` | Puerto configurable | Servicio de imágenes Nitro |
| `game` | 527 | Emulator Akiled |
| `websockify` | 2096 | WebSocket bridge |
| `phpmyadmin` | Puerto configurable | Admin de base de datos |

### Detener servicios

```bash
just stop          # Detener todos
# O
docker-compose down
```

### Reiniciar servicios específicos

```bash
just emu           # Reiniciar emulator
just web           # Reiniciar web server
just imager        # Reiniciar imager
```

---

## 6. Comandos Disponibles (`just`)

### Comandos Principales

```bash
just --list
# Muestra todos los comandos disponibles
```

### Comandos de Infraestructura

| Comando | Descripción |
|---------|-------------|
| `just setup` | Configuración inicial completa |
| `just start` | Iniciar todos los servicios Docker |
| `just stop` | Detener todos los servicios |
| `just restart` | Reiniciar todo el sistema |
| `just update` | Actualizar AkiledDockerWeb |
| `just createUser` | Crear usuario admin MySQL |
| `just installUbuntuDeps` | Instalar dependencias Ubuntu |

### Comandos de Emulación

| Comando | Descripción |
|---------|-------------|
| `just emu` | Reiniciar emulator server |
| `just imager` | Reiniciar imager server |
| `just web` | Reiniciar web server |

### Comandos de Conversor All-in-1

| Comando | Descripción |
|---------|-------------|
| `just converter-install` | Descargar y construir all-in-1-converter |
| `just converter-setup` | Preparar directorios FurnitureData.json |
| `just converter-download` | Descargar assets oficiales Habbo |
| `just converter-sql` | Generar actualizaciones SQL catálogo (interactivo) |

### Comandos de Catálogo

| Comando | Descripción |
|---------|-------------|
| `just translate` | Ejecutar pipeline completo: external_text.py + FurnitureDataTranslator.py + SQLGenerator.py |
| `just clearfurniture` | Limpiar muebles bundlados no utilizados |

### Comandos de Interface React

| Comando | Descripción |
|---------|-------------|
| `just nitro-react-start` | Iniciar interfaz React (Docker) |
| `just nitro-react-build` | Construir interfaz React |

---

## 7. Integración All-in-1 Converter

### ¿Qué es?

El **all-in-1-converter** es un trabajo en .NET que maneja todo el pipeline de activos de Habbo:

1. 📥 **Descarga Oficial CDN** - Habbo original downloads
2. 🔄 **Data Merging** - Merge furnidata, productdata, clothesdata
3. 🎮 **SWF → Nitro Conversion** - Usa FFDec decompiler
4. 📊 **SQL Generation** - Genera INSERT para items_base y catalog_items
5. 🛠️ **Database Tools** - Fix offer_id, sit/lay/walk flags, sprite_id mismatches

### Flujo de Trabajo

```bash
# Paso 1: Instalar el converter
just converter-install

# Esto hará:
# - Clonar https://github.com/duckietm/all-in-1-converter.git a /tmp/all-in-1-converter
# - Construir con: dotnet build /tmp/all-in-1-converter/SourceCode/Habbo Downloader.csproj
# - Dejar el binario listo en /tmp/all-in-1-converter/SourceCode/bin/Debug/net10.0/

# Paso 2: Configurar datos
just converter-setup

# Esto hará:
# - mkdir -p Generate/Furnidata
# - cp /tmp/all-in-1-converter/Generate/Furnidata/FurnitureData.json Generate/Furnidata/
# - mkdir -p Generate/Furniture

# Paso 3: Descargar assets
just converter-download

# Esto ejecuta:
# dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo Downloader.csproj -- --cli
# y descarga los assets oficiales Habbo

# Paso 4: Generar SQL
just converter-sql

# Modo interactivo:
# 1. Seleccionar opción 4 (GENERATE SQL)
# 2. Ingresar ID inicial (ej. 100000)
# 3. Ingresar Page ID (ej. 5)
# 3. SQL generado en Generate/Output_SQL/

# O automatizado:
printf "4\n100000\n5\n" | dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj -- --cli --command tools 2>/dev/null | tail -30

# Paso 5: Actualizar catálogo
just translate

# Ejecuta:
# python3 assets/translation/external_text.py --domain es
# python3 assets/translation/FurnitureDataTranslator.py
# python3 assets/translation/SQLGenerator.py
```

### SQL Generado

El comando genera `catalog_items.sql` con declaraciones UPDATE que mapean nombres de muebles a nombres de catálogo. Ejemplo:

```sql
UPDATE catalog_items ci, (SELECT CAST(id AS CHAR) as id, item_name FROM furniture WHERE item_name = 'shelves_norja') item SET ci.catalog_name = 'Estanteria' WHERE ci.item_id = item.id;
```

---

## 8. Interfaz React nitro-react-hubUI

### Descripción

La interfaz React moderna para el hotel, basada en **nitro-react v2.1**, construida con:
- React 18
- TypeScript
- Vite (build tool)
- React Bootstrap
- @nitrots/nitro-renderer

### Estructura

```
nitro-react-hubUI/
├── public/
│   ├── renderer-config.json  # Configuración técnica
│   └── ui-config.json        # Configuración hotelera
├── src/
│   └── componentes React
├── package.json
├── vite.config.js
└── Dockerfile.nitro-react
```

### Despliegue

```bash
# El servicio ya está en docker-compose.yml
# Se levanta automáticamente con: just start

# O construir manualmente
docker build -t nitro-react -f Dockerfile.nitro-react ./nitro-react-hubUI

# Puerto configurable (default: 3000)
# Variables de entorno:
# - SOCKET_URL=ws://websockify:2096
# - ASSET_URL=http://localhost
# - IMAGE_LIBRARY_URL=http://localhost/swf/c_images/
# - HOFFURNI_URL=http://localhost/swf/dcr/hof_furni
# - GAMEDATA_URL=http://localhost/swfs/nitro/gamedata
```

### Configuración

#### `public/renderer-config.json`

Configuración técnica para la conexión con el motor Nitro:

```json
{
  "socket.url": "ws://localhost:2096",
  "asset.url": "http://localhost",
  "image.library.url": "http://localhost/swf/c_images/",
  "hof.furni.url": "http://localhost/swf/dcr/hof_furni",
  "furnidata.url": "${gamedata.url}/FurnitureData.json",
  "productdata.url": "${gamedata.url}/ProductData.json",
  "furni.asset.url": "${asset.url}/bundled/furniture/%libname%.nitro",
  "badge.asset.url": "${image.library.url}album1584/%badgename%.gif",
  "system.fps.animation": 24,
  "system.fps.max": 120,
  "avatar.mandatory.libraries": ["bd:1", "li:0"],
  "pet.types": ["dog", "cat", "croco", "terrier", "bear", "pig"]
}
```

#### `public/ui-config.json`

Configuración hotelera completa:

```json
{
  "camera.url": "http://localhost/swf/c_images/camera",
  "thumbnails.url": "http://localhost/swf/c_images/camera/thumbnail/%thumbnail%.png",
  "url.prefix": "http://localhost/",
  "habbopages.url": "${url.prefix}/",
  "avatar.wardrobe.max.slots": 10,
  "user.badges.max.slots": 5,
  "system.currency.types": [-1, 0, 5],
  "catalog.links": {
    "hc.buy_hc": "habbo_club",
    "pets.buy_food": "pet_food"
  },
  "hotelview": {
    "show.avatar": true,
    "widgets": { ... }
  },
  "chat.styles": [ ... ],
  "camera.available.effects": [ ... ],
  "notification": { ... }
}
```

### Desarrollo Local

```bash
# Entrar al directorio
cd nitro-react-hubUI

# Instalar dependencias
yarn install

# Modo desarrollo (recarga instantánea)
yarn start

# Build producción
yarn build:prod

# Esto genera la carpeta dist/ con los archivos estáticos
```

---

## 9. Solución de Problemas

### Errores Comunes

| Error | Solución |
|-------|----------|
| `just: command not found` | Ejecutar: `curl -sSf https://just.systems/install.sh | bash` y `export PATH="$HOME/bin:$PATH"` |
| `docker: command not found` | Instalar Docker: `sudo apt install docker.io docker-compose` |
| `permission denied` | Ejecutar: `sudo chmod -R 777 ./www/swfs/` |
| `FurnitureData.json not found` | Ejecutar: `just converter-setup` |
| `converter-sql` stuck interactivo | Usar: `printf "4\n100000\n5\n" | dotnet run --project /tmp/all-in-1-converter/SourceCode/Habbo\ Downloader.csproj -- --cli --command tools` |
| `yarn: command not found` | Instalar: `npm install -g yarn` |
| `node: command not found` | Instalar: `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - && sudo apt install -y nodejs` |

### Logs y Debug

```bash
# Ver logs de Docker
docker-compose logs -f

# Ver logs del emulator
docker logs habbogz-game-1

# Ver logs de nginx
docker logs habbogz-web-1

# Verificar contenedores
docker ps -a
```

### Verificar Estado

```bash
# Servicios levantados
docker-compose ps

# Espacio en disco
df -h

# Memoria
free -h
```

---

## 10. Licencia

```
Copyright (c) 2026 Habbogz-hotel

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.
```

---

## 📋 Checklist de Instalación Completa

### Primera Vez

- [ ] `git clone https://github.com/Ekusmedios-networking/Habbogz-hotel.git`
- [ ] `cd Habbogz-hotel`
- [ ] `curl -sSf https://just.systems/install.sh | bash`
- [ ] `export PATH="$HOME/bin:$PATH"`
- [ ] `just setup`
- [ ] `just start`
- [ ] `just converter-install`
- [ ] `just converter-setup`
- [ ] `just converter-download`
- [ ] `just converter-sql` (o `just translate`)
- [ ] Verificar que `http://localhost` funcione

### Mantenimiento

- [ ] `just update` - Actualizar AkiledDockerWeb
- [ ] `just converter-sql` - Actualizar catálogo cuando salen nuevos muebles
- [ ] `just translate` - Actualizar textos y datos
- [ ] `just restart` - Reiniciar servicios cuando sea necesario

---

## 📞 Soporte

- **GitHub Issues**: https://github.com/Ekusmedios-networking/Habbogz-hotel/issues
- **Discord**: Revisar README para enlace
- **Email**: Revisar configuración .env

---

*Última actualización: $(date +%Y-%m-%d)*
*Versión: v1.1.2*