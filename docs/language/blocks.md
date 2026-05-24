# Carbon Java Script — Block Types & Properties

---

## `add item`

```
add item
name (ruby_sword)
type weapon
subtype sword
damage (10)
durability (120)
enchantability (15)
stack_size (1)
recipe (ruby_sword.cj)
model (resources/models/ruby_sword.glb)
texture (resources/textures/ruby_sword.png)

on (right_click)
run (scripts/fireball.cj)

on (hold)
run (scripts/ruby_effect.cj)

end
```

### Item Types
| Type | Description |
|------|-------------|
| `weapon` | Deals damage, has attack speed |
| `tool` | Mines blocks, requires subtype |
| `armor` | Worn in armor slots |
| `food` | Edible, restores hunger |
| `ingredient` | Crafting material only |
| `potion` | Drinkable, links to an effect |
| `block_item` | Places a block when used |
| `misc` | Anything that doesn't fit above |

### Tool Subtypes (used with `type tool`)
`sword`, `pickaxe`, `axe`, `shovel`, `hoe`

### Armor Slots (used with `type armor`)
`helmet`, `chestplate`, `leggings`, `boots`

### Food Properties
```
add item
name (ruby_apple)
type food
hunger (4)
saturation (2.4)
on (eaten)
run (scripts/ruby_eat_effect.cj)
end
```

---

## `add block`

```
add block
name (ruby_cluster)
property hardness (1)
property explosion_resistance (1)
property transparency (0.9)
property luminance (5)
property flammable (false)
property gravity (false)
tool (none)
texture (resources/textures/ruby_cluster.png)
model (resources/models/ruby_cluster.glb)

on (broken)
run (scripts/ruby_break.cj)

on (stepped_on)
run (scripts/ruby_step.cj)

end
```

### Block Properties
| Property | Description | Example |
|----------|-------------|---------|
| `hardness` | How long to mine | `1` to `50` |
| `explosion_resistance` | Blast resistance | `1` to `1200` |
| `transparency` | See-through level | `0.0` (solid) to `1.0` (glass) |
| `luminance` | Light emitted | `0` to `15` |
| `flammable` | Can catch fire | `true` or `false` |
| `gravity` | Falls like sand | `true` or `false` |

### Block Tool Requirements
`none`, `pickaxe`, `axe`, `shovel`, `hoe`
Add a tier after: `pickaxe_stone`, `pickaxe_iron`, `pickaxe_diamond`

---

## `add mob`

```
add mob
name (weak_skeleton)
health (10)
damage (2)
hostility (hostile)
item (minecraft:crossbow)
model (resources/models/weak_skeleton.glb)
texture (resources/textures/weak_skeleton.png)
size (1.0)

add behaviour
copy (minecraft:skeleton)

add behaviour
trigger (health < 5)
action (playsound resources/sounds/weak_skeleton_run.mp3)
action (run away)
action (run to minecraft:skeleton)
modifier movement_speed(*1.5)

on (spawned)
run (scripts/skeleton_spawn.cj)

on (died)
run (scripts/skeleton_drop.cj)

end
```

### Mob Hostility Types
| Type | Description |
|------|-------------|
| `passive` | Never attacks |
| `neutral` | Attacks only if provoked |
| `hostile` | Always attacks players |
| `boss` | Hostile, boss bar, high health |

### Mob Behaviour Actions
| Action | Description |
|--------|-------------|
| `run away` | Flee from current target |
| `run to (target)` | Move toward entity or player |
| `attack (target)` | Attack a specific target |
| `playsound (file or id)` | Play a sound |

### Mob Modifiers
```
modifier movement_speed(*1.5)   @ multiply
modifier damage(+2)             @ add
modifier health(/2)             @ divide
modifier attack_range(10)       @ set value directly
```

### Mob Spawn Conditions (TBD — to be finalized)
Biome, light level, height, dimension — not yet fully defined.

### Mob Drop Tables (TBD — to be finalized)
Syntax for drops not yet defined.

---

## `add command`

```
add command
name (spawn_wave)
permission (op)
argument (mob_type : string)
argument (target : selector)
argument (count : int)

execute
run (scripts/wave_spawner.cj, mob_type, target, count)
end
```

### Permissions
`op`, `all` (any player can use it)

### Argument Types
`string`, `int`, `float`, `selector`, `boolean`

### Receiving Arguments in a Script
```
carbonjava

input (mob_type : string)
input (target : selector)
input (count : int)

@ use mob_type, target, count as variables below
```

---

## `add effect`

```
add effect
name (flight)
levels (1)
run (scripts/flight.cj)
end
```

The linked script runs every tick while the effect is active on an entity.

---

## `add potion`

A potion is an item of `type potion` that links to an `add effect`:

```
add item
name (flight_potion)
type potion
effect (flight)
texture (resources/textures/flight_potion.png)
end
```

---

## `add action`

Reusable blocks of logic called from other scripts via `run`.

```
add action
fire (minecraft:fireball)
modifier damage(10)
modifier projectile_speed(2)
end

add action
effect (#p) (minecraft:speed 2 30)
end
```

### Target Selectors
| Selector | Meaning |
|----------|---------|
| `#p` | The player |
| `#s` | Self (the entity running the script) |
| `nearest_entity` | Closest entity |
| `target_entity` | The entity currently being fought |
