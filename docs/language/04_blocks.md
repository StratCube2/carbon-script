# Blocks

This page covers everything you can do with `add block`.

---

## Basic Structure

```
carbonjava

add block
name (your_block_name)
texture (resources/textures/your_block.png)
... properties ...
end
```

---

## Properties

| Property | What it does | Example |
|----------|-------------|---------|
| `hardness` | How long it takes to mine. 0 = unbreakable. | `hardness (2)` |
| `explosion_resistance` | How much it resists explosions. | `explosion_resistance (6)` |
| `transparency` | How see-through it is. 0.0 = solid, 1.0 = invisible. | `transparency (0.5)` |
| `luminance` | How much light it gives off. 0 = none, 15 = full. | `luminance (10)` |
| `flammable` | Can fire spread to it? | `flammable (false)` |
| `gravity` | Does it fall like sand? | `gravity (true)` |
| `slipperiness` | How slippery it is. 0.0 = normal, 0.98 = very slippery. | `slipperiness (0.8)` |
| `opacity` | How much it blocks light and vision. | `opacity (0.0)` |
| `sound` | What sound it makes when walked on or broken. | `sound (stone)` |
| `tool` | What tool is needed to mine it properly. | `tool (pickaxe)` |
| `texture` | The image to use for the block's face. | `texture (resources/textures/ruby_block.png)` |

### Sound Options
`stone`, `grass`, `wood`, `metal`, `glass`, `sand`, `gravel`, `wool`

### Tool Options
`none`, `pickaxe`, `axe`, `shovel`, `hoe`

---

## Block Types

You can give a block a special type to change how it behaves:

| Type | What it is |
|------|-----------|
| `slab` | A half-height block |
| `stair` | A staircase block |
| `wall` | A connectable wall (like cobblestone wall) |
| `crop` | A growable plant |
| `fluid` | A liquid |
| `leaf` | Decays when not near a log |

Set the type with `type`:

```
add block
name (ruby_slab)
type slab
texture (resources/textures/ruby_block.png)
hardness (3)
end
```

---

## Crops

Crops need a few extra properties:

```
add block
name (ruby_wheat)
type crop
property growth_stages (7)        @ how many growth steps before fully grown
property growth_rate (0.1)        @ chance to grow each tick (0.0 to 1.0)
texture (resources/textures/ruby_wheat.png)

on (broken)
run (scripts/ruby_wheat_drop.cj)

end
```

---

## Fluids

Fluids also need extra properties:

```
add block
name (ruby_liquid)
type fluid
property viscosity (0.5)          @ thickness. 0 = water speed, 1 = very slow
property flow_range (4)           @ how many blocks it spreads before stopping
texture (resources/textures/ruby_liquid.png)
luminance (8)
end
```

---

## Events on Blocks

You can run scripts when things happen to your block:

```
on (broken)
run (scripts/on_break.cj)

on (placed)
run (scripts/on_place.cj)

on (right_clicked)
run (scripts/on_use.cj)

on (stepped_on)
run (scripts/on_step.cj)

on (exploded)
run (scripts/on_explode.cj)

on (neighbor_changed)
run (scripts/on_neighbor.cj)

on (water_logged)
run (scripts/on_flood.cj)

```

See <a href="../reference/01_events.md">reference/01_events.md</a> for the full events list.

---

## Full Example — Ruby Ore

```
carbonjava

add block
name (ruby_ore)
hardness (3)
explosion_resistance (3)
tool (pickaxe)
sound (stone)
texture (resources/textures/ruby_ore.png)
recipe (recipes/ruby_ore.cj)

on (broken)
run (scripts/ruby_ore_drop.cj)

end
```
