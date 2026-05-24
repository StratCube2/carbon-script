# Events Reference

Events are used with `on (event_name)` inside `add item`, `add block`, `add mob`, and `add effect` blocks.

---

## Player Events

These fire based on what the player does.

| Event | When it fires |
|-------|--------------|
| `right_click` | Player right-clicks while holding the item |
| `left_click` | Player left-clicks while holding the item |
| `hold` | Every tick while the player is holding the item |
| `eat` | When the player finishes eating the item |
| `equip` | When the player puts the item in an armor slot or offhand |
| `unequip` | When the player removes the item from armor/offhand |
| `jump` | When the player jumps |
| `sneak` | When the player starts sneaking |
| `unsneak` | When the player stops sneaking |
| `sprint` | When the player starts sprinting |
| `unsprint` | When the player stops sprinting |
| `land` | When the player hits the ground after falling |
| `swim` | While the player is swimming |
| `take_damage` | When the player takes any damage |
| `deal_damage` | When the player hits something |
| `hit_mob` | When the player hits a mob specifically |
| `die` | When the player dies |
| `respawn` | When the player respawns |
| `item_picked_up` | When the player picks up an item from the ground |
| `item_dropped` | When the player drops an item |
| `inventory_click` | When the player clicks an item in their inventory |
| `dimension_change` | When the player moves between dimensions |
| `level_up` | When the player gains an XP level |

---

## Block Events

These fire based on what happens to a block.

| Event | When it fires |
|-------|--------------|
| `broken` | When the block is broken by a player or explosion |
| `placed` | When the block is placed |
| `right_clicked` | When a player right-clicks the block |
| `stepped_on` | When a player or mob walks on top of the block |
| `exploded` | When an explosion destroys the block |
| `neighbor_changed` | When a block next to this one is updated |
| `water_logged` | When the block becomes submerged in water |

---

## Mob Events

These fire based on what happens to your custom mob.

| Event | When it fires |
|-------|--------------|
| `spawned` | When the mob first appears in the world |
| `died` | When the mob is killed |
| `hurt` | When the mob takes damage |
| `idle` | Every tick while the mob is doing nothing |
| `target_changed` | When the mob picks a new target to attack |
| `tamed` | When a player successfully tames the mob |
| `angry` | When a neutral mob becomes hostile |
| `despawn` | When the mob is removed from the world |

---

## World Events

These fire based on what happens in the world. Attach these to a loop or a dedicated script.

| Event | When it fires |
|-------|--------------|
| `tick` | Every game tick (20 times per second) |
| `sunrise` | When day begins |
| `sunset` | When night begins |
| `storm_start` | When rain or thunder starts |
| `storm_end` | When rain or thunder stops |
| `thunder_strike` | When lightning hits the world |
| `time_set` | When the world time is changed via a command |

---

## Using Multiple Events

You can have as many `on` blocks as you like. Separate them with a blank line:

```
on (right_click)
run (scripts/attack.cj)

on (equip)
run (scripts/equipped_effect.cj)

on (hold)
if (sneaking)
run (scripts/sneak_hold.cj)

```

The blank line between `on` blocks tells Carbon where one ends and the next begins.
