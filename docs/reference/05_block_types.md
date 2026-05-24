# Block Types Reference

A full list of every block property, type, and valid value.

---

## All Block Properties

| Property | What it does | Range / Options | Example |
|----------|-------------|-----------------|---------|
| `name` | Internal block ID | Lowercase, underscores | `name (ruby_ore)` |
| `type` | Special block behaviour | See types below | `type slab` |
| `texture` | Image for the block face | Path to PNG | `texture (resources/textures/ruby_ore.png)` |
| `tool` | Required tool to mine it | See tools below | `tool (pickaxe)` |
| `hardness` | How long it takes to mine | 0 (instant) to 50 (unbreakable is -1) | `hardness (3)` |
| `explosion_resistance` | Blast resistance | 0 to 1200 | `explosion_resistance (3)` |
| `transparency` | See-through amount | 0.0 (solid) to 1.0 (invisible) | `transparency (0.5)` |
| `luminance` | Light level emitted | 0 to 15 | `luminance (10)` |
| `flammable` | Fire can spread to it | `true` or `false` | `flammable (false)` |
| `gravity` | Falls like sand/gravel | `true` or `false` | `gravity (true)` |
| `slipperiness` | How slippery to walk on | 0.0 to 0.98 | `slipperiness (0.8)` |
| `opacity` | Blocks light and vision | 0.0 to 1.0 | `opacity (0.0)` |
| `sound` | Sound when walked on/broken | See sounds below | `sound (stone)` |
| `recipe` | Crafting recipe to obtain it | Path to recipe file | `recipe (recipes/ruby_block.cj)` |

### Unbreakable Blocks
Set `hardness (-1)` to make a block unbreakable (like bedrock).

### Tool Options
`none`, `pickaxe`, `axe`, `shovel`, `hoe`

### Sound Options
`stone`, `grass`, `wood`, `metal`, `glass`, `sand`, `gravel`, `wool`, `bone`, `nether_brick`

---

## Block Types

| Type | What it does |
|------|-------------|
| `slab` | Half-height block, stacks into a full block |
| `stair` | Staircase shape, rotates based on placement |
| `wall` | Connects to adjacent wall blocks |
| `crop` | Growable plant, goes through growth stages |
| `fluid` | Liquid that flows and spreads |
| `leaf` | Decays when not within 6 blocks of a log |

---

## Crop-Specific Properties

Used with `type crop`:

| Property | What it does | Example |
|----------|-------------|---------|
| `property growth_stages` | How many stages before fully grown | `property growth_stages (7)` |
| `property growth_rate` | Chance to grow per tick (0.0 to 1.0) | `property growth_rate (0.1)` |

---

## Fluid-Specific Properties

Used with `type fluid`:

| Property | What it does | Example |
|----------|-------------|---------|
| `property viscosity` | Thickness. 0 = water speed, 1 = barely flows | `property viscosity (0.5)` |
| `property flow_range` | Blocks it spreads before stopping | `property flow_range (4)` |

---

## Block Events

| Event | When it fires |
|-------|--------------|
| `broken` | Block is destroyed |
| `placed` | Block is placed by a player |
| `right_clicked` | Player right-clicks the block |
| `stepped_on` | Player or mob walks on top |
| `exploded` | An explosion destroys it |
| `neighbor_changed` | An adjacent block is updated |
| `water_logged` | Block becomes submerged |

---

## Example Blocks

### Simple Ore
```
add block
name (ruby_ore)
hardness (3)
explosion_resistance (3)
tool (pickaxe)
sound (stone)
texture (resources/textures/ruby_ore.png)
end
```

### Glowing Crystal
```
add block
name (ruby_crystal)
hardness (1)
explosion_resistance (1)
transparency (0.6)
luminance (10)
opacity (0.0)
tool (none)
sound (glass)
texture (resources/textures/ruby_crystal.png)
end
```

### Falling Block
```
add block
name (ruby_sand)
hardness (0.5)
gravity (true)
sound (sand)
tool (shovel)
texture (resources/textures/ruby_sand.png)
end
```

### Crop
```
add block
name (ruby_crop)
type crop
property growth_stages (7)
property growth_rate (0.08)
texture (resources/textures/ruby_crop.png)

on (broken)
run (scripts/ruby_crop_drop.cj)

end
```
