# Lua 5.1 standard library (reference)

Signatures and semantics from the official manual, §5: <https://www.lua.org/manual/5.1/manual.html>. `[x]` marks an optional argument. Functions returning multiple values are noted.

## Basic functions (global)

| Function                        | Purpose                                                                                                     |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `assert(v [, message])`         | Raise `message` (default "assertion failed!") if `v` is falsy; otherwise return all arguments.              |
| `collectgarbage([opt [, arg]])` | Control the GC: `"collect"`, `"stop"`, `"restart"`, `"count"`, `"step"`, `"setpause"`, `"setstepmul"`.      |
| `dofile([filename])`            | Load and run a file (stdin if omitted). Errors propagate.                                                   |
| `error(message [, level])`      | Raise an error. `level` 1 = caller (default), 2 = caller's caller, 0 = no position info.                    |
| `_G`                            | The global environment table itself. `_G.x` ≡ global `x`.                                                   |
| `getfenv([f])`                  | Get the environment of function `f` (or the calling function; `1` = caller).                                |
| `getmetatable(object)`          | Return the metatable (or its `__metatable` field if set), else `nil`.                                       |
| `ipairs(t)`                     | Iterator over `1,2,…` until first `nil`. Returns `iter, t, 0`.                                              |
| `load(func [, chunkname])`      | Build a chunk by repeatedly calling `func` for string pieces. Returns chunk or `nil, err`.                  |
| `loadfile([filename])`          | Compile a file into a chunk without running it. Returns chunk or `nil, err`.                                |
| `loadstring(s [, chunkname])`   | Compile a **string** into a chunk. (5.1-only name; 5.2+ folds this into `load`.)                            |
| `next(t [, key])`               | Primitive table traversal; returns next key/value pair, or `nil` at the end.                                |
| `pairs(t)`                      | Iterator over all keys (unordered). Returns `next, t, nil`.                                                 |
| `pcall(f [, args...])`          | Protected call. Returns `true, results…` or `false, err`.                                                   |
| `print(...)`                    | Write `tostring` of each argument, tab-separated, plus newline, to stdout.                                  |
| `rawequal(a, b)`                | Equality without invoking `__eq`.                                                                           |
| `rawget(t, k)`                  | Index without invoking `__index`.                                                                           |
| `rawset(t, k, v)`               | Assign without invoking `__newindex`; returns `t`.                                                          |
| `select(n, ...)`                | `select("#", ...)` = arg count; `select(n, ...)` = args from position `n` on.                               |
| `setfenv(f, table)`             | Set the environment of function `f` (or stack level `f`). 5.1-only — removed in 5.2+.                       |
| `setmetatable(t, mt)`           | Set table `t`'s metatable (`mt` may be `nil`); returns `t`. Errors if `__metatable` is protected.           |
| `tonumber(e [, base])`          | Convert to number (optional `base` 2–36), else `nil`.                                                       |
| `tostring(e)`                   | Convert any value to a string (honors `__tostring`).                                                        |
| `type(v)`                       | Type name: `"nil"`, `"number"`, `"string"`, `"boolean"`, `"table"`, `"function"`, `"thread"`, `"userdata"`. |
| `unpack(list [, i [, j]])`      | Return `list[i]…list[j]` (default `1`…`#list`). **Global** in 5.1, not `table.unpack`.                      |
| `_VERSION`                      | The string `"Lua 5.1"`.                                                                                     |
| `xpcall(f, handler)`            | Like `pcall` but `handler` runs on error _before_ unwinding (use `debug.traceback`).                        |

`require(modname)` and `module(name [, ...])` are documented under **package/modules** below.

## `string` (Lua patterns, not regex — see §Patterns)

