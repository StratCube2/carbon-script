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
| <a href="getting-started/01_your_first_mod.md">getting-started/01_your_first_mod.md</a> | Making your first mod from scratch |
| <a href="getting-started/02_folder_structure.md">getting-started/02_folder_structure.md</a> | What goes where inside your mod zip |
| <a href="getting-started/03_carbon_mod_json.md">getting-started/03_carbon_mod_json.md</a> | The mod info file explained |

### The Language
| File | What it covers |
|------|---------------|
| <a href="language/01_basics.md">language/01_basics.md</a> | How Carbon Java works, comments, structure |
| <a href="language/02_logic_and_variables.md">language/02_logic_and_variables.md</a> | If statements, variables, loops |
| <a href="language/03_items.md">language/03_items.md</a> | Adding custom items, weapons, food, armor |
| <a href="language/04_blocks.md">language/04_blocks.md</a> | Adding custom blocks |
| <a href="language/05_mobs.md">language/05_mobs.md</a> | Adding custom mobs and AI |
| <a href="language/06_recipes.md">language/06_recipes.md</a> | Crafting, smelting, and other recipes |
| <a href="language/07_commands.md">language/07_commands.md</a> | Adding custom slash commands |
| <a href="language/08_effects_and_potions.md">language/08_effects_and_potions.md</a> | Custom potion effects |
| <a href="language/09_actions.md">language/09_actions.md</a> | Writing behavior scripts |

### Reference
| File | What it covers |
|------|---------------|
| <a href="reference/01_events.md">reference/01_events.md</a> | Every event you can listen to |
| <a href="reference/02_targets.md">reference/02_targets.md</a> | #p, #s, nearest_entity and more |
| <a href="reference/03_item_types.md">reference/03_item_types.md</a> | Full list of item types and properties |
| <a href="reference/04_mob_types.md">reference/04_mob_types.md</a> | Full list of mob properties and AI goals |
| <a href="reference/05_block_types.md">reference/05_block_types.md</a> | Full list of block properties |
| <a href="reference/06_errors.md">reference/06_errors.md</a> | Common errors and how to fix them |

### Advanced
| File | What it covers |
|------|---------------|
| <a href="advanced/01_allotrope.md">advanced/01_allotrope.md</a> | Injecting code into Carbon Loader (requires Java knowledge) |

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
Check the <a href="reference/06_errors.md">reference/06_errors.md</a> file for a full list of common mistakes and fixes.
