# Carbon Java Script — Recipes

Recipe files live in `src/recipes/` and use a special simplified format.
They do not start with `carbonjava` — they are purely data files.

---

## Shaped Crafting (3x3 Grid)

Positions are numbered 1–9, read left to right, top to bottom:

```
123
456
789
```

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

`null` means the slot is empty.
Item IDs can be Minecraft items (`minecraft:stick`) or mod items (`ruby`).
If no namespace is given, Carbon assumes it belongs to the current mod.

---

## Shapeless Crafting

A shapeless recipe doesn't care about position — just which items are present.

```
recipe shapeless
ruby
ruby
stick
```

---

## Smelting (Furnace)

```
recipe smelt
input (ruby_ore)
output (ruby)
experience (0.7)
cook_time (200)
```

`cook_time` is in ticks (200 = 10 seconds, the default).

---

## Smoking (Smoker — food only)

```
recipe smoke
input (raw_ruby_meat)
output (cooked_ruby_meat)
experience (0.35)
cook_time (100)
```

---

## Blasting (Blast Furnace — ores/metals)

```
recipe blast
input (ruby_ore)
output (ruby)
experience (0.7)
cook_time (100)
```

---

## Stonecutting

```
recipe stonecutter
input (ruby_block)
output (ruby_slab)
count (2)
```

---

## Smithing Table

```
recipe smithing
base (ruby_sword)
addition (minecraft:netherite_upgrade_smithing_template)
result (netherite_ruby_sword)
```

---

## Linking a Recipe to an Item

Inside an `add item` block, reference the recipe file by filename:

```
add item
name (ruby_sword)
recipe (ruby_sword.cj)
end
```

Carbon automatically looks in `src/recipes/` for this file.
