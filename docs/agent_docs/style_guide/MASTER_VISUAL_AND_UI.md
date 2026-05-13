# Master Visual & UI Guide

**Purpose:** Defines the visual identity — palettes, lighting logic, VFX vocabulary, camera behaviour, UI grids, iconography. Everything visible in the game traces back to a rule in this document.

**How you use this doc:** Fill in your project's actual palette, lighting rules, and UI grids. Replace every placeholder with your real values. Reference this doc from the PRD's Visual section so they stay consistent.

**How Claude should use this doc:** Load it for any UI, art, shader, lighting, or camera task. Cite specific sections when reviewing visuals or proposing new ones. Reject changes that violate the palette ratios or the UI grid without explicit user override.

---

## 1. Visual Identity in One Sentence

> *Replace with the single sentence that captures the look (e.g. "Brutalist industrial decay meets ancient technology that glows like dawn light").*

## 2. Palettes

Define 1-3 named palettes. Every visual decision pulls from one.

### Primary Palette: *[Name]*

| Token | Hex | Use |
|---|---|---|
| `--bg-base` | `#000000` | Backgrounds, void |
| `--fg-primary` | `#FFFFFF` | Foreground, key UI |
| `--accent-1` | `#000000` | Active states, important callouts |
| `--accent-2` | `#000000` | Secondary highlight |
| `--warning` | `#000000` | Errors, low-health, danger |

### Composition Rule

- *e.g. "70% of any screen is the base palette. The accent palette occupies at most 30% and only on the focal point."*

## 3. Lighting

- **Global ambience:** *e.g. `CanvasModulate` set to `--bg-base * 0.4`.*
- **Falloff:** *e.g. quadratic for tech lights, linear for sunlight.*
- **Time-of-day shifts:** *if applicable, list the named lighting states.*

## 4. VFX Vocabulary

Particles, shaders, and post-process effects are categorised. Each category has rules.

| Category | Examples | Rules |
|---|---|---|
| Impact | hit sparks, dust poofs, screen shake | Always paired — never one without the others |
| Atmospheric | rain, fog, embers | Loops at 30% intensity; spikes only on PRD beats |
| UI | tween easings, focus glow, tooltip fade | Use shared easing constants; no bespoke curves |

## 5. Camera

- **Resolution:** 1920×1080 base, Viewport stretch mode.
- **Default zoom:** 1.0.
- **Trauma-based shake:** $Shake = \text{max\_offset} \cdot \text{trauma}^2 \cdot \text{noise}(t)$.
- **Triggers:** *list events that adjust zoom (tight combat = 1.1, reveal = 0.7, etc.).*

## 6. UI Grid

- **Base unit:** 8px (or whatever your project chooses — 4px, 16px). Use multiples only.
- **Margins:** *outer / inner gutter rules.*
- **Typography scale:** *list the named sizes (Title / Heading / Body / Caption) and their px values.*

## 7. Iconography

- **Style:** *e.g. monochrome blueprint vector / pixel art / illustrated.*
- **Grid:** 24×24 by default. Padded by 2px on all sides for legibility at small sizes.
- **Degradation rule:** *if your project has durability or status states, describe how icons morph.*

## 8. Implementation Notes

- All colour values live in a `ColorTokens.gd` (or `colors.tres`) — never inline hex in scenes.
- All font sizes live in `FontTokens.gd`.
- Shared themes are `.tres` resources under `app/assets/themes/`.
- New UI components must be Type Variations of a Godot `Theme`, not bespoke styles.
