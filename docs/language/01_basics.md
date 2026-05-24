# The Basics of Carbon Lang

Carbon Lang is designed to be as simple as possible. This page explains how a `.cj` file works from top to bottom.

---

## The File Header

Every single `.cj` file must start with this on the very first line:

```
carbonjava
```

That's it. No brackets, no semicolons. Just that word. It tells Carbon Loader "this is a Carbon Lang file."

If you forget it, the file won't load.

---

## Comments

A comment is a note you leave for yourself. The game completely ignores it. Comments start with `@`.

```
carbonjava

@ This is a comment. The game ignores this line entirely.

add item
name (ruby)      @ You can put a comment at the end of a line too.
type ingredient
end
```

Use comments to remind yourself what things do, especially when you come back to a script weeks later.

---

## Blocks

Almost everything in Carbon Lang is written inside a **block**. A block starts with `add` and ends with `end`.

```
add item
  ... stuff goes here ...
end
```

Think of a block like a box. Everything inside the box belongs to that item (or mob, or block, etc.).

You can have as many blocks in one file as you want:

```
carbonjava

add item
name (ruby)
type ingredient
end

add item
name (ruby_sword)
type weapon
tier (diamond)
end
```

---

## Properties

Inside a block, you set properties using this pattern:

```
property_name (value)
```

For example:
```
name (ruby_sword)
damage (10)
durability (120)
```

Simple. The property name on the left, the value in parentheses on the right.

---

## Referencing Other Files

You can point to another `.cj` file using its path:

```
recipe (recipes/ruby_sword.cj)
run (scripts/fireball.cj)
```

The path starts from inside the `src/` folder.

---

## Indentation

Carbon Lang does **not** require indentation. These two blocks are identical to the compiler:

```
add item
name (ruby)
type ingredient
end
```

```
add item
  name (ruby)
  type ingredient
end
```

However, indenting makes your code much easier to read. It is strongly recommended, especially when you have blocks inside blocks (like `add drops` inside `add mob`).

---

## Quick Rules Checklist

- Every file starts with `carbonjava`
- Every `add` needs a matching `end`
- Comments use `@`
- Values go in parentheses `(like this)`
- Underscores instead of spaces in names: `ruby_sword` not `ruby sword`
- File paths start from inside `src/`
