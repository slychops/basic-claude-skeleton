# Features

**Purpose:** This directory holds *feature specs* — the per-feature documents that describe what is going to be built, why, and how acceptance is measured. Each spec is the contract between the user (or designer) and Claude when a new feature is implemented.

**How you use this directory:**

- New feature ideas land at the top level as `feature_name.md` using the template below.
- When a feature is approved and locked-in, move it into `committed/` and reference it from the relevant agent doc.
- When a feature ships, append a "Shipped" section with the date and PR link — keep it as a historical record.

**How Claude should use this directory:** Read the spec for the feature you are working on. Treat its *Acceptance Criteria* as the definition of "done" — implementation work is not finished until every box is ticked. If the spec is ambiguous, ask before implementing rather than guessing.

---

## Template

See [`_template.md`](_template.md) for the spec format. Copy it for every new feature.

## Folder Convention

- `docs/features/` — feature specs in flight (proposed, in design, in implementation)
- `docs/features/committed/` — locked specs that are in active development or recently shipped

A feature being in `committed/` does not mean shipped — it means scoped and agreed. The "Shipped" section inside the file tells you when it landed.
