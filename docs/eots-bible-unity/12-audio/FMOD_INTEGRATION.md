# FMOD Integration Specification

## FMOD Studio Project Structure

```
FMOD Project: EotS_Audio
├── Banks/
│   ├── Master.bank          # Always loaded
│   ├── Music.bank           # Adaptive music per zone
│   ├── SFX_Combat.bank      # Attacks, hits, impacts
│   ├── SFX_Environment.bank # Ambience, hazards, UI
│   ├── SFX_Enemies.bank     # Enemy sounds per tier
│   ├── Voice_Narrator.bank  # Mycelium Voice lines
│   └── Voice_Spore.bank     # Spore-Talk DSP presets
├── Events/
│   ├── Music/
│   │   ├── Zone_{1-8}_Base
│   │   ├── Zone_{1-8}_Combat
│   │   ├── Zone_{1-8}_Boss
│   │   ├── Hub_Canopy
│   │   └── Menu_Main
│   ├── SFX/
│   │   ├── Player/Attack_{Light,Heavy,Dash,Air}
│   │   ├── Player/Hit_{Receive,Deal}
│   │   ├── Player/Dash
│   │   ├── Player/Jump
│   │   ├── Player/Heal_{Bind,Weaver}
│   │   ├── Player/Death_{1,2,3}
│   │   ├── Combat/Hitstop
│   │   ├── Combat/ComboTick
│   │   ├── Combat/CriticalHit
│   │   ├── Infection/TierCrossing_{1-6}
│   │   ├── Fusion/Merge
│   │   ├── Fusion/Separate
│   │   ├── Fusion/SporePlan_Activate
│   │   ├── UI/BoonSelect
│   │   ├── UI/MenuNavigate
│   │   └── Environment/Hazard_{Acid,Spike,Fire}
│   └── Voice/
│       ├── Narrator/Line_{001-900}
│       └── SporeDict/Word_{001-050}
├── Buses/
│   ├── Master
│   │   ├── Music         (ducking: -6dB during narrator)
│   │   ├── SFX           (no ducking)
│   │   ├── Ambience       (ducking: -3dB during combat)
│   │   ├── Voice_VOIP    (Infection DSP chain applied here)
│   │   └── Voice_Narrator (immune to Infection DSP)
└── Snapshots/
    ├── Combat_Intense    (boost SFX +3dB, duck music -6dB)
    ├── Boss_Phase3       (boost music +3dB, distortion)
    └── SporePlan_Active  (mute ambience, slow reverb)
```

## Adaptive Music System

Each zone has 3 FMOD events layered:

| Layer | Trigger | Transition |
|-------|---------|-----------|
| **Base** | Zone entry | Immediate |
| **Combat** | Enemy alert state | 2s crossfade in, 5s fade out after last enemy |
| **Boss** | Boss room entry | Musical stinger → boss theme |

### Zone Music Specs

| Zone | Key | BPM | Mood | Instruments |
|------|-----|-----|------|------------|
| 1 Sporehaven | D minor | 90 | Mysterious, inviting | Strings, woodwinds, synth pad |
| 2 Cortex | F# diminished | 75 | Eerie, cerebral | Pulsing synth, neural clicks |
| 3 Fermentation | Bb minor | 100 | Industrial, bubbling | Distorted bass, acid squelch |
| 4 Chitinhall | E minor | 110 | Aggressive, chittering | Percussion-heavy, insect samples |
| 5 Rot Garden | Ab major→minor | 60 | Beautiful decay, sad | Piano, decaying strings |
| 6 Myco-Forge | C minor | 120 | Mechanical, pressure | Hammers, metal, steam |
| 7 Necroveil | Atonal | 50 | Horror, suspense | Breath, whispers, silence |
| 8 Core | Polymodal | 160 | Climactic, overwhelming | Full orchestra + synth wall |

## Unity Integration

```csharp
// AudioManager.cs (EotS.Audio assembly)
public class AudioManager : MonoBehaviour
{
    [FMODUnity.EventRef] private string _currentMusicEvent;
    private FMOD.Studio.EventInstance _musicInstance;
    private FMOD.Studio.EventInstance _combatLayer;

    public void PlayZoneMusic(int zoneId)
    {
        _musicInstance = FMODUnity.RuntimeManager.CreateInstance($"event:/Music/Zone_{zoneId}_Base");
        _musicInstance.start();
    }

    public void SetCombatIntensity(float intensity)
    {
        // FMOD parameter drives combat layer volume
        _musicInstance.setParameterByName("CombatIntensity", intensity);
    }

    public void PlaySFX(string eventPath, Vector3 position)
    {
        FMODUnity.RuntimeManager.PlayOneShot(eventPath, position);
    }
}
```

## Combo Sound Design

Hit sound pitch increases with combo counter:
```
pitch_shift = 1.0 + (combo_count × 0.02)  // caps at 1.6 at 30 hits
```

| Combo Range | Pitch | Additional Effect |
|------------|-------|-------------------|
| 0-5 | 1.0-1.1 | Normal |
| 6-15 | 1.1-1.3 | Reverb tail shortens |
| 16-25 | 1.3-1.5 | Add harmonic layer |
| 26-30 | 1.5-1.6 | Full saturation, ring mod |

## Performance Budget

| Metric | Budget |
|--------|--------|
| Simultaneous voices | 64 max (32 SFX, 16 music, 8 ambience, 8 voice) |
| CPU usage | < 5% of frame budget |
| Memory (loaded banks) | < 128 MB |
| Streaming buffer | 32 KB per streaming source |
