# MUSHclient world functions (reference)

MUSHclient exposes ~420 built-in functions. In Lua they are called directly (no `world.` prefix needed; a `world` table alias also exists). Full, authoritative docs with prototypes and per-function notes: <https://www.gammon.com.au/scripts/doc.php?general=function_list> — each entry links to `doc.php?function=<Name>`.

Grouped below by area for discovery. Signatures and return codes are on the linked pages.

## Output & colour

| Function                   | Purpose                                                                                 |
| -------------------------- | --------------------------------------------------------------------------------------- |
| `AdjustColour`             | Adjust an RGB colour                                                                    |
| `AnsiNote`                 | Make a note in the output window from text with ANSI colour codes imbedded              |
| `BoldColour`               | Gets/sets the RGB colour for one of the 8 ANSI bold colour                              |
| `ColourNameToRGB`          | Converts a named colour to a RGB colour code                                            |
| `ColourNote`               | Sends a message to the output window in specified colour                                |
| `ColourTell`               | Sends a message to the output window in specified colours - not terminated by a newline |
| `CustomColourBackground`   | Sets the RGB value for the background of a custom colour                                |
| `CustomColourText`         | Sets the RGB value for the text of a custom colour                                      |
| `GetClipboard`             | Gets the clipboard content                                                              |
| `GetCustomColourName`      | Gets the name of a custom colour                                                        |
| `GetMapColour`             | Returns the mapping for how a particular colour will be displayed                       |
| `Hyperlink`                | Creates a hyperlink in the output window                                                |
| `InfoColour`               | Sets the colour of the foreground                                                       |
| `MapColour`                | Changes the colour mapping - the way colours are displayed                              |
| `MapColourList`            | Returns an array of all the mapped colour                                               |
| `NormalColour`             | Gets/sets the RGB colour for one of the 8 ANSI normal colour                            |
| `Note`                     | Sends a note to the output window                                                       |
| `NoteColour`               | Chooses which custom colour will be used for world notes                                |
| `NoteColourBack`           | Chooses which RGB colour will be used for world notes - background colour               |
| `NoteColourFore`           | Chooses which RGB colour will be used for world notes - text colour                     |
| `NoteColourName`           | Chooses which RGB colour name will be used for world notes - text and background        |
| `NoteColourRGB`            | Chooses which RGB colour will be used for world notes - text and background             |
| `NotepadColour`            | Changes the text and background colour of the selected notepad window                   |
| `PickColour`               | Invokes the MUSHclient colour picker dialog                                             |
| `RGBColourToName`          | Converts an RGB colour code to its equivalent name                                      |
| `SendImmediate`            | Sends a message to the MUD immediately, bypassing the speedwalk queue                   |
| `SetBackgroundColour`      | Sets a background colour for the output window                                          |
| `SetClipboard`             | Sets the clipboard content                                                              |
| `SetCustomColourName`      | Sets the name of a custom colour                                                        |
| `SetFrameBackgroundColour` | Sets the background colour of the overall MUSHclient frame                              |
| `Tell`                     | Sends a message to the output window - not terminated by a newline                      |

## Sending to MUD

| Function        | Purpose                                                               |
| --------------- | --------------------------------------------------------------------- |
| `DoCommand`     | Queues a MUSHclient menu command                                      |
| `Execute`       | Executes a command as if you had typed it into the command window     |
| `PushCommand`   | Pushes the current command into the command history list              |
| `Queue`         | Queues a command for sending at the "speed walk" rate                 |
| `Send`          | Sends a message to the MUD                                            |
| `SendNoEcho`    | Sends a message to the MUD without echoing in the output window       |
| `SendPkt`       | Sends a low-level packet of data to the MUD                           |
| `SendPush`      | Sends a message to the MUD and saves it in the command history buffer |
| `SendSpecial`   | Sends a message to the MUD with various option                        |
| `SendToNotepad` | Creates a notepad and sends text to it                                |

## Triggers

