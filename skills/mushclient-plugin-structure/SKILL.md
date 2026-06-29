---
name: mushclient-plugin-structure
description: "Author the XML file for a MUSHclient plugin — the <muclient>/<plugin> wrapper, triggers/aliases/timers/variables blocks, the <script> CDATA section, send_to codes, and OnPlugin* lifecycle callbacks. Use when creating, editing, or debugging a .xml plugin for MUSHclient (the Gammon MUD client)."
compatibility: "MUSHclient 5.x on Windows (and under Wine/Linux). Lua is the recommended script language and ships built in; VBscript/JScript also work but need COM. Examples target Lua."
license: MIT
metadata:
    author: georgestephanis
    version: "1.0"
    written: "2026-06-29"
    written_against:
        mushclient: "5.07"
        lua: "5.1"
---

# MUSHclient plugin structure

A MUSHclient plugin is a single XML file bundling related triggers, aliases, timers, variables, and script into one installable unit. This skill covers the **file format and lifecycle**. For writing the Lua inside the `<script>` block, see the companion `mushclient-lua-scripting` skill.

## When to use

- Creating a new `.xml` plugin from scratch, or editing an existing one.
- Adding triggers/aliases/timers, wiring them to script handlers, or persisting state.
- Debugging "plugin won't load", parse errors, or callbacks that never fire.
- **Not** for the Lua API surface itself (functions, variables, miniwindows) — use `mushclient-lua-scripting`.

## Inputs required

- What the plugin should do, and which events drive it (incoming MUD text → triggers; user-typed commands → aliases; periodic work → timers).
- A stable **plugin `id`**: a 24-character hex string, unique per plugin (the Plugin Wizard generates one; otherwise invent a random 24-char hex value and keep it constant across versions).
- The target MUSHclient version for the `requires` attribute (use the oldest version whose features you rely on).
- Whether the plugin must remember state between sessions (drives `save_state` + `OnPluginSaveState`).

## Procedure

### 1. Start from the skeleton

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE muclient>
<muclient>
<plugin
   name="My_Plugin"
   author="Your Name"
   id="0123456789abcdef01234567"
   language="Lua"
   purpose="One-line summary shown in the plugins list"
   date_written="2026-06-29 10:00:00"
   requires="5.06"
   version="1.0"
   save_state="y"
   sequence="100"
   >
<description trim="y">
<![CDATA[
Longer help text. Shown by GetPluginInfo(GetPluginID(),3) — wire it to a "help" alias.
]]>
</description>
</plugin>

<triggers>
  <!-- <trigger ...>  -->
</triggers>

<aliases>
  <!-- <alias ...>  -->
</aliases>

<timers>
  <!-- <timer ...>  -->
</timers>

<variables>
  <!-- <variable name="...">value</variable>  -->
</variables>

<script>
<![CDATA[
-- Lua goes here. Functions named OnPlugin* are auto-detected as callbacks.
]]>
</script>

</muclient>
```

`<plugin>` header attributes: `name`, `author`, `id`, `language`, `purpose`, `date_written`, `requires`, `version` are standard. `save_state="y"` enables per-plugin state persistence. `sequence` (0–10000, default 5000) sets evaluation order across plugins — **lower runs earlier**. See `references/elements-and-attributes.md` for the full attribute list of every element.

### 2. Add triggers (react to MUD output)

```xml
<triggers>
  <trigger
   enabled="y"
   match="^You have (\d+) gold\.$"
   regexp="y"
   script="OnGold"
   sequence="100"
   omit_from_output="n"
  >
  </trigger>
</triggers>
```

- `match` is the pattern; set `regexp="y"` for a regular expression (PCRE), otherwise it's a wildcard pattern using `*`/`?`. Use named captures `(?P<name>...)` to get a named `wildcards` table in the handler.
- `script="OnGold"` routes matches to a Lua function `OnGold(name, line, wildcards)`. Alternatively put inline code in a `<send>` child with `send_to="12"` (12 = script) instead of a `script` attribute.
- Common flags: `omit_from_output="y"` (hide the line), `keep_evaluating="y"` (let later triggers also match), `ignore_case="y"`, `repeat="y"` (match multiple times on one line).

### 3. Add aliases (react to commands the user types)

```xml
<aliases>
  <alias match="gold" enabled="y" script="OnGold" sequence="100" />
  <alias match="greet *" enabled="y" send_to="12" sequence="100">
    <send>Send("say Hello, %1!")</send>
  </alias>
</aliases>
```

`%1`, `%2`… (or `%<name>` for named captures) expand to wildcards within `<send>`. `send_to` controls the destination of the `<send>` text — see the **send_to codes** table below.

### 4. Add timers (do something periodically or at a set time)

```xml
<timers>
  <timer name="tick" second="10" send_to="12" enabled="y" active_closed="y">
    <send>OnTick()</send>
  </timer>
