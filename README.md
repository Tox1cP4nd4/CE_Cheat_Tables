# Cheat Engine Tables — Game Memory Research

> **Educational purposes only.** These tables were created to map game memory structures
> and understand how external tools interact with game processes.
> This knowledge directly informs anti-cheat detection strategies.

> ⚠️ **Note:** All offsets are **outdated** and no longer match current game builds.
> These tables will **not function** with patched versions of the games.
> This repository exists purely as a research reference.

---

## Overview

Collection of **Cheat Engine** (`.CT`) tables built from scratch through manual reverse engineering.
Each table represents hours of static and dynamic analysis to locate, map, and validate
memory offsets for critical game structures.

No pre-existing offset databases were used — all addresses were found manually via:
- **Pointer scanning** to survive game restarts
- **Value scanning** (exact, increased/decreased, unknown initial value)
- **Memory breakpoints** to trace write access to target values
- **Assembly inspection** to understand how the game engine reads/writes each value

---

## Games Researched

### Borderlands 3
| Value | Type | Notes |
|---|---|---|
| Player Health | Float | Located via decreased value scan |
| Player Shield | Float | Located via decreased value scan |
| Ammunition | Int/Float | Per-weapon ammo counter, offset varies by weapon slot |
| Player Position X/Y/Z | Float | World-space coordinates, used for fly hack and ESP research |
| Pitch / Yaw (camera angles) | Float | View angles — basis for aimbot angle calculation |

### Assault Cube
| Value | Type | Notes |
|---|---|---|
| Player Health | Int | Direct address, stable across sessions |
| Ammunition | Int | Per-weapon, found via entity struct traversal |
| Player Position X/Y/Z | Float | Used for teleport and fly hack research |
| Pitch / Yaw | Float | View angles for aimbot implementation |

---

## Methodology

```
1. Attach Cheat Engine to game process
2. Trigger value change in-game (take damage, shoot, move)
3. Narrow scan results → isolate candidate addresses
4. Set write breakpoint → inspect assembly to find base pointer
5. Walk pointer chain → find stable multi-level pointer
6. Validate across game restarts and map loading
7. Document offset chain in .CT table with labels
```

---

## Anti-Cheat Relevance

Building these tables provided direct insight into what anti-cheat systems monitor:

| Research Finding | Detection Implication |
|---|---|
| Health/ammo written at fixed rate | Anti-cheat flags abnormal write frequency to protected regions |
| View angles modified externally | Kernel drivers detect `WriteProcessMemory` on angle structs |
| Position teleportation | Server-side validation catches impossible movement deltas |
| Pointer chain walking | Memory scanning patterns are detectable at driver level |
| Static offsets break on update | Anti-cheat forces updates specifically to invalidate known offsets |

---

## Technical Stack

- **Tool:** Cheat Engine 7.x
- **Techniques:** Pointer scanning, write breakpoints, assembly tracing, AOB scanning
- **Games:** Borderlands 3 (Unreal Engine 4), Assault Cube

---

## Disclaimer

These tables were created for **personal research and education** only.
The goal was to understand memory layout and process interaction from the attacker's
perspective — knowledge that is directly applicable to building and improving
anti-cheat detection systems. The author does not condone cheating in online games.
