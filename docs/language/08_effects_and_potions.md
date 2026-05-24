# Effects and Potions

Carbon lets you define custom potion effects and link them to drinkable potion items.

---

## Custom Effects

An effect defines what happens while it is active. Effects are defined using `add effect`.

```
carbonjava

add effect
name (flight)
levels (1)               @ how many levels (I, II, III, etc.) this effect has
run (scripts/flight_tick.cj)
end
```

The script in `run` is called **every tick** while the effect is active on a player.

---

## The Effect Script

The script that an effect calls should describe what happens each tick:

**`src/scripts/flight_tick.cj`**
```
carbonjava

if not (crouching)
run (scripts/apply_levitation.cj)

```

**`src/scripts/apply_levitation.cj`**
```
carbonjava

add action
effect (#s) (minecraft:levitation 1 1)
end
```

While the player has the `flight` effect and is not crouching, they float. Crouching cancels the levitation, simulating landing.

---

## Custom Potions

A potion is just a special item type that applies an effect when drunk:

```
carbonjava

add item
name (flight_potion)
type potion
effect (flight)                  @ the name of your add effect
duration (600)                   @ how many ticks the effect lasts (600 = 30 seconds)
levels (1)                       @ which level of the effect to apply
texture (resources/textures/flight_potion.png)
recipe (recipes/flight_potion.cj)
end
```

---

## Using Vanilla Effects

You can also apply vanilla Minecraft effects directly — no need to define a custom `add effect`:

```
add action
effect (#p) (minecraft:speed 2 200)          @ Speed II for 200 ticks (10 seconds)
effect (#p) (minecraft:regeneration 1 100)
end
```

The format is: `effect (target) (namespace:effect_name level duration)`

---

## Effect Targets

| Target | Who it affects |
|--------|---------------|
| `#p` | The player who triggered this |
| `#s` | The entity running this script (self) |
| `#a` | All players |
| `nearest_entity` | Closest entity |
| `target_entity` | The entity being fought |

---

## Full Example — Poison Blade

When you hit a mob with this sword, it gets poisoned.

**`src/definitions/items.cj`**
```
carbonjava

add item
name (poison_blade)
type weapon
tier (iron)
texture (resources/textures/poison_blade.png)
recipe (recipes/poison_blade.cj)

on (hit_mob)
run (scripts/poison_on_hit.cj)

end
```

**`src/scripts/poison_on_hit.cj`**
```
carbonjava

add action
effect (target_entity) (minecraft:poison 1 100)   @ Poison I for 5 seconds
end
```
