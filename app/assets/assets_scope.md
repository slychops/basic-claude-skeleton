# Assets Contract

**Purpose:** This file is the *contract* for `assets/`. It defines the layout, formats, and naming rules for all imported visual, audio, and animation source files. Following it keeps the asset library searchable and consistent as it scales from dozens to thousands of files.

**How you use this file:** When you import a new asset, check it against the `structure` rules below before committing. Add a new entry under `subdirectories_by` if you start a new asset category (e.g. `shaders/`, `particles/`). Source files (PSDs, Aseprite projects, Blender scenes) belong in a top-level `art_source/` folder outside `app/` — not here.

**How Claude should use this file:** Before importing or referencing an asset, read this contract. Verify the path, format, and naming match the rules. When the user asks to "add a sprite" or "import sound," confirm the target subdirectory and naming before writing any `preload()` paths.

---

```yaml
contract:
  path: "assets/"

  structure:
    images/:
      formats: [.png]
      naming: snake_case          # e.g., player_idle_sheet.png
      subdirectories_by: domain   # images/player/, images/enemies/, images/ui/
    fonts/:
      formats: [.ttf, .otf, .tres]
      naming: snake_case
    audio/:
      formats: [.ogg, .wav]
      naming: snake_case
      subdirectories:
        - sfx/                    # Short one-shot effects
        - ambience/               # Looping environmental audio
        - music/                  # Soundtrack tracks
      rules:
        - ".ogg for music and ambience (compressed, loopable)"
        - ".wav for sfx (low-latency, uncompressed)"
    animation/:
      formats: [.json, .atlas, .png, .tres]   # Spine, AnimationPlayer resources, etc.
      naming: snake_case
      subdirectories_by: entity   # animation/player/, animation/enemy_skeleton/
    themes/:
      formats: [.tres]
      naming: snake_case

  global_rules:
    - "No spaces in filenames"
    - "No uppercase in filenames"
    - "Prefix shared/generic assets with 'common_'"
    - "Godot .import files are auto-generated — never commit manual edits to them"
    - "Source files (PSD, Aseprite, Blender, etc.) belong in a top-level art_source/ outside app/"
    - "Reference an asset's path through a constant or @export var — never inline strings in hot paths"
```