| Function                  | Purpose                                                              |
| ------------------------- | -------------------------------------------------------------------- |
| `AddTrigger`              | Adds a trigger                                                       |
| `AddTriggerEx`            | Adds a trigger - extended argument                                   |
| `DeleteTemporaryTriggers` | Deletes all temporary trigger                                        |
| `DeleteTrigger`           | Deletes a trigger                                                    |
| `DeleteTriggerGroup`      | Deletes a group of trigger                                           |
| `EnableTrigger`           | Enables or disables a trigger                                        |
| `EnableTriggerGroup`      | Enables/disables a group of trigger                                  |
| `GetPluginTriggerInfo`    | Gets details about a named trigger for a specified plugin            |
| `GetPluginTriggerList`    | Gets the list of triggers in a specified plugin                      |
| `GetPluginTriggerOption`  | Gets the value of a named trigger option for a specified plugin      |
| `GetTrigger`              | Gets details about a named trigger                                   |
| `GetTriggerInfo`          | Gets details about a named trigger                                   |
| `GetTriggerList`          | Gets the list of trigger                                             |
| `GetTriggerOption`        | Gets the value of a named trigger option                             |
| `GetTriggerWildcard`      | Returns the contents of the specified wildcard for the named trigger |
| `IsTrigger`               | Tests to see if a trigger exist                                      |
| `SetTriggerOption`        | Sets the value of a named trigger option                             |
| `StopEvaluatingTriggers`  | Stops trigger evaluation                                             |

## Aliases

| Function                 | Purpose                                                           |
| ------------------------ | ----------------------------------------------------------------- |
| `AddAlias`               | Adds an alia                                                      |
| `DeleteAlias`            | Deletes an alia                                                   |
| `DeleteAliasGroup`       | Deletes a group of aliase                                         |
| `DeleteTemporaryAliases` | Deletes all temporary aliase                                      |
| `EnableAlias`            | Enables or disables an alia                                       |
| `EnableAliasGroup`       | Enables/disables a group of aliase                                |
| `GetAlias`               | Gets details about an alia                                        |
| `GetAliasInfo`           | Gets details about an alia                                        |
| `GetAliasList`           | Gets the list of aliase                                           |
| `GetAliasOption`         | Gets the value of a named alias option                            |
| `GetAliasWildcard`       | Returns the contents of the specified wildcard for the named alia |
| `GetPluginAliasInfo`     | Gets details about a named alias for a specified plugin           |
| `GetPluginAliasList`     | Gets the list of aliases in a specified plugin                    |
| `GetPluginAliasOption`   | Gets the value of a named alias option for a specified plugin     |
| `IsAlias`                | Tests to see if an alias exist                                    |
| `SetAliasOption`         | Sets the value of a named alias option                            |

## Timers

| Function                | Purpose                                                       |
| ----------------------- | ------------------------------------------------------------- |
| `AddTimer`              | Adds a timer                                                  |
| `DeleteTemporaryTimers` | Deletes all temporary timer                                   |
| `DeleteTimer`           | Deletes a timer                                               |
| `DeleteTimerGroup`      | Deletes a group of timer                                      |
| `EnableTimer`           | Enables or disables an timer                                  |
| `EnableTimerGroup`      | Enables/disables a group of timer                             |
| `GetPluginTimerInfo`    | Gets details about a named timer for a specified plugin       |
| `GetPluginTimerList`    | Gets the list of timers in a specified plugin                 |
| `GetPluginTimerOption`  | Gets the value of a named timer option for a specified plugin |
| `GetTimer`              | Gets details about a timer                                    |
| `GetTimerInfo`          | Gets details about a timer                                    |
| `GetTimerList`          | Gets the list of timer                                        |
| `GetTimerOption`        | Gets the value of a named timer option                        |
| `IsTimer`               | Tests to see if a timer exist                                 |
| `ResetTimer`            | Resets a named timer                                          |
| `ResetTimers`           | Resets all timer                                              |
| `SetTimerOption`        | Sets the value of a named timer option                        |

## Variables

| Function                | Purpose                                               |
| ----------------------- | ----------------------------------------------------- |
| `DeleteVariable`        | Deletes a variable                                    |
| `GetPluginVariable`     | Gets the contents of a variable belonging to a plugin |
| `GetPluginVariableList` | Gets the list of variables in a specified plugin      |
| `GetVariable`           | Gets the contents of a variable                       |
| `GetVariableList`       | Gets the list of variable                             |
| `SetVariable`           | Sets the value of a variable                          |

## Info & state

