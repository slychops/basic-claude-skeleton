# AI Prompt Codex

**Purpose:** A reference of the prompts, templates, and review checklists your team uses with LLMs (Claude Code, ChatGPT, Gemini, etc.). Capturing the prompts here keeps them version-controlled and shared — instead of every team member having their own private chat history.

**How you use this doc:** Add a prompt template every time you find one that produces consistently good results. Remove ones that stop working. Treat the "Component Contract" template as the recommended way to ask for new features.

**How Claude should use this doc:** Load it before generating a new component, before doing a code review, and before evaluating whether a generated artifact is acceptable. Apply the checklist in section 5 to your own output before reporting back to the user.

---

## 1. Global Context Prompt (Session Opener)

Use this at the start of a fresh chat to set baseline context with any LLM:

> "You are working on **[Project Name]**, a [genre/type] built in Godot 4.3. The repo root holds `docs/`, `.claude/`, `app/`, `README.md`, `CLAUDE.md`; Godot treats `app/` as `res://`.
>
> **Hard rules:**
>
> - **Style:** [the project's identity sentence from `MASTER_VISUAL_AND_UI.md`]
> - **Architecture:** Component-first. Logic in `.tscn` components under `src/domains/<domain>/`, never in monolith scripts.
> - **Code:** GDScript 2.0 with strict static typing. No untyped vars on public signatures.
> - **Cross-domain coupling:** Only via `SignalBus` or shared `.tres` Resources."

## 2. Implementation Prompt

**Best for:** Writing GDScript, refactoring, performance optimisation.

> "Act as the lead engineer. Implement the following feature using the 'Scene-as-Component' pattern.
>
> **Technical requirements:**
>
> - Use `@export` and `@onready` (Godot 4 syntax).
> - Communicate via `SignalBus` for cross-domain events; use local signals within a domain.
> - Use `move_toward` / `lerp` with `delta` for any movement that should feel weighted.
> - Include a header comment listing the script's purpose, dependencies, and required signals.
> - If creating a state for a state machine, follow the `enter()`, `exit()`, `physics_update(delta)` pattern."

## 3. Architect / Critic Prompt

**Best for:** System design, reviews, visual audits.

> "Act as the systems architect.
>
> **Task:** [Describe — design a new system / review this script / audit this scene].
>
> **Evaluation criteria:**
>
> - **PRD alignment:** Does this serve a Pillar from the PRD?
> - **Architectural integrity:** Is it properly decoupled? Any `preload()` of another domain?
> - **Visual audit (if image provided):** Are the palette ratios in `MASTER_VISUAL_AND_UI.md` respected?
> - **Performance:** Anything in `_process()` that should be cached or deferred?"

## 4. Component Contract Template

Use when requesting a new feature so the LLM knows the I/O it must satisfy.

```markdown
### Feature: [Name]
**File Path:** app/src/domains/[domain]/components/[Name].tscn
**Inputs (Exported Vars):**
- [e.g., speed: float]
- [e.g., decay_rate: float]
**Outputs (Signals):**
- [e.g., signal effect_triggered(intensity)]
**Dependencies:**
- [autoloads / resources used]
**Logic Goal:** [one-sentence behaviour summary]
**Acceptance Test:**
- [the failing test that must pass when this feature is complete]
```

## 5. Pre-Merge Checklist

Before merging any AI-generated code, walk through this list:

- [ ] Does the script use `class_name` if it's referenced from `.tscn` files?
- [ ] Are public method parameters and return types annotated?
- [ ] Are `@export` vars typed?
- [ ] Movement / physics: frame-rate independent (uses `delta`)?
- [ ] Any hardcoded data that should be in a Resource?
- [ ] Any cross-domain `preload()` / `load()` that should route through `SignalBus`?
- [ ] At least one passing test covers the new behaviour?
- [ ] CLAUDE.md / supporting docs updated if conventions changed?

## 6. When to Reject AI Output

Reject and re-prompt when:

- The code violates a hard rule in `core_scope.md` or `domains_scope.md`.
- Tests are missing for new public behaviour.
- The output silently changes an unrelated file.
- Style nits dominate the diff — the LLM should focus on substance.

## 7. Style Correction Prompts

Reusable nudges when output drifts from project tone:

- **"Too clean":** "This feels too sterile. How does the world friction (durability, weight, decay) show up in this mechanic? Re-read `PRD.md` pillar [N] before answering."
- **"Too generic":** "Cite a specific section of `MASTER_VISUAL_AND_UI.md` or `AUDIO_AND_FEEL.md` in your next response. If you cannot, ask me to clarify the rule first."
