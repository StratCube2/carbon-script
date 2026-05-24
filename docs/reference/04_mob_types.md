# Mob Types Reference

A full list of every mob property, hostility type, AI goal, and spawn condition.

---

## All Mob Properties

| Property | Required | What it does | Example |
|----------|----------|-------------|---------|
| `name` | Yes | Internal mob ID | `name (shadow_zombie)` |
| `health` | Yes | Max health points (2 = one heart) | `health (20)` |
| `damage` | Yes | Damage dealt per hit | `damage (4)` |
| `hostility` | Yes | How aggressive it is | `hostility (hostile)` |
| `model` | Recommended | Path to `.glb` 3D model | `model (resources/models/mob.glb)` |
| `texture` | Recommended | Path to texture PNG | `texture (resources/textures/mob.png)` |
| `item` | No | Item held in main hand | `item (minecraft:crossbow)` |
| `scale` | No | Size multiplier | `scale (1.5)` |
| `copy` | No | Copy all stats from another mob | `copy (minecraft:skeleton)` |
| `knockback_resistance` | No | 0.0 to 1.0, how much knockback it ignores | `knockback_resistance (0.5)` |
| `armor` | No | Armor points (each 2 = one chestplate bar) | `armor (4)` |
| `follow_range` | No | How far it can detect players (in blocks) | `follow_range (16)` |
| `movement_speed` | No | Movement speed. 0.25 is roughly player walk speed | `movement_speed (0.35)` |

---

## Hostility Types

| Type | Behaviour |
|------|----------|
| `passive` | Never attacks. Flees if hit. (Cow, Pig) |
| `neutral` | Only attacks if provoked. (Enderman, Bee, Wolf) |
| `hostile` | Attacks players on sight. (Zombie, Skeleton) |
| `boss` | High health, shows boss bar, special AI. |
| `ambient` | Ignored by everything, never attacks. (Bat) |
| `scared` | Actively runs away from players and mobs. (Rabbit) |

---

## Spawn Conditions

Add these directly inside `add mob`, alongside other properties.

| Property | What it does | Example |
|----------|-------------|---------|
| `spawn_biome` | Which biomes it spawns in | `spawn_biome (plains, forest, taiga)` |
| `spawn_light` | Max light level for spawning | `spawn_light (max 7)` |
| `spawn_height` | Y level range | `spawn_height (min 40, max 128)` |
| `spawn_dimension` | Which dimension | `spawn_dimension (overworld)` |
| `spawn_weight` | How common. Higher = more common. | `spawn_weight (10)` |

### Valid Dimensions
`overworld`, `nether`, `end`

### Common Biome Names
`plains`, `forest`, `taiga`, `desert`, `jungle`, `swamp`, `savanna`,
`snowy_plains`, `mountains`, `ocean`, `river`, `nether_wastes`,
`soul_sand_valley`, `crimson_forest`, `warped_forest`, `basalt_deltas`,
`the_end`, `end_highlands`

---

## AI Goals

Used inside `add behaviour` blocks.

| Goal | What the mob does |
|------|-----------------|
| `wander` | Moves randomly around nearby |
| `look_at_player` | Turns its head to face nearby players |
| `avoid_water` | Stays out of water |
| `follow_owner` | Follows the player who tamed it |
| `defend_owner` | Attacks anything that hits its owner |
| `panic` | Runs away from threats when health is low |
| `graze` | Eats grass blocks (for passive mobs) |
| `hunt (target)` | Actively seeks and kills a specific mob |
| `attack (target)` | Attacks a specific target |
| `run away` | Flees from the nearest threat |
| `run to (target)` | Moves toward a specific mob or entity |
| `sit` | Stays still (for tamed mobs) |

---

## Behaviour Trigger Conditions

Used with `trigger (condition)` inside `add behaviour`.

| Condition | When it's true |
|-----------|---------------|
| `health < X` | Health is below X |
| `health > X` | Health is above X |
| `target_in_range (X)` | Target is within X blocks |
| `on_fire` | The mob is on fire |
| `in_water` | The mob is in water |
| `day` | It is daytime |
| `night` | It is nighttime |

---

## Drop Table Reference

Inside `add drops`, each drop uses this format:
```
drop (item_id, chance, min_amount, max_amount)
```

| Parameter | What it means |
|-----------|--------------|
| `item_id` | The item to drop. Use `modid:name` or just `name` for your own items. |
| `chance` | Probability between 0.0 (never) and 1.0 (always) |
| `min_amount` | Minimum number dropped |
| `max_amount` | Maximum number dropped |

```
add drops
drop (ruby, 0.75, 1, 4)                    @ 75% chance, 1 to 4 rubies
drop (minecraft:experience_bottle, 0.1, 1, 1)
end
```

---

## Mob Events

| Event | When it fires |
|-------|--------------|
| `spawned` | When the mob first appears |
| `died` | When the mob is killed |
| `hurt` | When the mob takes any damage |
| `idle` | Every tick while doing nothing |
| `target_changed` | When the mob picks a new attack target |
| `tamed` | When a player tames the mob |
| `angry` | When a neutral mob becomes hostile |
| `despawn` | When the mob is removed from the world |
