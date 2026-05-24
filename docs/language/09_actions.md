# Actions

Action scripts live in `src/scripts/`. They describe what happens when something occurs — a right-click, a mob dying, an effect ticking.

---

## Basic Structure

```
carbonjava

add action
... what to do ...
end
```

You can have multiple `add action` blocks in one file. They run in order.

---

## Applying Effects

```
add action
effect (#p) (minecraft:speed 2 100)      @ Speed II for 5 seconds
end
```

Format: `effect (target) (namespace:effect level duration)`

- Duration is in ticks (20 ticks = 1 second)
- Level 1 = effect level I, level 2 = effect level II, etc.

---

## Firing Projectiles

```
add action
fire (minecraft:fireball)
modifier damage(10)
modifier projectile_speed(2)
end
```

You can fire any Minecraft projectile: `minecraft:fireball`, `minecraft:arrow`, `minecraft:snowball`, `minecraft:trident`, etc.

---

## Playing Sounds

```
add action
playsound (resources/sounds/my_sound.mp3)     @ a sound from your mod
end

add action
playsound (minecraft:entity.lightning_bolt.thunder)    @ a vanilla sound
end
```

---

## Spawning Mobs

```
add action
spawn (minecraft:zombie) at (#p)              @ spawn a zombie at the player
spawn (my_mod:ruby_skeleton) at (nearest_entity)
end
```

---

## Dealing Damage

```
add action
damage (target_entity) (5)                    @ deal 5 damage to the target
end
```

---

## Teleporting

```
add action
teleport (#p) to (nearest_entity)            @ teleport player to nearest entity
end
```

---

## Targets

| Target | What it refers to |
|--------|------------------|
| `#p` | The player who triggered this |
| `#s` | The entity this script is attached to (self) |
| `#a` | All players |
| `nearest_entity` | The closest entity to the script runner |
| `target_entity` | The entity currently being fought |

---

## Modifiers

Modifiers adjust numbers inside an action. You can use plain values or math:

```
modifier damage(10)           @ set damage to 10
modifier damage(+5)           @ add 5 to damage
modifier damage(*2)           @ double the damage
modifier movement_speed(*1.5) @ multiply movement speed by 1.5
```

---

## Full Example — Fireball Wand

When right-clicked, fires a fast fireball.

**`src/definitions/items.cj`**
```
carbonjava

add item
name (fireball_wand)
type usable
tier (iron)
texture (resources/textures/fireball_wand.png)

on (right_click)
run (scripts/wand_fire.cj)

end
```

**`src/scripts/wand_fire.cj`**
```
carbonjava

add action
playsound (minecraft:entity.blaze.shoot)
end

add action
fire (minecraft:fireball)
modifier damage(8)
modifier projectile_speed(3)
end
```
