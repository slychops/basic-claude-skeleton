# Feature: [Name]

**Status:** Proposed / In Design / In Implementation / Shipped
**Owner:** [name]
**Created:** YYYY-MM-DD | **Last Updated:** YYYY-MM-DD

## Summary

One paragraph: what is this feature, who is it for, why does it earn its place in the project.

## Pillar Alignment

Which PRD pillar(s) does this serve? If you can't name one, the feature is probably out of scope.

- Pillar [X] — *one sentence on how this feature serves it.*

## User Story

> *As a [player / NPC / system], I want [behaviour] so that [outcome].*

## Acceptance Criteria

Concrete checks that must be true when this feature is "done." Phrase each as a verifiable statement.

- [ ] *The player can ...*
- [ ] *On [event], the system ...*
- [ ] *Tests cover the new public API on [component].*
- [ ] *Performance budget: feature does not cost more than X ms/frame at target load.*

## Out of Scope

What this feature is *not* doing — to stop scope creep mid-implementation.

- *e.g. "Multiplayer support" — defer to a future feature spec.*

## Technical Approach

High-level shape only — full design lives in the relevant agent doc.

- **Domain:** `app/src/domains/<domain>/`
- **New components:** `[Name1.tscn], [Name2.tscn]`
- **Signals:** *list new signals on `SignalBus` if any.*
- **Resources:** *list new `.tres` schemas if any.*
- **Tests:** *the failing test(s) that will drive implementation.*

## Open Questions

Anything unresolved that blocks implementation. Resolve before moving to `committed/`.

- *example question — assignee — status*

## Risks

- *one-line risks with a mitigation note each.*

## Shipped

*Fill in when the feature lands.*

- **Date:** YYYY-MM-DD
- **PR:** #N
- **Follow-ups:** *list any deferred work.*