| Function                  | Purpose                                                                            |
| ------------------------- | ---------------------------------------------------------------------------------- |
| `GetChatInfo`             | Get information about a chat connection                                            |
| `GetInfo`                 | Gets information about the current world                                           |
| `GetLineInfo`             | Gets details about a specified line in the output window                           |
| `GetOption`               | Gets value of a named world option                                                 |
| `GetOptionList`           | Gets the list of world option                                                      |
| `GetStyleInfo`            | Gets details about a specified style run for a specified line in the output window |
| `GetWorld`                | Gets an object reference to the named world                                        |
| `GetWorldById`            | Gets an object reference to the world given its unique ID                          |
| `GetWorldID`              | Returns the 24-character ID of the current world                                   |
| `GetWorldIdList`          | Gets the list of open worlds - returning their world ID                            |
| `GetWorldList`            | Gets the list of open worlds - returning their world name                          |
| `GetWorldWindowPosition`  | Returns the position and size of the current world window                          |
| `GetWorldWindowPositionX` | Returns the position and size of a specific world window                           |
| `SetOption`               | Sets value of a named world option                                                 |
| `Version`                 | Gets the MUSHclient version string                                                 |
| `WorldName`               | Gets the world's name                                                              |

## Plugins

| Function            | Purpose                                           |
| ------------------- | ------------------------------------------------- |
| `BroadcastPlugin`   | Broadcasts a message to all installed plugin      |
| `CallPlugin`        | Calls a routine in a plugin                       |
| `EnablePlugin`      | Enables or disables the specified plugin          |
| `GetPluginID`       | Returns the 24-character ID of the current plugin |
| `GetPluginInfo`     | Gets details about a specified plugin             |
| `GetPluginList`     | Gets a list of installed plugins                  |
| `GetPluginName`     | Returns the name of the current plugin            |
| `IsPluginInstalled` | Checks to see if a particular plugin is installed |
| `LoadPlugin`        | Loads a plugin from disk                          |
| `PluginSupports`    | Checks if a plugin supports a particular routine  |
| `ReloadPlugin`      | Reloads an installed plugin                       |
| `UnloadPlugin`      | Unloads an installed plugin                       |

## Arrays

| Function           | Purpose                                             |
| ------------------ | --------------------------------------------------- |
| `ArrayClear`       | Clears an array                                     |
| `ArrayCount`       | Returns the number of array                         |
| `ArrayCreate`      | Creates an array                                    |
| `ArrayDelete`      | Deletes an array                                    |
| `ArrayDeleteKey`   | Deletes a key/value pair from an array              |
| `ArrayExists`      | Tests to see if the specified array exist           |
| `ArrayExport`      | Exports values from an array into a single string   |
| `ArrayExportKeys`  | Exports keys from an array into a single string     |
| `ArrayGet`         | Gets the value of an array item                     |
| `ArrayGetFirstKey` | Gets the key of the first element in the array      |
| `ArrayGetLastKey`  | Gets the key of the last element in the array       |
| `ArrayImport`      | Imports values into an array from a single string   |
| `ArrayKeyExists`   | Tests to see if the specified array key exist       |
| `ArrayListAll`     | Gets the list of array                              |
| `ArrayListKeys`    | Gets the list of all the keys in an array           |
| `ArrayListValues`  | Gets the list of all the values in an array         |
| `ArraySet`         | Sets the value of an array item                     |
| `ArraySize`        | Returns the number of elements in a specified array |

## Database / SQLite

| Function                  | Purpose                                                                         |
| ------------------------- | ------------------------------------------------------------------------------- |
| `DatabaseChanges`         | Returns a count of the changes to the database by the most recent SQL statement |
| `DatabaseClose`           | Closes an SQLite database                                                       |
| `DatabaseColumnName`      | Find the name of a specified column returned by an SQL statement                |
| `DatabaseColumnNames`     | Return a table of all the columns returned by an SQL statement                  |
| `DatabaseColumnText`      | Returns the contents of an SQL column, as text                                  |
| `DatabaseColumnType`      | Returns the type of data in an SQL column                                       |
| `DatabaseColumnValue`     | Returns the contents of an SQL column, as text, float, integer, or null         |
| `DatabaseColumnValues`    | Returns the contents of all the SQL columns after a step                        |
| `DatabaseColumns`         | Find how many columns will be returned by an SQL statement                      |
| `DatabaseError`           | Returns an English string describing the most recent SQL error                  |
| `DatabaseExec`            | Executes SQL code against an SQLite database                                    |
| `DatabaseFinalize`        | Finalizes                                                                       |
| `DatabaseGetField`        | Returns a single field from an SQL database                                     |
| `DatabaseInfo`            | Returns information about a database                                            |
| `DatabaseLastInsertRowid` | Returns the most recently automatically allocated database key                  |
| `DatabaseList`            | Lists all database                                                              |
| `DatabaseOpen`            | Opens an SQLite database                                                        |
| `DatabasePrepare`         | Prepares an SQL statement for execution                                         |
| `DatabaseReset`           | Resets a previously-prepared SQL statement to the start                         |
| `DatabaseStep`            | Executes a previously-prepared SQL statement                                    |
| `DatabaseTotalChanges`    | Returns a count of the total changes to the database                            |

