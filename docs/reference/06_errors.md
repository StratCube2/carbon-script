# Common Errors

When something goes wrong, Carbon shows you a message like this:

```
[Carbon] Error in src/definitions/items.cj line 6:
  "durabilty" is not recognised. Did you mean "durability"?
```

This page lists the most common errors and how to fix them.

---

## Missing `carbonjava` Header

**Error:**
```
[Carbon] Error in src/definitions/items.cj line 1:
  File does not start with "carbonjava". This file will not be loaded.
```

**Fix:** Make sure the very first line of your file is exactly:
```
carbonjava
```
No spaces before it, no capital letters, nothing else on that line.

---

## Typo in a Property Name

**Error:**
```
[Carbon] Error in src/definitions/items.cj line 8:
  "durabilty" is not recognised. Did you mean "durability"?
```

**Fix:** Check the spelling. Carbon will usually suggest the correct word. Look it up in the relevant reference file if you're unsure.

---

## Missing `end`

**Error:**
```
[Carbon] Error in src/definitions/items.cj line 14:
  Reached end of file but "add item" block was never closed. Did you forget "end"?
```

**Fix:** Every `add` block needs a matching `end`. Count your `add` and `end` keywords — they should be equal.

```
add item          ← opens block
name (ruby)
type ingredient
end               ← closes block. Required!
```

---

## Wrong File in `run`

**Error:**
```
[Carbon] Error in src/definitions/items.cj line 10:
  Could not find "scripts/firbal.cj". Check the file name and path.
```

**Fix:** Check that the file exists and that the path and spelling are correct. Paths start from inside `src/`.

```
run (scripts/fireball.cj)     ← correct
run (scripts/firbal.cj)       ← typo
run (fireball.cj)             ← missing folder name
```

---

## Wrong Classloader Target (Allotrope Only)

**Error:**
```
[Carbon] Error loading allotrope mod "my_mod":
  "src/guifix.cj" targets "#FabricMainWindow.java" which is a child-only class,
  but this file is listed under "parent" mixins. Move it to "child" in carbon.mod.json.
```

**Fix:** Check your `carbon.mod.json`. Make sure files targeting Minecraft classes are in `"child"` and files targeting the Carbon Loader window are in `"parent"`.

---

## Invalid Value for a Property

**Error:**
```
[Carbon] Error in src/definitions/items.cj line 7:
  "tier" expects wood, stone, iron, diamond, or netherite. Got "ruby".
```

**Fix:** Use one of the accepted values. Check the reference file for the full list of valid values for each property.

---

## Missing Required Property

**Error:**
```
[Carbon] Error in src/definitions/items.cj:
  "add item" block is missing required property "type".
```

**Fix:** Make sure you include all required properties. For items, `name` and `type` are always required.

---

## Nested `add` Without `end`

**Error:**
```
[Carbon] Error in src/definitions/mobs.cj line 18:
  Found "add behaviour" but the previous "add drops" block was never closed.
```

**Fix:** When you nest blocks (like `add drops` inside `add mob`), each inner block needs its own `end` before the outer block's `end`.

```
add mob
  name (ruby_slime)

  add drops
    drop (ruby, 0.5, 1, 3)
  end              ← closes add drops

end                ← closes add mob
```

---

## Script Input Mismatch

**Error:**
```
[Carbon] Error in src/scripts/wave_spawner.cj line 3:
  This script expects input "mob_type" but it was called without any arguments.
```

**Fix:** When calling a script that declares `input (...)`, you must pass the arguments in `run`:

```
run (scripts/wave_spawner.cj, mob_type)    ← correct
run (scripts/wave_spawner.cj)              ← missing argument
```

---

## Invalid `carbon.mod.json`

**Error:**
```
[Carbon] Could not load mod: carbon.mod.json is missing required field "id".
```

**Fix:** Open `carbon.mod.json` and check all required fields are present:
`id`, `name`, `version`, `authors`, `minecraft_version`, `type`

---

## Quick Checklist When Something Doesn't Work

1. Does every `.cj` file start with `carbonjava`?
2. Does every `add` have a matching `end`?
3. Are all file paths spelled correctly?
4. Are all property names spelled correctly?
5. Is `carbon.mod.json` valid and complete?
6. Are all required properties included (`name`, `type`, etc.)?
