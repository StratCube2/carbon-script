# Carbon Java Script — Events

Events are used with `on` inside any `add` block, or standalone in action scripts.

```
on (event_name)
run (script.cj)

on (other_event)
run (other.cj)
```

An empty line between `on` blocks separates them. They are not closed with `end`
individually — they end when the next `on` block begins, or when `end` closes
the parent block.

---

## Player Events

| Event | Trigger |
|-------|---------|
| `right_click` | Player right-clicks while holding item |
| `left_click` | Player left-clicks while holding item |
| `hold` | Player is currently holding this item (runs every tick) |
| `equip` | Player equips the item |
| `unequip` | Player unequips the item |
| `jump` | Player jumps |
| `sneak` | Player starts sneaking |
| `unsneak` | Player stops sneaking |
| `sprint` | Player starts sprinting |
| `unsprint` | Player stops sprinting |
| `land` | Player lands after falling |
| `swim` | Player starts swimming |
| `eat` | Player finishes eating this item |
| `take_damage` | Player takes any damage |
| `deal_damage` | Player deals damage to an entity |
| `die` | Player dies |
| `respawn` | Player respawns |

---

## Block Events

| Event | Trigger |
|-------|---------|
| `broken` | Block is broken by a player or entity |
| `placed` | Block is placed |
| `stepped_on` | An entity steps on the block |
| `right_clicked` | Player right-clicks the block |
| `exploded` | Block is destroyed by an explosion |

---

## Mob Events

| Event | Trigger |
|-------|---------|
| `spawned` | Mob spawns in the world |
| `died` | Mob dies |
| `hurt` | Mob takes damage |
| `idle` | Mob has no target (runs every tick) |
| `target_changed` | Mob switches to a new attack target |

---

## World Events

| Event | Trigger |
|-------|---------|
| `tick` | Every game tick (20x per second) |
| `sunrise` | In-game sunrise |
| `sunset` | In-game sunset |
| `storm_start` | A storm begins |
| `storm_end` | A storm ends |

---

## Using Events with Conditions

`on` can be combined with `if` for conditional behaviour:

```
on (right_click)
if (sneaking)
run (special_attack.cj)
else
run (normal_attack.cj)
```

---

## NOTE — Events List is Not Final

The events list above covers what has been confirmed so far. More events
will be added as the spec is completed, particularly around:
- Inventory changes
- Advancement unlocks
- Dimension travel
- Entity collision
