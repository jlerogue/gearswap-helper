# Newcomer Dropdown Workflow

This document defines the preferred GearSwap Helper workflow for newcomers.

## Core design goal

GearSwap Helper should feel like a guided setup wizard, not a code editor.

The user should make choices through dropdown menus and guided forms. Lua should be generated at the end, but the user should not need to understand Lua to build useful GearSwap sets.

## Primary workflow

The app should guide the user in this order:

```text
1. Choose job.
2. Choose macro book and equip set/style lock preferences.
3. Choose a set/trigger to create.
4. Review the description of what that set/trigger does.
5. Choose gear for each slot from inventory-filtered dropdowns.
6. Review individual gear stats.
7. Review total non-zero set bonuses.
8. Name the completed set.
9. Create another set from scratch or clone an existing set.
10. Generate Lua output.
```

## Job selection

The first dropdown should be job selection.

Supported jobs:

```text
WAR, MNK, WHM, BLM, RDM, THF, PLD, DRK, BST, BRD, RNG, SAM, NIN, DRG, SMN, BLU, COR, PUP, DNC, SCH, GEO, RUN
```

The selected job controls:

- Which gear can be equipped.
- Which default set/trigger templates are suggested.
- Which descriptions are shown.
- Which macro book/equip set suggestions are available.
- Which generated Lua template is used.

## Macro book and style lock setup

After job selection, the user should select macro/equip preferences.

Fields:

```text
Macro book number
Macro page number, optional
Style lock enabled: yes/no
Style lock set/equipset number, optional
Default idle set
Default engaged set
```

This should eventually generate comments or helper commands in the Lua output. Version one can store these values and include them in the generated documentation/output comments.

Example output comment:

```lua
-- Macro Book: 8
-- Macro Page: 1
-- Style Lock Equipset: 20
```

## Set/trigger selection

The app should present a list of recommended set/trigger templates for the selected job.

Each option should have:

- Name.
- Category.
- Plain-English description.
- Suggested Lua set path.
- Trigger behavior.

Example options for DRK:

```text
Idle
Used when standing still or after actions finish. Usually emphasizes damage taken reduction, refresh, movement speed, or survivability.
Lua set: sets.idle

Engaged
Used while actively attacking. Usually emphasizes accuracy, haste, Store TP, multi-attack, and survivability.
Lua set: sets.engaged

Engaged Accuracy
Used when accuracy is more important than damage. Usually swaps in higher accuracy gear.
Lua set: sets.engaged.Acc

Precast Fast Cast
Used briefly before casting magic. Usually emphasizes Fast Cast and reduced casting time.
Lua set: sets.precast.FC

Weapon Skill
Used when activating a weapon skill. Usually emphasizes weapon skill modifiers, attack, accuracy, weapon skill damage, and multi-hit stats depending on the weapon skill.
Lua set: sets.precast.WS

Dark Magic
Used while casting Dark Magic spells. Usually emphasizes Dark Magic Skill, magic accuracy, and spell-specific stats.
Lua set: sets.midcast.DarkMagic
```

The set/trigger descriptions should be visible beside or below the dropdown so newcomers understand what they are choosing.

## Starting point for a new set

When creating a new set, the user should choose:

```text
Start from scratch
Start from an existing set
Start from a recommended template
```

If starting from an existing set, show a dropdown of already-created sets. The new set should copy all gear selections from the source set, then allow edits.

This is important because many GearSwap sets are small variations of each other.

Examples:

```text
Engaged Accuracy can start from Engaged.
Weapon Skill Accuracy can start from Weapon Skill.
Idle DT can start from Idle.
```

## Inventory-driven gear choices

The gear picker should use the uploaded inventory file as the source of selectable gear.

For each equipment slot, the dropdown should only show gear that:

```text
1. Exists in the uploaded inventory.
2. Can be equipped in that slot.
3. Can be equipped by the selected job.
```

If item stat/job/slot data is incomplete, the app should clearly mark the item as unverified rather than hiding it incorrectly.

Recommended dropdown behavior:

