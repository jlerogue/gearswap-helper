# Keichii GearSwap

Personal Windower GearSwap setup for **Keichii**, based on the Tetsouo GearSwap modular framework and customized from Keichii's exported inventory.

## Layout

- `Keichii/` — character-specific GearSwap files, job set files, and job config files.
- `docs/` — README/help files generated while building the job packages.
- `inventory/` — inventory exports and reference workbooks used to build gearsets.
- `upstream/` — notes and key files from the Tetsouo framework used as the basis for this setup.

## Install

Copy the repository contents into:

```text
<Windower4>/addons/GearSwap/data/
```

Then load a job file in-game, for example:

```text
//lua load gearswap
//gs load Keichii_WAR
```

## Important commands

```text
//gs c checksets
//gs c rf
//gs c wo preview
//gs showswaps
```

## Notes

This repository contains owned-gear set files generated from Keichii's exported inventory. If GearSwap reports missing items, first check whether the item is in inventory or an active wardrobe.