</timers>
```

- Interval timer: set `hour`/`minute`/`second` to the period. "At" timer: add `at_time="y"` to fire at a clock time.
- `active_closed="y"` lets it run while disconnected. Or skip the XML timer and call `AddTimer`/`DoAfter` from script.

### 5. Variables and persisting state

Declare initial values with `<variable name="x">value</variable>`. Read/write at runtime with `GetVariable("x")` / `SetVariable("x", v)` (values are always strings). With `save_state="y"`, MUSHclient saves variables to a per-plugin state file on close; restore happens automatically before `OnPluginInstall`. Use `OnPluginSaveState()` to serialize richer data into a variable just before saving:

```lua
function OnPluginSaveState()
  SetVariable("settings", serialize.save("settings"))  -- serialize is a built-in util
end
```

### 6. Wire up lifecycle callbacks

Define any `OnPlugin*` function and MUSHclient calls it automatically — the names are hard-coded. Most useful: `OnPluginInstall` (setup, after state restore), `OnPluginConnect`/`OnPluginDisconnect`, `OnPluginSaveState`, `OnPluginClose`, `OnPluginBroadcast(msg,id,name,text)` (inter-plugin messaging via `BroadcastPlugin`). Full list with exact parameters: `references/plugin-callbacks.md`.

### 7. Install and iterate

Put the `.xml` in MUSHclient's `plugins` directory (or any path), then **File → Plugins → Add**. To reload after editing: select it and **Reinstall**, or script `ReloadPlugin(GetPluginID())`. `requires` blocks loading on older clients.

## send_to codes

Used by the `send_to` attribute on `<send>` and the `SendTo`/`sendto.*` argument of `AddTriggerEx`/`AddAlias`:

| Code      | Name                                     | Destination                                                   |
| --------- | ---------------------------------------- | ------------------------------------------------------------- |
| 0         | world                                    | Send to the MUD                                               |
| 1         | command                                  | The command window                                            |
| 2         | output                                   | Echo to output window (not sent to MUD)                       |
| 3         | status                                   | Status line                                                   |
| 4 / 5 / 7 | notepad / notepadappend / notepadreplace | A notepad window                                              |
| 6         | logfile                                  | The log file                                                  |
| 8         | commandqueue                             | World command queue (speedwalk-aware)                         |
| 9         | variable                                 | Store into a variable                                         |
| 10        | execute                                  | Run as if typed (re-parsed for aliases)                       |
| 11        | speedwalk                                | Interpret as a speedwalk string                               |
| 12        | script                                   | **Send to the script engine (run as Lua)** — the usual choice |
| 13        | immediate                                | Send to world ahead of the speedwalk queue                    |
| 14        | scriptafteromit                          | Script, after lines have been omitted                         |

## Verification

- The plugin appears in **File → Plugins** with no error dialog on Add. Parse errors point to a line/column in the XML.
- Trigger/alias handlers fire: temporarily add `Note("handler ran: " .. line)` and watch the output window.
- `GetPluginInfo(GetPluginID(), 16)` / `PluginSupports(GetPluginID(), "OnPluginConnect")` confirm a callback was detected.
- After a close/reopen cycle, saved variables persist (only if `save_state="y"`).

## Failure modes

- **Plugin won't load / XML parse error.** Almost always unescaped `<`, `>`, or `&` **outside** a CDATA section. Wrap script and `<send>` bodies in `<![CDATA[ ... ]]>`, or escape as `&lt; &gt; &amp; &quot;`. Real-world VBscript plugins escape inline (`active &lt;&gt; &quot;&quot;`); Lua plugins almost always use CDATA.
- **Callback never fires.** The function name is misspelled, or it's defined _inside_ another function so MUSHclient's name scan misses it — define `OnPlugin*` at the top level of the `<script>` block. Names are case-sensitive.
- **State not persisting.** `save_state="y"` missing, or you stored a Lua table in a variable without serializing (variables hold strings only).
- **Trigger matches the wrong things / not at all.** `regexp="y"` forgotten (so `*` is treated as a wildcard, not regex), or anchoring (`^`/`$`) missing. Test the pattern in the trigger editor.
- **Two plugins fight over order.** Adjust `sequence` — lower numbers evaluate first.
- **Duplicate `id`.** Copy-pasting a plugin without changing `id` makes MUSHclient treat them as the same plugin. Generate a fresh 24-char hex id per plugin.

## Escalation

- If the plugin needs GUI surfaces (miniwindows, custom drawing), graphics, or deep API use, hand off to `mushclient-lua-scripting` and the miniwindows docs.
- For anything ambiguous about a specific function's prototype or return codes, consult the authoritative per-function page: `https://www.gammon.com.au/scripts/doc.php?function=<Name>`.
