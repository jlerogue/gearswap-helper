# GearSwap Developer Reference

This document summarizes the GearSwap documentation that matters for the GearSwap Helper web app.

Primary docs:

- GearSwap docs: https://docs.windower.net/addons/gearswap/
- GearSwap reference: https://docs.windower.net/addons/gearswap/reference/
- Tetsouo GearSwap framework: https://github.com/Tetsouo/Gearswap

## What GearSwap does

GearSwap is a Windower addon that lets users define Lua gear sets and equip them automatically in response to in-game events such as precast, midcast, aftercast, idle, engaged, job ability usage, weapon skills, and custom commands.

For this project, GearSwap should be treated as the runtime. GearSwap Helper should generate or safely edit Lua files that GearSwap can load.

## Relevant GearSwap commands

Common commands that matter for this project:

```text
//lua load gearswap
//gs load <file>
//gs reload
//gs showswaps
//gs debugmode
//gs export
```

The export command is especially important because it can be used as a bridge from the player account into the web app. It can export currently equipped gear, inventory information, and gear data that can be parsed into app-readable structures.

## User script lifecycle

GearSwap user files commonly define functions such as:

```lua
function get_sets()
end

function precast(spell)
end

function midcast(spell)
end

function aftercast(spell)
end

function status_change(new, old)
end

function self_command(command)
end
```

The web app should expose these as user-friendly trigger choices instead of requiring users to write Lua directly.

## Common set categories

The app should support at least these set groups:

```lua
sets.idle
sets.engaged
sets.precast.FC
sets.precast.WS
sets.midcast
sets.midcast.FastRecast
sets.midcast.Cure
sets.midcast.EnfeeblingMagic
sets.midcast.ElementalMagic
sets.aftercast
```

The exact sets vary by job. The app should not assume every job uses every set.

## Recommended trigger model

Store triggers as structured data first, then generate Lua.

Example internal model:

```json
{
  "name": "Fast Cast",
  "event": "precast",
  "condition": {
    "spell_skill": "Magic"
  },
  "set": "precast.FC"
}
```

Generated Lua can then call the matching set.

## Editing existing Lua files

Full arbitrary Lua parsing is risky because GearSwap files can use variables, helper functions, inheritance, conditionals, and framework-specific logic.

Version one should prefer one of these approaches:

1. Generate complete new files.
2. Edit only marked app-managed blocks.
3. Import existing files as reference but export clean generated files.

Recommended block format:

```lua
-- BEGIN GEARSWAP_HELPER SET: engaged.Acc
sets.engaged.Acc = {
    head="Example Helm",
    body="Example Mail",
}
-- END GEARSWAP_HELPER SET: engaged.Acc
```

The app can safely replace text between those markers without trying to understand the whole Lua file.

## Item and augment handling

GearSwap supports normal item names and augmented item tables.

Simple item:

```lua
head="Flamma Zucchetto +2"
```

Augmented item:

```lua
back={ name="Ankou's Mantle", augments={'DEX+20','Accuracy+20 Attack+20','Store TP+10','Damage taken-5%'} }
```

The web app should store gear as structured slot data and only generate Lua at export time.

## Output goals

The app should be able to export:

```text
<character>_<job>.lua
<character>/sets/<job>_sets.lua
<character>/jobs/<job>.lua
README/help files
inventory summaries
zip folder for drag-and-drop install
```

## Design implications

- GearSwap Helper should be framework-aware but not framework-dependent.
- Tetsouo-style output can be one export target.
- Simple standalone GearSwap files should be another export target.
- The app should validate item names, slots, duplicate equipment, missing gear, and unsupported augments before export.
