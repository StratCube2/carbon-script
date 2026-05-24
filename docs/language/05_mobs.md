# Mobs

This page covers everything you can do with `add mob`.

---

## Basic Structure

```
carbonjava

add mob
name (your_mob_name)
health (20)
damage (2)
hostility (hostile)
model (resources/models/your_mob.glb)
... more properties ...
end
```

---

## Core Properties

| Property | What it does | Example |
|----------|-------------|---------|
| `name` | The mob's ID | `name (shadow_zombie)` |
| `health` | Max health (each heart = 2) | `health (20)` |
| `damage` | Damage dealt per hit | `damage (4)` |
| `hostility` | How aggressive it is | `hostility (hostile)` |
| `model` | Path to the 3D model file | `model (resources/models/shadow_zombie.glb)` |
| `texture` | Texture for the model | `texture (resources/textures/shadow_zombie.png)` |
| `item` | Item the mob holds | `item (minecraft:crossbow)` |
| `scale` | Size multiplier | `scale (1.5)` |
| `copy` | Copy all stats from another mob | `copy (minecraft:skeleton)` |

---

## Hostility Types

| Type | Behaviour |
|------|----------|
| `passive` | Never attacks. (Cow, Pig) |
| `neutral` | Only attacks if hit first. (Enderman, Bee) |
| `hostile` | Attacks players on sight. (Zombie, Skeleton) |
| `boss` | High health, shows a boss bar, complex AI. (Wither, Ender Dragon) |
| `ambient` | Ignored by everything, never attacks. (Bat) |
| `scared` | Actively runs away from players and mobs. (Rabbit) |

---

## Spawn Conditions

Control where and when your mob appears naturally:

```
spawn_biome (plains, forest)         @ list of biomes where it can spawn
spawn_light (max 7)                  @ only spawns where light level is 7 or below
spawn_height (min 40, max 128)       @ Y level range
spawn_dimension (overworld)          @ overworld, nether, or end
spawn_weight (10)                    @ higher = more common. Default is 10.
```

---

## Behaviour Blocks

Behaviour blocks define what the mob does. You can have as many as you want inside one `add mob`.

### Copying Another Mob's AI

The easiest way — just copy everything from a vanilla mob:

```
add behaviour
copy (minecraft:skeleton)
end
```

### Custom AI Goals

Define what the mob tries to do:

```
add behaviour
wander                               @ moves around randomly
look_at_player                       @ turns to face nearby players
avoid_water                          @ stays out of water
panic                                @ runs away when health is low
end
```

### Trigger-Based Behaviour

Run actions when a condition is met:

```
add behaviour
trigger (health < 5)
action (playsound resources/sounds/hurt.mp3)
action (run away)
action (run to minecraft:skeleton)
modifier movement_speed(*1.5)        @ speed x1.5 when triggered
end
```

### Available AI Goals

| Goal | What it does |
|------|-------------|
| `wander` | Moves randomly |
| `look_at_player` | Faces nearby players |
| `avoid_water` | Stays out of water |
| `follow_owner` | Follows the player who tamed it |
| `defend_owner` | Attacks anything that hits the owner |
| `panic` | Flees when health is low |
| `graze` | Eats grass blocks |
| `hunt (target)` | Actively seeks and kills a specific mob type |
| `attack (target)` | Attacks a specific target |
| `run away` | Flees from the nearest threat |
| `run to (target)` | Moves toward a specific target |

---

## Drop Tables

Use `add drops` inside your `add mob` to define what it drops. Syntax:
`drop (item, chance, min_amount, max_amount)`

- **chance** is between 0.0 (never) and 1.0 (always)

```
add mob
name (ruby_skeleton)
health (20)
damage (3)
hostility (hostile)

add drops
drop (ruby, 0.5, 1, 3)                    @ 50% chance, drops 1 to 3
drop (minecraft:bone, 1.0, 0, 2)          @ always drops 0 to 2 bones
drop (minecraft:diamond, 0.01, 1, 1)      @ 1% chance for one diamond
end

end
```

Note: `add drops` has its own `end` before the mob's `end`.

---

## Mob Events

```
on (spawned)
run (scripts/on_spawn.cj)

on (died)
run (scripts/on_death.cj)

on (hurt)
run (scripts/on_hurt.cj)

on (target_changed)
run (scripts/on_aggro.cj)

on (tamed)
run (scripts/on_tame.cj)

```

---

## Full Example — Weak Skeleton

```
carbonjava

add mob
name (weak_skeleton)
health (10)
damage (2)
hostility (hostile)
item (minecraft:crossbow)
model (resources/models/weak_skeleton.glb)

spawn_biome (plains, desert)
spawn_light (max 7)
spawn_dimension (overworld)
spawn_weight (8)

add behaviour
copy (minecraft:skeleton)
end

add behaviour
trigger (health < 5)
action (playsound resources/sounds/weak_skeleton_run.mp3)
action (run away)
action (run to minecraft:skeleton)
modifier movement_speed(*1.5)
end

add drops
drop (ruby, 0.3, 1, 2)
drop (minecraft:bone, 1.0, 0, 2)
end

on (died)
run (scripts/weak_skeleton_death.cj)

end
```
