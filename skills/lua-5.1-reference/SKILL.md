---
name: lua-5.1-reference
description: "Reference for the Lua 5.1 language and standard library — types, operators and precedence, scoping/closures, metatables and metamethods, error handling, coroutines, string patterns, and the basic/string/table/math/io/os/coroutine/debug/package libraries. Use when writing or debugging Lua 5.1 code, or when 5.2+ idioms (goto, integer subtype, table.unpack, bit32) need translating down to 5.1."
compatibility: "Lua 5.1 specifically (the version embedded in MUSHclient, LuaJIT's language baseline, Redis, nginx/OpenResty, WoW, etc.). Not 5.2/5.3/5.4 — those differ; see the gotchas section."
license: MIT
metadata:
    author: georgestephanis
    version: "1.0"
    written: "2026-06-29"
    written_against:
        lua: "5.1"
        source: "https://www.lua.org/manual/5.1/manual.html"
---

# Lua 5.1 reference

Lua 5.1 is a small, dynamically-typed language still widely embedded (MUSHclient, LuaJIT's baseline, Redis, OpenResty, game engines). This skill is a working reference for the **language and standard library** as defined by the [official 5.1 manual](https://www.lua.org/manual/5.1/manual.html). It is deliberately pinned to 5.1 because later versions changed meaningful things.

## When to use

- Writing or debugging Lua **5.1** code, including embedded contexts (e.g. MUSHclient plugins — pair with `mushclient-lua-scripting`).
- You need exact semantics: operator precedence, `#` on tables with holes, modulo definition, equality without coercion, metamethod names, `setfenv`/environments.
- Translating Lua 5.2+ code down to 5.1 (or recognizing why 5.2+ snippets fail under 5.1).
- Looking up a standard-library function's signature or a string-pattern class — see `references/standard-library.md`.
- **Not** the place for the Lua C API (`lua_*`/`luaL_*`); that's section 3–4 of the manual and rarely needed from script land.

## Inputs required

- Confirm the target really is 5.1 (`print(_VERSION)` → `"Lua 5.1"`). LuaJIT reports `"Lua 5.1"` but adds extensions; Redis/OpenResty are 5.1; many systems ship 5.3/5.4 where this skill's gotchas flip.

## Language essentials

### Types

Eight types: `nil`, `boolean`, `number` (all numbers are **IEEE 754 doubles** — no integer subtype in 5.1), `string` (8-bit clean, immutable, interned), `table` (the only data structure), `function`, `userdata`, `thread` (coroutines). `type(v)` returns the type name as a string.

### Truthiness

Only `false` and `nil` are falsy. **`0`, `""`, and `NaN` are all truthy.**

### Operators & precedence (low → high)

```text
or
and
<   >   <=   >=   ~=   ==
..
+   -
*   /   %
not   #   - (unary)
^
```

- `..` (concat) and `^` (exponent) are **right-associative**; all other binary operators are left-associative.
- Modulo: `a % b == a - math.floor(a/b)*b` (result takes the sign of the divisor).
- `/` is always float division (no `//`). `^` works for any exponent: `x^-0.5`.
- `==`/`~=` do **no coercion**: `"0" == 0` is `false`. Different types are never equal. Tables/functions/userdata/threads compare by identity.
- `..` and arithmetic coerce between numbers and numeric strings (`"10" + 1 == 11`, `1 .. 2 == "12"`).
- `#` length: exact for sequences `1..n` with no holes; **undefined on tables with `nil` holes** (may return any border). Don't use `#` to count a table with gaps — track a count yourself.

### Variables, scope, closures

- Globals live in a table (`_G`); a bare `x` is `_G.x` unless a `local x` is in scope.
- `local` has **lexical (block) scope**. Declare before use; a local is visible only after its declaration to the end of its block.
- Functions are first-class values and **lexical closures** — an inner function captures the actual local variables (upvalues), shared by reference:

```lua
local function counter()
  local n = 0
  return function() n = n + 1; return n end
end
```

- `function t.f()` is sugar for `t.f = function()`; `function t:m(a)` is sugar for `t.m = function(self, a)` and `o:m(x)` passes `o` as `self`.

### Multiple values & varargs

Functions and several expressions yield **multiple values**. Adjustment rules bite often:

- A function call or `...` in the **last** position of a list expands fully; **anywhere else it's truncated to one value**. `print(f(), 1)` passes only `f()`'s first value.
- Wrap in parentheses to force one value: `(f())`.
- In 5.1 varargs use `...` and `select`: `select("#", ...)` is the count, `select(n, ...)` drops the first n-1. `unpack(t)` expands an array (it's a global, **not** `table.unpack`).

### Tables

The single structuring type — arrays, maps, objects, namespaces. Array part is **1-indexed** by convention (`ipairs`, `#`, `table.*` all assume 1-based). `t.k` is sugar for `t["k"]`. `pairs` iterates all keys (unordered); `ipairs` iterates `1,2,3,…` until the first `nil`.

### Metatables & metamethods

`setmetatable(t, mt)` / `getmetatable(t)`. Behavior is overridden via `__`-prefixed keys (events). The full 5.1 set:

`__index`, `__newindex`, `__call`, `__add`, `__sub`, `__mul`, `__div`, `__mod`, `__pow`, `__unm`, `__concat`, `__len`, `__eq`, `__lt`, `__le`, `__gc` (userdata only in pure 5.1), `__mode` (weak tables), `__metatable`, `__tostring`.

`__index` may be a function or a table (the classic prototype/inheritance mechanism). `__eq`/`__lt`/`__le` only fire when both operands share the type and metamethod. See `references/language.md` for the dispatch rules and a class pattern.

### Error handling

- `error(msg [, level])` raises; `level=1` (default) blames the caller, `2` blames the caller's caller, `0` adds no position.
- `pcall(f, ...)` → `true, results...` or `false, err`. `xpcall(f, handler)` runs `handler` (e.g. `debug.traceback`) **before** the stack unwinds.
- `assert(v [, msg])` raises `msg` (or "assertion failed!") when `v` is falsy, else returns its arguments.
- Errors are values, not necessarily strings — `error({code=42})` is valid.

### Coroutines

Collaborative, single-threaded. `coroutine.create(f)` → thread; `coroutine.resume(co, ...)` runs/continues it; `coroutine.yield(...)` suspends and returns values to the matching `resume`; `coroutine.wrap(f)` returns a function that resumes (errors propagate instead of returning `false`). `coroutine.status(co)` → `suspended`/`running`/`normal`/`dead`.

## Standard library

Nine libraries. Quick map (full signatures + descriptions in `references/standard-library.md`):

| Library           | Highlights                                                                                                                                                                                                                                     |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| basic             | `print`, `type`, `tostring`/`tonumber`, `pairs`/`ipairs`/`next`, `pcall`/`xpcall`/`error`/`assert`, `select`, `unpack`, `rawget`/`rawset`/`rawequal`, `setmetatable`/`getmetatable`, `setfenv`/`getfenv`, `require`, `pcall`, `_G`, `_VERSION` |
| `string`          | `format`, `find`/`match`/`gmatch`/`gsub` (Lua **patterns**, not regex), `sub`, `rep`, `byte`/`char`, `len`, `lower`/`upper`, `reverse`                                                                                                         |
| `table`           | `insert`, `remove`, `concat`, `sort`, `maxn`                                                                                                                                                                                                   |
| `math`            | `floor`/`ceil`, `abs`, `min`/`max`, `random`/`randomseed`, `sqrt`, `huge`, `pi`, trig, `fmod`/`modf`, `pow`/`log`/`log10`/`exp`                                                                                                                |
| `io`              | `open`, `read`/`write`, `lines`, `close`, file handles, `io.stdout`/`stdin`/`stderr`                                                                                                                                                           |
| `os`              | `time`, `date`, `clock`, `difftime`, `getenv`, `execute`, `remove`/`rename`, `exit`                                                                                                                                                            |
| `coroutine`       | `create`, `resume`, `yield`, `wrap`, `status`, `running`                                                                                                                                                                                       |
| `debug`           | `traceback`, `getinfo`, `getlocal`/`setlocal`, `getupvalue`/`setupvalue`, `sethook`, `getmetatable`                                                                                                                                            |
| `package`/modules | `require`, `module`, `package.path`/`cpath`/`loaded`/`preload`/`loaders`/`seeall`                                                                                                                                                              |

## Gotchas — and 5.1 vs 5.2+

These are the things that most often break code moved between versions. **Everything below is the 5.1 behavior.**

- **No `goto`/labels** — added in 5.2. Restructure with functions/flags.
- **`unpack` is global**, not `table.unpack` (5.2+ moved it). `table.unpack` is `nil` in 5.1.
- **No bitwise operators** and **no `bit32`** library. Use an embedded `bit`/`bit32` lib if the host provides one (MUSHclient ships `bit`); otherwise do arithmetic.
- **Environments use `setfenv`/`getfenv`** (function environments). 5.2 replaced these with `_ENV` upvalues — `setfenv` is gone in 5.2+. This is a hard incompatibility for sandboxing code.
- **`__gc` only runs for userdata** in pure 5.1; tables with `__gc` are ignored (5.2+ honors them).
- **`#` on tables with holes is undefined** (same in all versions, but people rely on it anyway).
- **`math.random` integer form**: `math.random(m, n)` and `math.random(n)` exist; seed with `math.randomseed(os.time())` or sequences repeat.
- **`os.exit` takes no boolean** in 5.1 (only a numeric/absent code); the boolean/close form is 5.2+.
- **`load` differs**: in 5.1 `load` takes a _function_ that returns chunk pieces; to compile a string use `loadstring(s [, chunkname])`. (5.2+ merged them into `load`.)
- **Integer division `//`** doesn't exist (5.3+). All numbers are doubles; use `math.floor(a/b)`.
- **`string.format` `%s`** calls `tostring`-like coercion only on numbers/strings — pass a string for arbitrary values, and note `%d` on a float errors if it has a fractional part in strict builds.
- **Long strings/comments**: `[[ ... ]]` and `--[[ ... ]]`, with levels `[=[ ... ]=]`. A `[==[` opener needs the matching `]==]`.

## Verification

- `print(_VERSION)` confirms `Lua 5.1`.
- Syntax-check without running: `luac5.1 -p file.lua` (or `luac -p` if the only Lua present is 5.1).
- Probe semantics in an interactive `lua5.1` REPL — the manual's `-->` notation marks expected results.
- For embedded hosts (MUSHclient, Redis, OpenResty), run snippets in that host; the language is 5.1 but available libraries differ.

## Failure modes

- **`attempt to call a nil value (global 'table.unpack'/'goto'/'bit32')`** — a 5.2+ idiom under 5.1. Apply the gotchas above.
- **`'#' returns wrong count`** — the table has `nil` holes; track length explicitly or use `table.maxn` (which itself is removed in 5.3+).
- **Pattern didn't match like a regex** — Lua patterns are _not_ regular expressions: no alternation `|`, no `\d` (use `%d`), `*`/`+`/`-`/`?` apply to a single class, magic chars are `^$()%.[]*+-?`. See `references/standard-library.md` §patterns. If you need real regex in an embedded host, use that host's PCRE binding.
- **Off-by-one** — Lua is 1-indexed; `string.sub(s, 1, 1)` is the first char, `s:sub(-1)` the last.
- **Lost return values** — a multi-value call not in tail position was truncated to one value. Reorder or capture into locals.
- **Float surprise** — `0.1 + 0.2 ~= 0.3`; all numbers are doubles. Compare with a tolerance.

## Escalation

- For the C API (embedding/extending Lua in C) consult manual sections 3–4 directly — out of scope here.
- For LuaJIT-specific extensions (FFI, `bit`, `jit.*`, some 5.2 features backported) see the LuaJIT docs; this skill covers only standard 5.1.
- For exact wording on any edge case, the manual is the source of truth: <https://www.lua.org/manual/5.1/manual.html>.
