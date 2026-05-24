# Carbon Lang Script — Core Syntax

## File Header

Every `.cj` file must begin with `carbonjava` on the very first line.
This identifies the file as a Carbon Lang Script file.

```
carbonjava

@ everything after this is your script
```

---

## Comments

Comments use `@`. They can appear at the end of any line or on their own line.
They are completely ignored by the compiler.

```
@ This is a full line comment

name (ruby_sword)   @ This is an inline comment
```

---

## Block Structure

All definitions use `add [type]` to open a block and `end` to close it.
A single file can contain multiple `add` blocks.

```
carbonjava

add item
name (ruby_sword)
type weapon
damage (10)
end

add item
name (ruby_pickaxe)
type tool
subtype pickaxe
end
```

---

## Values

Values are always written in parentheses.

```
name (ruby_sword)
damage (10)
health (20)
model (resources/models/ruby_sword.glb)
```

Underscores are used in code. In-game, they become spaces automatically.
`ruby_sword` displays as **Ruby Sword** in game.

---

## All `add` Block Types

| Block Type | Purpose |
|------------|---------|
| `add item` | Define a custom item |
| `add block` | Define a custom block |
| `add mob` | Define a custom mob |
| `add command` | Define a custom slash command |
| `add potion` | Define a custom potion item |
| `add effect` | Define a custom status effect |
| `add behaviour` | Define mob AI (used inside `add mob`) |
| `add action` | Define a reusable action script |
| `allotrope` | Inject Java into Carbon Loader (advanced) |

---

## Properties use `property` keyword for blocks

For blocks, some fields use the `property` keyword:

```
add block
name (ruby_cluster)
property hardness (1)
property explosion_resistance (1)
property transparency (0.9)
tool (none)
texture (resources/textures/ruby_cluster.png)
end
```

For items and mobs, properties are written directly without the `property` keyword:

```
add item
name (ruby_sword)
damage (10)
durability (120)
end
```
