# Debug Console

## Overview

In-game debug console accessible in development builds via backtick (`) key.
Provides runtime inspection, command execution, and cheat codes for testing.

## Console UI

```
┌─────────────────────────────────────────────────┐
│ [EotS Debug Console]                         [X] │
│─────────────────────────────────────────────────│
│ > spawn enemy Sporeling 5                        │
│ Spawned 5 Sporeling entities at player position  │
│ > set infection 75                               │
│ Infection set to 75 for Player 0                 │
│ > timescale 0.5                                  │
│ Time scale set to 0.5x                           │
│─────────────────────────────────────────────────│
│ > _                                              │
└─────────────────────────────────────────────────┘
```

## Command Reference

### Player Commands

| Command | Arguments | Description |
|---------|-----------|-------------|
| `god` | — | Toggle invincibility |
| `heal` | [amount] | Heal player (default: full) |
| `kill` | — | Kill local player |
| `set infection` | level (0-100) | Set infection tier |
| `set class` | class_name | Switch class (Bind, Weaver, Striker, Warden) |
| `set hp` | value | Set current HP |
| `give boon` | boon_name [rarity] | Grant boon |
| `give echo` | amount | Grant Echoes currency |
| `teleport` | x y | Teleport to coordinates |
| `noclip` | — | Toggle collision |

### World Commands

| Command | Arguments | Description |
|---------|-----------|-------------|
| `spawn enemy` | type [count] | Spawn enemies at player pos |
| `spawn boss` | boss_name | Spawn boss in current room |
| `clear enemies` | — | Destroy all enemies |
| `goto zone` | zone_id (1-8) | Warp to zone |
| `goto room` | room_index | Warp to room in current zone |
| `goto hub` | — | Return to Canopy hub |
| `set tree stage` | stage (0-4) | Set Community Tree stage |
| `unlock all` | — | Unlock all zones/abilities |

### Network Commands

| Command | Arguments | Description |
|---------|-----------|-------------|
| `net stats` | — | Show RTT, packet loss, bandwidth |
| `net simulate` | latency_ms loss_pct | Simulate network conditions |
| `net desync` | — | Force desync for testing |
| `net rollback` | depth | Force rollback of N ticks |
| `net disconnect` | [player_id] | Simulate player disconnect |
| `net spectate` | — | Switch to spectator mode |

### Simulation Commands

| Command | Arguments | Description |
|---------|-----------|-------------|
| `timescale` | multiplier | Set simulation speed (0.1-10.0) |
| `pause` | — | Toggle simulation pause |
| `step` | [ticks] | Step N ticks while paused (default: 1) |
| `seed` | value | Set RNG seed for next zone gen |
| `determinism check` | ticks | Run determinism test for N ticks |

### Profiling Commands

| Command | Arguments | Description |
|---------|-----------|-------------|
| `perf toggle` | — | Toggle performance overlay |
| `perf log` | [seconds] | Log perf data for N seconds |
| `mem snapshot` | — | Capture memory snapshot |
| `entity count` | — | Show entity count by archetype |
| `system times` | — | Show per-system tick times |

### Rendering Commands

| Command | Arguments | Description |
|---------|-----------|-------------|
| `show hitboxes` | — | Toggle hitbox visualization |
| `show collision` | — | Toggle collision grid overlay |
| `show nav` | — | Toggle navigation grid |
| `show spatial` | — | Toggle spatial hash grid |
| `wireframe` | — | Toggle wireframe rendering |
| `screenshot` | — | Capture screenshot to disk |

### Audio Commands

| Command | Arguments | Description |
|---------|-----------|-------------|
| `mute` | [bus_name] | Mute bus (or all) |
| `audio voices` | — | Show active voice count |
| `play sfx` | event_path | Play FMOD event |
| `spore talk test` | tier (0-100) | Test Spore-Talk DSP at tier |

## Command Registration

```csharp
public class DebugConsole : MonoBehaviour
{
    private Dictionary<string, Action<string[]>> _commands = new();

    public void RegisterCommand(string name, Action<string[]> handler, string help)
    {
        _commands[name.ToLower()] = handler;
        _helpStrings[name.ToLower()] = help;
    }

    private void ExecuteCommand(string input)
    {
        var parts = input.Split(' ');
        var cmd = parts[0].ToLower();
        var args = parts[1..];
        
        if (_commands.TryGetValue(cmd, out var handler))
            handler(args);
        else
            Log($"Unknown command: {cmd}. Type 'help' for list.");
    }
}
```

## Access Control

| Build Type | Console Access | Network Cheats | Logging |
|-----------|---------------|---------------|---------|
| Editor | Full | Full | Verbose |
| Development Build | Full | Full | Verbose |
| QA Build | Limited (no spawns) | Read-only stats | Standard |
| Release Build | Disabled | Disabled | Minimal |

## Console Log Levels

| Level | Color | Example |
|-------|-------|---------|
| Info | White | "Spawned 5 Sporeling entities" |
| Warning | Yellow | "Entity count exceeding budget (4000/4096)" |
| Error | Red | "Failed to parse command arguments" |
| System | Cyan | "Debug console initialized" |
| Network | Green | "RTT: 45ms, Loss: 0.5%" |

## History and Autocomplete

| Feature | Behavior |
|---------|----------|
| History | Up/Down arrows cycle through last 50 commands |
| Autocomplete | Tab completes command names |
| Argument hints | Shows expected arguments after command name |
| Persistent history | Saved to `{persistentDataPath}/console_history.txt` |
| Multi-line output | Scrollable output buffer (500 lines) |