## Miniwindows / graphics

| Function                   | Purpose                                                                                   |
| -------------------------- | ----------------------------------------------------------------------------------------- |
| `AddFont`                  | Adds a custom font for use by MUSHclient                                                  |
| `BlendPixel`               | Blends a single pixel with another, using a specified blending mode                       |
| `InfoFont`                 | Changes the font, font size and style for text on the info bar                            |
| `NotepadFont`              | Changes the font and style of the selected notepad window                                 |
| `SetInputFont`             | Sets the font for the input window                                                        |
| `SetOutputFont`            | Sets the font for the output window                                                       |
| `WindowAddHotspot`         | Adds a hotspot to a miniwindow                                                            |
| `WindowArc`                | Draws an arc in a miniwindow                                                              |
| `WindowBezier`             | Draws a Bézier curve in a miniwindow                                                      |
| `WindowBlendImage`         | Blends an image into a miniwindow, using a specified blending mode                        |
| `WindowCircleOp`           | Draws ellipses, filled rectangles, round rectangles, chords, pies in a miniwindow         |
| `WindowCreate`             | Creates a miniwindow                                                                      |
| `WindowCreateImage`        | Creates an image in a miniwindow                                                          |
| `WindowDelete`             | Deletes a miniwindow                                                                      |
| `WindowDeleteAllHotspots`  | Deletes all hotspots from a miniwindow                                                    |
| `WindowDeleteHotspot`      | Deletes a hotspot from a miniwindow                                                       |
| `WindowDragHandler`        | Adds a drag handler to a miniwindow hotspot                                               |
| `WindowDrawImage`          | Draws an image into a miniwindow                                                          |
| `WindowDrawImageAlpha`     | Draws an image into a miniwindow respecting the alpha channel                             |
| `WindowFilter`             | Performs a filtering operation over part of the miniwindow                                |
| `WindowFont`               | Loads a font into a miniwindow                                                            |
| `WindowFontInfo`           | Returns information about a font                                                          |
| `WindowFontList`           | Lists all fonts loaded into a miniwindow                                                  |
| `WindowGetImageAlpha`      | Draws the alpha channel of an image into a miniwindow                                     |
| `WindowGetPixel`           | Gets the colour of a single pixel in a miniwindow                                         |
| `WindowGradient`           | Draws a gradient in a rectangle                                                           |
| `WindowHotspotInfo`        | Returns information about a hotspot                                                       |
| `WindowHotspotList`        | Lists all hotspots installed into a miniwindow                                            |
| `WindowHotspotTooltip`     | Changes the tooltip text for a hotspot in a miniwindow                                    |
| `WindowImageFromWindow`    | Creates an image from another miniwindow                                                  |
| `WindowImageInfo`          | Returns information about an image                                                        |
| `WindowImageList`          | Lists all images installed into a miniwindow                                              |
| `WindowImageOp`            | Draws an ellipse, rectangle or round rectangle, filled with an image                      |
| `WindowInfo`               | Returns information about a miniwindow                                                    |
| `WindowLine`               | Draws a line in a miniwindow                                                              |
| `WindowList`               | Lists all miniwindow                                                                      |
| `WindowLoadImage`          | Loads an image into a miniwindow from a disk file                                         |
| `WindowMenu`               | Creates a pop-up menu inside a miniwindow                                                 |
| `WindowMergeImageAlpha`    | Merges an image into a miniwindow based on an alpha mask                                  |
| `WindowMoveHotspot`        | Moves a hotspot in a miniwindow                                                           |
| `WindowPolygon`            | Draws a polygon in a miniwindow                                                           |
| `WindowPosition`           | Moves a miniwindow                                                                        |
| `WindowRectOp`             | Draws a rectangle in a miniwindow                                                         |
| `WindowResize`             | Resizes a miniwindow                                                                      |
| `WindowScrollwheelHandler` | Adds a scroll-wheel handler to a miniwindow hotspot                                       |
| `WindowSetPixel`           | Sets a single pixel in a miniwindow to the specified colour                               |
| `WindowSetZOrder`          | Sets the Z-Order for a miniwindow                                                         |
| `WindowShow`               | Shows or hides a miniwindow                                                               |
| `WindowText`               | Draws text into a miniwindow                                                              |
| `WindowTextWidth`          | Calculates the width of text in a miniwindow                                              |
| `WindowTransformImage`     | Draws an image into a miniwindow with optional rotation, scaling, reflection and shearing |
| `WindowWrite`              | Writes the contents of a miniwindow to disk as a graphics file                            |

