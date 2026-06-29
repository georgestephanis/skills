---
name: mushclient-lua-scripting
description: "Write Lua inside a MUSHclient plugin or world — call the ~420 world functions (Note/ColourNote/Send/AddTriggerEx/GetVariable…), handle trigger/alias/timer callbacks and their wildcards, persist state, and use the bundled Lua modules and utils. Use when writing or debugging the Lua in a MUSHclient .xml plugin or world script file."
compatibility: "MUSHclient 5.x with built-in Lua 5.1. Lua is hard-coded (no COM), so it also works under Wine/Linux. Functions are called directly — no `world.` prefix required."
license: MIT
metadata:
    author: georgestephanis
    version: "1.0"
    written: "2026-06-29"
    written_against:
        mushclient: "5.07"
        lua: "5.1"
---

# MUSHclient Lua scripting

MUSHclient embeds **Lua 5.1** and exposes ~420 built-in "world functions" directly into the global namespace. This skill covers writing that Lua. For the surrounding plugin XML file (the `<plugin>` header, trigger/alias/timer XML, callbacks), see the companion `mushclient-plugin-structure` skill.

## When to use

- Writing the body of a `<script>` block, a world script file, or a "send to script" snippet.
- Calling world functions, handling trigger/alias/timer matches, reading/writing variables, persisting state.
- Debugging Lua errors, `nil` returns, or "function not found" in a MUSHclient context.
- **Not** for the XML structure or `send_to`/callback wiring — that's `mushclient-plugin-structure`.

## Key facts about MUSHclient's Lua

- **No COM.** Unlike VBscript/JScript, Lua is compiled into MUSHclient. Call functions **directly**: `Note("hi")`, not `world.Note("hi")`. A `world` table alias also exists (`world.Note("hi")` works) but is unnecessary in Lua and mostly seen in ported examples.
- **It's Lua 5.1** — not 5.2+. No goto, no integer subtype, `unpack` (not `table.unpack`), `string.gmatch` exists but `%g` etc. follow 5.1 rules. For language/stdlib details and 5.1-vs-5.2+ gotchas, see the `lua-5.1-reference` skill.
- **Variables are strings.** `GetVariable`/`SetVariable` store text only. Numbers come back as strings (`tonumber(GetVariable("hp"))`); tables must be serialized.
- **One Lua state per plugin.** Each plugin has its own globals; they don't leak between plugins. Share data via `BroadcastPlugin`/`CallPlugin` or saved variables.
- **Errors are reported, not fatal to the client.** A runtime error in a handler prints a traceback to the output window and aborts that call only.

## Inputs required

- Which world functions the task needs (browse `references/world-functions.md`; confirm exact prototypes/return codes on the linked Gammon page).
- For trigger/alias work: the capture groups you'll read from `wildcards`.
- For persistence: whether the plugin sets `save_state="y"` (see `mushclient-plugin-structure`).

## Core patterns

### Output to the screen

```lua
Note("plain line")
ColourNote("yellow", "blue", "yellow on blue")      -- named or "#RRGGBB" colours
ColourTell("cyan", "", "no newline ")               -- Tell/ColourTell omit the trailing newline
ColourNote("white", "", "end of the tell")
```

`Note`/`ColourNote` print to the output window **without** sending to the MUD. Prefer these for plugin UI and debugging.

### Send to the MUD

```lua
Send("look")                 -- sends "look\n" to the MUD (echoes in command window)
SendNoEcho("password123")    -- sends without echoing
Execute("north")             -- as if typed: re-parsed through aliases/speedwalk
```

### Trigger / alias / timer handlers

A handler named by a `script="OnGold"` attribute receives `(name, line, wildcards)`:

```lua
function OnGold(name, line, wildcards)
  local amount = tonumber(wildcards[1])     -- numeric capture group 1
  -- named captures from (?P<who>...) are wildcards.who / wildcards["who"]
  Note("You have " .. amount .. " gold.")
end
```

### Variables and persistence

```lua
SetVariable("count", tostring(n))            -- store (string only)
local n = tonumber(GetVariable("count")) or 0  -- read with a fallback

-- For tables, use the bundled serialize util + OnPluginSaveState (needs save_state="y"):
function OnPluginSaveState()
  SetVariable("data", serialize.save("data"))   -- serialize.save reads the global `data`
end
-- On next load, restore in OnPluginInstall:
function OnPluginInstall()
  local saved = GetVariable("data")
  if saved then assert(loadstring(saved))() end  -- re-creates global `data`
end
```

### Creating triggers/timers from script (instead of XML)

```lua
AddTriggerEx("gold", "^You have (\\d+) gold\\.$", "", trigger_flag.Enabled + trigger_flag.RegularExpression,
             custom_colour.NoChange, 0, "", "OnGold", sendto.script, 100)
AddTimer("tick", 0, 0, 10, "", timer_flag.Enabled, "OnTick")   -- every 10 seconds
EnableTrigger("gold", true)
```

Constants like `sendto.script` (12), `trigger_flag.*`, `timer_flag.*`, `custom_colour.*`, `error_code.*` are predefined Lua tables — prefer them over magic numbers. See `references/lua-environment.md`.

### Querying state

```lua
local connected = GetInfo(106)         -- GetInfo(code) returns world/connection facts
local hp = GetVariable("hp")
```

`GetInfo(n)` takes a numeric code (see the GetInfo doc page) and is the catch-all for world/session info.

## Verification

- Drop `Note(...)`/`tprint(...)` probes in a handler and watch the output window.
- Check return codes: most action functions return `error_code.eOK` (0) on success; compare against `error_code.*` rather than `0`.
- For triggers created in script, `GetTriggerInfo("name", 21)` returns the match count.
- Syntax-check a snippet outside MUSHclient with `luac -p` if you have a Lua 5.1 binary (`luac5.1 -p file.lua`).

## Failure modes

- **`attempt to index global 'world' (a nil value)`** — only when Lua's `world` alias is disabled; just call the function bare (`Note(...)`).
- **"No such function" / `nil` call** — typo, or a COM-only example using `world.Foo` syntax incorrectly. Confirm the name in `references/world-functions.md`.
- **Comparing a variable as a number fails** — it's a string; wrap with `tonumber()`. Empty/unset variables return `nil` from `GetVariable`.
- **State lost on restart** — missing `save_state="y"`, or you stored a table without serializing.
- **Regex backslashes** — in XML `match` attributes and in Lua string literals, `\d` must be written `\\d` (Lua string escape). MUSHclient regexes are **PCRE**, not Lua patterns.
- **Lua 5.2+ idioms** — `table.unpack`, `goto`, `bit32` don't exist. Use `unpack`, and the bundled `bit` library for bit ops.
- **Long-running handler blocks the UI** — MUSHclient script is single-threaded; avoid busy loops. Use timers / `DoAfter` for deferred work.

## Escalation

- Miniwindows, fonts, images, and custom drawing are a large sub-API — start at <https://www.gammon.com.au/scripts/doc.php?general=miniwindows> and the `Window*` functions in `references/world-functions.md`.
- For a function's exact prototype, arguments, and return/error codes, the per-function page is authoritative: `https://www.gammon.com.au/scripts/doc.php?function=<Name>`.
- Bundled modules (sqlite3, rex/PCRE, lpeg, bit, bc) and Gammon's util libraries: `references/lua-environment.md`.
