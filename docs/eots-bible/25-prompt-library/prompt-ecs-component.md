# Prompt: Create ECS Component

## Description

Generates a deterministic ECS component as a C# value-type struct using FixedPoint64 arithmetic, with full XML documentation and an accompanying unit test file.

---

## Prompt

```xml
<agent-prompt name="create-ecs-component">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <project>Echoes of the Spore -- deterministic multiplayer action-roguelike</project>
    <engine>Unity 6</engine>
    <language>C# 12</language>
    <bible-ref>docs/eots-bible/13-tech-architecture/TECH_ARCH.md</bible-ref>
    <bible-ref>docs/eots-bible/02-core-pillars/CORE_PILLARS.md</bible-ref>

    <architecture>
      <pattern>Custom lightweight ECS (not Unity DOTS). Components are plain C# structs stored in contiguous arrays. Systems iterate over component spans.</pattern>
      <namespace>EotS.ECS.Components</namespace>
      <test-namespace>EotS.Tests.EditMode.ECS</test-namespace>
    </architecture>

    <constraints>
      <constraint id="C1">Component MUST be a C# struct (value type), NEVER a class</constraint>
      <constraint id="C2">Use FixedPoint64 for ALL numeric fields -- NEVER float, double, or decimal</constraint>
      <constraint id="C3">ZERO Unity API references in the component file (no UnityEngine, no MonoBehaviour, no Vector3)</constraint>
      <constraint id="C4">Include complete XML documentation comments on the struct and every public field</constraint>
      <constraint id="C5">Include a companion unit test file with at least 3 test methods</constraint>
      <constraint id="C6">Struct must implement IEquatable&lt;T&gt; with proper Equals and GetHashCode</constraint>
      <constraint id="C7">All fields MUST be readonly where possible for immutability</constraint>
      <constraint id="C8">Provide a static Default property returning a zero-initialized instance</constraint>
      <constraint id="C9">Struct must be [Serializable] for snapshot/rollback serialization</constraint>
      <constraint id="C10">Include a ToString() override for debug logging</constraint>
    </constraints>
  </context>

  <input>
    <param name="component_name" required="true">{{NAME}}</param>
    <param name="fields" required="true">{{FIELD_LIST}}</param>
    <param name="description" required="true">{{PURPOSE}}</param>
    <param name="category" required="false" default="Gameplay">{{CATEGORY}}</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.ECS/Components/{{CATEGORY}}/{{NAME}}.cs">
      <description>The component struct source file</description>
    </file>
    <file path="Tests/EditMode/ECS/Components/{{NAME}}Tests.cs">
      <description>NUnit unit tests for the component</description>
    </file>
  </output>

  <quality-gates>
    <gate id="QG1">Code compiles with zero warnings under C# 12 / .NET Standard 2.1</gate>
    <gate id="QG2">No float, double, or decimal literals anywhere in output</gate>
    <gate id="QG3">No using UnityEngine or any Unity namespace in component file</gate>
    <gate id="QG4">All tests pass when run via Unity Test Runner (EditMode)</gate>
    <gate id="QG5">Struct size does not exceed 64 bytes (cache-line friendly)</gate>
    <gate id="QG6">IEquatable&lt;T&gt; implemented with value equality</gate>
    <gate id="QG7">GetHashCode uses unchecked multiply-and-xor pattern</gate>
  </quality-gates>

  <instructions>
    <step>Read the TECH_ARCH.md bible reference for ECS conventions.</step>
    <step>Generate the component struct in the specified namespace with all constraints applied.</step>
    <step>Generate the test file with: (a) default-value test, (b) equality/inequality test, (c) hash-code consistency test, plus any field-specific tests.</step>
    <step>Verify all quality gates are met before outputting.</step>
    <step>Output ONLY the two files -- no explanatory prose outside the code blocks.</step>
  </instructions>

</agent-prompt>
```

---

## Example Invocation

```xml
<agent-prompt name="create-ecs-component">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <!-- (same context block as above) -->
  </context>

  <input>
    <param name="component_name">HealthComponent</param>
    <param name="fields">
      current  : FixedPoint64 -- current hit points
      max      : FixedPoint64 -- maximum hit points
      regenRate: FixedPoint64 -- HP regenerated per tick
      isDead   : bool         -- true when current reaches zero
    </param>
    <param name="description">Tracks an entity's health pool, regeneration rate, and alive/dead state for the combat simulation.</param>
    <param name="category">Combat</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.ECS/Components/Combat/HealthComponent.cs"/>
    <file path="Tests/EditMode/ECS/Components/HealthComponentTests.cs"/>
  </output>

</agent-prompt>
```
