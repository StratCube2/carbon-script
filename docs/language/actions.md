# Carbon Java Script — Actions & Execution

---

## `run` — Execute a Script

Runs a `.cj` script file. The path is relative to the `src/` folder.

```
run (scripts/fireball.cj)
```

### Passing Arguments
```
run (scripts/wave_spawner.cj, mob_type)
run (scripts/attack.cj, target, damage)
```

---

## Parallel Execution with `and`

By default, `run` statements execute one after another (sequentially).
Using `and` makes two or more scripts fire at the same time.

```
run (scripts/fireball.cj) and (scripts/sound.cj)   @ fires together
run (scripts/explosion.cj)                          @ fires after both finish
```

---

## `fire` — Launch a Projectile

Used inside `add action` blocks to launch a projectile.

```
add action
fire (minecraft:fireball)
modifier damage(10)
modifier projectile_speed(2)
end
```

Any Minecraft projectile ID or custom projectile can be used.

### Fire Modifiers
| Modifier | Description |
|----------|-------------|
| `damage(value)` | Damage on hit |
| `projectile_speed(value)` | Launch speed |
| `projectile_gravity(value)` | How fast it falls |
| `explode(value)` | Explosion strength on impact |

---

## `effect` — Apply a Status Effect

Applies a Minecraft or custom status effect to a target.

```
add action
effect (#p) (minecraft:speed 2 30)
end
```

Structure: `effect (target) (effect_id level duration_in_seconds)`

### Targets
| Target | Meaning |
|--------|---------|
| `#p` | The player |
| `#s` | Self (the entity running the script) |
| `nearest_entity` | Closest entity |
| `target_entity` | Current attack target |
| A variable | Any stored entity selector |

---

## `playsound` — Play Audio

```
action (playsound resources/sounds/my_sound.mp3)
action (playsound minecraft:entity.skeleton.hurt)
```

Can reference a file in `resources/sounds/` or a vanilla Minecraft sound ID.

---

## Dependency Scripts

When a script is called via `run` as a dependency (e.g. with `copy` in a mob),
it detects the caller's type automatically and adapts its behaviour. No manual
type declaration is needed beyond `input` for passed arguments.

Example — a script used by multiple mob types:

```
carbonjava

input (mob_type : string)

add mob
name (wave_enemy)
copy (mob_type)
health (20)
end
```

The `copy` keyword pulls the base properties from the given mob type. When
`mob_type` is a variable, it resolves at runtime.
