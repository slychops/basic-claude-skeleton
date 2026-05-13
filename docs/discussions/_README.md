# Discussions

**Purpose:** This directory holds research, proposals, and exploratory thinking that has *not* yet been committed to as project policy. It is the place for "should we use X?" and "here is a comparison of three approaches" — work that is too rough for `agent_docs/` but too valuable to lose.

**How you use this directory:** Drop a markdown file per topic. Date it. State its status (Research / Proposal / Decided / Abandoned) at the top. When a discussion turns into a decision, either promote the content into the relevant `agent_docs/` file or archive the discussion with a "Resolved" status and a link to the resulting commit / doc.

**How Claude should use this directory:** Treat discussion files as *non-authoritative*. They capture thinking, not policy. When asked to implement something based on a discussion, confirm with the user whether the discussion has been promoted to a decision.

---

## File Naming

- `YYYY-MM-DD_short_topic.md` — e.g. `2026-04-10_asset_pipeline.md`.
- Use snake_case for the topic; keep it short and grep-able.

## Suggested Frontmatter

Every discussion doc opens with:

```markdown
# [Topic]

**Date:** YYYY-MM-DD | **Status:** Research / Proposal / Decided / Abandoned
**Owner:** [name]
**Resolves to:** [link to agent_doc / commit / PR if decided, else "TBD"]

## Status & Decisions
- [Table of decisions made so far with status column]

## Context
- [Why this discussion exists]

## Options Considered
- [Approach A — pros, cons]
- [Approach B — pros, cons]

## Recommendation
- [What you'd do today, with caveats]

## Open Questions
- [The unknowns blocking a decision]
```
