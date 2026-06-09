# GearSwap Helper Web App Design Notes

This document captures the current design direction for the GearSwap Helper app.

## Core concept

GearSwap Helper should be a local-first web app that lets users upload inventory exports and Lua files, build or edit gear sets through a browser UI, then download a ready-to-install GearSwap folder or ZIP.

The app should prioritize safe generation over risky mutation of arbitrary Lua.

## Intended user workflow

```text
1. Export inventory and/or equipped gear from GearSwap.
2. Open GearSwap Helper in a browser.
3. Upload the export files and any existing Lua files.
4. Select or create a character profile.
5. Add jobs.
6. Add gear sets for each job.
7. Assign triggers/modes.
8. Review validation warnings and stat summaries.
9. Export a ready-to-use folder or ZIP.
10. Drag the output into Windower/addons/GearSwap/data.
```

## Initial app features

Version one should support:

- Upload inventory exports.
- Upload existing GearSwap Lua files as reference.
- Create character profiles.
- Create jobs.
- Create named sets.
- Fill sets by slot.
- Capture or paste GearSwap-exported gear.
- Export clean Lua files.
- Export a README for the generated package.
- Export a ZIP folder.

## Later app features

Later versions can add:

- Import and edit app-managed Lua blocks.
- Tetsouo framework export target.
- Full stat summaries.
- Augment editor.
- Missing gear validation.
- Duplicate slot validation.
- Mode/keybind documentation generator.
- GitHub repository export.
- Optional Windower helper addon.

## Recommended technology stack

A good first stack would be:

```text
React or Next.js
TypeScript
Local browser file parsing
JSZip for ZIP generation
A JSON item/stat database
Optional IndexedDB/localStorage for saved local profiles
```

The first version should not require a backend server.

## Data model

Suggested internal shape:

```json
{
  "characters": {
    "Shinbou": {
      "jobs": {
        "DRK": {
          "sets": {
            "engaged.Acc": {
              "description": "Accuracy-focused engaged set.",
              "trigger": {
                "event": "engaged",
                "mode": "Accuracy"
              },
              "gear": {
                "head": {
                  "name": "Example Helm",
                  "augments": []
                }
              }
            }
          }
        }
      }
    }
  }
}
```

The app should store structured data internally and generate Lua only at export time.

## Slot list

Use standard GearSwap slot names:

```text
main
sub
range
ammo
head
body
hands
legs
feet
neck
waist
left_ear
right_ear
left_ring
right_ring
back
```

## Trigger editor

Triggers should be selected from user-friendly templates instead of freeform Lua at first.

Examples:

```text
Idle
Engaged
Precast Fast Cast
Precast Weapon Skill
Midcast Cure
Midcast Elemental Magic
Midcast Enfeebling Magic
Aftercast default
Custom command mode
```

The exporter can convert these templates into Lua.

## Lua editing strategy

Preferred order:

1. Generate new files.
2. Import existing files for reference only.
3. Support app-managed blocks.
4. Consider full Lua parsing only if needed later.

Full arbitrary Lua editing should not be a version-one goal.

## Stat display strategy

The app should distinguish between:

```text
Calculated stats
Manual stats
Conditional stats
Unknown/unverified stats
```

This avoids false precision. FFXI gear stats include augments, set bonuses, hidden effects, conditional effects, and job-specific interactions that may not be captured by a simple static item table.

## Export targets

Support multiple export profiles:

```text
Simple GearSwap single-file export
Tetsouo-style modular export
Documentation-only export
Inventory summary export
```

## Validation checks

Useful validation checks:

- Required slots missing.
- Duplicate items in impossible slots.
- Item not found in uploaded inventory.
- Unknown item name.
- Invalid augment format.
- Unsupported slot name.
- Set has no trigger.
- Trigger references a missing set.
- Generated Lua syntax check.

## Repository role

This repository should collect the design docs, sample exports, source code, test fixtures, and generated examples needed to build the app.
