---
name: review-pr
description: Reviews code changes for bugs, logic errors, and Godot/GDScript-specific issues. Quality over quantity — caps at 10 findings.
argument-hint: [scope] [source]
parameters:
  scope:
    type: string
    description: "Review focus: all (default), security, logic, style, quick"
  source:
    type: string
    description: "Input: staged (default), a PR number, or a branch name"
---

# PR Review Skill

**Purpose:** This skill performs an opinionated, Godot/GDScript-aware code review on a diff. It is intentionally strict on substance (correctness, architecture, performance) and lenient on style nits unless the `style` scope is explicitly chosen. The goal is to surface the issues a senior reviewer would flag — not to drown the user in suggestions.

**How you use this skill:** Invoke it as `/review-pr [scope] [source]`. Common forms:
- `/review-pr` → review currently staged + unstaged changes with default scope.
- `/review-pr all 123` → review GitHub PR #123.
- `/review-pr quick` → fast pass for critical issues only.
- `/review-pr style feature/my-branch` → style-only pass on a branch.

**How Claude should use this skill:** This is a *read-only* skill. Never commit, push, or modify files while running it. Always read full files (not just hunks) before flagging anything. Apply the confidence threshold — a low-confidence style nit is worse than no finding at all. Cap output at 10 findings; if more exist, keep the highest-confidence ones and note the cap in the summary.

---

## 1. Input Resolution

Parse `scope` and `source`. Defaults: scope=`all`, source=`staged`.

| Source | Command |
|--------|---------|
| `staged` (or empty) | `git diff` + `git diff --cached` (combine both) |
| A number (e.g. `123`) | `gh pr diff {number}` |
| A string (branch name) | `git diff main...{source}` |

If the diff is empty, return "No changes to review." and stop.

**Gather context:**

- List changed files, group by extension (`.gd`, `.tscn`, `.gdshader`, `.tres`).
- For each changed `.gd` file: **read the full current file**, not just the hunks. Diff-only blindness is the #1 cause of false positives.
- For `.tscn` files: hunks are usually sufficient.
- Read `CLAUDE.md` for project conventions; consult `docs/agent_docs/architecture/TECHNICAL_MASTER.md` and `docs/agent_docs/workflow/PROMPT_CODEX.md` for architecture and review standards.

**Git blame filtering** (PR / branch sources only):
- Focus findings on new code (`+` lines). If an issue exists in unchanged code, mark it as *pre-existing* and filter it out of the main output.

## 2. Review Categories

### Critical (blocks merge)

These auto-score +30 confidence:

- Null/nil access on unguarded paths.
- Signal connected but the handler is missing or has the wrong signature.
- Infinite loops or missing break/return conditions.
- `@onready` node path that doesn't match the `.tscn` tree.
- Invalid autoload references — valid autoloads come from `app/src/core/core_scope.md`.
- `.tscn` references a script or resource path that was deleted in the same diff.
- `await` on a signal that may never fire (no timeout, no fallback).
- Dictionary access without `.get()` default where missing keys would crash downstream.
- Secrets or credentials in code (keys, passwords, tokens, hardcoded URLs with auth).
- Use of deprecated APIs (`TileMap`, `ParallaxBackground`, `RigidBody2D` for characters — see `TECHNICAL_MASTER.md §7`).

### Important (should fix before merge)

- Missing edge cases (empty array access, division by zero, unhandled `match` arm).
- Off-by-one errors in loop bounds or array indexing.
- Signal emitted with wrong argument count or types.
- `_ready()` / `_process()` doing expensive work that should be deferred or cached.
- **Cross-domain coupling** — `preload("res://src/domains/<other>/...")` instead of `SignalBus`.
- Unused variables, unreachable code after early return.
- `queue_free()` without disconnecting signals first.
- Missing return types or untyped parameters on public methods (static typing is mandatory — see `TECHNICAL_MASTER.md §6`).
- Resource loading inside hot paths (`_process`, `_physics_process`).
- `get_node()` / `$` calls inside `_process()` — should be `@onready`-cached.
- Hardcoded data that should live in a `.tres` Resource.

### Suggestions (only in `style` scope)

Suppressed in all other scopes to reduce noise:

- Naming: not `snake_case` for vars/functions, not `PascalCase` for classes/enums, not `SCREAMING_SNAKE` for constants.
- Script declaration order violated (signals → enums → consts → @export → vars → @onready → lifecycle → public → private → callbacks).
- Signals not defined at top of file.
- Missing type annotation on `@export var`.
- Callback not named `_on_[signal_name]`.
- Magic numbers without a `const`.
- Long functions (50+ lines) or long scripts (400+ lines).
- `get_node()` string paths instead of `@onready` / `$`.
- Old signal syntax (`connect("signal", self, "method")`) instead of `signal.connect(callable)`.

