# Commands

Custom slash commands live in `src/commands/`. Each command is its own `.cj` file.

---

## Basic Structure

```
carbonjava

add command
name (command_name)
permission (all)
execute
run (scripts/my_script.cj)
end
```

---

## Permission Levels

| Permission | Who can use it |
|------------|---------------|
| `all` | Every player |
| `op` | Only server operators |

---

## Arguments

You can add arguments that players type after the command. Arguments are typed — this gives players autocomplete suggestions in game.

```
add command
name (spawn_wave)
permission (op)
argument (mob_type : string)
execute
run (scripts/wave_spawner.cj, mob_type)
end
```

A player would type: `/spawn_wave zombie`

### Argument Types

| Type | What it accepts | Example input |
|------|----------------|---------------|
| `string` | Any word | `zombie` |
| `integer` | A whole number | `5` |
| `float` | A decimal number | `1.5` |
| `selector` | A player or entity target | `@a`, `@p`, `PlayerName` |
| `boolean` | true or false | `true` |

---

## Multiple Arguments

```
add command
name (sethealth)
permission (op)
argument (target : selector)
argument (amount : integer)
execute
run (scripts/set_health.cj, target, amount)
end
```

Player types: `/sethealth @p 20`

---

## Receiving Arguments in a Script

In the script that the command calls, declare the inputs at the top:

```
carbonjava

input (mob_type : string)

@ Now you can use mob_type anywhere in this script
add mob
name (wave_enemy)
copy (mob_type)
health (20)
end
```

---

## Full Example

**`src/commands/heal.cj`**
```
carbonjava

add command
name (heal)
permission (all)
argument (target : selector)
execute
run (scripts/do_heal.cj, target)
end
```

**`src/scripts/do_heal.cj`**
```
carbonjava

input (target : selector)

add action
effect (target) (minecraft:instant_health 1 1)
end
```

---

## Running Minecraft Commands from Scripts

Inside any script, you can run a Minecraft command directly using `cmd`:

```
add action
cmd (/msg target "Someone wants to teleport to you!")
end
```

This is different from `run`, which calls another `.cj` script.
Use `run` for Carbon logic, `cmd` for Minecraft commands.
