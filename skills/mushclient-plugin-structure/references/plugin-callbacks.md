# Plugin callbacks (reference)

Authoritative source: <https://www.gammon.com.au/scripts/doc.php?general=plugin_callbacks>.

Define any of these `OnPlugin*` functions at the **top level** of your `<script>` block and MUSHclient calls them automatically — the names are hard-coded and detected when the plugin is installed. Signatures below are exact (parameter names match the docs). For callbacks that gate an action, returning `false` (or the relevant code) can cancel/alter it; see the per-callback notes online.

## Lifecycle

| Callback              | Parameters | Fires when                                                                                                |
| --------------------- | ---------- | --------------------------------------------------------------------------------------------------------- |
| `OnPluginInstall`     | —          | Plugin added; runs after state restore. Do setup here. Top-level script runs _before_ this.               |
| `OnPluginListChanged` | —          | A plugin was added/removed/enabled/disabled, or the world finished loading plugins.                       |
| `OnPluginClose`       | —          | Plugin is being removed (and when the world closes). Tear down here.                                      |
| `OnPluginEnable`      | —          | After the plugin's enabled flag is turned on.                                                             |
| `OnPluginDisable`     | —          | After the plugin's enabled flag is turned off.                                                            |
| `OnPluginSaveState`   | —          | Just before MUSHclient saves plugin state — serialize data into variables now. Requires `save_state="y"`. |
| `OnPluginWorldSave`   | —          | The world file is being saved.                                                                            |
| `OnPluginGetFocus`    | —          | The plugin's world is gaining focus.                                                                      |
| `OnPluginLoseFocus`   | —          | The plugin's world is losing focus.                                                                       |
| `OnPluginTick`        | —          | Roughly every "tick" — periodic hook for light polling.                                                   |

## Connection

| Callback             | Parameters | Fires when                                   |
| -------------------- | ---------- | -------------------------------------------- |
| `OnPluginConnect`    | —          | World connected (after login/NAWS/log open). |
| `OnPluginDisconnect` | —          | World disconnected (before the log closes).  |

## Input — commands the user types

| Callback                 | Parameters | Fires when                                                                    |
| ------------------------ | ---------- | ----------------------------------------------------------------------------- |
| `OnPluginCommand`        | `(sText)`  | User pressed Enter; about to process the command. Return `false` to suppress. |
| `OnPluginCommandChanged` | —          | The command-window contents changed.                                          |
| `OnPluginCommandEntered` | `(sText)`  | The command after alias processing.                                           |
| `OnPluginTabComplete`    | `(s)`      | Tab-completion requested.                                                     |

## Sending / receiving MUD text

| Callback                 | Parameters | Fires when                                                       |
| ------------------------ | ---------- | ---------------------------------------------------------------- |
| `OnPluginSend`           | `(sText)`  | About to send text to the MUD. Return `false` to block the send. |
| `OnPluginSent`           | `(sText)`  | After text was sent to the MUD.                                  |
| `OnPluginLineReceived`   | `(sText)`  | A complete line arrived from the MUD (before triggers).          |
| `OnPluginPartialLine`    | `(sText)`  | A partial (un-terminated) line arrived.                          |
| `OnPluginPacketReceived` | `(sText)`  | A raw network packet arrived (before line assembly).             |

## Inter-plugin & debug

| Callback              | Parameters              | Fires when                                                                     |
| --------------------- | ----------------------- | ------------------------------------------------------------------------------ |
| `OnPluginBroadcast`   | `(msg, id, name, text)` | Another plugin called `BroadcastPlugin` — primary inter-plugin messaging hook. |
| `OnPluginTrace`       | `(line)`                | A trace line was produced.                                                     |
| `OnPluginPacketDebug` | `(line)`                | Packet-debug output.                                                           |

## Display / drawing

| Callback                     | Parameters                     | Fires when                                                      |
| ---------------------------- | ------------------------------ | --------------------------------------------------------------- |
| `OnPluginScreendraw`         | `(type, log, line)`            | A line is being drawn to the screen.                            |
| `OnPluginDrawOutputWindow`   | `(firstline, offset, notused)` | Custom drawing over the output window (miniwindow compositing). |
| `OnPluginWorldOutputResized` | —                              | The output window was resized.                                  |
| `OnPluginMouseMoved`         | `(x, y, mw)`                   | Mouse moved over a miniwindow.                                  |
| `OnPluginPlaySound`          | `(sound)`                      | A sound is about to play.                                       |

## Telnet negotiation

| Callback                       | Parameters     | Fires when                                                         |
| ------------------------------ | -------------- | ------------------------------------------------------------------ |
| `OnPluginTelnetRequest`        | `(type, data)` | Server WILL/DO negotiation for an option; return `true` to accept. |
| `OnPluginTelnetOption`         | `(data)`       | A telnet option packet arrived.                                    |
| `OnPluginTelnetSubnegotiation` | `(type, data)` | Telnet subnegotiation (e.g. ATCP/GMCP/MSDP payloads).              |
| `OnPlugin_IAC_GA`              | —              | An IAC GA (Go-Ahead) was received.                                 |

## Chat (MUSHclient peer-to-peer chat)

| Callback                     | Parameters             | Fires when                               |
| ---------------------------- | ---------------------- | ---------------------------------------- |
| `OnPluginChatAccept`         | `(sText)`              | Incoming chat connection request.        |
| `OnPluginChatMessage`        | `(id, message, sText)` | Chat message received.                   |
| `OnPluginChatMessageOut`     | `(id, message, sText)` | Chat message being sent.                 |
| `OnPluginChatDisplay`        | `(message, sText)`     | A chat message is about to be displayed. |
| `OnPluginChatNewUser`        | `(id, name)`           | A chat user connected.                   |
| `OnPluginChatUserDisconnect` | `(id, name)`           | A chat user disconnected.                |

## Trigger/alias/timer handlers vs. callbacks

The `script="..."` attribute on a trigger/alias/timer names a **handler**, not a callback. Handlers always receive `(name, line, wildcards)`:

- `name` — the trigger/alias/timer's name.
- `line` — the matched line (triggers) or typed command (aliases).
- `wildcards` — a Lua table of captures: numeric `wildcards[1]..` and, for named captures `(?P<x>..)`, `wildcards["x"]`.
