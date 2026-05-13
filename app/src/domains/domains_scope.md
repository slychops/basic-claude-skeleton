# Domain Contract

**Purpose:** This file is the *contract* for `src/domains/`. Each subdirectory under it is a self-contained feature module — a "Lego brick" that owns its scenes, scripts, components, and resources. Cross-domain dependencies are explicitly forbidden so the codebase stays decoupled and AI agents can modify one domain without breaking others.

**How you use this file:** When you add a new feature, decide whether it deserves its own domain (a self-contained system with state and behaviour) or whether it should live inside an existing one. Add new entries to `known_domains` as you commit them. Treat the per-domain structure as a starting layout — not every domain needs all subdirectories.

**How Claude should use this file:** Before creating any file under `src/domains/`, read this contract. Reject any code that imports from another domain directly (`dependencies.forbidden`). Surface a `SignalBus` route or shared `Resource` instead. When asked to "add feature X," first decide whether it is a new domain or an addition to an existing one, and confirm with the user before scaffolding.

---

```yaml
contract:
  path: "src/domains/"

  known_domains: []   # populate as domains are created, e.g. [player, enemies, ui_menu]

  per_domain_structure:
    allowed_subdirectories:
      - components/    # Reusable .tscn components (HealthComponent, HitboxComponent)
      - states/        # State machine states (IdleState, RunState)
      - scenes/        # Composed entity scenes (Player.tscn)
      - scripts/       # Supporting GDScript files
      - ui/            # Domain-specific UI
      - resources/     # Domain-specific .tres definitions
    rule: "Use only the subdirectories the domain actually needs. Don't pre-create empty folders."

  naming:
    directories: snake_case
    scripts: PascalCase.gd
    scenes: PascalCase.tscn
    resources: snake_case.tres

  dependencies:
    forbidden:
      - pattern: 'preload("res://src/domains/{other}/'
        reason: "Direct cross-domain imports create hidden coupling"
      - pattern: 'load("res://src/domains/{other}/'
        reason: "Direct cross-domain imports create hidden coupling"
    allowed_cross_domain:
      - SignalBus         # Autoload event hub for decoupled communication
      - Shared Resources  # .tres files in data/ for shared definitions
      - Core autoloads    # GameManager, SaveSystem

  scripting:
    typing: strict_static  # var health: int = 10, never untyped
    required_header: |
      ## [Brief description of purpose]
      ## Required signals: [list or "None"]
      ## Dependencies: [list autoloads/resources used or "None"]
```
