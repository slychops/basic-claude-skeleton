# Full Production Plan (Content & Scaling)

**Purpose:** This document picks up where `INITIALIZATION_PLAN.md` ends. It describes the longer-arc phases: expanding the prototype, polishing systems, building content, and preparing for whatever shipping looks like for this project (early access, marketplace launch, internal demo).

**How you use this doc:** Re-plan every milestone. The phases here are a default skeleton — your project will collapse or expand them. Each phase should end with a measurable milestone (a playable build, an internal demo, a feature-complete vertical slice).

**How Claude should use this doc:** Load it when the task is about roadmap, prioritization, or "what comes after the prototype." Don't treat the phase descriptions as prescriptive — they exist to be replaced with the project's real plan.

---

## Phase 4: Vertical Slice

**Target:** *fill in dates*

- Extend the core mechanic into a complete, polished slice of one session.
- Add the supporting systems (audio, VFX, UI chrome) at production quality for the slice only.
- The slice is "demo-ready" — you would show it to someone outside the team without apologising.

## Phase 5: Content Multiplication

**Target:** *fill in dates*

- Use the slice as a template. Multiply content: more levels, more enemies, more items, more rooms.
- Lean on data-driven Resources — adding content should not require new code.
- Begin LLM-assisted content generation if applicable (see `workflow/PROMPT_CODEX.md`).

## Phase 6: Polish & Cohesion

**Target:** *fill in dates*

- Pass on audio, VFX, screen-shake, hit-pause, juice.
- Cohesion editing: cut anything that doesn't earn its place against the PRD pillars.
- Performance budget: lock the target frame time and start enforcing it in CI.

## Phase 7: Pre-Ship Hardening

**Target:** *fill in dates*

- Crash logging, telemetry (if applicable), bug-reporting flow.
- Localization pass, accessibility pass.
- Build/export pipeline for every target platform.
- Marketing assets, store-page copy, press kit.

## Milestone 1: Feature-Complete Vertical Slice

**Target:** End of Phase 4

**Success Criteria:** A single end-to-end session that demonstrates every core pillar of the PRD at production quality. No critical bugs, no placeholder art in the slice, runs at target framerate.

## Milestone 2: Content-Complete

**Target:** End of Phase 5

**Success Criteria:** Every system the shipping product needs is implemented (even if not fully tuned). Content count meets the PRD's session-length and replayability targets.

## Milestone 3: Ship-Ready

**Target:** End of Phase 7

**Success Criteria:** The build is publishable. Outstanding work is polish and content tuning only.

## Working Practice Through Production

- Re-read the PRD at the start of each phase. Cut features that no longer serve the pillars.
- Run weekly "is this still the game we set out to make" reviews against the pillars.
- Maintain a "kill list" — features cut from scope, with one line on why. Future-you will thank present-you.