### Strengths (always include 1-3)

This output gets posted to PRs — acknowledge good patterns:

- Proper null guards before member access.
- Clean signal-driven architecture via `SignalBus`.
- Good scene/component separation (logic in `.gd`, layout in `.tscn`).
- Type annotations on function signatures.
- Domain encapsulation — cross-domain communication via signals, not direct refs.
- Proper use of `.tres` Resources for data-driven design.

## 3. Godot/GDScript-Specific Checks

In addition to the categories above:

**Script quality:**

- `class_name` present if the script is referenced in `.tscn` files.
- `@export` vars always have type annotations.
- `_on_*` callback names match the signal they connect to.
- Godot 4 signal syntax (`signal.connect(callable)`).
- Type hints on public method parameters and return types.
- Script header comment documenting purpose and required signals.

**Scene quality (`.tscn`):**

- Node additions/removals have corresponding script changes if the node has a script attached.
- `[ext_resource]` paths point to files that exist (not deleted in same diff).
- `anchor_*` / `offset_*` sanity: `anchor_left <= anchor_right`, `anchor_top <= anchor_bottom`.
- `mouse_filter` correct: `STOP` for clickable, `IGNORE` for overlays / decorative.
- Shallow node trees preferred — flag nesting deeper than 5 levels.

**Architecture:**

- Total autoload count stays reasonable (flag if adding beyond ~5).
- No autoload-to-autoload coupling.
- No UI node references stored in autoloads.
- Components are self-contained `.tscn` scenes.
- State machines use `enter()`, `exit()`, `physics_update(delta)`.

**Performance:**

- No `get_node()` / `$` inside `_process()` / `_physics_process()`.
- No Array/Dictionary allocations inside hot loops.
- `preload()` for compile-time-known paths; `load()` only for runtime-determined ones.
- No `preload()` inside loops.
- Disable `_process()` when idle via `set_process(false)`.
- Check `tween.is_running()` before creating new tweens.

## 4. Scope Filtering

| Scope | Categories Included | Confidence Threshold |
|-------|--------------------|---------------------|
| `all` | Critical + Important + Strengths | ≥ 70 |
| `security` | Critical only (nulls, secrets, input validation, file paths) | ≥ 50 |
| `logic` | Critical + Important (control flow, state, edge cases) | ≥ 50 |
| `style` | Suggestions only | ≥ 50 |
| `quick` | Critical only + one-line tally of the rest | ≥ 70 |

## 5. Confidence Scoring

Score every finding before including it.

| Factor | Score |
|--------|-------|
| Issue is in the diff (changed lines) | +20 |
| Would cause a runtime failure / crash | +30 |
| Violates a rule in `CLAUDE.md` / `TECHNICAL_MASTER.md` | +20 |
| A senior Godot dev would flag this | +20 |
| Style nit a linter would catch | -30 |
| Issue is in unchanged / pre-existing code | -40 |

Only include findings meeting the threshold for the active scope. Track filtered items for the transparency line in the output.

## 6. Output Format

### For `all`, `security`, `logic` scopes:

```markdown
## Code Review — [scope] scope

**Verdict: [APPROVE | REQUEST_CHANGES | COMMENT]**

> [2-3 sentence executive summary: what these changes do, overall quality]

### Critical Issues
- **[file:line]** [description] `(confidence: N)`
  ```gdscript
  # the problematic code
  ```
  **Fix:** [concrete suggestion with corrected code]

### Important Issues
- **[file:line]** [description] `(confidence: N)`
  **Fix:** [suggestion]

### Strengths
- **[file]** [what was done well]

---
**Summary:** X critical, Y important | Reviewed N files (M .gd, K .tscn)
**Filtered:** Z items (reasons: N pre-existing, N below threshold, N style-only)
```

### For `style` scope:

Same format but only the Suggestions section (no Critical/Important).

### For `quick` scope:

```markdown
## Quick Review

**Verdict: [APPROVE | REQUEST_CHANGES]**

> [1 sentence summary]

### Critical Issues
[list or "None found"]

**Tally:** X critical, Y important, Z suggestions | N files
```

**Verdict rules:**

- `REQUEST_CHANGES` — any Critical with confidence ≥ 70.
- `COMMENT` — no Critical but has Important.
- `APPROVE` — no Critical, no Important (or only low-confidence Important).

## 7. Execution Rules

- **Read-only.** Do not commit, push, create PRs, or modify files.
- **Cap at 10 findings.** Keep the highest-confidence ones if more exist.
- **No duplicates.** Consolidate identical issues across files into one entry.
- **Be specific.** Every finding has `file:line` and a concrete fix.
- **Substance over style.** When uncertain, err on not reporting.
- **Read `CLAUDE.md` first.** Project conventions override generic best practices.
