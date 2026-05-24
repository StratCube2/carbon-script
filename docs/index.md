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
| [Your First Mod Guide](getting-started/01_your_first_mod.md) | Making your first mod from scratch |
| [Folder Structure Guide](getting-started/02_folder_structure.md) | What goes where inside your mod zip |
| [Carbon Mod JSON Guide](getting-started/03_carbon_mod_json.md) | The mod info file explained |

### The Language
| File | What it covers |
|------|---------------|
| [Basics Guide](language/01_basics.md) | How Carbon Java works, comments, structure |
| [Logic & Variables Guide](language/02_logic_and_variables.md) | If statements, variables, loops |
| [Items Guide](language/03_items.md) | Adding custom items, weapons, food, armor |
| [Blocks Guide](language/04_blocks.md) | Adding custom blocks |
| [Mobs Guide](language/05_mobs.md) | Adding custom mobs and AI |
| [Recipes Guide](language/06_recipes.md) | Crafting, smelting, and other recipes |
| [Commands Guide](language/07_commands.md) | Adding custom slash commands |
| [Effects & Potions Guide](language/08_effects_and_potions.md) | Custom potion effects |
| [Actions Guide](language/09_actions.md) | Writing behavior scripts |

### Reference
| File | What it covers |
|------|---------------|
| [Events Reference](reference/01_events.md) | Every event you can listen to |
| [Targets Reference](reference/02_targets.md) | #p, #s, nearest_entity and more |
| [Item Types Reference](reference/03_item_types.md) | Full list of item types and properties |
| [Mob Types Reference](reference/04_mob_types.md) | Full list of mob properties and AI goals |
| [Block Types Reference](reference/05_block_types.md) | Full list of block properties |
| [Common Errors Reference](reference/06_errors.md) | Common errors and how to fix them |

### Advanced
| File | What it covers |
|------|---------------|
| [Allotrope Guide](advanced/01_allotrope.md) | Injecting code into Carbon Loader (requires Java knowledge) |

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
Check the [Common Errors Reference](reference/06_errors.md) file for a full list of common mistakes and fixes.
