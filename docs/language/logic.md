# Carbon Java Script — Logic, Conditions & Loops

---

## `if` Statement

```
if (condition)
run (script.cj)
```

---

## `else`

```
if (holding)
run (ruby_effect.cj)
else
run (normal.cj)
```

---

## `anif` — And If

Used when you need multiple conditions to all be true.
Because there is no indentation in Carbon Java, `anif` replaces chained `and` conditions.

```
if (holding)
anif (sneaking)
run (fireball.cj)
else
run (effect.cj)
```

---

## `or` — Either Condition

Used when any one of the conditions being true is enough.

```
if (holding) or (crouching)
run (fireball.cj)
```

---

## `not` — Negation

`not` negates any condition. Works with `if` and `anif`.

```
if not (crouching)
run (levitation.cj)

anif not (sneaking)
run (other.cj)
```

`not` is equivalent to `!=` — the condition must be false for the block to run.

---

## Conditions Reference

| Condition | Meaning |
|-----------|---------|
| `holding` | Player is holding this item |
| `sneaking` | Player is sneaking |
| `crouching` | Same as sneaking |
| `sprinting` | Player is sprinting |
| `health < 5` | Entity health comparison |
| `distance #s target < range` | Distance between two entities |
| `count > 10` | Variable comparison |
| `health < max_health / 2` | Expression in condition |

Any variable or math expression is valid in a condition.

---

## `loop` — Repeating a Fixed Number of Times

```
loop (5)
run (spawn.cj)
end
```

Runs `spawn.cj` exactly 5 times.

---

## `loop` — Every Tick

A `loop` with no number runs every game tick (20 times per second).

```
loop
run (flight.cj)
end
```

---

## `stop` — Exiting a Loop Early

```
loop
run (spawn.cj)
if (count > 10)
stop
end
```

`stop` immediately exits the current loop.

---

## Full Logic Example

```
carbonjava

count = 0

loop
  if (health < 5)
  anif not (crouching)
  run (heal.cj)

  count = count + 1
  if (count > 20)
  stop
end
```
