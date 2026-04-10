# Prompt: Create ECS System

## Description

Generates a deterministic ECS system that processes component data in a pure, side-effect-free update loop. Systems never touch Unity API -- they operate on component spans and emit events via command buffers.

---

## Prompt

```xml
<agent-prompt name="create-ecs-system">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <project>Echoes of the Spore -- deterministic multiplayer action-roguelike</project>
    <engine>Unity 6</engine>
    <language>C# 12</language>
    <bible-ref>docs/eots-bible/13-tech-architecture/TECH_ARCH.md</bible-ref>
    <bible-ref>docs/eots-bible/14-networking/NETWORKING.md</bible-ref>

    <architecture>
      <pattern>Systems are static classes with a single public static void Execute method. They receive ReadOnlySpan or Span of component data plus a FixedPoint64 deltaTime. Side effects go through a CommandBuffer. Systems are registered in a SystemSchedule that defines execution order and phase (PrePhysics, Physics, PostPhysics, Render).</pattern>
      <namespace>EotS.ECS.Systems</namespace>
      <test-namespace>EotS.Tests.EditMode.ECS</test-namespace>
    </architecture>

    <constraints>
      <constraint id="C1">System MUST be a static class with no instance state</constraint>
      <constraint id="C2">Execute method signature: public static void Execute(Span&lt;T&gt; components, in SimulationContext ctx)</constraint>
      <constraint id="C3">FixedPoint64 for ALL arithmetic -- NEVER float, double, or decimal</constraint>
      <constraint id="C4">ZERO Unity API references (no UnityEngine, no MonoBehaviour, no Time.deltaTime)</constraint>
      <constraint id="C5">All mutations go through CommandBuffer for rollback compatibility</constraint>
      <constraint id="C6">Must be fully deterministic: same input produces same output on all platforms</constraint>
      <constraint id="C7">Include XML documentation on the class and Execute method</constraint>
      <constraint id="C8">Include a [SystemPhase] attribute specifying execution phase</constraint>
      <constraint id="C9">Include a [SystemDependency] attribute listing required component types</constraint>
      <constraint id="C10">Include unit test file with at least 4 tests: (a) basic execution, (b) edge case, (c) determinism verification, (d) empty-span no-op</constraint>
    </constraints>
  </context>

  <input>
    <param name="system_name" required="true">{{NAME}}</param>
    <param name="processed_components" required="true">{{COMPONENT_LIST}}</param>
    <param name="phase" required="true">{{PHASE}}</param>
    <param name="description" required="true">{{PURPOSE}}</param>
    <param name="events_emitted" required="false">{{EVENT_LIST}}</param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.ECS/Systems/{{NAME}}.cs">
      <description>The system static class</description>
    </file>
    <file path="Tests/EditMode/ECS/Systems/{{NAME}}Tests.cs">
      <description>NUnit unit tests for the system</description>
    </file>
  </output>

  <quality-gates>
    <gate id="QG1">Code compiles with zero warnings under C# 12 / .NET Standard 2.1</gate>
    <gate id="QG2">No float, double, or decimal anywhere in output</gate>
    <gate id="QG3">No Unity namespace references in system file</gate>
    <gate id="QG4">Determinism test: run Execute twice with identical input, assert byte-identical output</gate>
    <gate id="QG5">Empty-span test: Execute with Span&lt;T&gt;.Empty must not throw</gate>
    <gate id="QG6">All mutations routed through CommandBuffer, never direct field writes on components from other archetypes</gate>
    <gate id="QG7">System class is static with no static fields (no hidden state)</gate>
  </quality-gates>

  <instructions>
    <step>Read the TECH_ARCH.md and NETWORKING.md bible references for system conventions and rollback requirements.</step>
    <step>Generate the system class with the Execute method signature, phase attribute, and dependency attribute.</step>
    <step>Implement the core logic loop over the component span using FixedPoint64 arithmetic.</step>
    <step>Emit any specified events through the CommandBuffer on ctx.</step>
    <step>Generate the test file covering all four required test scenarios.</step>
    <step>Verify all quality gates before outputting.</step>
    <step>Output ONLY the two files.</step>
  </instructions>

</agent-prompt>
```

---

## Example Invocation

```xml
<agent-prompt name="create-ecs-system">

  <role>Lead Sovereign Architect -- Echoes of the Spore (Unity 6 / C#)</role>

  <context>
    <!-- (same context block as above) -->
  </context>

  <input>
    <param name="system_name">HealthRegenSystem</param>
    <param name="processed_components">
      HealthComponent (read-write) -- current, max, regenRate, isDead
    </param>
    <param name="phase">PostPhysics</param>
    <param name="description">Each tick, applies regenRate to current health clamped to max. Skips entities where isDead is true. Emits EntityHealedEvent when health increases.</param>
    <param name="events_emitted">
      EntityHealedEvent -- entityId, amountHealed
    </param>
  </input>

  <output>
    <file path="Assets/Scripts/EotS.ECS/Systems/HealthRegenSystem.cs"/>
    <file path="Tests/EditMode/ECS/Systems/HealthRegenSystemTests.cs"/>
  </output>

</agent-prompt>
```