| Function                                     | Purpose                                                                                                       |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `string.byte(s [, i [, j]])`                 | Numeric codes of bytes `i..j` (default `i=1`, `j=i`). Multiple returns.                                       |
| `string.char(...)`                           | Build a string from byte codes.                                                                               |
| `string.dump(function)`                      | Binary representation of a Lua function (no upvalues).                                                        |
| `string.find(s, pattern [, init [, plain]])` | Return start, end indices of first match (+ captures), or `nil`. `plain=true` = literal search.               |
| `string.format(fmt, ...)`                    | `printf`-style: `%d %i %u %f %g %e %s %q %x %X %o %c %%`. `%q` quotes for safe re-reading.                    |
| `string.gmatch(s, pattern)`                  | Iterator returning successive captures (or whole matches) — no position args.                                 |
| `string.gsub(s, pattern, repl [, n])`        | Replace up to `n` matches; `repl` may be string (`%1`…`%9`, `%0`), table, or function. Returns result, count. |
| `string.len(s)`                              | Byte length (embedded zeros counted). Same as `#s`.                                                           |
| `string.lower(s)` / `string.upper(s)`        | Case conversion (locale-dependent).                                                                           |
| `string.match(s, pattern [, init])`          | Return captures of first match (or whole match), else `nil`.                                                  |
| `string.rep(s, n)`                           | `s` concatenated `n` times.                                                                                   |
| `string.reverse(s)`                          | Reversed string.                                                                                              |
| `string.sub(s, i [, j])`                     | Substring `i..j`; negatives count from the end; default `j = -1`.                                             |

All `string.f(s, ...)` are also callable as methods: `s:f(...)` (strings have a metatable with `__index = string`).

## `table`

| Function                              | Purpose                                                                         |
| ------------------------------------- | ------------------------------------------------------------------------------- |
| `table.concat(t [, sep [, i [, j]]])` | Join `t[i]..t[j]` (numbers/strings) with `sep`.                                 |
| `table.insert(t, [pos,] value)`       | Insert `value` at `pos` (default end), shifting elements up.                    |
| `table.maxn(t)`                       | Largest positive numeric key (or 0). Removed in 5.3+.                           |
| `table.remove(t [, pos])`             | Remove and return element at `pos` (default last), shifting down.               |
| `table.sort(t [, comp])`              | In-place sort; `comp(a,b)` returns true when `a` should come first. Not stable. |

## `math`

| Function                                                             | Purpose                                                   |
| -------------------------------------------------------------------- | --------------------------------------------------------- |
| `math.abs`, `math.ceil`, `math.floor`                                | Absolute value, round up, round down.                     |
| `math.max(...)`, `math.min(...)`                                     | Max/min of arguments.                                     |
| `math.sqrt`, `math.pow(x,y)`, `math.exp`, `math.log`, `math.log10`   | Powers and logs (`math.pow` removed in 5.3+; use `x^y`).  |
| `math.fmod(x,y)`, `math.modf(x)`                                     | C `fmod`; integer and fractional parts (two returns).     |
| `math.sin/cos/tan`, `asin/acos/atan`, `atan2(y,x)`, `sinh/cosh/tanh` | Trigonometry (radians).                                   |
| `math.deg(x)`, `math.rad(x)`                                         | Radians↔degrees.                                          |
| `math.frexp(x)`, `math.ldexp(m,e)`                                   | Mantissa/exponent split and recombine.                    |
| `math.random([m [, n]])`                                             | `[0,1)` with no args; `[1,m]` with one; `[m,n]` with two. |
| `math.randomseed(x)`                                                 | Seed the generator (e.g. `os.time()`).                    |
| `math.pi`, `math.huge`                                               | π and `+inf`.                                             |

## `io`

Two styles: simple (`io.read`/`io.write` on the default files) and handle methods (`f:read`, `f:write`, …).

