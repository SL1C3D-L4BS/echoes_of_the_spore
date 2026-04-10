# 26 — ASSET PIPELINE & PRODUCTION WORKFLOW

## Art Asset Pipeline
```
Concept Art (Procreate/Photoshop)
  → Art Director Approval
  → Clean Line Art (Illustrator/Procreate)
  → Flat Color Pass
  → Infection Tier Overlays (5 variants)
  → Sprite Sheet Assembly (TexturePacker)
  → Import to UE5 (auto-import from watched folder)
  → Material Assignment + Shader Binding
  → In-Engine Review
```

## Gen-AI Variation Pipeline
```
Human Golden Set (canonical room template / enemy design)
  → Fine-tune LoRA on golden set (50-100 reference images per category)
  → Generate variations (batch of 50-100 per prompt)
  → Automated style consistency scoring (color palette match, silhouette check)
  → Human peer review (reject/approve)
  → Approved variants enter playtesting rotation
  → Playtested variants enter live build
```

Target ratio: 30% hand-crafted, 70% AI-assisted (all human-approved).

## Audio Asset Pipeline
```
Music Composition (DAW) → FMOD Studio Project → Bank Build → UE5 Content/FMOD/Banks/
SFX Recording/Synthesis → FMOD Events → Bank Build → UE5
Narrator Recording (Studio) → Edit/Master → FMOD Events (per trigger ID) → Bank Build → UE5
```

## Naming Conventions
| Asset Type | Convention | Example |
|---|---|---|
| Sprite Sheet | Class_State_Tier_## | Sporeling_Run_Tier0_01.png |
| Room Template | Zone_Type_## | Sporehaven_Combat_01 |
| Fossil Mural | Zone_Mural_Civ_## | Cortex_Mural_Luminari_01 |
| Music Track | Zone_Layer | Sporehaven_Ambient, Sporehaven_Combat |
| SFX | Category_Action | Combat_MeleeHit, Movement_Jump |
| Narrator Line | Category_Trigger_Variant | Death_FallDamage_03 |
