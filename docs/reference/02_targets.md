# Targets Reference

Targets tell Carbon Java *who* an action applies to. They are used in `effect`, `damage`, `spawn`, `teleport`, and similar action lines.

---

## Built-In Targets

| Target | What it refers to |
|--------|------------------|
| `#p` | The player who triggered this script |
| `#s` | The entity this script belongs to (self) |
| `#a` | Every player currently in the world |
| `nearest_entity` | The closest entity to the script runner |
| `target_entity` | The entity currently being attacked by `#s` |

---

## Examples

```
@ Give the triggering player speed
effect (#p) (minecraft:speed 1 100)

@ Damage the mob you're currently fighting
damage (target_entity) (5)

@ Apply an effect to yourself (the mob running this script)
effect (#s) (minecraft:strength 1 200)

@ Give all players a reward
effect (#a) (minecraft:regeneration 1 100)

@ Spawn a mob at the nearest entity
spawn (minecraft:zombie) at (nearest_entity)
```

---

## Storing a Target in a Variable

You can save a target to a variable and reuse it:

```
target = nearest_entity
range = 10

if (distance #s target < range)
run (scripts/attack.cj, target)
```

This is useful when you want to check conditions about a specific entity before acting on them.

---

## Distance Checks

Use `distance` to measure how far apart two things are:

```
if (distance #p nearest_entity < 5)
run (scripts/too_close_warning.cj)

if (distance #s target < range)
action (attack target)
```

---

## Using Variables as Targets

If you store `nearest_entity` or `#p` into a variable, you can pass it to other scripts:

```
@ In your item script
my_target = nearest_entity
run (scripts/launch_toward.cj, my_target)
```

```
@ In launch_toward.cj
input (my_target)

add action
fire (minecraft:fireball) at (my_target)
end
```

---

## Built-in Read-Only Properties

These can be read like variables but cannot be assigned to.

### Player Properties (`#p.`)

| Property | Type | What it returns |
|----------|------|----------------|
| `#p.name` | string | Username |
| `#p.uuid` | string | Unique ID |
| `#p.health` | float | Current health |
| `#p.max_health` | float | Max health |
| `#p.hunger` | integer | Hunger level (0–20) |
| `#p.position` | position | XYZ coordinates |
| `#p.dimension` | string | `overworld`, `nether`, or `end` |
| `#p.gamemode` | string | `survival`, `creative`, `adventure`, `spectator` |
| `#p.xp_level` | integer | Current XP level |

### Entity/Self Properties (`#s.`)

| Property | Type | What it returns |
|----------|------|----------------|
| `#s.name` | string | Entity type name |
| `#s.health` | float | Current health |
| `#s.max_health` | float | Max health |
| `#s.position` | position | XYZ coordinates |

---

## Writing to Another Player's Data

```
#player(target).tpa_requester = #p.name
```

Use `#player(variable).field` to write persistent data to a specific player who isn't `#p`.

---

## Finding a Player by Name

```
requester = find_player (#p.tpa_requester)
```

Returns an entity if online, `null` if not. Always check for `null` before using the result.
