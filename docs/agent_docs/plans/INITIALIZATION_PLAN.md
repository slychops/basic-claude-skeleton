# Initialization Plan (Pre-Production & Pipeline)

**Purpose:** This document describes the *first* phases of work — getting the project skeleton, autoloads, and core architecture in place. It ends at the "graybox prototype" milestone where a player can interact with the core loop in its most stripped-down form.

**How you use this doc:** Fill in real dates, replace the placeholder phases with the actual setup work your project needs, and update Milestone 0's success criteria to match your project's "minimum viable interaction." Tick items off as you complete them.

**How Claude should use this doc:** Load it when the task is about initialization, scaffolding, or "what should we work on next" during the first weeks. Treat the phase order as a default — only deviate when the user explicitly asks to.

---

## Phase 1: Project Setup & Conventions

**Target:** Week 1

- Initialize `app/` with `src/core/`, `src/domains/`, `assets/`, `data/`, and `test/` (already scaffolded by this skeleton — verify before continuing).
- Create `src/core/SignalBus.tscn` and `src/core/GameManager.gd` stubs and register them as autoloads.
- Set up the test runner (gdUnit4) and confirm a trivial test passes headless.
- Fill in `PRD.md` (Vision, Pillars, Core Loops).
- Fill in `TECHNICAL_MASTER.md` sections that are project-specific.

## Phase 2: Core Mechanic Vertical Slice

**Target:** Week 2-3

- Stand up the *one* core mechanic the PRD's "moment-to-moment" loop depends on.
- Build it as a `.tscn` component under the relevant domain — not in a monolithic script.
- Add at least one passing test before any logic is written (Red/Green).
- Wire a single signal through `SignalBus` end-to-end as a coupling sanity check.

## Phase 3: Data Foundation

**Target:** Week 3-4

- Define the base Resource schemas your project depends on (`BaseItem.tres`, `LevelData.tres`, etc.).
- Move any hardcoded tuning values into Resources under `app/data/`.
- Add an Inspector-driven workflow so non-coders can tweak values without touching `.gd`.

## Milestone 0: The "Graybox" Prototype

**Target:** End of Week 4

**Success Criteria:** *Replace with your project's "minimum viable interaction" — the smallest demo that proves the core loop works.*

- The player can [execute the core verb] without crashing.
- One full session loop (start → core action → end-state) is playable.
- All implemented features have at least one passing test.
- `CLAUDE.md`, `PRD.md`, and `TECHNICAL_MASTER.md` accurately describe what exists.

## Working Practice Through Initialization

- **Branch per feature.** No work on `main`.
- **TDD by default.** Red commit, then green commit, then refactor commit.
- **Commit cadence.** Commit when a unit of work is reviewable, not when the day ends.
- **`/commit` skill.** Use it — see `.claude/skills/commit/SKILL.md`. It runs the review skill before pushing.
