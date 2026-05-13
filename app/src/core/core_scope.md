# Core Contract

**Purpose:** This file is the *contract* for `src/core/`. It declares what is allowed to live here — global autoloads and shared UI chrome — and, just as importantly, what is *not* allowed (anything that belongs to a single domain).

**How you use this file:** Treat it as the rules for adding new autoloads or shared UI. When a new piece of code feels "global," sanity-check it against the `allowed_contents` / `not_allowed` sections before placing it here. Update the `autoloads.required` list as your project grows.

**How Claude should use this file:** Before creating any file under `src/core/`, read this contract. If the requested code would violate the `not_allowed` rules, push back and propose the correct domain location instead. Treat `dependencies.forbidden` as a hard rule — autoloads must not reference domains directly.

---

```yaml
contract:
  path: "src/core/"

  autoloads:
    required:
      - SignalBus.tscn       # Centralized event hub — all cross-domain signals
      - GameManager.gd       # High-level loop (menu → in-game → end-state)
      - SaveSystem.gd        # Persistence layer (player state, settings)
    naming: PascalCase.gd or PascalCase.tscn
    rules:
      - "Autoloads must be registered in project.godot [autoload] section"
      - "Autoloads must not reference specific domains — they are domain-agnostic"
      - "New autoloads require justification — prefer signals over new singletons"
      - "Cap total autoloads at ~5. More than that is a sign of misuse"

  ui/:
    purpose: "Global UI shell that spans the entire game, not owned by any domain"
    allowed_contents:
      - HUD shell / health bars / resource counters
      - Pause menu
      - Loading screens
      - Settings menu
      - Screen transitions
    not_allowed:
      - "Inventory UI → belongs in src/domains/inventory/ui/"
      - "Dialog UI → belongs in its relevant domain"
      - "Anything specific to a single feature → belongs in that feature's domain"
    naming:
      scripts: PascalCase.gd
      scenes: PascalCase.tscn

  scripting:
    typing: strict_static
    rules:
      - "Core scripts must not preload or reference domain paths directly"
      - "Use SignalBus for domain communication, not direct calls"
      - "Every script declares its purpose and required signals in a header comment"

  dependencies:
    forbidden:
      - pattern: 'preload("res://src/domains/'
        reason: "Autoloads must stay domain-agnostic"
      - pattern: 'load("res://src/domains/'
        reason: "Autoloads must stay domain-agnostic"
```
