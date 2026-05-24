# Items

This page covers everything you can do with `add item`.

---

## Basic Structure

```
carbonjava

add item
name (your_item_name)
type (item_type)
... properties ...
end
```

---

## Item Types

| Type | What it is |
|------|-----------|
| `weapon` | Swords, axes used for combat |
| `tool` | Pickaxes, shovels, hoes used for mining/digging |
| `armor` | Helmets, chestplates, leggings, boots |
| `food` | Edible items that restore hunger |
| `ingredient` | Crafting materials (like diamonds or your ruby) |
| `potion` | Drinkable item that applies an effect |
| `block_item` | An item that places a block when used |
| `usable` | Items with a right-click action (compass, totem, brush) |
| `consumable` | Items that disappear on use (like firework rockets) |
| `misc` | Anything that doesn't fit the other categories |

---

## Weapons

```
add item
name (ruby_sword)
type weapon
tier (diamond)        @ Sets damage and durability automatically
texture (resources/textures/ruby_sword.png)
recipe (recipes/ruby_sword.cj)

on (right_click)
run (scripts/ruby_sword_ability.cj)

end
```

### Tiers for Weapons

| Tier | Based on |
|------|---------|
| `wood` | Wooden sword stats |
| `stone` | Stone sword stats |
| `iron` | Iron sword stats |
| `diamond` | Diamond sword stats |
| `netherite` | Netherite sword stats |

You can override specific values after setting the tier:

```
tier (diamond)
damage (20)         @ Overrides just the damage, keeps diamond durability
```

---

## Tools

For tools, combine the tool type and tier using an underscore:

```
add item
name (ruby_pickaxe)
type tool
tool (pickaxe_diamond)      @ pickaxe with diamond-level stats
texture (resources/textures/ruby_pickaxe.png)
recipe (recipes/ruby_pickaxe.cj)
end
```

### Tool Subtypes

`pickaxe`, `axe`, `shovel`, `hoe`, `brush`

### Tool Tiers

`wood`, `stone`, `iron`, `diamond`, `netherite`

So `tool (axe_iron)` = an iron-tier axe. `tool (shovel_netherite)` = a netherite-tier shovel.

---

## Armor

```
add item
name (ruby_chestplate)
type armor
slot (chestplate)             @ helmet, chestplate, leggings, boots
material (diamond)            @ sets base protection automatically
texture (resources/textures/ruby_chestplate.png)
recipe (recipes/ruby_chestplate.cj)
end
```

You can override specific values after the material:

```
material (netherite)
protection (20)               @ override just protection
toughness (4.0)
knockback_resistance (0.2)
```

### Armor Slots

`helmet`, `chestplate`, `leggings`, `boots`

### Armor Materials (Presets)

`leather`, `chainmail`, `iron`, `diamond`, `netherite`

---

## Food

```
add item
name (ruby_apple)
type food
hunger (4)                    @ hunger bars restored (each bar = 2 points)
saturation (0.8)              @ how long before hunger drops again
always_edible (true)          @ can eat even when full
is_meat (false)               @ affects which mobs are attracted to it
texture (resources/textures/ruby_apple.png)

on (eaten)
run (scripts/ruby_apple_effect.cj)

end
```

---

## Usable Items (Right-Click Actions)

```
add item
name (ruby_wand)
type usable
tier (diamond)
texture (resources/textures/ruby_wand.png)

on (right_click)
run (scripts/wand_fire.cj)

on (right_click)
anif (sneaking)
run (scripts/wand_special.cj)

end
```

---

## Events Inside Items

You can respond to many things happening with an item. Put `on (event)` blocks inside your `add item`, before the closing `end`.

```
on (right_click)
run (scripts/use.cj)

on (equip)
run (scripts/equipped.cj)

on (hold)                     @ runs every tick while held
run (scripts/held_tick.cj)

```

A blank line between `on` blocks separates them. See <a href="../reference/01_events/">reference/01_events/</a> for the full list of events.

---

## Full Example — Ruby Sword

```
carbonjava

add item
name (ruby_sword)
type weapon
tier (diamond)
damage (12)                   @ override damage to be slightly above diamond
texture (resources/textures/ruby_sword.png)
recipe (recipes/ruby_sword.cj)

on (right_click)
anif (sneaking)
run (scripts/ruby_dash.cj)

on (hit_mob)
run (scripts/ruby_on_hit.cj)

end
```
