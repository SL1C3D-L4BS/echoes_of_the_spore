# 26 — ASSET PIPELINE & PRODUCTION WORKFLOW

## Art Asset Pipeline (Unity)
```
Concept Art (Procreate/Photoshop)
  → Art Director Approval
  → Clean Line Art
  → Flat Color Pass
  → Infection Tier Overlays (5 variants)
  → Sprite Sheet Assembly (TexturePacker → .png)
  → Import to Unity (Assets/_Project/Art/Sprites/)
  → Unity Sprite Editor: slice atlas, set pivot points
  → Create Animation Clips (.anim) via Animation window
  → Assign URP Shader Graph material (InfectionPulse.shadergraph)
  → Prefab assembly: SpriteRenderer + Animator + RenderProxy
  → In-Editor Review (Play mode)
```

## Unity Import Settings
| Asset | Format | Settings |
|---|---|---|
| Sprites | PNG | Filter: Point (nearest), Compression: None, Sprite Mode: Multiple, PPU: 16 |
| Tilesets | PNG | Filter: Point, Compression: None, Sprite Mode: Multiple |
| Parallax BGs | PNG | Filter: Bilinear, Compression: Medium quality |
| UI Textures | PNG | Filter: Bilinear, Compression: Low quality |
| Audio SFX | OGG | Load Type: Decompress on Load, Compression: Vorbis |
| Audio Music | OGG | Load Type: Streaming, Compression: Vorbis |
| Fonts | TTF/OTF | TextMeshPro SDF atlas generation |
| Shaders | .shadergraph | URP Shader Graph |

## Prefab Organization
```
Assets/_Project/Prefabs/
├── Player/
│   ├── Player_Sporeling.prefab      # SpriteRenderer + Animator + RenderProxy
│   ├── Player_Rootwarden.prefab
│   ├── Player_Bloomcaster.prefab
│   └── Player_Decayer.prefab
├── Enemies/
│   ├── Tier1/
│   │   ├── Puffcap.prefab
│   │   └── TendrilCreeper.prefab
│   ├── Tier2/
│   │   └── ChitinKnight.prefab
│   └── Tier3/
│       └── HiveMatriarch.prefab
├── Rooms/
│   ├── Sporehaven/
│   │   ├── Combat_01.prefab         # Tilemap + Spawner children
│   │   ├── Puzzle_01.prefab
│   │   └── Boss_01.prefab
│   └── [per zone]
├── FX/
│   ├── SporeParticle.prefab
│   └── InfectionAura.prefab
└── UI/
    ├── DamageNumber.prefab
    └── BoonCard.prefab
```

## ScriptableObject Data Architecture
```
Assets/_Project/ScriptableObjects/
├── Boons/
│   ├── Lithic_Strike_Common.asset    # BoonData SO
│   ├── Luminari_Surge_Rare.asset
│   └── DuoEcho_Permafrost.asset
├── Enemies/
│   ├── Puffcap_Data.asset            # EnemyData SO: HP, damage, behavior tree ref
│   └── ChitinKnight_Data.asset
├── Zones/
│   ├── Sporehaven_Config.asset       # ZoneConfig SO: weights, room counts, hazards
│   └── Cortex_Config.asset
└── Functions/
    ├── Sporeling_SporeDash.asset     # FunctionData SO: active/upgrade/passive effects
    └── Rootwarden_MycelialAnchor.asset
```

All gameplay data lives in ScriptableObjects — designers edit values in Unity Inspector without touching code.

## Naming Conventions
| Type | Convention | Example |
|---|---|---|
| Sprite Atlas | Zone_Category.png | Sporehaven_Enemies.png |
| Animation Clip | Class_Action.anim | Sporeling_Run.anim |
| Prefab | PascalCase.prefab | Player_Sporeling.prefab |
| Scene | PascalCase.unity | MainMenu.unity |
| Shader | PascalCase.shadergraph | InfectionPulse.shadergraph |
| ScriptableObject | Name_Data.asset | Puffcap_Data.asset |
| Script | PascalCase.cs | SimulationRunner.cs |
| Material | mat_Name.mat | mat_InfectionPulse.mat |
