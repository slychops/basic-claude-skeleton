# Technical Architecture

**Purpose:** This is the engineering source of truth. Architectural patterns, autoload contracts, performance rules, and deprecated-API lists live here. It is the document Claude reads before adding a new system, refactoring an existing one, or making a performance decision.

**How you use this doc:** Fill in the project-specific math and conventions as they solidify. Add new sections for any architectural concept that affects more than one domain (state machines, save format, networking, etc.). Keep it terse — link out to in-code examples rather than pasting long snippets.

**How Claude should use this doc:** Load it for any task that touches autoloads, scene composition, physics, performance, or cross-domain coupling. Treat the *Development Standards* and *Deprecated APIs* sections as hard rules. When a proposed change conflicts with a rule here, surface the conflict before writing code.

---

## 1. Core Philosophy: Scene-Based Composition

We avoid deep class inheritance. We use **Composition over Inheritance** via Godot Scenes (`.tscn`).

- **The Rule:** If a feature has state, physical presence, or visual logic, it must be a standalone `.tscn` file.
- **The Logic:** Actors (Player/NPCs) are "Empty Shells" populated by child-scene components.
- **The Benefit:** Decouples logic so AI agents can modify a `HealthComponent.tscn` without risking the `Player.tscn` root logic.

## 2. Project Structure

The Godot engine treats `app/` as its project root (`res://`). Code is organized by feature domain — each domain owns its components, scenes, and scripts.

```
app/                          # Godot Project Root (res://)
├── project.godot
├── src/
│   ├── core/                 # Autoloads (SignalBus, GameManager, SaveSystem)
│   └── domains/              # Self-contained feature modules
├── assets/                   # Sprites, audio, textures, animation
├── data/                     # .tres Resources (items, configs, NPCs)
└── test/                     # gdUnit4 test suites
```

Cross-domain communication happens through `SignalBus` or shared Resources — never direct references between domains. See `app/src/domains/domains_scope.md` for the hard rules.

## 3. Global Infrastructure (Autoloads)

Minimal, high-impact singletons that act as the project's "nervous system." Register each one in `project.godot` under `[autoload]`.

- **`SignalBus.tscn`** — Centralized event hub. All cross-domain signals are emitted here.
- **`GameManager.gd`** — High-level loop: menu → in-game → end-state.
- **`SaveSystem.gd`** — Serializes persistent state.

Cap the autoload count at ~5. Each additional autoload is global coupling — justify it explicitly or use signals instead.

## 4. Parallel State Machines

Use **separate state machines** for orthogonal concerns (e.g., movement vs combat) to prevent combinatorial explosion. This maps cleanly to animation tracks — Track 0 for locomotion, Track 1 for upper-body actions.

Each state follows the pattern:

```gdscript
func enter() -> void: ...
func exit() -> void: ...
func physics_update(delta: float) -> void: ...
```

Replace the bodies with whatever your state needs; keep the signature.

## 5. Data-Driven Configuration

Use Godot `.tres` (Resources) for any data that designers or AI agents might tweak: item stats, enemy archetypes, level metadata, balancing tables.

- Items inherit from a single `BaseItem.tres` schema.
- Hot-paths read `@export var` values, not hardcoded constants.
- Resources live under `app/data/`, grouped by domain when count grows.

## 6. Development Standards

- **Type Safety:** All GDScript must use static typing (e.g., `var health: int = 10`).
- **Base Resolution:** 1920×1080 with "Viewport" stretch mode (override per project if needed).
- **Scene-First:** Modify in `.tscn` properties before writing code overrides.
- **Documentation Headers:** Every script declares its purpose and required signals in a header comment.
- **Testing:** gdUnit4 for unit and integration tests. Run headless:
  ```bash
  godot --headless -s res://addons/gdUnit4/bin/gdUnit4Cli.gd -a res://test/
  ```
- **Red/Green TDD:** All new code follows the Red/Green/Refactor cycle. Write a failing test first.

## 7. Godot 4.x API Standards

Avoid deprecated APIs — all code must use current Godot 4.x equivalents:

| Deprecated | Use Instead | Notes |
|-----------|-------------|-------|
| `TileMap` | `TileMapLayer` | Separate layer nodes for background, collision, foreground |
| `ParallaxBackground` | `Parallax2D` | Far layers at `0.1` motion_scale, near at `0.6+` |
| `RigidBody2D` (for characters) | `CharacterBody2D` | Full control over jump curves, acceleration, coyote time |
| `connect("signal", self, "method")` | `signal.connect(callable)` | Godot 4 syntax |

## 8. Performance Patterns

- **Chunk large maps** — separate `TileMapLayer` nodes loaded/unloaded by camera proximity.
- **Object-pool** high-frequency objects (bullets, particles, collectibles).
- **Gate `_process` on visibility** — don't tick what's off-screen.
- **Cache `@onready` references** — never `get_node()` / `$` inside `_process()`.
- **No `preload()` inside loops** — hoist it to the top of the file.
- **Reuse `StyleBoxFlat`** — don't recreate each frame.

## 9. Testing & CI

- All public methods on autoloads and shared components ship with at least one unit test.
- New features land behind a Red/Green commit pair (failing test, then implementation).
- CI runs the gdUnit4 suite headless before any PR can be merged (see `.claude/skills/commit/SKILL.md`).
