# Personal Skills

A personal directory of **agent skills** — self-contained, reusable procedures that teach an AI assistant (Claude Code, Cursor, etc.) how to perform a specific task. Used across personal projects and machines.

For agent-facing instructions (format, conventions, linting), see **[AGENTS.md](AGENTS.md)**.

## Structure

```text
skills/             — One subdirectory per skill, each containing a SKILL.md
  _template/        — Starting point for a new skill
```

## Skill format

Each skill lives in its own directory with a `SKILL.md` using YAML frontmatter:

```yaml
---
name: skill-name
description: "What this skill does (concise, used for routing). Use when…"
compatibility: "Target platform and version constraints"
---
```

Followed by these sections, in order:

1. **When to use** — trigger conditions
2. **Inputs required** — what to gather before starting
3. **Procedure** — step-by-step checklist
4. **Verification** — how to confirm success
5. **Failure modes** — common gotchas
6. **Escalation** — when to ask for help

Optionally add a `references/` subdirectory for deeper docs and a `scripts/` subdirectory for deterministic helpers.

## Skills

| Skill                                                                      | Description                                                                                                                                                                                        |
| -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [mushclient-plugin-structure](skills/mushclient-plugin-structure/SKILL.md) | Author the XML file for a MUSHclient plugin — the `<muclient>`/`<plugin>` wrapper, triggers/aliases/timers/variables blocks, `<script>` CDATA, send_to codes, and `OnPlugin*` lifecycle callbacks. |
| [mushclient-lua-scripting](skills/mushclient-lua-scripting/SKILL.md)       | Write Lua inside MUSHclient — the ~420 world functions, trigger/alias/timer handlers and wildcards, state persistence, and the bundled Lua modules and utils.                                      |
| [lua-5.1-reference](skills/lua-5.1-reference/SKILL.md)                     | Reference for the Lua 5.1 language and standard library — types, operators, metatables/metamethods, closures, coroutines, string patterns, and the basic/string/table/math/io/os/debug libraries.  |

## Authoring a new skill

1. Copy `skills/_template/` to `skills/<new-name>/`.
2. Fill in the frontmatter and the six sections.
3. Add a row to the **Skills** table above.
4. Run `npm run lint` and fix everything before committing.

## Linting

```sh
npm install   # once
npm run lint  # Prettier + markdownlint over the repo
npm run format  # auto-fix formatting
```

A pre-commit hook (simple-git-hooks + lint-staged) runs the same checks on staged files.
