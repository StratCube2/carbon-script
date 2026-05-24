# Your First Mod

This guide walks you through making a simple mod that adds one item to the game. By the end you will have a working Ruby gem item with a crafting recipe.

---

## Step 1 — Set Up Your Mod Folder

Download the **Carbon Mod Template** (a blank mod zip from the Carbon website). Unzip it. You will see this:

```
my_mod/
├── carbon.mod.json
├── resources/
│   ├── textures/
│   ├── models/
│   └── sounds/
└── src/
    ├── definitions/
    ├── commands/
    ├── recipes/
    └── scripts/
```

Don't worry about all the folders yet. You only need a few for this guide.

---

## Step 2 — Fill In Your Mod Info

Open `carbon.mod.json` in any text editor (Notepad is fine).

```json
{
  "id": "ruby_mod",
  "name": "Ruby Mod",
  "version": "1.0.0",
  "authors": ["your_name_here"],
  "description": "Adds rubies to the game!",
  "minecraft_version": "1.20.1",
  "type": "carbon",
  "dependencies": []
}
```

Save it. This is your mod's ID card.

> The `id` must be lowercase with no spaces. Use underscores instead.

---

## Step 3 — Add Your Item

Open `src/definitions/items.cj` and type this:

```
carbonjava

add item
name (ruby)
type ingredient
texture (resources/textures/ruby.png)
end
```

What each line means:

| Line | What it does |
|------|-------------|
| `carbonjava` | Marks this as a Carbon Java file. Every .cj file starts with this. |
| `add item` | You are defining a new item. |
| `name (ruby)` | The item's ID. Shows in game as "Ruby". |
| `type ingredient` | This item is used for crafting. |
| `texture (...)` | Path to the item's image. |
| `end` | Closes the block. Always required. |

---

## Step 4 — Add a Texture

Put your `ruby.png` into `resources/textures/`. It should be 16x16 pixels like a normal Minecraft item texture.

---

## Step 5 — Add a Recipe

Create `src/recipes/ruby.cj`:

```
recipe
1. null
2. null
3. null
4. null
5. minecraft:diamond
6. null
7. null
8. null
9. null
```

The grid positions are:
```
1 2 3
4 5 6
7 8 9
```

So this puts a diamond in the center slot. Now link the recipe in your item:

```
carbonjava

add item
name (ruby)
type ingredient
texture (resources/textures/ruby.png)
recipe (recipes/ruby.cj)
end
```

---

## Step 6 — Pack and Load

Select everything inside `my_mod/`, zip it, rename to `ruby_mod.zip`, and drop it in your Carbon mods folder. Launch the game. You can now craft your ruby!

---

## What's Next?

- Add more items → <a href="../language/03_items/">language/03_items/</a>
- Make a more complex recipe → <a href="../language/06_recipes/">language/06_recipes/</a>
- Add a mob → <a href="../language/05_mobs/">language/05_mobs/</a>
