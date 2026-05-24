# Carbon Lang Script — Variables, Types & Persistent Data

---

## Variables

Variables are declared by simply writing `name = value`. No keywords needed.
No type declarations needed. Types are inferred automatically by the compiler.

```
target = nearest_entity
range = 10
count = 0
speed = 1.5
mob_name = "shadow_zombie"
```

---

## Type Inference

The compiler reads the right side of `=` and automatically assigns the correct
Java/Minecraft type. The user never needs to know about this.

| What you write | Inferred type | Java type |
|----------------|--------------|-----------|
| `range = 10` | Integer | `int` |
| `speed = 1.5` | Decimal | `float` / `double` |
| `name = "shadow_zombie"` | String | `String` |
| `target = nearest_entity` | Entity selector | `entity` / `selector` |
| `flag = true` | Boolean | `boolean` |

---

## Using Variables

Variables can be used anywhere a value is expected.

```
if (distance #s target < range)
run (attack.cj, target)

health_threshold = max_health / 2
if (health < health_threshold)
run (flee.cj)
```

---

## Math

Full math expressions are supported in both assignments and conditions.

```
count = count + 1
speed = speed * 1.5
damage = base_damage + bonus
if (health < max_health / 2)
if (count * 2 > limit)
```

### Operators
| Operator | Meaning |
|----------|---------|
| `+` | Add |
| `-` | Subtract |
| `*` | Multiply |
| `/` | Divide |
| `%` | Remainder (modulo) |

### Shorthand Assignment
```
count += 1      @ same as count = count + 1
count -= 1
count *= 2
count /= 2
```

---

## Passing Variables to Scripts

Variables can be passed into other scripts via `run`:

```
run (wave_spawner.cj, mob_type)
run (attack.cj, target, damage)
```

The receiving script declares its inputs at the top:

```
carbonjava

input (mob_type : string)
input (target : selector)
input (damage : int)
```

---

## Persistent Data

Regular variables are lost when the script ends. Persistent data uses dot
notation with a scope prefix and is saved between sessions.

### Scope Prefixes

| Prefix | Scope | Example |
|--------|-------|---------|
| `#p.` | Per player | `#p.clicks` |
| `#a.` | All players | `#a.total_kills` |
| `#w.` | Per world | `#w.boss_kills` |
| `#mod.` | Global to the mod | `#mod.total_spawns` |
| `#e.` | Per entity | `#e.hits` |
| `#b.` | Per block | `#b.times_stepped` |

### Usage

```
#p.clicks += 1
#w.boss_kills = #w.boss_kills + 1

if (#p.clicks > 100)
run (reward.cj)
```

Persistent data supports the same math operators as regular variables.
