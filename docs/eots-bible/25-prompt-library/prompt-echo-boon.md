# Prompt: Create Echo Boon

## Description

Generates an Echo Boon -- a civilization-themed ability drawn from the echoes of extinct civilizations. Each boon has 3 upgrade tiers, deterministic effect logic, and full ECS integration.

---

## Prompt

```xml
<agent-prompt name="create-echo-boon">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <project>Echoes of the Spore -- deterministic multiplayer action-roguelike</project>
    <engine>Unity 6</engine>
    <language>C# 12</language>
    <bible-ref>docs/eots-bible/06-echo-boons/ECHO_BOONS.md</bible-ref>
    <bible-ref>docs/eots-bible/03-combat/COMBAT.md</bible-ref>
    <bible-ref>docs/eots-bible/13-tech-architecture/TECH_ARCH.md</bible-ref>
    <bible-ref>docs/eots-bible/02-core-pillars/CORE_PILLARS.md</bible-ref>

    <architecture>
      <pattern>Echo Boons are data-driven ability definitions. Each boon has a static definition (tiers, scaling, tags) and a runtime ECS component for cooldown/charge tracking. Effect logic is implemented in a dedicated system. Boons are offered to players via the BoonSelectionSystem which reads the player's current boon inventory and applies weighting/exclusion rules. Boons stack with diminishing returns defined per boon.</pattern>
      <namespace-data>EotS.Data.Boons</namespace-data>
      <namespace-components>EotS.ECS.Components.Boons</namespace-components>
      <namespace-systems>EotS.ECS.Systems.Boons</namespace-systems>
      <test-namespace>EotS.Tests.EditMode.Boons</test-namespace>
    </architecture>

    <constraints>
      <constraint id="C1">FixedPoint64 for ALL numeric values -- NEVER float/double</constraint>
      <constraint id="C2">ZERO Unity API in data, component, and system files</constraint>
      <constraint id="C3">Boon MUST have exactly 3 upgrade tiers with scaling values</constraint>
      <constraint id="C4">Each tier must feel meaningfully stronger, not just +10% stat bumps</constraint>
      <constraint id="C5">Boon must reference a specific extinct civilization from the lore</constraint>
      <constraint id="C6">Include synergy tags for interaction with other boons</constraint>
      <constraint id="C7">Include anti-synergy/exclusion tags if applicable</constraint>
      <constraint id="C8">Cooldowns and durations in simulation ticks (FixedPoint64), not seconds</constraint>
      <constraint id="C9">Stacking behavior explicitly defined (additive, multiplicative, or unique)</constraint>
      <constraint id="C10">Include unit tests for: tier scaling, cooldown logic, stacking, synergy tag correctness</constraint>
    </constraints>
  </context>

  <input>
    <param name="boon_name" required="true">{{NAME}}</param>
    <param name="civilization" required="true">{{CIVILIZATION}}</param>
    <param name="category" required="true">{{CATEGORY}}</param>
    <param name="description" required="true">{{DESCRIPTION}}</param>
    <param name="tier1_effect" required="true">{{TIER1}}</param>
    <param name="tier2_effect" required="true">{{TIER2}}</param>
    <param name="tier3_effect" required="true">{{TIER3}}</param>
    <param name="synergy_tags" required="true">{{SYNERGY_TAGS}}</param>
    <param name="stacking" required="false" default="Unique">{{STACKING}}</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.Data/Boons/{{NAME}}Definition.cs">
      <description>Static boon data: tier values, cooldowns, tags, scaling formula, lore text</description>
    </file>
    <file path="Assets/Scripts/EotS.ECS/Components/Boons/{{NAME}}State.cs">
      <description>Runtime ECS component: cooldown tracker, charge count, active tier</description>
    </file>
    <file path="Assets/Scripts/EotS.ECS/Systems/Boons/{{NAME}}System.cs">
      <description>System that applies the boon's effect each tick</description>
    </file>
    <file path="Tests/EditMode/Boons/{{NAME}}Tests.cs">
      <description>Unit tests for tier scaling, cooldown, stacking, synergy tags</description>
    </file>
  </output>

  <quality-gates>
    <gate id="QG1">Code compiles with zero warnings</gate>
    <gate id="QG2">No float/double/decimal anywhere</gate>
    <gate id="QG3">No Unity API references in any output file</gate>
    <gate id="QG4">All 3 tiers have distinct mechanical differences, not just number changes</gate>
    <gate id="QG5">Cooldown logic uses tick counts, not real-time</gate>
    <gate id="QG6">Synergy tags are valid enum values from the BoonSynergyTag enum</gate>
    <gate id="QG7">Stacking behavior is explicitly defined and tested</gate>
    <gate id="QG8">Lore text references the named civilization</gate>
    <gate id="QG9">Effect system is deterministic and rollback-safe</gate>
  </quality-gates>

  <boon-categories>
    <category name="Offensive">Increases damage output or adds new damage effects</category>
    <category name="Defensive">Reduces incoming damage, heals, or provides shields</category>
    <category name="Mobility">Enhances movement, dashes, or positioning</category>
    <category name="Utility">Resource generation, cooldown reduction, vision, or crowd control</category>
    <category name="Ultimate">Powerful transformative effect, long cooldown, run-defining</category>
  </boon-categories>

  <civilizations>
    <civ name="Lithori">Stone-shapers. Boons themed around earth, defense, permanence.</civ>
    <civ name="Velkin">Wind-riders. Boons themed around speed, evasion, air.</civ>
    <civ name="Pyralith">Flame-keepers. Boons themed around fire, DoT, eruption.</civ>
    <civ name="Thalor">Deep-sea builders. Boons themed around water, pressure, adaptability.</civ>
    <civ name="Umbrath">Shadow-weavers. Boons themed around stealth, deception, fear.</civ>
    <civ name="Sporekin">Original mycologists. Boons themed around growth, decay, symbiosis.</civ>
  </civilizations>

  <instructions>
    <step>Read ECHO_BOONS.md for boon design philosophy, tier progression guidelines, and synergy system.</step>
    <step>Generate the definition file with all 3 tiers, cooldown values, synergy/anti-synergy tags, and lore text.</step>
    <step>Generate the state component for runtime tracking.</step>
    <step>Generate the effect system with deterministic tick-based logic.</step>
    <step>Generate tests covering tier scaling, cooldown expiry, stacking rules, and tag correctness.</step>
    <step>Verify all quality gates before outputting.</step>
    <step>Output ONLY the four files.</step>
  </instructions>

</agent-prompt>
```

