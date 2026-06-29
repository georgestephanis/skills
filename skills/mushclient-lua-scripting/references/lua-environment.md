# MUSHclient Lua environment (reference)

MUSHclient embeds **Lua 5.1** (hard-coded, no COM). Source docs: <https://www.gammon.com.au/scripts/doc.php?general=lua> and the per-topic pages linked below.

## Calling conventions

- World functions are global: `Note(...)`, `Send(...)`, `GetVariable(...)`. No `world.` prefix needed (a `world` table alias also exists for ported code).
- Handler functions named by a `script=` attribute receive `(name, line, wildcards)`.
- Functions return Lua values directly; action functions usually return an `error_code.*` number (0 = `eOK`).

## Predefined constant tables

Use these instead of magic numbers (all are global Lua tables):

| Table           | Used for                                                                                                                                            |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sendto`        | `send_to` destinations: `sendto.world` (0) … `sendto.script` (12) … `sendto.immediate` (13).                                                        |
| `trigger_flag`  | `AddTriggerEx` flags: `Enabled`, `RegularExpression`, `KeepEvaluating`, `IgnoreCase`, `OmitFromOutput`, `OmitFromLog`, `Replace`, `Temporary`, etc. |
| `alias_flag`    | `AddAlias`/`AddAliasEx` flags: `Enabled`, `RegularExpression`, `IgnoreAliasCase`, `OmitFromLogFile`, `AliasQueue`, etc.                             |
| `timer_flag`    | `AddTimer` flags: `Enabled`, `AtTime`, `OneShot`, `ActiveWhenClosed`, `Replace`, `Temporary`.                                                       |
| `custom_colour` | `NoChange` plus custom colour indices for trigger colouring.                                                                                        |
| `error_code`    | Return codes: `eOK`, `eTriggerNotFound`, `eBadRegularExpression`, `eAliasAlreadyExists`, … Compare results against these.                           |
| `error_desc`    | Human-readable text for an `error_code`.                                                                                                            |
| `miniwin`       | Constants for the `Window*` miniwindow API (positions, pen styles, brush styles, fonts).                                                            |

## Bundled Lua standard & extension modules

Available via `require` (or already loaded). Each has a doc page at `doc.php?general=lua_<name>`:

| Module                                                                 | What it is                                                                                                                             |
| ---------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `string`, `table`, `math`, `os`, `io`, `coroutine`, `debug`, `package` | Lua 5.1 standard libraries (`lua_string`, `lua_tables`, `lua_math`, `lua_os`, `lua_io`, `lua_coroutines`, `lua_debug`, `lua_package`). |
| `bit`                                                                  | Bit-manipulation library (`lua_bit`) — Lua 5.1 has no native bit ops.                                                                  |
| `bc`                                                                   | Arbitrary-precision ("big number") arithmetic (`lua_bc`).                                                                              |
| `rex` / `rex_pcre`                                                     | PCRE regular expressions (`lua_rex`) — same engine MUSHclient triggers use.                                                            |
| `lpeg`                                                                 | Parsing Expression Grammars (`lua_lpeg`).                                                                                              |
| `sqlite3`                                                              | SQLite database interface (`lua_sqlite3`); also see the `Database*` world functions.                                                   |
| `utils`                                                                | Gammon's utility library — see below (`lua_utils`).                                                                                    |

## The `utils` library (highlights)

~45 helpers under `utils.*` (full list: <https://www.gammon.com.au/scripts/doc.php?general=lua_utils>). Frequently useful:

- `utils.split(s, sep)` — split a delimited string into a table.
- `utils.msgbox(text, title, ...)`, `utils.editbox(...)`, `utils.choose(...)`, `utils.multilistbox(...)` — native Windows dialogs.
- `utils.filepicker(...)`, `utils.directorypicker(...)`, `utils.fontpicker(...)` — standard pickers.
- `utils.base64encode/decode`, `utils.tohex/fromhex`, `utils.md5`, `utils.sha256`, `utils.hash` — encoding/hashing.
- `utils.compress` / `utils.decompress` — zlib compression.
- `utils.utf8encode/decode/convert/sub/valid` — UTF-8 handling.
- `utils.xmlread(s)` — parse XML into a nested table.
- `utils.readdir(path)` — read a directory into a table.
- `utils.timer()` — high-resolution timer (for benchmarking).
- `utils.shellexecute(...)` — run a Windows shell command.

## Gammon's helper globals (from the bundled `lua/` scripts)

These come from script files MUSHclient auto-loads and are widely used in community plugins:

- `tprint(t)` — pretty-print a table to the output window (debugging).
- `serialize.save(name)` / `serialize.save_simple(v)` — serialize a global (by name) or a value into Lua source you can store in a variable and `loadstring()` back.
- `copytable.deep(t)` / `copytable.shallow(t)` — table copies.
- `fixsb(name)` — scrollback/miniwindow helper used in window plugins.

If one of these is `nil` in your plugin, `require` the relevant module or copy the helper in — auto-loading depends on the install's `lua/` directory and the world's script-prefix settings.

## Package paths / `require`

`package.path` / `package.cpath` are seeded relative to the MUSHclient install (its `lua/` folder) and the world file. To load your own modules, add to `package.path` or place files where MUSHclient already searches. Prefer `require "mymodule"` over the legacy `<include>` XML element for Lua.
