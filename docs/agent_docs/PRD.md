# Product Requirements Document

**Purpose:** The PRD is the source of truth for *what* you are building and *why*. Vision, core loops, audience, success criteria, and trade-offs live here. Implementation detail belongs in `architecture/`, `design/`, or `style_guide/` — keep this document at the level of "what does the player experience and why does it matter."

**How you use this doc:** Fill in each section below as decisions solidify. Treat unanswered sections as open design questions. Re-visit the PRD whenever a scope decision is needed — if a proposed feature doesn't trace back to a pillar here, it is probably out of scope.

**How Claude should use this doc:** Load it when the task is about feature scope, design trade-offs, or "why does the project work this way." Cite specific sections when pushing back on out-of-scope proposals. If a section is empty, ask the user to fill it in rather than guessing.

---

## 1. Vision (One Paragraph)

> *Replace this with a single paragraph describing the project. Who is the audience, what is the core fantasy, what makes it different from the obvious competitors?*

## 2. Pillars (3-5 Non-Negotiables)

The feel words / mechanics every other decision serves:

1. **Pillar A** — *one-sentence explanation.*
2. **Pillar B** — *one-sentence explanation.*
3. **Pillar C** — *one-sentence explanation.*

Anything that conflicts with these pillars gets cut, no matter how cool it looks in isolation.

## 3. Core Loops

Describe each loop as **input → action → reward → next decision**. The shorter the loop, the more often the player feels it; cover the moment-to-moment loop first, then the session loop, then the meta loop.

- **Moment-to-moment** (seconds): *e.g., movement, combat, micro-decisions.*
- **Session** (minutes to ~30 min): *e.g., expedition / level / match.*
- **Meta** (hours / sessions): *e.g., progression, unlocks, narrative beats.*

## 4. Key Mechanics

For each mechanic: name, one-line description, why it exists (which pillar it serves), open questions.

- **Mechanic A** — *what it is.*
  - *Pillar served:* [Pillar A]
  - *Open questions:* [list]

## 5. Target Audience & Reference Games

- **Who plays this:** *the realistic player profile, not "everyone".*
- **They also play:** *3-5 reference titles. For each, note one thing you take and one thing you reject.*

## 6. Success Criteria

How you'll know the project is working before launch. Pick measurable signals where possible.

- *e.g., "A new player can finish the tutorial in <10 minutes without help."*
- *e.g., "Average session length in playtests is 25-45 minutes."*

## 7. Explicit Non-Goals

What this project is *not* doing — and what would tempt you to do but you've chosen against. Saving this list now stops scope creep later.

- *e.g., "No multiplayer in v1."*
- *e.g., "No procedural narrative — handcrafted only."*

## 8. Open Questions / Risks

Track design questions you haven't answered and the major risks (technical, design, or business) you are aware of. Update with dates when you resolve each one.

| Question / Risk | Owner | Status |
|---|---|---|
| *example* | *name* | Open / In progress / Resolved (date) |