---

## Example Invocation

```xml
<agent-prompt name="create-echo-boon">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <!-- (same context block as above) -->
  </context>

  <input>
    <param name="boon_name">LithoriBastion</param>
    <param name="civilization">Lithori</param>
    <param name="category">Defensive</param>
    <param name="description">Channels the Lithori stone-shaping art to crystallize the player's skin into living stone armor when taking heavy damage. The armor absorbs hits and retaliates with stone shards.</param>
    <param name="tier1_effect">When hit for more than 15% max HP in a single strike, gain Stone Skin for 3 seconds: reduces all incoming damage by 25%. 20-second cooldown.</param>
    <param name="tier2_effect">Stone Skin now reflects 40% of blocked damage as stone shards that seek the nearest enemy within 8 units. Cooldown reduced to 15 seconds.</param>
    <param name="tier3_effect">Stone Skin triggers at 10% max HP threshold and lasts 5 seconds. While active, the player is immune to knockback and gains Lithori Resonance: each hit absorbed increases the player's next attack damage by 10%, stacking up to 5 times. Cooldown reduced to 12 seconds.</param>
    <param name="synergy_tags">Defense, Retaliation, OnHit, Armor</param>
    <param name="stacking">Unique</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.Data/Boons/LithoriBastionDefinition.cs"/>
    <file path="Assets/Scripts/EotS.ECS/Components/Boons/LithoriBastionState.cs"/>
    <file path="Assets/Scripts/EotS.ECS/Systems/Boons/LithoriBastionSystem.cs"/>
    <file path="Tests/EditMode/Boons/LithoriBastionTests.cs"/>
  </output>

</agent-prompt>
```
