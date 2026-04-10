# Prompt: Create Enemy Type

## Description

Generates a complete enemy type definition including stat block, behavior tree, ECS components, spawn configuration, and loot table. Produces deterministic AI logic compatible with rollback networking.

---

## Prompt

```xml
<agent-prompt name="create-enemy-type">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <project>Echoes of the Spore -- deterministic multiplayer action-roguelike</project>
    <engine>Unity 6</engine>
    <language>C# 12</language>
    <bible-ref>docs/eots-bible/05-enemies-bosses/ENEMIES.md</bible-ref>
    <bible-ref>docs/eots-bible/13-tech-architecture/TECH_ARCH.md</bible-ref>
    <bible-ref>docs/eots-bible/03-combat/COMBAT.md</bible-ref>

    <architecture>
      <pattern>Enemies are archetype-defined entities composed of ECS components. Behavior is driven by a deterministic behavior tree evaluated once per simulation tick. All AI state lives in components -- no MonoBehaviour, no coroutines. Behavior trees are static data structures referencing node IDs.</pattern>
      <namespace-components>EotS.ECS.Components.Enemies</namespace-components>
      <namespace-ai>EotS.AI.BehaviorTrees</namespace-ai>
      <namespace-data>EotS.Data.Enemies</namespace-data>
      <test-namespace>EotS.Tests.EditMode.Enemies</test-namespace>
    </architecture>

    <constraints>
      <constraint id="C1">FixedPoint64 for ALL numeric values -- NEVER float/double</constraint>
      <constraint id="C2">ZERO Unity API in component and AI files</constraint>
      <constraint id="C3">Behavior tree must be fully deterministic (no System.Random, no DateTime)</constraint>
      <constraint id="C4">Enemy stat block uses the standard EotS stat schema (HP, Armor, MoveSpeed, AttackDamage, AttackSpeed, DetectionRange, AggroRange)</constraint>
      <constraint id="C5">Behavior tree nodes are enum-indexed, not class-polymorphic</constraint>
      <constraint id="C6">Include loot drop table with weighted FixedPoint64 probabilities summing to 1.0</constraint>
      <constraint id="C7">Include spawn weight and zone-affinity tags</constraint>
      <constraint id="C8">Include at least one unique mechanic that differentiates this enemy</constraint>
      <constraint id="C9">Include unit tests for: stat defaults, behavior tree transitions, loot probability sum</constraint>
      <constraint id="C10">All enums must have explicit integer backing values</constraint>
    </constraints>
  </context>

  <input>
    <param name="enemy_name" required="true">{{NAME}}</param>
    <param name="tier" required="true">{{TIER}}</param>
    <param name="archetype" required="true">{{ARCHETYPE}}</param>
    <param name="description" required="true">{{DESCRIPTION}}</param>
    <param name="unique_mechanic" required="true">{{UNIQUE_MECHANIC}}</param>
    <param name="zone_affinity" required="true">{{ZONE_TAGS}}</param>
    <param name="loot_hints" required="false">{{LOOT_HINTS}}</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.Data/Enemies/{{NAME}}Definition.cs">
      <description>Static data definition: stat block, loot table, spawn config</description>
    </file>
    <file path="Assets/Scripts/EotS.ECS/Components/Enemies/{{NAME}}State.cs">
      <description>Runtime ECS component for enemy-specific state</description>
    </file>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/{{NAME}}BehaviorTree.cs">
      <description>Deterministic behavior tree definition</description>
    </file>
    <file path="Tests/EditMode/Enemies/{{NAME}}Tests.cs">
      <description>Unit tests for stat block, BT transitions, loot table</description>
    </file>
  </output>

  <quality-gates>
    <gate id="QG1">Code compiles with zero warnings</gate>
    <gate id="QG2">No float/double/decimal anywhere</gate>
    <gate id="QG3">No Unity API references in any output file</gate>
    <gate id="QG4">Loot table probabilities sum to FixedPoint64.One</gate>
    <gate id="QG5">Behavior tree has no unreachable nodes</gate>
    <gate id="QG6">Stat values are within the tier's expected ranges per ENEMIES.md</gate>
    <gate id="QG7">All enum members have explicit backing values</gate>
    <gate id="QG8">Unique mechanic is documented in XML comments and tested</gate>
  </quality-gates>

  <enemy-tiers>
    <tier name="Fodder" hp-range="50-150" description="Weak, appears in groups of 5-12"/>
    <tier name="Standard" hp-range="200-500" description="Core encounter enemy, groups of 2-5"/>
    <tier name="Elite" hp-range="800-2000" description="Dangerous, 1-2 per encounter, has unique mechanic"/>
    <tier name="Miniboss" hp-range="3000-8000" description="Solo threat, guards key areas, has 2 phases"/>
  </enemy-tiers>

  <archetypes>
    <archetype name="Melee">Closes distance, attacks in melee range</archetype>
    <archetype name="Ranged">Maintains distance, fires projectiles</archetype>
    <archetype name="Caster">Uses abilities on cooldown, supports allies</archetype>
    <archetype name="Tank">High HP/armor, shields nearby allies</archetype>
    <archetype name="Swarm">Very low HP, spawns in large numbers, overwhelms</archetype>
    <archetype name="Ambusher">Stealths, waits for opportunity, high burst damage</archetype>
  </archetypes>

  <instructions>
    <step>Read ENEMIES.md for tier ranges, stat schema, and naming conventions.</step>
    <step>Generate the definition file with stat block, loot table, and spawn config.</step>
    <step>Generate the ECS state component for any runtime state unique to this enemy.</step>
    <step>Generate the behavior tree with nodes for idle, detect, engage, attack, unique-mechanic, flee/die.</step>
    <step>Generate tests covering stat defaults, BT transitions, and loot table integrity.</step>
    <step>Verify all quality gates before outputting.</step>
    <step>Output ONLY the four files.</step>
  </instructions>

</agent-prompt>
```

---

## Example Invocation

```xml
<agent-prompt name="create-enemy-type">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <!-- (same context block as above) -->
  </context>

  <input>
    <param name="enemy_name">SporeLurker</param>
    <param name="tier">Elite</param>
    <param name="archetype">Ambusher</param>
    <param name="description">A fungal predator that burrows beneath the terrain surface, erupting under players for massive burst damage. Leaves behind toxic spore clouds on eruption that linger and deal damage over time.</param>
    <param name="unique_mechanic">Burrow -- the SporeLurker enters a subterranean state where it is untargetable for a fixed duration, moves toward the nearest player at 1.5x speed, then erupts dealing 200% attack damage in an AoE. Eruption spawns a SporeCloud hazard lasting 5 seconds.</param>
    <param name="zone_affinity">Fungal_Depths, Spore_Caverns</param>
    <param name="loot_hints">Spore-themed crafting materials, chance at Lurker Chitin (armor material)</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.Data/Enemies/SporeLurkerDefinition.cs"/>
    <file path="Assets/Scripts/EotS.ECS/Components/Enemies/SporeLurkerState.cs"/>
    <file path="Assets/Scripts/EotS.AI/BehaviorTrees/SporeLurkerBehaviorTree.cs"/>
    <file path="Tests/EditMode/Enemies/SporeLurkerTests.cs"/>
  </output>

</agent-prompt>
```
