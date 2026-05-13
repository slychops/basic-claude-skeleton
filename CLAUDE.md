# CLAUDE.md

This file is the entry point [Claude Code](https://claude.com/claude-code) reads at the start of every session. Keep it lean — under ~100 lines. Detailed specs live in `docs/` and should be loaded on demand only when the current task touches them.

**How to use this file:** Update the *Project Overview*, *Technical Baseline*, and the *Supporting Documents* table to fit your project. Leave the *Directive* and *Working Agreement* sections — they encode preferences that travel well across projects.

**How Claude should use this file:** Treat its directives as overrides for any default behaviour. Before answering a non-trivial question, glance at the *Supporting Documents* table and load only the row that matches the current task — never read every doc up front.

---

## Directive

Challenge ideas honestly. Provide balanced analysis with pros and cons. Do not flatter or validate without substance — push back when something doesn't hold up. When uncertain, say so and ask before acting.

## Project Overview

**[Project Name]** — *one-paragraph pitch describing what the project is, who it is for, and the core experience.*

Philosophy: *the three non-negotiable feel words / pillars (e.g., "heavy, methodical, sparse").*

**Project Start:** YYYY-MM-DD | **Phase:** Initialization

## Technical Baseline

- **Engine:** Godot 4.3+ (replace if using a different stack)
- **Language:** GDScript 2.0 — strict static typing mandatory
- **Resolution:** 1920x1080, Viewport stretch mode
- **Testing:** gdUnit4 (`godot --headless -s res://addons/gdUnit4/bin/gdUnit4Cli.gd`)
- **Data:** `.tres` Resource files for all game data — never hardcode values
- **Commits:** Conventional — `feat(domain):`, `fix(core):`, `docs(style):`
- **Architecture:** Component-first `.tscn` composition. Signals over nested ifs. Parallel state machines for movement vs combat.
- **Structure:** Domain-based (`app/src/domains/`) — each domain owns its scenes, scripts, and components.

## Working Agreement

- **Red/Green TDD where possible.** Write a failing test first, then the minimum code to pass it, then refactor.
- **Prefer `.tscn` for layout** — `.gd` scripts hold logic and behaviour, not scene construction.
- **No multi-line shell commands.** Always single-line strings; line continuations trigger approval prompts.
- **Cross-domain communication via signals or shared Resources** — never direct `preload("res://src/domains/<other>/...")`.
- **Static typing is mandatory** on public method signatures and `@export var`s.
- **Read only what you need.** For code edits use `Read`/`Grep`/`Glob` directly; reserve the `Explore` subagent for broad codebase questions and `gather_context` for URLs or files > ~30KB.

## Supporting Documents

Read only the row that matches the task you are working on.

| Document | What it covers | Consult for |
|----------|---------------|-------------|
| [`PRD.md`](docs/agent_docs/PRD.md) | Product vision, core loops, key mechanics | "What are we building and why" |
| [`TECHNICAL_MASTER.md`](docs/agent_docs/architecture/TECHNICAL_MASTER.md) | Engine patterns, autoloads, physics math, deprecated APIs | New systems, components, performance |
| [`MASTER_VISUAL_AND_UI.md`](docs/agent_docs/style_guide/MASTER_VISUAL_AND_UI.md) | Palettes, VFX, particles, camera, UI grids | Visual work, lighting, UI |
| [`AUDIO_AND_FEEL.md`](docs/agent_docs/style_guide/AUDIO_AND_FEEL.md) | Material matrix, haptics, audio bus layout | Sound design, "juice" |
| [`PROMPT_CODEX.md`](docs/agent_docs/workflow/PROMPT_CODEX.md) | Prompt patterns, component contracts, review checklist | Code review, feature generation |
| [`INITIALIZATION_PLAN.md`](docs/agent_docs/plans/INITIALIZATION_PLAN.md) | Phase 1-3 setup, Milestone 0 criteria | Initialization work |
| [`FULL_PRODUCTION_PLAN.md`](docs/agent_docs/plans/FULL_PRODUCTION_PLAN.md) | Phase 4-7+ content and scaling roadmap | Post-prototype planning |

*Design-specific docs (economy, AI, level design, etc.) live under `docs/agent_docs/design/` — add them as your project demands and link here.*

## Quick Triggers

- `/commit` → review changes, then commit and open a PR. See `.claude/skills/commit/SKILL.md`.
- `/review-pr` → run a Godot-aware code review on the current diff or a target PR.
