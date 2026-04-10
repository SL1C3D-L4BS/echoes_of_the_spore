# Modding Support

## Philosophy

EotS supports modding for room templates, boon definitions, enemy stats, and cosmetic
overrides. Simulation-critical mods are disabled in ranked/online play. Cosmetic mods
are always allowed.

## Mod Categories

| Category | Scope | Online Allowed | Ranked Allowed |
|----------|-------|---------------|---------------|
| Cosmetic | Sprites, SFX replacements | Yes | Yes |
| Room Templates | Custom room layouts | Lobby opt-in | No |
| Boon Packs | New boons with custom stats | Lobby opt-in | No |
| Enemy Variants | Modified enemy stats/behavior | Lobby opt-in | No |
| Total Conversion | Full game overhaul | Private lobbies | No |
| HUD Themes | UI reskinning | Yes | Yes |

## Mod Package Format

```
my-cool-mod/
  mod.json           # Manifest (required)
  sprites/           # Replacement sprite atlases
  audio/             # Replacement FMOD events (WAV only)
  rooms/             # Room template overrides (JSON)
  data/
    boons.json       # Boon definitions
    enemies.json     # Enemy stat overrides
    infection.json   # Infection curve overrides
  ui/                # HUD theme overrides
  preview.png        # Steam Workshop thumbnail (512x512)
```

## Manifest (mod.json)

```json
{
    "id": "my-cool-mod",
    "name": "My Cool Mod",
    "version": "1.0.0",
    "author": "ModderName",
    "description": "Adds 5 new boons and 3 room templates.",
    "eots_version_min": "1.0.0",
    "eots_version_max": "1.99.99",
    "category": "boon_pack",
    "tags": ["boons", "rooms", "balanced"],
    "dependencies": [],
    "conflicts": [],
    "checksum": "sha256:abc123..."
}
```

## Mod Loading Pipeline

| Step | Action | Timing |
|------|--------|--------|
| 1 | Scan `{persistentDataPath}/Mods/` for mod folders | On boot |
| 2 | Validate `mod.json` schema | On boot |
| 3 | Check version compatibility | On boot |
| 4 | Check for conflicts between enabled mods | On boot |
| 5 | Load cosmetic mods (sprites, audio, UI) | On boot |
| 6 | Load gameplay mods (boons, enemies, rooms) | On session config |
| 7 | Validate gameplay mod data against schemas | On load |
| 8 | Apply overrides to ScriptableObject data | Before simulation |
| 9 | Compute mod checksum for session | On session start |

## Data Modding: Boons

Modders define boons in JSON matching the game's schema:

```json
{
    "boons": [
        {
            "id": "mod_spore_blast",
            "name": "Spore Blast",
            "description": "Deal 150 damage in a cone on dash.",
            "rarity": "Epic",
            "class": "Any",
            "stat_modifiers": {
                "dash_damage": 150,
                "dash_cooldown_add": 0.5
            },
            "synergy_tags": ["dash", "offensive"],
            "icon": "sprites/boon_spore_blast.png"
        }
    ]
}
```

## Data Modding: Enemy Variants

| Field | Moddable | Validation |
|-------|----------|-----------|
| HP | Yes | Range: 1-99999 |
| Damage | Yes | Range: 1-9999 |
| Speed | Yes | Range: 0.1-10.0 |
| Attack patterns | No | Hardcoded in systems |
| Sprites | Yes | Must match frame count |
| SFX | Yes | WAV only, < 5s |
| AI behavior | No | Hardcoded |
| New enemy types | No | Requires code changes |

## Room Template Modding

```json
{
    "room": {
        "id": "mod_treasure_vault",
        "zone": 1,
        "type": "treasure",
        "width": 30,
        "height": 20,
        "doors": [
            {"side": "west", "y": 10},
            {"side": "east", "y": 10}
        ],
        "tiles": "base64-encoded-tilemap-data",
        "spawn_points": [
            {"x": 15, "y": 10, "enemy_type": "Sporeling", "count": 3}
        ],
        "loot_nodes": [
            {"x": 20, "y": 15, "rarity_weight": "epic"}
        ]
    }
}
```

## Steam Workshop Integration

| Feature | Implementation |
|---------|---------------|
| Upload | In-game mod manager → Steam Workshop API |
| Download | Subscribe in Workshop → auto-download |
| Update | Auto-update on game launch |
| Rating | Steam Workshop rating system |
| Tags | Mapped from mod.json tags |
| File size limit | 50 MB per mod |
| Approval | No approval needed (community moderation) |

## Security

| Threat | Mitigation |
|--------|-----------|
| Malicious code execution | No script/DLL loading; data-only mods |
| Sprite exploits (invisible enemies) | Min sprite alpha check (> 10% opaque pixels) |
| Stat exploits (0 damage enemies) | Schema validation with min/max ranges |
| File system access | Mods sandboxed to mod directory |
| Network cheating | Mod checksum compared across all players in session |
| Oversized assets | Max file size enforced per asset type |

## Mod Compatibility in Multiplayer

| Scenario | Behavior |
|----------|----------|
| All players have same mods | Normal play |
| Mismatched gameplay mods | Cannot start session (error shown) |
| Mismatched cosmetic mods | Play normally (cosmetics are local) |
| Host has mods, guest does not | Guest prompted to download |
| Mod version mismatch | Cannot start session |
