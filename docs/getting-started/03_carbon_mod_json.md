# carbon.mod.json

This file lives at the root of your mod zip. It tells Carbon Loader everything it needs to know about your mod before it loads anything.

---

## Basic Carbon Mod

This is what most mods will use:

```json
{
  "id": "ruby_mod",
  "name": "Ruby Mod",
  "version": "1.0.0",
  "authors": ["your_name"],
  "description": "Adds rubies and ruby gear to Minecraft.",
  "icon": "resources/textures/icon.png",
  "minecraft_version": "1.20.1",
  "type": "carbon",
  "dependencies": []
}
```

---

## Every Field Explained

| Field | Required | What it does |
|-------|----------|-------------|
| `id` | Yes | Unique ID for your mod. Lowercase, no spaces. |
| `name` | Yes | Display name shown in the mod list. |
| `version` | Yes | Your mod's version number. |
| `authors` | Yes | List of author names. |
| `description` | No | Short description shown in the mod list. |
| `icon` | No | Path to a PNG shown as your mod's icon. |
| `minecraft_version` | Yes | Which Minecraft version this mod targets. |
| `type` | Yes | What kind of mod this is (see below). |
| `dependencies` | No | Other mods your mod needs to work. |

---

## The `type` Field

There are three types of Carbon mod:

| Type | What it means |
|------|--------------|
| `"carbon"` | A normal mod made with Carbon Lang scripts. This is what you want. |
| `"allotrope"` | An advanced mod that injects code into Carbon Loader itself. Requires Java knowledge. |
| `"fabric"` | A regular Fabric mod that wants to connect to Carbon Loader features. |

---

## Adding Dependencies

If your mod needs another mod to be installed first, list it under `dependencies`:

```json
"dependencies": [
  "carbon_lib",
  "another_mod"
]
```

Use the other mod's `id` value, not its display name.

---

## Allotrope Mods (Advanced)

If your mod type is `"allotrope"`, you need an extra section to tell Carbon Loader which scripts inject into which part of the loader.

```json
{
  "id": "my_allotrope_mod",
  "name": "My Allotrope Mod",
  "version": "1.0.0",
  "authors": ["your_name"],
  "minecraft_version": "1.20.1",
  "type": "allotrope",
  "allotrope": {
    "inject_parent": true,
    "inject_child": true,
    "mixins": {
      "parent": [
        "src/stubuiaddon.cj"
      ],
      "child": [
        "src/guifix.cj"
      ]
    }
  }
}
```

- `inject_parent` — Does this mod touch the Carbon Loader window/terminal?
- `inject_child` — Does this mod touch the Minecraft game itself?
- `mixins.parent` — Scripts that run in the Carbon Loader side.
- `mixins.child` — Scripts that run in the Minecraft side.

If you just write `"mixins": ["src/guifix.cj"]` as a flat list, Carbon assumes it is a child (Minecraft-side) mixin.

> See <a href="../../advanced/01_allotrope/">advanced/01_allotrope/</a> for full details on writing Allotrope mods.

---

## Persistent Variables

If your mod needs to store global data that survives server restarts — like a kill counter, a global flag, or a leaderboard entry — declare them in `persistent_vars`:

```json
{
  "id": "ruby_mod",
  "name": "Ruby Mod",
  "version": "1.0.0",
  "authors": ["your_name"],
  "minecraft_version": "1.20.1",
  "type": "carbon",
  "dependencies": [],
  "persistent_vars": {
    "total_rubies_mined": "integer",
    "server_boss_killed": "boolean",
    "top_player": "string",
    "difficulty_multiplier": "float"
  }
}
```

Then in any script you can use them with `#mod.`:
```
#mod.total_rubies_mined += 1

if (#mod.server_boss_killed == false)
run (scripts/spawn_boss.cj)
```

### Valid types

| Type | Default value | Example use |
|------|--------------|-------------|
| `"integer"` | `0` | Counters, scores |
| `"float"` | `0.0` | Multipliers, rates |
| `"boolean"` | `false` | Flags, one-time events |
| `"string"` | `""` | Player names, labels |

> If you use `#mod.something` in a script but forget to declare it here, Carbon will show an error and refuse to load the mod.
