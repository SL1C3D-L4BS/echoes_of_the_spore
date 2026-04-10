# Prompt: Design Boss Fight

## Description

Generates a complete 3-phase boss encounter including stat scaling per phase, attack patterns, transition triggers, arena hazards, loot table, and deterministic AI. Produces all ECS data, behavior trees, and tests.

---

## Prompt

```xml
<agent-prompt name="design-boss-fight">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <project>Echoes of the Spore -- deterministic multiplayer action-roguelike</project>
    <engine>Unity 6</engine>
    <language>C# 12</language>
    <bible-ref>docs/eots-bible/05-enemies-bosses/ENEMIES.md</bible-ref>
    <bible-ref>docs/eots-bible/05-enemies-bosses/BOSSES.md</bible-ref>
    <bible-ref>docs/eots-bible/03-combat/COMBAT.md</bible-ref>
    <bible-ref>docs/eots-bible/07-zones/ZONES.md</bible-ref>
    <bible-ref>docs/eots-bible/13-tech-architecture/TECH_ARCH.md</bible-ref>

    <architecture>
      <pattern>Bosses are high-archetype entities with a BossPhaseController component that drives phase transitions. Each phase has its own behavior tree and stat modifiers. Arena hazards are spawned entities with their own components. Boss encounters are fully deterministic for rollback networking and replay.</pattern>
      <namespace-data>EotS.Data.Bosses</namespace-data>
      <namespace-components>EotS.ECS.Components.Bosses</namespace-components>
      <namespace-ai>EotS.AI.BehaviorTrees.Bosses</namespace-ai>
      <namespace-arena>EotS.Data.Arenas</namespace-arena>
      <test-namespace>EotS.Tests.EditMode.Bosses</test-namespace>
    </architecture>

    <constraints>
      <constraint id="C1">FixedPoint64 for ALL numeric values -- NEVER float/double</constraint>
      <constraint id="C2">ZERO Unity API in data, component, and AI files</constraint>
      <constraint id="C3">Boss MUST have exactly 3 phases with clear HP-threshold transitions</constraint>
      <constraint id="C4">Each phase must introduce at least one new attack or mechanic</constraint>
      <constraint id="C5">Phase transitions must be interruptible by rollback (state-driven, not event-driven)</constraint>
      <constraint id="C6">Arena hazards must be entity-spawned, not hard-coded effects</constraint>
      <constraint id="C7">Loot table must include a guaranteed unique drop and weighted random drops</constraint>
      <constraint id="C8">Include enrage timer mechanic if fight exceeds expected duration</constraint>
      <constraint id="C9">Scale stat block for 1-4 player count using a FixedPoint64 scaling formula</constraint>
      <constraint id="C10">All behavior tree nodes are deterministic and enum-indexed</constraint>
    </constraints>
  </context>

  <input>
    <param name="boss_name" required="true">{{NAME}}</param>
    <param name="zone" required="true">{{ZONE}}</param>
    <param name="tier" required="true">{{TIER}}</param>
    <param name="theme" required="true">{{THEME}}</param>
    <param name="description" required="true">{{DESCRIPTION}}</param>
    <param name="phase1_mechanic" required="true">{{PHASE1_MECHANIC}}</param>
    <param name="phase2_mechanic" required="true">{{PHASE2_MECHANIC}}</param>
    <param name="phase3_mechanic" required="true">{{PHASE3_MECHANIC}}</param>
    <param name="arena_description" required="true">{{ARENA}}</param>
    <param name="unique_loot" required="true">{{UNIQUE_DROP}}</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.Data/Bosses/{{NAME}}Definition.cs">
      <description>Static boss data: stat block per phase, scaling formula, enrage config, loot table</description>
    </file>
    <file path="Assets/Scripts/EotS.ECS/Components/Bosses/{{NAME}}State.cs">
      <description>Runtime ECS component: phase index, phase timer, mechanic-specific state</description>
    </file>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/Bosses/{{NAME}}Phase1BT.cs">
      <description>Phase 1 behavior tree</description>
    </file>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/Bosses/{{NAME}}Phase2BT.cs">
      <description>Phase 2 behavior tree</description>
    </file>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/Bosses/{{NAME}}Phase3BT.cs">
      <description>Phase 3 behavior tree</description>
    </file>
    <file path="Assets/Scripts/EotS.Data/Arenas/{{NAME}}ArenaDefinition.cs">
      <description>Arena layout, hazard spawn points, hazard definitions</description>
    </file>
    <file path="Tests/EditMode/Bosses/{{NAME}}Tests.cs">
      <description>Unit tests for phase transitions, stat scaling, loot table, enrage</description>
    </file>
  </output>

  <quality-gates>
    <gate id="QG1">Code compiles with zero warnings</gate>
    <gate id="QG2">No float/double/decimal anywhere</gate>
    <gate id="QG3">No Unity API references in any output file</gate>
    <gate id="QG4">Phase transitions occur at correct HP thresholds (66%, 33% default)</gate>
    <gate id="QG5">Player-count scaling formula produces valid stat values for 1-4 players</gate>
    <gate id="QG6">Loot table probabilities sum to FixedPoint64.One per category</gate>
    <gate id="QG7">Enrage timer triggers stat boost after configured duration</gate>
    <gate id="QG8">Each phase BT has no unreachable nodes</gate>
    <gate id="QG9">All three phases introduce distinct mechanics (no duplicates)</gate>
    <gate id="QG10">Arena hazards are entity definitions, not inline effects</gate>
  </quality-gates>

  <boss-tiers>
    <tier name="Zone" hp="15000-30000" description="End-of-zone boss, 3 phases, 1 unique drop"/>
    <tier name="World" hp="50000-100000" description="Overworld boss, 3 phases, multiple unique drops, requires specific strategy"/>
    <tier name="Final" hp="150000-300000" description="Endgame boss, 3+ phases, complex mechanics, pinnacle loot"/>
  </boss-tiers>

  <phase-template>
    <phase index="1" hp-range="100%-66%" description="Introduction phase. Teaches the boss's core pattern. Moderate damage, predictable timing."/>
    <phase index="2" hp-range="66%-33%" description="Escalation phase. Adds a new mechanic, increases tempo. Arena hazards activate."/>
    <phase index="3" hp-range="33%-0%" description="Desperation phase. Most dangerous. Combines all mechanics, enrage threat. DPS check."/>
  </phase-template>

  <instructions>
    <step>Read BOSSES.md and ENEMIES.md for boss design philosophy, stat ranges, and loot conventions.</step>
    <step>Generate the definition file with per-phase stat blocks, player-count scaling, enrage config, and loot table.</step>
    <step>Generate the state component with phase tracking and mechanic-specific fields.</step>
    <step>Generate one behavior tree file per phase, each introducing the specified mechanic.</step>
    <step>Generate the arena definition with hazard entity specs and spawn geometry.</step>
    <step>Generate tests covering: phase transitions, scaling for 1/2/3/4 players, loot integrity, enrage trigger.</step>
    <step>Verify all quality gates before outputting.</step>
    <step>Output ONLY the seven files.</step>
  </instructions>

</agent-prompt>
```

