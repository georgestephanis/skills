# Plugin XML elements & attributes (reference)

Authoritative source: <https://www.gammon.com.au/scripts/doc.php?general=plugins> and the trigger/alias/timer editor dialogs (every editor field maps to an XML attribute of the same name). Attributes are lowercase; boolean attributes take `"y"`/`"n"`.

## Document shell

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE muclient>
<muclient>
  ... one <plugin> block, then triggers/aliases/timers/variables/script blocks ...
</muclient>
```

Optional **entities** can be declared in the DOCTYPE and reused as `&name;` (handy for user-tunable constants):

```xml
<!DOCTYPE muclient [
  <!ENTITY afk_command "afk" >
  <!ENTITY timer_mins  "5"  >
]>
```

You may repeat `<triggers>`, `<aliases>`, `<timers>`, `<script>` blocks multiple times in one file — MUSHclient concatenates them. This lets you group a feature's trigger + handler together.

## `<plugin>` attributes

| Attribute      | Meaning                                                                                                 |
| -------------- | ------------------------------------------------------------------------------------------------------- |
| `name`         | Display name (no spaces is conventional; use underscores).                                              |
| `author`       | Plugin author.                                                                                          |
| `id`           | **24-char hex**, unique and stable per plugin. Identifies the plugin to `CallPlugin`, state files, etc. |
| `language`     | Script language: `Lua` (recommended), `VBscript`, `JScript`, `PerlScript`, `Python`, `Tcl`, `PHP`.      |
| `purpose`      | One-line summary in the plugins list.                                                                   |
| `date_written` | `YYYY-MM-DD HH:MM:SS`.                                                                                  |
| `requires`     | Minimum MUSHclient version; loading is refused on older clients.                                        |
| `version`      | Plugin version string.                                                                                  |
| `save_state`   | `"y"` to persist variables to a per-plugin state file on close.                                         |
| `sequence`     | Evaluation order across plugins, 0–10000 (default 5000). Lower = earlier.                               |

A `<description trim="y"><![CDATA[ ... ]]></description>` child holds longer help text, retrievable via `GetPluginInfo(GetPluginID(), 3)`.

## `<trigger>` attributes (common)

| Attribute                                          | Meaning                                                              |
| -------------------------------------------------- | -------------------------------------------------------------------- |
| `match`                                            | Pattern to match against incoming lines.                             |
| `regexp`                                           | `"y"` = `match` is a PCRE regex; else wildcard (`*`, `?`).           |
| `enabled`                                          | `"y"`/`"n"`.                                                         |
| `script`                                           | Name of the script function to call: `fn(name, line, wildcards)`.    |
| `send_to`                                          | Destination for the `<send>` body (see send_to table in SKILL.md).   |
| `sequence`                                         | Order among triggers, 0–10000.                                       |
| `name`                                             | Trigger name (needed for `EnableTrigger`, `SetTriggerOption`, etc.). |
| `group`                                            | Group name for bulk enable/disable.                                  |
| `omit_from_output`                                 | `"y"` hides the matched line from the output window.                 |
| `omit_from_log`                                    | `"y"` keeps it out of the log file.                                  |
| `keep_evaluating`                                  | `"y"` lets later triggers also test the line.                        |
| `ignore_case`                                      | `"y"` case-insensitive match.                                        |
| `repeat`                                           | `"y"` re-match repeatedly within the same line.                      |
| `expand_variables`                                 | `"y"` expand `@var` in the match/send.                               |
| `custom_colour`                                    | Apply a custom colour (1–16) to the matched text.                    |
| `colour_changes` / `multi_line` / `lines_to_match` | Multi-line and colour-trigger options.                               |

## `<alias>` attributes (common)

| Attribute                                       | Meaning                                              |
| ----------------------------------------------- | ---------------------------------------------------- |
| `match`                                         | The command pattern the user types.                  |
| `regexp`                                        | `"y"` for regex aliases; else wildcard.              |
| `enabled`                                       | `"y"`/`"n"`.                                         |
| `script`                                        | Script function `fn(name, line, wildcards)`.         |
| `send_to`                                       | Destination for `<send>` (12 = script).              |
| `sequence`                                      | Order among aliases.                                 |
| `name` / `group`                                | For programmatic control / grouping.                 |
| `ignore_case`                                   | Case-insensitive.                                    |
| `echo_alias`                                    | `"y"` echoes the typed command to the output window. |
| `omit_from_command_history`                     | Keep out of the command-recall history.              |
| `menu` / `keep_evaluating` / `expand_variables` | As per the alias editor.                             |

Within a `<send>`, `%0` is the whole match, `%1..%9` are wildcards, `%<name>` is a named capture.

## `<timer>` attributes (common)

| Attribute                                         | Meaning                                                         |
| ------------------------------------------------- | --------------------------------------------------------------- |
| `hour` / `minute` / `second`                      | Interval (every N) **or** clock time (with `at_time="y"`).      |
| `at_time`                                         | `"y"` = fire at the given clock time instead of every interval. |
| `enabled`                                         | `"y"`/`"n"`.                                                    |
| `script` / `send_to`                              | Handler function / destination of `<send>`.                     |
| `name` / `group`                                  | For `EnableTimer`, `ResetTimer`, etc.                           |
| `active_closed`                                   | `"y"` keeps the timer running while disconnected.               |
| `one_shot`                                        | `"y"` fires once then disables itself.                          |
| `offset_hour` / `offset_minute` / `offset_second` | Phase offset for interval timers.                               |

## `<variable>` and `<include>`

```xml
<variables>
  <variable name="count">0</variable>
</variables>
```

Variable values are strings. Read/write with `GetVariable` / `SetVariable`. With `save_state="y"` they survive restarts.

```xml
<include name="constants.lua"/>
```

`<include>` pulls in an external script file (resolved relative to the plugin) — used in older plugins for shared constant libraries (e.g. `constants.vbs`). For Lua, prefer `require` over `<include>`.
