# AGENTS.md

Instructions for AI agents (Claude Code, Cursor, etc.) working in this repository.

This is the **canonical** agent-instructions file for this repo. `CLAUDE.md` simply points here.

## What this repository is

A personal directory of **agent skills** — self-contained, reusable procedures that teach an AI assistant how to perform a specific task. Each skill lives in its own directory under `skills/` and is authored to be dropped into any agent that supports the skill format.

This repo is for **personal projects**: scratch ideas, one-off automations, and reusable workflows the author wants on hand across machines. It is intentionally lower-ceremony than a shared/published skills repo — but skills should still be correct, self-contained, and lint-clean.

## Repository layout

```text
skills/             — One subdirectory per skill, each containing a SKILL.md
  _template/        — Copy this when starting a new skill
README.md           — Human-facing overview and skill index
AGENTS.md           — This file (canonical agent instructions)
CLAUDE.md           — Pointer to AGENTS.md
```

## Skill file format

Each skill is a directory containing a `SKILL.md` with mandatory YAML frontmatter:

```yaml
---
name: skill-name
description: "One-line description used for routing. Start with a verb; say when to use it."
compatibility: "Platform and version constraints, or 'Any platform.'"
license: MIT
metadata:
    author: georgestephanis
    version: "1.0"
    written: "YYYY-MM-DD"
---
```

The body should follow this section order:

1. **When to use** — trigger conditions and, where helpful, when _not_ to use it.
2. **Inputs required** — what to gather or confirm before starting.
3. **Procedure** — the step-by-step checklist.
4. **Verification** — how to confirm the task succeeded.
5. **Failure modes** — common gotchas and how to recover.
6. **Escalation** — when to stop and ask the user.

Keep `SKILL.md` concise. Push depth (long reference material, API tables, examples) into a `references/*.md` subdirectory, and deterministic helpers into a `scripts/` subdirectory.

## Authoring conventions

- **`name`** must match the skill's directory name exactly, kebab-case.
- **`description`** is what routing keys off — make it specific and trigger-rich. Lead with what it does, then "Use when…".
- Convert relative dates to absolute (`2026-06-29`, not "today") in skill bodies and the `written` field.
- Prefer concrete commands and verifiable steps over prose. If a step can be checked, say how to check it.
- One skill = one coherent task. If a skill sprawls across unrelated jobs, split it.

## Starting a new skill

1. Copy `skills/_template/` to `skills/<new-name>/`.
2. Fill in the frontmatter and the six sections.
3. Add a one-line entry to the skill index in `README.md`.
4. Run `npm run lint` and fix everything before declaring done.

## Linting

Run `npm run lint` before finishing any task that touches `.md` files. It runs Prettier (formatting) and markdownlint-cli2 (rules) across the repo. The pre-commit hook enforces the same checks, so unlinted files will block commits.

Formatting follows the repo config: **tabs**, `proseWrap: preserve`. Don't hand-reflow prose.
