# Basic Claude Skeleton

A starter skeleton for new projects developed with [Claude Code](https://claude.com/claude-code) as a co-author. It ships with an opinionated folder layout, a lean `CLAUDE.md`, a set of supporting agent docs you can extend, and two ready-to-use skills (`commit`, `review-pr`).

The defaults assume a **Godot 4** game project, but the structure is light — swap `app/` for any engine or runtime and the docs/skills still pull their weight.

## How to Use This Repo

1. **Don't fork it on GitHub** — clone it once, drop the `.git` history, and re-initialise:

   ```bash
   git clone https://github.com/slychops/basic-claude-skeleton my-new-project
   cd my-new-project
   rm -rf .git
   git init
   ```

2. **Rename the project** in `app/project.godot` (the `config/name=` line) and update the title in `README.md` and `CLAUDE.md`.

3. **Fill in the agent docs** under `docs/agent_docs/` — they ship as templates with placeholders. Each file has a "How to use this doc" header explaining what it is for.

4. **Start coding** under `app/src/`. The `*_scope.md` files in each directory are *contracts* describing what is allowed there — Claude will read them before adding files.

5. **First commit** when the docs reflect your project:

   ```bash
   git add -A
   git commit -m "chore: initialise from basic-claude-skeleton"
   ```

## Folder Layout

```
/
├── app/                          # Engine project root (Godot 4 by default)
│   ├── project.godot
│   ├── src/
│   │   ├── core/                 # Autoloads & shared chrome
│   │   └── domains/              # Self-contained feature modules
│   ├── assets/                   # Art, audio, fonts, animation
│   ├── data/                     # .tres / .json data resources
│   └── test/                     # Test suites (gdUnit4 by default)
├── docs/
│   ├── agent_docs/               # Specs Claude reads on demand
│   │   ├── PRD.md
│   │   ├── architecture/
│   │   ├── design/
│   │   ├── plans/
│   │   ├── style_guide/
│   │   └── workflow/
│   ├── discussions/              # Research, proposals, open questions
│   └── features/                 # Feature specs (proposed → committed)
├── .claude/
│   └── skills/                   # Project-local skills (commit, review-pr)
├── CLAUDE.md                     # Entry point for Claude — kept lean
├── README.md                     # This file
└── .gitignore
```

## What Each Top-Level File Is For

| File | What it is | How you use it | How Claude uses it |
|---|---|---|---|
| `CLAUDE.md` | The one file Claude reads on every session | Keep it under ~100 lines. Reference detail in `docs/`, never inline it | Reads it first; treats its directives as overrides |
| `README.md` | Public-facing project intro (this file) | Update title, vision paragraph, install steps | Reads if asked about onboarding or repo purpose |
| `.gitignore` | Standard ignores (Godot + macOS + IDE + node + python) | Append project-specific entries at the bottom | Respects it — never tries to commit ignored paths |
| `app/` | The actual code | Where you (and Claude) build the project | Honours the `*_scope.md` contracts in each subdir |
| `docs/agent_docs/` | Detailed specs | Edit as the design solidifies; treat as source of truth | Reads only the file(s) relevant to the current task |

## Prerequisites

- **Godot 4.3+** for the default `app/` setup (swap if you're using a different engine).
- **GitHub CLI (`gh`)** if you want the `commit` skill to open PRs for you.
- **Claude Code** ([install instructions](https://docs.claude.com/en/docs/claude-code/quickstart)).

## What This Skeleton Is *Not*

- It is not a framework — there is no runtime code to invoke. It is documentation, conventions, and folder shapes.
- It is not specific to any genre. The PRD, design, and style-guide files are templates with prompts asking you to fill them in.
- It is not exhaustive — add the autoloads, components, and patterns your project actually needs.

## License

This skeleton is unlicensed by default. Add your own LICENSE file before publishing anything built on top of it.
