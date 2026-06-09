# Windower Developer Reference

This document summarizes the Windower Lua documentation that matters for GearSwap Helper.

Primary docs:

- Windower Lua wiki: https://github.com/Windower/Lua/wiki/
- Writing addons: https://github.com/Windower/Lua/wiki/Writing-Addons
- Events: https://github.com/Windower/Lua/wiki/Events
- Functions: https://github.com/Windower/Lua/wiki/Functions
- Packets: https://github.com/Windower/Lua/wiki/Packets
- Windower docs: https://docs.windower.net/

## Addon vs plugin

For this project, a Windower-side helper should be a Lua addon, not a compiled plugin.

The web app should do the main editing work. A Windower addon can be added later only for convenience tasks such as exporting inventory, capturing currently equipped gear, or reloading GearSwap.

## What a helper addon could do

A small addon could provide commands like:

```text
//gsh export inventory
//gsh export equipped
//gsh export job DRK
//gsh reload
```

Possible addon responsibilities:

- Run or assist with GearSwap export workflows.
- Save local export files into a predictable folder.
- Capture currently equipped gear.
- Provide a reminder of where to upload files into the web app.
- Reload GearSwap after generated files are copied into place.

The addon should not be responsible for the full editor UI.

## Why the main editor should be a web app

A browser app is better for:

- Drag-and-drop uploads.
- Searchable gear tables.
- Job and set navigation.
- Validation warnings.
- Stat summaries.
- ZIP export.
- Framework-specific export options.

Building a complex GUI entirely inside Windower Lua would add unnecessary complexity.

## Useful Windower concepts

Windower Lua addons commonly use:

```lua
_addon.name = 'addon_name'
_addon.author = 'author'
_addon.version = '1.0'
_addon.commands = {'command'}

windower.register_event('addon command', function(...)
end)
```

A helper addon can listen for addon commands and call Windower/GearSwap commands as needed.

## Events

Windower exposes events that addons can register for. For this project, the most useful events are likely command-related or load/unload-related events.

GearSwap itself already handles gameplay-related events. GearSwap Helper should avoid duplicating GearSwap behavior unless there is a specific reason.

## File handling

A Windower addon can write helper output into its own addon folder or another predictable local path. For the web app workflow, the important thing is consistency:

```text
Windower4/addons/gearswap-helper/exports/
```

or:

```text
Windower4/addons/GearSwap/data/exports/
```

The exact path should be chosen after testing what is easiest for normal users to find.

## Recommended division of labor

```text
Windower addon:
  - Optional export bridge
  - Optional reload shortcut
  - Optional currently equipped gear capture

Web app:
  - Inventory parser
  - Lua parser
  - Gear set editor
  - Trigger editor
  - Stat calculator
  - Validation
  - Exporter
  - ZIP packaging

GearSwap:
  - Runtime gear changing
  - In-game event response
```

## Development note

The first working version does not need a Windower addon. It can rely on manual GearSwap exports and manual file upload. Add the Windower helper only after the web app proves useful.
