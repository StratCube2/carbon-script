# Carbon Java — Documentation

Welcome! This is the official documentation for **Carbon Java** (`.cj`), the scripting language for making Minecraft mods using **Carbon Loader**.

You do **not** need to know Java, Gradle, or any complicated tools. If you can write a shopping list, you can write a Carbon mod.

---

## What is a Carbon Mod?

A Carbon mod is a `.zip` file you drop into your mods folder. Inside that zip are scripts written in Carbon Java, plus any textures, models, or sounds your mod uses. Carbon Loader reads the zip, compiles your scripts on the fly, and loads your mod into the game — no building required.

---

## Documentation Index

### Getting Started (Start Here)
| File | What it covers |
|------|---------------|
| `getting-started/01_your_first_mod.md` | Making your first mod from scratch |
| `getting-started/02_folder_structure.md` | What goes where inside your mod zip |
| `getting-started/03_carbon_mod_json.md` | The mod info file explained |

### The Language
| File | What it covers |
|------|---------------|
| `language/01_basics.md` | How Carbon Java works, comments, structure |
| `language/02_logic_and_variables.md` | If statements, variables, loops |
| `language/03_items.md` | Adding custom items, weapons, food, armor |
| `language/04_blocks.md` | Adding custom blocks |
| `language/05_mobs.md` | Adding custom mobs and AI |
| `language/06_recipes.md` | Crafting, smelting, and other recipes |
| `language/07_commands.md` | Adding custom slash commands |
| `language/08_effects_and_potions.md` | Custom potion effects |
| `language/09_actions.md` | Writing behavior scripts |

### Reference
| File | What it covers |
|------|---------------|
| `reference/01_events.md` | Every event you can listen to |
| `reference/02_targets.md` | #p, #s, nearest_entity and more |
| `reference/03_item_types.md` | Full list of item types and properties |
| `reference/04_mob_types.md` | Full list of mob properties and AI goals |
| `reference/05_block_types.md` | Full list of block properties |
| `reference/06_errors.md` | Common errors and how to fix them |

### Advanced
| File | What it covers |
|------|---------------|
| `advanced/01_allotrope.md` | Injecting code into Carbon Loader (requires Java knowledge) |

---

## Quick Example

Here is a complete, working mod that adds a Ruby Sword:

**`src/definitions/items.cj`**
```
carbonjava

add item
name (ruby_sword)
type weapon
tier (diamond)
recipe (recipes/ruby_sword.cj)
end
```

**`src/recipes/ruby_sword.cj`**
```
recipe
1. null
2. ruby
3. null
4. null
5. ruby
6. null
7. null
8. stick
9. null
```

That's it. Two files, a sword in the game.

---

## Need Help?

If you see an error like:
```
[Carbon] Error in src/definitions/items.cj line 4:
  "tipe" is not recognised. Did you mean "type"?
```
Check the `reference/06_errors.md` file for a full list of common mistakes and fixes.
