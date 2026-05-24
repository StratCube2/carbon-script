# Carbon Lang Script — Mod Folder Structure

Carbon mods are distributed as `.zip` files. Carbon Lib unzips them at runtime
into a temp directory and loads them. No build step is required from the modder.

---

## Full Folder Layout

```
my_awesome_mod.zip
│
├── carbon.mod.json                   # Mod metadata (required)
│
├── resources/                        # Visual and audio assets
│   ├── textures/                     # Block and item PNG textures
│   ├── models/                       # Custom 3D models (.glb or .obj)
│   └── sounds/                       # Audio files (.mp3, .ogg)
│
└── src/                              # All Carbon Lang Script logic
    ├── definitions/                  # Global registration
    │   ├── mobs.cj                   # All "add mob" declarations
    │   ├── blocks.cj                 # All "add block" declarations
    │   ├── items.cj                  # All "add item" declarations
    │   └── potions.cj                # Custom potions and effects
    │
    ├── commands/                     # Custom slash commands
    │   └── my_command.cj             # Contains "add command" blocks
    │
    ├── recipes/                      # Crafting and smelting recipes
    │   └── ruby_sword.cj             # A single recipe file
    │
    └── scripts/                      # Reusable action and behaviour scripts
        ├── fireball.cj               # An action script
        ├── wave_spawner.cj           # A dependency script with inputs
        └── flight.cj                 # A tick-loop behaviour script
```

---

## Rules

- `carbon.mod.json` must be at the root of the zip.
- `src/definitions/` is where all item, mob, block, and potion declarations go.
- `src/recipes/` contains only recipe files. These do not start with `carbonjava`.
- `src/scripts/` contains reusable action and behaviour logic.
- `src/commands/` contains command definitions.
- `resources/` is passed directly to the client for rendering. Carbon does not
  process these files — they are loaded as-is.

---

## Hot Reload

When Carbon Loader detects that a `.zip` has changed, it unloads the old version
and reloads the new one without restarting Minecraft. This is one of the core
features of the Carbon architecture.

On successful reload:
```
[Carbon] my_awesome_mod reloaded successfully.
```

On error:
```
[Carbon] Error in src/definitions/items.cj line 6:
  "durabilty" is not recognised. Did you mean "durability"?
  Mod not loaded.
```

---

## Namespace Isolation

Each mod's ID (from `carbon.mod.json`) is used as a namespace. Two mods can
both have an item called `ruby` without conflicting — they become
`my_mod:ruby` and `other_mod:ruby` internally.

When referencing your own mod's items, you can omit the namespace:
```
recipe
2. ruby        @ refers to your mod's ruby
5. minecraft:stick   @ explicit vanilla namespace
```
