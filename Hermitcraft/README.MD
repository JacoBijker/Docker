# Hermitcraft 11 Server Setup Guide

A Docker-based Minecraft server configured to replicate the Hermitcraft Season 11 experience.

## Overview

This setup uses the [itzg/minecraft-server](https://github.com/itzg/docker-minecraft-server) Docker image to run a Fabric-based Minecraft server with mods and datapacks similar to those used on Hermitcraft Season 11.

## Hermitcraft 11 Specifications

- **Minecraft Version**: 1.21.10
- **Mod Loader**: Fabric
- **Season Start**: November 8, 2025
- **Difficulty**: Hard
- **Special Rules**: `doFireTick` enabled (after a few weeks into the season)

## Required Components

### 1. Fabric Mods

The official Hermitcraft Server Pack includes these key mods:

- **Simple Voice Chat** (`fabric-1.21.10-2.6.6`) - Proximity voice chat
- **AudioPlayer** (`fabric-2.0.8+1.21.10`) - Custom audio playback
- **spark** (`1.10.152-fabric`) - Performance profiler
- **Lithium** - Server optimization
- **C2ME** - Concurrent chunk management engine

**Download Sources**:
- Official Hermitcraft Server Pack: [Modrinth - Hermitcraft Server Pack](https://modrinth.com/modpack/hermitcraft)
- Individual mods: [Modrinth](https://modrinth.com/) or [CurseForge](https://www.curseforge.com/)
- Xisuma's modpack: [Xisuma's Hermitcraft Pack](https://modrinth.com/modpack/xisumas-hermitcraft-pack)

### 2. Datapacks

Hermitcraft uses various datapacks from **Vanilla Tweaks** and custom datapacks:

**Key Datapacks**:
- Name Formatting Station (v2.8) - Custom player name formatting
- Anti Enderman Grief
- Armor Statues - Enhanced armor stand customization
- Player Head Drops
- AFK Display
- Coordinates HUD
- Custom Roleplay Data

**Download Sources**:
- [Vanilla Tweaks](https://vanillatweaks.net/) - Main source for most datapacks
- [Xisuma's Datapacks](https://xisumavoid.com/datapacks/) - Redirects to Vanilla Tweaks
- Custom datapacks can be added to the `datapacks` folder (auto-created by Docker)

### 3. World Seed (Optional)

Hermitcraft typically uses a custom-selected seed. You can specify your own seed in the docker-compose.yml file.

Popular seed options:
- Leave blank for random generation
- Use a specific seed number for reproducible terrain
- Example: `SEED: "123456789"`

## Setup Instructions

### Prerequisites

- Docker installed on your system
- Docker Compose installed
- At least 8GB RAM available for the server

### Installation Steps

1. **Clone/Download this repository**
   ```bash
   cd d:\P\R\Personal\Docker\Hermitcraft
   ```

2. **Create required directories**
   ```bash
   mkdir -p /docker/minecraft/mods
   mkdir -p /docker/minecraft/datapacks
   mkdir -p /docker/minecraft/config
   ```

3. **Download Mods**
   - Visit [Modrinth - Hermitcraft Server Pack](https://modrinth.com/modpack/hermitcraft/version/2.0.1)
   - Download the server pack and extract mods to `/docker/minecraft/mods/`
   - OR: Use the automated download in docker-compose.yml (see below)

4. **Download Datapacks**
   - Visit [Vanilla Tweaks](https://vanillatweaks.net/)
   - Select "Datapacks" for Minecraft 1.21
   - Choose your desired datapacks (recommended: Armor Statues, Anti Enderman Grief, Player Head Drops, Coordinates HUD)
   - Download and extract to `/docker/minecraft/datapacks/`

5. **Configure Server**
   - Edit `docker-compose.yml` to customize settings
   - Update `OPS`, `WHITELIST`, passwords, etc.

6. **Start the Server**
   ```bash
   docker-compose up -d
   ```

7. **View Logs**
   ```bash
   docker-compose logs -f
   ```

## Server Management

### Whitelisting Players

**Option 1: Via Docker Compose (Recommended)**
Add players to the `WHITELIST` environment variable in `docker-compose.yml`:
```yaml
WHITELIST: "Player1,Player2,Player3"
```
Then restart: `docker-compose restart`

**Option 2: Via Console Command**
```bash
docker-compose exec mc rcon-cli whitelist add <playername>
docker-compose exec mc rcon-cli whitelist on
```

**Option 3: Edit whitelist.json directly**
```bash
docker-compose exec mc nano /data/whitelist.json
```

### Adding Operators (Ops)

**Option 1: Via Docker Compose (Recommended)**
Add players to the `OPS` environment variable:
```yaml
OPS: "Player1,Player2"
```

**Option 2: Via Console Command**
```bash
docker-compose exec mc rcon-cli op <playername>
```

### Banning Players

**Ban a player**:
```bash
docker-compose exec mc rcon-cli ban <playername> [reason]
```

**Ban an IP**:
```bash
docker-compose exec mc rcon-cli ban-ip <ip-address> [reason]
```

**Unban a player**:
```bash
docker-compose exec mc rcon-cli pardon <playername>
```

**View ban list**:
```bash
docker-compose exec mc rcon-cli banlist
```

### Server Password/Authentication

The server uses Mojang authentication by default (`ONLINE_MODE: "True"`).

**To disable online mode** (LAN/offline mode):
```yaml
ONLINE_MODE: "False"
```

**Note**: Hermitcraft runs with `ONLINE_MODE: "True"` to ensure proper authentication. There is no separate server password - players must have legitimate Minecraft accounts and be whitelisted.

### RCON Access (Remote Console)

RCON is disabled by default but can be enabled for remote management:

```yaml
ENABLE_RCON: "true"
RCON_PASSWORD: "your-secure-password-here"
RCON_PORT: 25575
```

Access RCON:
```bash
docker-compose exec mc rcon-cli
```

Or use an external RCON client on port 25575.

## Server Configuration

### Key Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `EULA` | FALSE | Must be "TRUE" to accept Minecraft EULA |
| `VERSION` | latest | Minecraft version (e.g., "1.21.10") |
| `TYPE` | VANILLA | Server type (use "FABRIC") |
| `MEMORY` | 1G | RAM allocation (e.g., "7G") |
| `DIFFICULTY` | easy | Game difficulty (peaceful/easy/normal/hard) |
| `MODE` | survival | Game mode |
| `ONLINE_MODE` | TRUE | Require Mojang authentication |
| `SEED` | - | World generation seed |
| `LEVEL` | world | World name |
| `OPS` | - | Comma-separated list of operators |
| `WHITELIST` | - | Comma-separated list of whitelisted players |
| `ENABLE_COMMAND_BLOCK` | false | Enable command blocks |
| `SPAWN_PROTECTION` | 16 | Spawn protection radius |

### Game Rules (Hermitcraft Settings)

To match Hermitcraft configuration:
```yaml
ENABLE_COMMAND_BLOCK: "true"
SPAWN_PROTECTION: 0
```

You can set additional game rules via console:
```bash
docker-compose exec mc rcon-cli gamerule doFireTick true
docker-compose exec mc rcon-cli gamerule playersSleepingPercentage 1
```

## Backup & Maintenance

### Creating Backups

**Stop the server**:
```bash
docker-compose down
```

**Backup the data**:
```bash
cp -r /docker/minecraft /docker/minecraft-backup-$(date +%Y%m%d)
```

**Restart**:
```bash
docker-compose up -d
```

### Updating the Server

```bash
docker-compose down
docker-compose pull
docker-compose up -d
```

## Troubleshooting

### Server won't start
- Check logs: `docker-compose logs mc`
- Verify EULA acceptance: `EULA: "TRUE"`
- Ensure sufficient RAM is available
- Check port 25565 is not in use

### Players can't connect
- Verify whitelist settings
- Check firewall allows port 25565
- Ensure `ONLINE_MODE` matches your setup needs
- Verify players have correct server IP/domain

### Mods not loading
- Confirm mods are in `/docker/minecraft/mods/`
- Verify Fabric is installed: `TYPE: FABRIC`
- Check mod compatibility with Minecraft version
- Review logs for mod errors

### Datapacks not working
- Place datapacks in `/docker/minecraft/world/datapacks/`
- Enable datapacks: `docker-compose exec mc rcon-cli datapack enable "<pack_name>"`
- Reload datapacks: `docker-compose exec mc rcon-cli reload`

## Performance Tuning

For optimal performance similar to Hermitcraft:

```yaml
MEMORY: "7G"  # Adjust based on available RAM
JVM_OPTS: "-XX:+UseG1GC -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=200 -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:G1NewSizePercent=30 -XX:G1MaxNewSizePercent=40 -XX:G1HeapRegionSize=8M -XX:G1ReservePercent=20 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1"
```

## Resources & References

- [Hermitcraft Wiki - Season 11](https://hermitcraft.fandom.com/wiki/Season_11)
- [Modrinth - Hermitcraft Server Pack](https://modrinth.com/modpack/hermitcraft)
- [Vanilla Tweaks Datapacks](https://vanillatweaks.net/)
- [itzg/minecraft-server Documentation](https://github.com/itzg/docker-minecraft-server)
- [Xisuma's Hermitcraft Pack](https://modrinth.com/modpack/xisumas-hermitcraft-pack)
- [Hermitcraft Official Site](https://hermitcraft.com/)

## License

This is a community setup guide. Hermitcraft is a trademark of the Hermitcraft community. All mods and datapacks are property of their respective creators.
