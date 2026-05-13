# Addons

Godot reads third-party plugins from this folder. Nothing is checked in by
default — install whatever the project needs and commit the resulting
subdirectory.

## Expected: gdUnit4

`CLAUDE.md` and `TECHNICAL_MASTER.md` reference gdUnit4 as the test runner.
Install it before running the test suite:

- **Asset Library (recommended):** open the Godot editor → AssetLib tab →
  search "gdUnit4" → Download → Install. Godot drops it at
  `app/addons/gdUnit4/`.
- **Manual:** clone <https://github.com/MikeSchulze/gdUnit4> into
  `app/addons/gdUnit4/`.

Enable it under Project → Project Settings → Plugins. The headless test
command in `CLAUDE.md` (`godot --headless -s
res://addons/gdUnit4/bin/gdUnit4Cli.gd -a res://test/`) assumes this path.

If you swap test frameworks (GUT, etc.), update this file and the test
command in `CLAUDE.md` to match.