```text
Head: show only head equipment usable by the selected job.
Body: show only body equipment usable by the selected job.
Main: show only weapons usable by the selected job.
Back: show only back pieces usable by the selected job.
```

Each dropdown option should show useful summary text:

```text
Sakpata's Plate — DEF 160, Accuracy +40, Attack +40, Store TP +8, DT -10%
```

## Gear stats display

When an item is selected, show its stats near the slot.

Suggested display:

```text
Head: Flamma Zucchetto +2
Accuracy +45
Attack +42
Haste +6%
Store TP +6
```

Do not display stats with a value of 0.

For augmented items, show:

```text
Base stats
Augment stats
Manual/unverified stats
```

## Total set bonus panel

Somewhere persistent on the page, show the total bonuses for the current set.

Only display stats where the total is not 0.

Example:

```text
Current Set Totals
Accuracy +286
Attack +392
Haste +25%
Store TP +46
Double Attack +18%
Damage Taken -38%
Weapon Skill Damage +10%
```

This panel should update as gear is selected or removed.

## Stat handling rules

The app should aggregate numeric stats from selected gear.

Initial stat categories should include:

```text
Accuracy
Ranged Accuracy
Magic Accuracy
Attack
Ranged Attack
Magic Attack Bonus
Haste
Store TP
Double Attack
Triple Attack
Quadruple Attack
Dual Wield
Fast Cast
Weapon Skill Damage
Critical Hit Rate
Critical Hit Damage
Subtle Blow
Enmity
Damage Taken
Physical Damage Taken
Magic Damage Taken
Refresh
Regen
STR
DEX
VIT
AGI
INT
MND
CHR
HP
MP
```

Stats with total value 0 should not be displayed.

Conditional, hidden, set bonus, or unverified stats should be grouped separately.

Suggested group labels:

```text
Calculated Totals
Manual / Augment Totals
Conditional or Unverified
```

## Set naming

After gear is selected, the user should be prompted to name the set.

The app should suggest a name from the selected trigger.

Examples:

```text
Idle
Engaged
Engaged.Acc
Precast.FC
Precast.WS
Midcast.DarkMagic
```

The user can override the name.

## Lua output

The final output should generate GearSwap-compatible Lua.

Example:

```lua
sets.engaged.Acc = {
    main="Caladbolg",
    sub="Utu Grip",
    ammo="Coiste Bodhar",
    head="Flamma Zucchetto +2",
    body="Sakpata's Plate",
}
```

For augmented gear:

```lua
back={ name="Ankou's Mantle", augments={'DEX+20','Accuracy+20 Attack+20','Store TP+10','Damage taken-5%'} }
```

## Feedback and implementation recommendations

### Recommended version-one scope

Version one should support:

- Job dropdown.
- Macro book/style lock fields.
- Set/trigger dropdown with descriptions.
- Manual inventory paste/upload.
- Basic inventory parsing.
- Gear selection by slot.
- Starting from scratch or cloning a previously created set.
- Non-zero stat total display.
- Lua output generation.

### Do not overbuild the parser first

The app should not initially try to perfectly understand every possible GearSwap Lua file. Existing file updating should remain copy/paste based and should prefer app-managed blocks.

### Build item/stat data as a separate layer

The app needs structured item data to filter gear by job and slot and calculate totals. Uploaded inventory tells the app what the user owns, but an item database tells the app what those items are.

Recommended data shape:

```json
{
  "name": "Sakpata's Plate",
  "slots": ["body"],
  "jobs": ["WAR", "PLD", "DRK", "BST", "SAM", "DRG", "RUN"],
  "stats": {
    "Accuracy": 40,
    "Attack": 40,
    "Store TP": 8,
    "Damage Taken": -10
  }
}
```

### Treat missing data carefully

If an owned item does not exist in the item database, the app should still allow manual selection, but mark it as:

```text
Owned, stats unknown
```

### Prioritize newcomer clarity

Every set/trigger option should explain what it does in plain English.

Example:

```text
Precast Fast Cast: Equips briefly before a spell begins casting. This helps reduce casting time and recast delay.
```

### Keep copy/paste output

The final app should continue to produce copyable Lua text. Direct file editing should not be required.
