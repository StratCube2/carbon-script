# Carbon Lang — Documentation

Welcome! This is the official documentation for **Carbon Lang** (`.cj`), the scripting language for making Minecraft mods using **Carbon Loader**.

You do **not** need to know Java, Gradle, or any complicated tools. If you can write a shopping list, you can write a Carbon mod.

---

## What is a Carbon Mod?

A Carbon mod is a `.zip` file you drop into your mods folder. Inside that zip are scripts written in Carbon Lang, plus any textures, models, or sounds your mod uses. Carbon Loader reads the zip, compiles your scripts on the fly, and loads your mod into the game — no building required.

---

## Documentation Index

### Getting Started (Start Here)
| File | What it covers |
|------|---------------|
| <a href="getting-started/01_your_first_mod/">getting-started/01_your_first_mod/</a> | Making your first mod from scratch |
| <a href="getting-started/02_folder_structure/">getting-started/02_folder_structure/</a> | What goes where inside your mod zip |
| <a href="getting-started/03_carbon_mod_json/">getting-started/03_carbon_mod_json/</a> | The mod info file explained |

### The Language
| File | What it covers |
|------|---------------|
| <a href="language/01_basics/">language/01_basics/</a> | How Carbon Lang works, comments, structure |
| <a href="language/02_logic_and_variables/">language/02_logic_and_variables/</a> | If statements, variables, loops |
| <a href="language/03_items/">language/03_items/</a> | Adding custom items, weapons, food, armor |
| <a href="language/04_blocks/">language/04_blocks/</a> | Adding custom blocks |
| <a href="language/05_mobs/">language/05_mobs/</a> | Adding custom mobs and AI |
| <a href="language/06_recipes/">language/06_recipes/</a> | Crafting, smelting, and other recipes |
| <a href="language/07_commands/">language/07_commands/</a> | Adding custom slash commands |
| <a href="language/08_effects_and_potions/">language/08_effects_and_potions/</a> | Custom potion effects |
| <a href="language/09_actions/">language/09_actions/</a> | Writing behavior scripts |

### Reference
| File | What it covers |
|------|---------------|
| <a href="reference/01_events/">reference/01_events/</a> | Every event you can listen to |
| <a href="reference/02_targets/">reference/02_targets/</a> | #p, #s, nearest_entity and more |
| <a href="reference/03_item_types/">reference/03_item_types/</a> | Full list of item types and properties |
| <a href="reference/04_mob_types/">reference/04_mob_types/</a> | Full list of mob properties and AI goals |
| <a href="reference/05_block_types/">reference/05_block_types/</a> | Full list of block properties |
| <a href="reference/06_errors/">reference/06_errors/</a> | Common errors and how to fix them |

### Advanced
| File | What it covers |
|------|---------------|
| <a href="advanced/01_allotrope/">advanced/01_allotrope/</a> | Injecting code into Carbon Loader (requires Java knowledge) |

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
Check the <a href="reference/06_errors/">reference/06_errors/</a> file for a full list of common mistakes and fixes.