| Function                                                              | Purpose                                                                                                                                                               |
| --------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `io.open(filename [, mode])`                                          | Open a file → handle, or `nil, errmsg`. Modes `"r"`, `"w"`, `"a"`, `"r+"`, `"w+"`, `"a+"`, append `"b"` for binary.                                                   |
| `io.close([file])`                                                    | Close a file (default output file).                                                                                                                                   |
| `io.read(...)` / `file:read(...)`                                     | Read formats: `"*l"` (line, drops the newline), `"*a"` (whole file), `"*n"` (a number), or a byte count `n`. (`"*L"`, the keep-newline format, is 5.2+ — not in 5.1.) |
| `io.write(...)` / `file:write(...)`                                   | Write strings/numbers (no separators or newline). Returns the file.                                                                                                   |
| `io.lines([filename])` / `file:lines()`                               | Iterator over lines. With a filename, opens/closes automatically.                                                                                                     |
| `io.input([file])`, `io.output([file])`                               | Get/set the default input/output file.                                                                                                                                |
| `io.popen(prog [, mode])`                                             | Run a shell command, returning a file handle (platform-dependent).                                                                                                    |
| `io.tmpfile()`, `io.type(obj)`                                        | Temp file handle; `"file"`/`"closed file"`/`nil`.                                                                                                                     |
| `io.stdin`, `io.stdout`, `io.stderr`                                  | Predefined handles.                                                                                                                                                   |
| `file:seek([whence [, offset]])`, `file:flush()`, `file:setvbuf(...)` | Position, flush, buffering.                                                                                                                                           |

## `os`

| Function                                     | Purpose                                                                              |
| -------------------------------------------- | ------------------------------------------------------------------------------------ |
| `os.time([table])`                           | Current time (epoch), or time of a date table (`year/month/day/hour/min/sec/isdst`). |
| `os.date([format [, time]])`                 | Format a time. `"*t"`/`"!*t"` returns a table; `!` = UTC; else `strftime` format.    |
| `os.clock()`                                 | CPU time used by the program, in seconds (for benchmarking).                         |
| `os.difftime(t2, t1)`                        | Seconds between two times.                                                           |
| `os.getenv(name)`                            | Environment variable value or `nil`.                                                 |
| `os.execute([command])`                      | Run a shell command; returns the system-dependent status.                            |
| `os.remove(filename)`, `os.rename(old, new)` | Delete / rename; return `true` or `nil, err`.                                        |
| `os.tmpname()`                               | A name usable for a temp file.                                                       |
| `os.exit([code])`                            | Terminate the process. **5.1 takes a numeric code only** (no boolean form).          |
| `os.setlocale(locale [, category])`          | Set/query locale.                                                                    |

## `coroutine`

| Function                       | Purpose                                                                                   |
| ------------------------------ | ----------------------------------------------------------------------------------------- |
| `coroutine.create(f)`          | New coroutine (a thread) from function `f`; does not start it.                            |
| `coroutine.resume(co [, ...])` | Start/continue `co`; returns `true, yielded…` or `false, err`.                            |
| `coroutine.yield(...)`         | Suspend the running coroutine, passing values back to `resume`.                           |
| `coroutine.wrap(f)`            | Return a function that resumes the coroutine; errors propagate (no `true/false` wrapper). |
| `coroutine.status(co)`         | `"suspended"`, `"running"`, `"normal"`, or `"dead"`.                                      |
| `coroutine.running()`          | The running coroutine, or `nil` in the main thread.                                       |

## `debug`

| Function                                                            | Purpose                                                                          |
| ------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| `debug.traceback([thread,] [message [, level]])`                    | A traceback string (ideal `xpcall` handler).                                     |
| `debug.getinfo([thread,] f [, what])`                               | Info table about function/stack level `f`.                                       |
| `debug.getlocal([thread,] level, i)` / `debug.setlocal(...)`        | Read/write local variables at a stack level.                                     |
| `debug.getupvalue(f, i)` / `debug.setupvalue(f, i, v)`              | Read/write a closure's upvalues.                                                 |
| `debug.getmetatable(v)` / `debug.setmetatable(v, mt)`               | Metatable access bypassing `__metatable`; can set metatables on non-table types. |
| `debug.sethook([thread,] hook, mask [, count])` / `debug.gethook()` | Install/read a debug hook.                                                       |
| `debug.getfenv(o)` / `debug.setfenv(o, t)`                          | Environment of any object (5.1).                                                 |
| `debug.getregistry()`                                               | The C registry table.                                                            |