## Notepad

| Function                   | Purpose                                                       |
| -------------------------- | ------------------------------------------------------------- |
| `ActivateNotepad`          | Activates a notepad window                                    |
| `AppendToNotepad`          | Appends text to a notepad window                              |
| `CloseNotepad`             | Closes a notepad window                                       |
| `GetNotepadLength`         | Gets the length of the text in a notepad window               |
| `GetNotepadList`           | Gets the list of open notepads - returning their title        |
| `GetNotepadText`           | Gets the text from a notepad window                           |
| `GetNotepadWindowPosition` | Returns the position and size of the specified notepad window |
| `MoveNotepadWindow`        | Move and resize the specified notepad window                  |
| `NotepadReadOnly`          | Make a selected notepad window read-only                      |
| `NotepadSaveMethod`        | Changes the save method for this notepad window               |
| `ReplaceNotepad`           | Replaces text in a notepad window                             |
| `SaveNotepad`              | Saves a notepad window to disk                                |

## Accelerators / keys / menus

| Function           | Purpose                                                           |
| ------------------ | ----------------------------------------------------------------- |
| `Accelerator`      | Add or modify an accelerator key                                  |
| `AcceleratorList`  | List defined accelerator                                          |
| `AcceleratorTo`    | Add or modify an accelerator key - with "Send To" parameter       |
| `DoAfterSpeedWalk` | Adds a one-shot, temporary speedwalk timer - simplified interface |
| `Menu`             | Creates a pop-up menu inside the command window                   |

## Sounds / speech

| Function         | Purpose                                         |
| ---------------- | ----------------------------------------------- |
| `GetSoundStatus` | Gets the status of a sound started by PlaySound |
| `PlaySound`      | Plays a sound using DirectSound                 |
| `Sound`          | Plays a sound                                   |
| `StopSound`      | Stop playing a sound started by PlaySound       |

## MXP / chat

| Function                 | Purpose                                                                  |
| ------------------------ | ------------------------------------------------------------------------ |
| `ChatAcceptCalls`        | Accepts incoming chat call                                               |
| `ChatCall`               | Calls a chat server                                                      |
| `ChatCallzChat`          | Calls a zChat chat server                                                |
| `ChatDisconnect`         | Disconnects a current chat call                                          |
| `ChatDisconnectAll`      | Disconnects all current chat call                                        |
| `ChatEverybody`          | Sends a chat message to every connected chat user                        |
| `ChatGetID`              | Looks up what chat ID                                                    |
| `ChatGroup`              | Sends a chat message to every connected chat user in the specified group |
| `ChatID`                 | Sends a chat message to a particular chat session                        |
| `ChatMessage`            | Sends a message to a chat user                                           |
| `ChatNameChange`         | Changes your chat name                                                   |
| `ChatNote`               | Does a note using ANSI codes for the chat system                         |
| `ChatPasteEverybody`     | Pastes the clipboard contents to every connected person                  |
| `ChatPasteText`          | Pastes the clipboard contents to that person                             |
| `ChatPeekConnections`    | Sends a "peek connections" message to the specified chat user            |
| `ChatPersonal`           | Sends a chat message to a particular person                              |
| `ChatPing`               | Sends a ping message to the specified chat user                          |
| `ChatRequestConnections` | Sends a "request connections" message to the specified chat user         |
| `ChatSendFile`           | Starts sending a file to the specified chat user                         |
| `ChatStopAcceptingCalls` | Stops this world from accepting chat call                                |
| `ChatStopFileTransfer`   | Stops a file transfer in progress to that chat user                      |

