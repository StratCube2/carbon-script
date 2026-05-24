# Folder Structure

Every Carbon mod is a `.zip` file. Here is what the inside of that zip should look like, and what each folder is for.

---

## Full Layout

```
my_mod.zip
│
├── carbon.mod.json              ← Mod info (name, version, author)
│
├── resources/                   ← Images, sounds, 3D models
│   ├── textures/                ← PNG files for items and blocks
│   ├── models/                  ← 3D model files (.glb)
│   └── sounds/                  ← Audio files
│
└── src/                         ← All your Carbon Java scripts
    ├── definitions/             ← Where you define things that exist in the game
    │   ├── items.cj             ← Your custom items
    │   ├── blocks.cj            ← Your custom blocks
    │   ├── mobs.cj              ← Your custom mobs
    │   └── potions.cj           ← Your custom potion effects
    │
    ├── commands/                ← Custom slash commands
    │   └── mycommand.cj
    │
    ├── recipes/                 ← Crafting and smelting recipes
    │   └── ruby_sword.cj
    │
    └── scripts/                 ← Reusable behavior and action scripts
        └── fireball_action.cj
```

---

## What Goes Where

### `carbon.mod.json`
The first thing Carbon Loader reads. Contains your mod's name, version, and other info. See [Carbon Mod JSON Guide](03_carbon_mod_json.md) for full details.

### `resources/textures/`
PNG image files for your items and blocks. Should be 16x16 pixels for items. Larger sizes work for blocks and mobs.

### `resources/models/`
3D model files in `.glb` format. Used for custom mobs and blocks that need a 3D shape beyond a simple cube.

### `resources/sounds/`
Audio files for your mod. Referenced in scripts using `playsound (resources/sounds/myfile.mp3)`.

### `src/definitions/`
This is where you tell the game what exists. Items, blocks, mobs, and potions are all defined here using `add item`, `add block`, `add mob`, etc.

### `src/commands/`
Scripts that add new `/commands` to the game. Each command is its own `.cj` file.

### `src/recipes/`
Crafting grid recipes, smelting recipes, and so on. Each recipe is its own `.cj` file.

### `src/scripts/`
Reusable scripts called by other scripts. For example, a script that fires a fireball, or one that gives the player a speed boost. These are the scripts you reference with `run (scripts/myscript.cj)`.

---

## Rules

- You do not need every folder. If your mod has no mobs, skip `models/` and leave `mobs.cj` empty or delete it.
- File names can be anything, but use lowercase and underscores. No spaces.
- You can have as many `.cj` files inside each folder as you want.
- The `carbonjava` header is required at the top of every single `.cj` file.
