# Recipes

Recipe files live in `src/recipes/`. Each recipe is its own `.cj` file.

---

## Shaped Crafting

A shaped recipe requires items to be placed in specific slots of a 3x3 crafting grid.

The slots are numbered like this:
```
1 2 3
4 5 6
7 8 9
```

Use `null` for empty slots.

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

This makes a sword shape (two rubies in a column with a stick below).

For item IDs, vanilla Minecraft items use `minecraft:item_name`. Items from your mod just use their name directly.

---

## Shapeless Crafting

A shapeless recipe doesn't care about positions — just what items are in the grid.

```
recipe shapeless
input (ruby, ruby, ruby)
output (ruby_block)
```

---

## Smelting

```
recipe smelting
input (ruby_ore)
output (ruby)
experience (0.7)      @ XP given when smelting completes
cook_time (200)       @ in ticks. 200 ticks = 10 seconds
```

---

## Smoking

Smoking is like smelting but faster, and only works on food. Uses a smoker block.

```
recipe smoking
input (raw_ruby_meat)
output (cooked_ruby_meat)
experience (0.35)
cook_time (100)
```

---

## Blasting

Blasting is like smelting but faster for ores. Uses a blast furnace.

```
recipe blasting
input (ruby_ore)
output (ruby)
experience (0.7)
cook_time (100)
```

---

## Campfire Cooking

```
recipe campfire
input (raw_ruby_meat)
output (cooked_ruby_meat)
cook_time (600)       @ campfires are slow
```

---

## Stonecutting

Used to cut blocks into slabs, stairs, etc. Uses a stonecutter block.

```
recipe stonecutting
input (ruby_block)
output (ruby_slab)
count (2)             @ how many outputs per craft
```

---

## Smithing

Used to upgrade items. Uses a smithing table. In 1.20.1 this requires a template, base item, and addition.

```
recipe smithing
template (minecraft:netherite_upgrade_smithing_template)
base (ruby_sword)
addition (minecraft:netherite_ingot)
output (netherite_ruby_sword)
```

---

## Linking a Recipe to an Item

Inside your `add item` block, add:

```
recipe (recipes/ruby_sword.cj)
```

The path is relative to `src/`.