## Utility

| Function                  | Purpose                                                                             |
| ------------------------- | ----------------------------------------------------------------------------------- |
| `ANSI`                    | Generates an ANSI colour sequence                                                   |
| `Activate`                | Activates the world window                                                          |
| `ActivateClient`          | Activates the main MUSHclient window                                                |
| `AddMapperComment`        | Adds a comment to the auto-map sequence                                             |
| `AddSpellCheckWord`       | Adds a word to the user spell check dictionary                                      |
| `AddToMapper`             | Adds a mapping direction to the auto-map sequence                                   |
| `Base64Decode`            | Takes a base-64 encoded string and decodes it                                       |
| `Base64Encode`            | Encodes a string using base-64 encoding                                             |
| `Bookmark`                | Sets of clears a bookmark on the nominated line                                     |
| `ChangeDir`               | Changes the MUSHclient working directory                                            |
| `CloseLog`                | Closes the log file                                                                 |
| `Connect`                 | Connects the world to the MUD server                                                |
| `CreateGUID`              | Creates a GUID - Global Unique Identifier                                           |
| `Debug`                   | Displays debugging information about the world                                      |
| `DeleteAllMapItems`       | Deletes the all items from the auto-mapper sequence                                 |
| `DeleteCommandHistory`    | Deletes command history list                                                        |
| `DeleteGroup`             | Deletes a group of triggers, aliases and timer                                      |
| `DeleteLastMapItem`       | Deletes the most recently-added item from the auto-mapper sequence                  |
| `DeleteLines`             | Clears some recent lines from the output window                                     |
| `DeleteOutput`            | Clears all output from the output window                                            |
| `DiscardQueue`            | Discards the speed walk queue                                                       |
| `Disconnect`              | Disconnects the world from the MUD server                                           |
| `DoAfter`                 | Adds a one-shot, temporary timer - simplified interface                             |
| `DoAfterNote`             | Adds a one-shot, temporary note timer - simplified interface                        |
| `DoAfterSpecial`          | Adds a one-shot, temporary, timer to carry out some special action                  |
| `EchoInput`               | A flag to indicate whether we are echoing command input to the output window        |
| `EditDistance`            | Returns the Levenshtein Edit Distance between two word                              |
| `EnableGroup`             | Enables/disables a group of triggers, aliases and timer                             |
| `EnableMapping`           | Enables or disables the auto-mapper                                                 |
| `ErrorDesc`               | Converts a MUSHclient script error code into an human-readable description          |
| `EvaluateSpeedwalk`       | Evaluates a speed walk string                                                       |
| `ExportXML`               | Exports a world item in XML format                                                  |
| `FilterPixel`             | Performs a filtering operation on one pixel                                         |
| `FixupEscapeSequences`    | Converts "escape sequences" like &#x5C;t to their equivalent codes                  |
| `FixupHTML`               | Fixes up text for writing as HTML                                                   |
| `FlashIcon`               | Flashes the MUSHclient icon on the Windows taskbar                                  |
| `FlushLog`                | Flushes the log file to disk                                                        |
| `GenerateName`            | Generates a random character name                                                   |
| `GetAlphaOption`          | Gets the value of an alphanumeric configuration option                              |
| `GetAlphaOptionList`      | Gets the list of world alphanumeric option                                          |
| `GetChatList`             | Gets the list of chat session                                                       |
| `GetChatOption`           | Gets the value of a chat session option                                             |
| `GetCommand`              | Gets the current command in the command window                                      |
| `GetCommandList`          | Returns some or all commands from the command history                               |
| `GetConnectDuration`      | Returns the number of seconds this world has been connected                         |
| `GetCurrentValue`         | Gets the current value of a named world option                                      |
| `GetDefaultValue`         | Gets the default value of a named world option                                      |
| `GetDeviceCaps`           | Gets screen device capabilitie                                                      |
| `GetEntity`               | Retrieves the value of an MXP server-defined entity                                 |
| `GetFrame`                | Returns the address of the main MUSHclient frame window                             |
| `GetGlobalOption`         | Gets the value of a global configuration option                                     |
| `GetGlobalOptionList`     | Gets the list of global option                                                      |
| `GetHostAddress`          | Returns a list of IP addresses that correspond to a host name on the Internet       |
| `GetHostName`             | Returns the host name that corresponds to an IP address on the Internet             |
| `GetInternalCommandsList` | Returns a list of the internal MUSHclient command name                              |
| `GetLineCount`            | Gets count of lines received                                                        |
| `GetLinesInBufferCount`   | Returns the number of lines in the output window                                    |
| `GetLoadedValue`          | Gets value of a named world option, as loaded from the world file                   |
| `GetMainWindowPosition`   | Returns the position and size of the main MUSHclient window                         |
| `GetMappingCount`         | Gets a count of the number of items in the auto-map sequence                        |
| `GetMappingItem`          | Gets one item from the auto-map sequence                                            |
| `GetMappingString`        | Returns the speedwalk string generated by the auto-mapper                           |
| `GetNoteStyle`            | Gets the style for note                                                             |
| `GetNotes`                | Gets the world's note                                                               |
| `GetQueue`                | Returns a variant array which is a list of queued command                           |
| `GetReceivedBytes`        | Returns the number of bytes received from the world                                 |
| `GetRecentLines`          | Assembles a block of text from recent MUD output                                    |
| `GetScriptTime`           | Returns the amount of time spent in script routine                                  |
| `GetSelectionEndColumn`   | Returns the endling column of the selection in the output window                    |
| `GetSelectionEndLine`     | Returns the last line of the selection in the output window                         |
| `GetSelectionStartColumn` | Returns the starting column of the selection in the output window                   |
| `GetSelectionStartLine`   | Returns the starting line of the selection in the output window                     |
| `GetSentBytes`            | Returns the number of bytes sent to the world                                       |
| `GetSysColor`             | Gets the colour of various windows item                                             |
| `GetSystemMetrics`        | Returns selected system information from Window                                     |
| `GetUdpPort`              | Find a free port for UDP listening                                                  |
| `GetUniqueID`             | Creates a unique ID for general use, or for making Plugin ID                        |
| `GetUniqueNumber`         | Returns a unique number                                                             |
| `GetXMLEntity`            | Retrieves the value of a standard entity                                            |
| `Hash`                    | Produces a hash                                                                     |
| `Help`                    | Shows help for a script function, or a list of function                             |
| `ImportXML`               | Imports configuration data in XML format                                            |
| `Info`                    | Adds text to the "info" tool bar                                                    |
| `InfoBackground`          | Sets the background colour for the info bar                                         |
| `InfoClear`               | Clears all text from the info bar                                                   |
| `IsConnected`             | Tests to see if the world is connected to the MUD server                            |
| `IsLogOpen`               | Tests to see if a log file is open                                                  |
| `LogInput`                | The property of whether commands are logged to the log file                         |
| `LogNotes`                | The property of whether notes are logged to the log file                            |
| `LogOutput`               | The property of whether MUD output is logged to the log file                        |
| `LogSend`                 | Sends a message to the MUD and logs it                                              |
| `MakeRegularExpression`   | Converts wildcard matching text to a regular expression                             |
| `Mapping`                 | A flag to indicate whether we are mapping the world                                 |
| `Metaphone`               | Returns the metaphone code for the supplied word                                    |
| `MoveMainWindow`          | Move and resize the main MUSHclient window                                          |
| `MoveWorldWindow`         | Move and resize a world window                                                      |
| `MoveWorldWindowX`        | Move and resize a specific world window                                             |
| `MtRand`                  | Returns pseudo-random number using the Mersenne Twister algorithm                   |
| `MtSrand`                 | Seed the Mersenne Twister pseudo-random number generator                            |
| `NoteHr`                  | Draws a horizontal rule in the output window                                        |
| `NoteStyle`               | Sets the style for note                                                             |
| `Open`                    | Opens a named document                                                              |
| `OpenBrowser`             | Opens a supplied URL in your default web browser                                    |
| `OpenLog`                 | Opens a log file                                                                    |
| `PasteCommand`            | Pastes text into the command window, replacing the current selection                |
| `Pause`                   | Turns pause mode on or off                                                          |
| `ReadNamesFile`           | Loads in a file for generating character name                                       |
| `Redraw`                  | Schedules a redraw for all windows for this world                                   |
| `RemoveBacktracks`        | Removes backtracks from a speed walk string                                         |
| `RemoveMapReverses`       | A flag to indicate whether we backtracks are removed during mapping                 |
| `Repaint`                 | Forces an immediate redraw for all windows for this world                           |
| `Replace`                 | Replaces one substring with another                                                 |
| `Reset`                   | Resets all outstanding MXP/Pueblo tag                                               |
| `ResetIP`                 | Resets the cached IP address of the world and proxy server                          |
| `ResetStatusTime`         | Resets the time elapsed shown on the status bar                                     |
| `ReverseSpeedwalk`        | Reverses a speed walk string                                                        |
| `Save`                    | Saves world configuration                                                           |
| `SaveState`               | Saves the state of the current plugin                                               |
| `SelectCommand`           | Selects                                                                             |
| `SetAlphaOption`          | Sets value of a named world alphanumeric option                                     |
| `SetBackgroundImage`      | Sets a background image for the output window                                       |
| `SetChanged`              | Sets or clears the "document has changed" flag                                      |
| `SetChatOption`           | Sets the value of a chat session option                                             |
| `SetCommand`              | Sends text to the command window                                                    |
| `SetCommandSelection`     | Selects specified columns in the command window                                     |
| `SetCommandWindowHeight`  | Set the height of the command                                                       |
| `SetCursor`               | Changes the shape of the mouse cursor                                               |
| `SetEntity`               | Sets the value of an MXP entity                                                     |
| `SetForegroundImage`      | Sets a foreground image for the output window                                       |
| `SetMainTitle`            | Sets the main output window title                                                   |
| `SetNotes`                | Sets the notes for the world                                                        |
| `SetScroll`               | Sets the scroll bar position, and hides or shows it                                 |
| `SetSelection`            | Sets a selection range in the output window                                         |
| `SetStatus`               | Sets the status line text                                                           |
| `SetTitle`                | Sets the world window title                                                         |
| `SetToolBarPosition`      | Sets the position of the game toolbars on the screen                                |
| `SetUnseenLines`          | Sets the number of "unseen lines" for this world                                    |
| `SetWorldWindowStatus`    | Changes the status of the current world window                                      |
| `ShiftTabCompleteItem`    | Adds an item to the list shown for Shift+Tab completion                             |
| `ShowInfoBar`             | Shows or hides the "info" tool bar                                                  |
| `Simulate`                | Simulate input from the MUD, for debugging purpose                                  |
| `SpeedWalkDelay`          | The number of milliseconds delay between speed walk command                         |
| `SpellCheck`              | Spell checks an arbitrary string of text                                            |
| `SpellCheckCommand`       | Spell checks the text in the command window                                         |
| `SpellCheckDlg`           | Spell checks an arbitrary string of text, invloking the spell-checker dialog        |
| `StripANSI`               | Strips ANSI colour sequences from a string                                          |
| `TextRectangle`           | Specifies the size of the rectangle in which text is displayed in the output window |
| `Trace`                   | Trace mode property                                                                 |
| `TraceOut`                | Outputs the supplied message to the world Trace                                     |
| `TranslateDebug`          | Sends a debugging message to the localizing translator script                       |
| `TranslateGerman`         | Translate German umluat sequence                                                    |
| `Transparency`            | Sets the transparency of the main MUSHclient window under Windows XP                |
| `Trim`                    | Trims leading and trailing spaces from a string                                     |
| `UdpListen`               | Listens for incoming packets using the UDP protocol                                 |
| `UdpPortList`             | Returns an array of all the UDP ports in use by this world                          |
| `UdpSend`                 | Sends a packet over the network using the UDP protocol                              |
| `WorldAddress`            | Returns the TCP/IP address of the current world                                     |
| `WorldPort`               | Returns the port number of the current world                                        |
| `WriteLog`                | Writes to the log file                                                              |
| `checksum`                | of a specified piece of text                                                        |
| `highlights`              | the current command in the command window                                           |
| `identifier`              | corresponds to a particular chat name                                               |
| `input`                   | window                                                                              |

<!-- total: 422 functions -->
