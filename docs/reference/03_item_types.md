# Item Types Reference

A full list of every item type, property, and valid value.

---

## All Item Types

| Type | What it is |
|------|-----------|
| `weapon` | Combat item — swords, axes |
| `tool` | Mining/digging item — pickaxes, shovels, hoes |
| `armor` | Equippable protection |
| `food` | Edible — restores hunger |
| `ingredient` | Crafting material |
| `potion` | Drinkable — applies an effect |
| `block_item` | Places a block when used |
| `usable` | Has a right-click action (compass, brush, totem) |
| `consumable` | Disappears on use (firework rockets) |
| `misc` | Anything else |

---

## Shared Properties (All Items)

| Property | Required | What it does |
|----------|----------|-------------|
| `name` | Yes | Internal ID. Underscores only, no spaces. |
| `type` | Yes | The item type from the list above. |
| `texture` | Recommended | Path to the item's PNG image. |
| `recipe` | No | Path to a recipe `.cj` file. |

---

## Weapon Properties

| Property | What it does | Default |
|----------|-------------|---------|
| `tier` | Sets damage and durability from a preset | none |
| `damage` | Overrides damage value | from tier |
| `durability` | Overrides durability value | from tier |
| `enchantability` | How easily it can be enchanted (higher = better) | from tier |

### Weapon Tiers

| Tier | Damage | Durability |
|------|--------|-----------|
| `wood` | 4 | 59 |
| `stone` | 5 | 131 |
| `iron` | 6 | 250 |
| `diamond` | 7 | 1561 |
| `netherite` | 8 | 2031 |

---

## Tool Properties

| Property | What it does |
|----------|-------------|
| `tool` | Subtype and tier combined: `tool (pickaxe_diamond)` |

### Tool Subtypes
`pickaxe`, `axe`, `shovel`, `hoe`, `brush`

### Tool Tiers
`wood`, `stone`, `iron`, `diamond`, `netherite`

Combine with underscore: `tool (subtype_tier)` → `tool (pickaxe_iron)`

---

## Armor Properties

| Property | What it does | Example |
|----------|-------------|---------|
| `slot` | Which slot it goes in | `slot (chestplate)` |
| `material` | Preset that sets all stats | `material (diamond)` |
| `protection` | Overrides protection points | `protection (8)` |
| `toughness` | Overrides toughness | `toughness (2.0)` |
| `knockback_resistance` | Overrides knockback resistance | `knockback_resistance (0.1)` |

### Armor Slots
`helmet`, `chestplate`, `leggings`, `boots`

### Armor Materials (Presets)

| Material | Protection (full set) | Toughness | Knockback Resistance |
|----------|----------------------|-----------|---------------------|
| `leather` | 7 | 0 | 0 |
| `chainmail` | 12 | 0 | 0 |
| `iron` | 15 | 0 | 0 |
| `diamond` | 20 | 8 | 0 |
| `netherite` | 20 | 12 | 0.4 |

Note: protection values above are for the full set. Individual piece values vary.

---

## Food Properties

| Property | What it does | Example |
|----------|-------------|---------|
| `hunger` | Hunger points restored (2 = one drumstick) | `hunger (6)` |
| `saturation` | How long before hunger drops again | `saturation (1.2)` |
| `always_edible` | Can eat even when full | `always_edible (true)` |
| `is_meat` | Wolves and other meat-eaters will want it | `is_meat (false)` |

---

## Potion Properties

| Property | What it does | Example |
|----------|-------------|---------|
| `effect` | Name of an `add effect` to apply | `effect (flight)` |
| `duration` | How long the effect lasts in ticks | `duration (600)` |
| `levels` | Which level of the effect | `levels (1)` |

---

## Stack Size

By default items stack to 64. Override with:

```
stack_size (1)      @ for weapons, armor, tools that shouldn't stack
stack_size (16)     @ for things like snowballs
```