---

## Example Invocation

```xml
<agent-prompt name="design-boss-fight">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <!-- (same context block as above) -->
  </context>

  <input>
    <param name="boss_name">QueenMycelium</param>
    <param name="zone">Fungal_Depths</param>
    <param name="tier">Zone</param>
    <param name="theme">Parasitic fungal intelligence that has consumed an ancient guardian, puppeting its massive stone body with fungal tendrils.</param>
    <param name="description">A colossal stone golem overgrown with bioluminescent fungal networks. The true boss is the mycelium colony controlling the host body. Players must damage the exposed fungal cores while avoiding the golem's crushing attacks.</param>
    <param name="phase1_mechanic">Tremor Slam -- the golem raises both fists and slams the ground, creating radial shockwaves that players must jump over. Exposes a fungal core on the golem's back for 4 seconds after each slam.</param>
    <param name="phase2_mechanic">Spore Sentinels -- the queen detaches three Spore Sentinel adds that orbit the arena and periodically fire tracking spore bolts. Killing a Sentinel stuns the golem for 3 seconds. New Sentinels respawn after 20 seconds.</param>
    <param name="phase3_mechanic">Mycelium Network -- fungal roots spread across the arena floor in expanding patterns. Standing on roots drains player health and heals the boss. Safe zones shrink over time. The golem attacks faster with each root cluster active.</param>
    <param name="arena_description">Circular cavern, 40-unit radius. Four destructible fungal pillars at cardinal positions provide temporary cover. Central pit (10-unit radius) is hazard terrain dealing damage. Bioluminescent ceiling provides ambient light with pulsing intensity tied to boss phase.</param>
    <param name="unique_loot">Crown of the Mycelium -- headpiece that grants the wearer passive health regeneration that scales with the number of nearby enemies (the parasitic network now serves you)</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.Data/Bosses/QueenMyceliumDefinition.cs"/>
    <file path="Assets/Scripts/EotS.ECS/Components/Bosses/QueenMyceliumState.cs"/>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/Bosses/QueenMyceliumPhase1BT.cs"/>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/Bosses/QueenMyceliumPhase2BT.cs"/>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/Bosses/QueenMyceliumPhase3BT.cs"/>
    <file path="Assets/Scripts/EotS.Data/Arenas/QueenMyceliumArenaDefinition.cs"/>
    <file path="Tests/EditMode/Bosses/QueenMyceliumTests.cs"/>
  </output>

</agent-prompt>
```
