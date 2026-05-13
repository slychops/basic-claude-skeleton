# Design Docs

**Purpose:** This directory holds detailed *design* documents — the systems and tuning behind specific mechanics. Each file is a deep-dive on one slice of the game (economy, AI, level design, progression, etc.). They sit one level below the PRD: the PRD says *what* the system is and *why*, design docs say *how it works in detail*.

**How you use this directory:** Add a new file per major system as it becomes meaningful. Name files in `SCREAMING_SNAKE_CASE.md` (e.g. `ECONOMY_MATRIX.md`, `ENEMY_AI.md`, `LEVEL_DESIGN.md`). Each file should open with its own purpose / how-to-use / how-Claude-uses paragraphs, mirroring the template the rest of `docs/agent_docs/` uses.

**How Claude should use this directory:** Read only the file that matches the current task. Don't load all of them up-front. When adding a new design doc, ask the user to confirm the file name and scope before writing it.

---

## Suggested Design Doc Names

These are common slices worth documenting. Add yours below as they become real.

- `ECONOMY_MATRIX.md` — resource tiers, durability, weight, synergies, throughput math
- `ENEMY_AI.md` — perception, positioning, behaviour trees / state machines, telegraphs
- `LEVEL_DESIGN.md` — movement metrics, room templates, reveal framing, hazard design
- `PROGRESSION.md` — unlock structure, meta-progression, difficulty curve
- `COMBAT.md` — damage model, hitboxes/hurtboxes, balancing rules

## Template for a New Design Doc

```markdown
# [System Name]

**Purpose:** [what this system is and why it exists]
**How you use this doc:** [when to update it, what level of detail belongs here]
**How Claude should use this doc:** [when to load it, what rules to treat as hard]

---

## 1. Philosophy
## 2. Core Rules / Math
## 3. Component Architecture
## 4. Tuning Targets
## 5. Open Questions
```