## package / modules

| Name                                 | Purpose                                                                                                |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| `require(modname)`                   | Load a module once (cached in `package.loaded`); searches `package.loaders`. Returns the module value. |
| `module(name [, ...])`               | Declare a module (legacy; sets up a namespace table). Options like `package.seeall`.                   |
| `package.path` / `package.cpath`     | `;`-separated search templates (with `?`) for Lua / C modules.                                         |
| `package.loaded`                     | Table of already-loaded modules (the `require` cache).                                                 |
| `package.preload`                    | Table of loader functions for modules not on disk.                                                     |
| `package.loaders`                    | The ordered list of searcher functions `require` tries. (Renamed `package.searchers` in 5.2+.)         |
| `package.loadlib(libname, funcname)` | Load a C library and return a function from it.                                                        |
| `package.seeall(module)`             | Set a module's metatable so it sees globals (used with `module`).                                      |

## §Patterns — Lua string patterns (NOT regular expressions)

Used by `find`, `match`, `gmatch`, `gsub`. Magic characters: `^ $ ( ) % . [ ] * + - ?`. Escape any magic (or any punctuation) with `%`.

**Character classes:**

| Class       | Matches                                                            | Complement             |
| ----------- | ------------------------------------------------------------------ | ---------------------- |
| `.`         | any character                                                      | —                      |
| `%a` / `%A` | letters / non-letters                                              | uppercase = complement |
| `%d` / `%D` | digits / non-digits                                                |                        |
| `%l` / `%u` | lower / upper case letters                                         |                        |
| `%s` / `%S` | space / non-space                                                  |                        |
| `%w` / `%W` | alphanumeric / non                                                 |                        |
| `%p` / `%P` | punctuation / non                                                  |                        |
| `%c` / `%C` | control chars / non                                                |                        |
| `%x` / `%X` | hex digits / non                                                   |                        |
| `%z`        | the `\0` byte                                                      |                        |
| `%<punct>`  | that punctuation char literally (e.g. `%%`, `%.`)                  |                        |
| `[set]`     | union of classes/chars; ranges with `-` (e.g. `[%w_]`, `[0-9a-f]`) | `[^set]` = complement  |

**Pattern items (quantifiers apply to a single class):**

| Item      | Meaning                                                                    |
| --------- | -------------------------------------------------------------------------- |
| `*`       | 0 or more, **longest** (greedy)                                            |
| `+`       | 1 or more, longest                                                         |
| `-`       | 0 or more, **shortest** (lazy)                                             |
| `?`       | 0 or 1                                                                     |
| `^` / `$` | anchor at start / end of subject (only special at the pattern's start/end) |

**Captures & specials:**

| Syntax    | Meaning                                                                                   |
| --------- | ----------------------------------------------------------------------------------------- |
| `(...)`   | capture the matched substring                                                             |
| `()`      | **position capture** — captures the current string index (a number)                       |
| `%1`–`%9` | back-reference to a previous capture (in pattern), or substitution in `gsub` repl         |
| `%bxy`    | balanced match: from an `x` to its matching `y` (e.g. `%b()` for balanced parens)         |
| `%f[set]` | frontier pattern: an empty match at a transition from a char not in `set` to one in `set` |

**No alternation (`|`), no `{n,m}` counts, no `\d`-style escapes, no lookaround.** For those, use a real regex engine if the host provides one (e.g. PCRE bindings). Replacement `%0` in `gsub` is the whole match.
