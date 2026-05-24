# Logic and Variables

This page covers how to make decisions, store information, and repeat things in Carbon Java.

---

## Variables

A variable is a named box that holds a value. You create one by just writing its name, an equals sign, and a value.

```
@ Creating variables
count = 0
range = 10
speed = 1.5
target = nearest_entity
message = "Hello!"
```

No special keywords needed. Just write it.

You can change a variable the same way:

```
count = count + 1
speed = speed * 2
```

### Types — Invisible to You

You never need to write a type. Carbon figures it out automatically:

| What you write | What Carbon sees |
|----------------|-----------------|
| `count = 10` | count is a whole number |
| `speed = 1.5` | speed is a decimal number |
| `name = "ruby"` | name is text |
| `target = nearest_entity` | target is an entity |

### Persistent Data

Normal variables reset when the script ends. If you want data to stick around, use a **prefix**:

| Prefix | What it saves |
|--------|--------------|
| `#p.` | Saved per player |
| `#a.` | Applies to all players at once |
| `#e.` | Saved per entity |
| `#b.` | Saved per block |
| `#w.` | Saved for the whole world |
| `#mod.` | Saved globally for your mod |

```
@ Count how many times this player has right-clicked
#p.clicks = #p.clicks + 1

@ Track a world-wide boss kill counter
#w.boss_kills = #w.boss_kills + 1

@ Give all players a score
#a.score += 10
```

---

## If Statements

An `if` checks whether something is true. If it is, the code inside runs.

```
if (holding)
run (scripts/glow_effect.cj)
```

If not true, it skips to `else` (if you have one):

```
if (holding)
run (scripts/glow_effect.cj)
else
run (scripts/normal.cj)
```

### And — `anif`

Because Carbon Java doesn't use indentation for logic, "and" is written as `anif` on the next line:

```
if (holding)
anif (sneaking)
run (scripts/special_ability.cj)
```

This only runs if the player is BOTH holding the item AND sneaking.

### Or

Use `or` on the same line as `if`:

```
if (holding) or (sneaking)
run (scripts/glow.cj)
```

This runs if the player is holding OR sneaking.

### Not

Use `not` to check the opposite:

```
if not (crouching)
run (scripts/fly.cj)
```

You can combine `anif not`:

```
if (holding)
anif not (sneaking)
run (scripts/run_fast.cj)
```

### Math in Conditions

You can use math inside conditions:

```
if (health < max_health / 2)
run (scripts/low_health_warning.cj)

if (distance #s target < range)
run (scripts/attack.cj, target)
```

---

## Loops

A `loop` repeats something a set number of times:

```
loop (5)
run (scripts/spawn_enemy.cj)
end
```

A `loop` with no number runs **every game tick** (20 times per second):

```
loop
if (#p.is_flying)
run (scripts/flight_particle.cj)
end
```

### Stopping a Loop Early

Use `stop` to exit a loop before it finishes:

```
loop (100)
run (scripts/spawn.cj)
if (count > 10)
stop
end
```

---

## Running Scripts

Use `run` to call another script:

```
run (scripts/fireball.cj)
```

### Passing Values Into a Script

You can send variables along with the script:

```
run (scripts/wave_spawner.cj, mob_type)
```

In `wave_spawner.cj`, you receive it at the top:

```
carbonjava

input (mob_type : string)

@ Now you can use mob_type in this script
add mob
name (wave_enemy)
copy (mob_type)
health (20)
end
```

### Running Two Scripts at the Same Time

```
run (scripts/explosion.cj) and (scripts/sound.cj)   @ These fire together
run (scripts/cleanup.cj)                              @ This fires after both finish
```

---

## Full Example

```
carbonjava

@ Track clicks and give an effect every 10 clicks

#p.clicks = #p.clicks + 1

if (#p.clicks >= 10)
run (scripts/reward_effect.cj)
#p.clicks = 0          @ Reset the counter
```

---

## Writing to Another Player's Data

To write persistent data to a specific player who isn't the current player, use `#player(variable).field`:

```
@ Store the sender's name in the target player's data
#player(target).tpa_requester = #p.name
```

`target` must be a variable holding a player/entity.

---

## Built-in Read-Only Properties

These are built into Carbon — you can read them but cannot assign to them.

**On the current player (`#p.`):**
```
name = #p.name           @ username
hp = #p.health           @ current health
level = #p.xp_level      @ XP level
mode = #p.gamemode       @ survival, creative, etc.
```

**On self/mob (`#s.`):**
```
hp = #s.health
pos = #s.position
```

---

## Exiting a Script Early

Use `exit` to stop a script immediately:

```
if (#p.tpa_requester == "null")
run (scripts/no_request.cj)
exit

@ This only runs if the condition above was false
requester = find_player (#p.tpa_requester)
```

`exit` stops the whole script. `stop` only exits a `loop`.

---

## Finding a Player by Name

Use `find_player` to look up an online player from a stored name:

```
requester = find_player (#p.tpa_requester)

if (requester == null)
run (scripts/player_offline.cj)
exit
```

Returns `null` if the player is not online. Always check for `null` before using the result.
