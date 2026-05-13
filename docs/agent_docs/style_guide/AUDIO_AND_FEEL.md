# Audio & Feel

**Purpose:** Sound is half of how a game feels. This document defines the audio identity (mix, material matrix, ducking rules) and the haptic / feedback rules that turn mechanics into "juice." Treat it as the audio-side counterpart to `MASTER_VISUAL_AND_UI.md`.

**How you use this doc:** Fill in your project's actual audio bus layout, material matrix, and haptic events. Replace the templates with real values. Add new sections as your project picks up audio-driven systems (music interactivity, dialogue routing, accessibility audio).

**How Claude should use this doc:** Load it for any audio, haptics, or "juice" task. When a new mechanic is added, ask whether it needs an audio + haptic event, and add the entry here before writing the code. Reject implementations that emit raw `AudioStreamPlayer` calls when the project has a manager autoload.

---

## 1. Audio Identity in One Sentence

> *Replace with the audio identity (e.g. "Low-fidelity, tactile, mechanical — every sound has a layer of friction or decay").*

## 2. Audio Bus Layout

Decoupled buses keep mixing predictable. Default layout:

```
Master                  # Final output, limiter only
├── World               # Footsteps, impacts, physics
├── Ambience            # Wind, rain, environmental loops
├── Music               # Soundtrack
└── UI                  # Menu sounds, prompts, dialogue
```

**Ducking rule:** Place an `AudioEffectLimiter` on Ambience and Music; side-chain to World so impacts cut through.

## 3. The Material Matrix

Footsteps, impacts, and physics SFX query the surface they hit. Define one row per material that exists in your game:

| Surface | Sound Profile | Haptics |
|---|---|---|
| *example: rusted metal* | *high-pitch clank + hollow reverb* | *sharp short rumble* |
| *example: wet mud* | *damp squelch + suction* | *soft lingering rumble* |

The `MovementComponent` (or equivalent) reads the ground's `PhysicsMaterial` or metadata tag and dispatches to the right entry.

## 4. Feedback Events ("Juice")

Trauma-based feedback: a single `trauma` value `[0, 1]` decays over time and drives screen-shake + haptics + audio low-pass in unison. Trigger trauma from gameplay events:

| Event | Trauma | Audio | Visual |
|---|---|---|---|
| *example: heavy landing* | $\text{clamp}((v_y - v_{threshold}) / v_{max}, 0, 1)$ | thud + 0.2s music ducking | shake + vignette pulse |
| *example: critical hit* | 0.4 | layered impact + hit-pause 80ms | freeze-frame + flash |

## 5. Music Interactivity

- **Layered tracks** for tension states (calm / alert / combat), cross-fade on state change.
- **Stinger library** for one-shot events (boss appearance, level cleared).
- **Tempo-locked** to gameplay where it earns its place — don't force it.

## 6. Accessibility

- Every audio-only signal (e.g. "enemy approaching") has a visual equivalent.
- Master / Music / SFX / UI volume sliders are mandatory in settings.
- Caption all dialogue. Provide a "reduce motion / shake" toggle.

## 7. Implementation Notes

- All SFX play through an `AudioManager` autoload — never instantiate `AudioStreamPlayer` directly from a domain.
- Define haptic events as named constants on `FeedbackBus` so designers can tune them without code changes.
- One-shots are `.wav` (uncompressed, low-latency). Loops are `.ogg` (compressed).
