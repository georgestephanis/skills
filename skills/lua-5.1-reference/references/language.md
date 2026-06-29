# Lua 5.1 language details (reference)

Deeper semantics from the official manual, §2: <https://www.lua.org/manual/5.1/manual.html>. The SKILL.md covers the essentials; this file is for the edge cases.

## Lexical conventions

- **Keywords (21, reserved):** `and break do else elseif end false for function if in local nil not or repeat return then true until while`.
- **Identifiers:** letters/digits/underscore, not starting with a digit. Names like `_VERSION` (underscore + uppercase) are reserved by convention for Lua internals.
- **Comments:** `--` to end of line; long comment `--[[ ... ]]` with levels `--[=[ ... ]=]`.
- **Numbers:** decimal `3`, `3.0`, `3.1416`, `0.31416e1`, `0xff` (hex). All are doubles.
- **Short strings:** `"..."` or `'...'` with escapes `\a \b \f \n \r \t \v \\ \" \' \ddd` (up to 3 decimal digits) and `\<newline>`. `\0` is allowed (strings are 8-bit clean).
- **Long strings:** `[[ ... ]]`, levels `[=[ ... ]=]`. No escape processing; a newline right after the opening bracket is dropped. Pick a level whose closing sequence doesn't appear in the content.

## Assignment & multiple values

- Multiple assignment evaluates **all** right-hand expressions before assigning: `a, b = b, a` swaps. Extra values are discarded; missing ones become `nil`.
- A function call / `...` expands to all its values **only in the last slot** of a list (args, table constructor, assignment RHS, return). Elsewhere it's adjusted to one value. `(f())` forces exactly one.

## Visibility / scope rules

- A `local` is in scope from **after** its declaration to the end of the innermost enclosing block. A `local x = x` initializes the new local from the outer `x`.
- Each `local function f` is visible inside its own body (enabling recursion); `local f = function() ... end` is not (the name isn't yet in scope when the function is defined). Use `local function` for recursive locals.
- Closures capture variables, not values — multiple closures over the same local share it (and see later mutations).

## Metatable dispatch (details)

To "get a metamethod" the interpreter does `rawget(getmetatable(obj) or {}, event)` — metamethod lookup itself never triggers metamethods.

- **`__index`** — used on a _read_ of an absent key. If a function: `__index(t, k)`. If a table: lookup continues in it (chains form prototype inheritance).
- **`__newindex`** — used on a _write_ to an absent key. Function: `__newindex(t, k, v)`. Table: the write is redirected there. Use `rawset` to store on the original.
- **Arithmetic** (`__add __sub __mul __div __mod __pow __unm`) and **`__concat`** — tried on the first operand, then the second.
- **`__len`** — for `#` (in pure 5.1 effective for userdata; table `#` uses the raw border).
- **Comparison** — `__eq` only fires when both operands are the same type _and_ share the metamethod; `__lt` and `__le` similarly. `a > b` ⇒ `b < a`; `a >= b` ⇒ `b <= a`.
- **`__call`** — makes a table callable: `obj(args)` ⇒ `__call(obj, args)`.
- **`__tostring`** — used by `print`/`tostring`.
- **`__metatable`** — if set, `getmetatable` returns this value and `setmetatable` errors (locks the metatable).
- **`__mode`** — `"k"`, `"v"`, or `"kv"` makes a table's keys and/or values weak (see GC).
- **`__gc`** — finalizer; in pure 5.1 only honored for **userdata**, not tables.

### Idiomatic class / inheritance

```lua
local Animal = {}
Animal.__index = Animal

function Animal.new(name)
  return setmetatable({ name = name }, Animal)
end
function Animal:speak()           -- ':' gives implicit self
  return self.name .. " makes a sound"
end

local Dog = setmetatable({}, { __index = Animal })  -- Dog inherits Animal
Dog.__index = Dog
function Dog:speak() return self.name .. " barks" end

local d = setmetatable({ name = "Rex" }, Dog)
print(d:speak())   --> Rex barks
```

## Environments (5.1-specific)

Every function has an **environment table** — the table used to resolve its free (global) names. Defaults to `_G`.

- `getfenv(f)` / `setfenv(f, env)` read/replace it. `f` may be a function or a stack level (`1` = the caller). `setfenv(1, {})` sandboxes the current function's globals.
- Threads and the main chunk also have environments.
- **This whole mechanism is removed in 5.2+** (replaced by the `_ENV` upvalue). Code using `setfenv`/`getfenv` will not run on 5.2+ unmodified — a frequent porting blocker for sandboxes and DSLs.

## Garbage collection

- Incremental mark-and-sweep, tuned via `collectgarbage("setpause", n)` and `collectgarbage("setstepmul", n)`; `collectgarbage("count")` returns memory in KB; `collectgarbage("collect")` forces a full cycle.
- **Weak tables:** controlled by `__mode`. Weak keys/values don't prevent collection of their referents; entries vanish after the object is collected. Use for caches and object-association maps.
- **Finalizers:** `__gc` runs when a userdata is collected (pure 5.1: userdata only). Order is reverse of creation; resurrected objects are finalized once.

## Coroutine semantics

- A coroutine is a first-class **thread** value, cooperatively scheduled — only one runs at a time and it yields control explicitly.
- `resume` and `yield` exchange values both ways: arguments to `resume` become the results of the `yield` that suspended the coroutine (or the arguments to the body on first resume); arguments to `yield` become the extra results of `resume`.
- An error inside a coroutine: `resume` returns `false, err` (the coroutine becomes `dead`); a `wrap`-ed coroutine re-raises the error in the caller.
- You cannot `yield` across a C call boundary in pure 5.1 (e.g. from inside `pcall`, `table.sort` comparator, or metamethods) — doing so raises "attempt to yield across metamethod/C-call boundary".

## Error handling details

- `error(value, level)` can raise **any value**, not just strings; `pcall`/`xpcall` return it verbatim as the second result.
- `level` controls only the position prefix added to _string_ messages: `1` = where `error` was called, `2` = that function's caller, `0` = no prefix.
- `assert(v, msg)` returns all its arguments when `v` is truthy, so `local f = assert(io.open(name))` is idiomatic.
- For a traceback, use `xpcall(f, debug.traceback)` — the handler runs while the failing stack is still live.
