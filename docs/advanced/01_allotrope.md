# Allotrope Mixins

> **Warning:** This section requires knowledge of Java. It is intended for advanced users who want to modify Carbon Loader itself, not for general Minecraft modding. If you are new to modding, you do not need this.

---

## What is Allotrope?

Allotrope is the system that lets you inject Java code directly into Carbon Loader's internals. It works like Fabric's Mixin system — you target a specific Java class and method, and insert code at a specific point.

Use cases:
- Adding new UI elements to the Carbon Loader terminal window
- Modifying how the Child (Minecraft) session starts
- Intercepting the handoff between the Parent and Child processes

---

## The Allotrope Script

An Allotrope script is a `.cj` file that contains raw Java code wrapped in Carbon syntax.

```
carbonjava

allotrope (#FabricMainWindow.java)
#at HEAD
  @ Your Java code goes here.
  @ Example: System.out.println("Carbon Loader started!");
#end

end
```

### Structure Breakdown

| Part | What it does |
|------|-------------|
| `allotrope (#ClassName.java)` | The Java class you are targeting |
| `#at POINT` | Where in the method to inject |
| Java code between `#at` and `#end` | The code that gets injected |
| `#end` | Closes the injection |
| `end` | Closes the `allotrope` block |

---

## Multiple Injections in One File

You can target multiple classes in a single `.cj` file:

```
carbonjava

allotrope (#FabricMainWindow.java)
#at HEAD
  @ code here
#end

allotrope (#KnotClassLoader.java)
#at TAIL
  @ code here
#end

end
```

---

## Injection Points

These control exactly where inside a method your code is inserted.

| Point | Where it injects |
|-------|----------------|
| `HEAD` | At the very top of the method, before anything else runs |
| `TAIL` | At the bottom of the method, just before it returns |
| `RETURN` | At every `return` statement in the method |
| `INVOKE` | Before or after a specific method call inside this method |
| `FIELD` | When a specific field is read or written |
| `NEW` | When a `new` object is being created inside this method |
| `JUMP` | At the start of a branch (an `if` or `else` in the Java code) |
| `THROW` | Just before an exception is thrown |
| `SHIFT` | Moves the injection point forward or back by a number of instructions |
| `SLICE` | Injects into a specific range of instructions |
| `ON` | Wraps the entire method — runs your code both before and after |

### Using `INVOKE` (Targeting a Specific Method Call)

```
allotrope (#GameRenderer.java)
#at INVOKE (net.minecraft.client.render.WorldRenderer.render)
  @ Runs just before WorldRenderer.render() is called inside GameRenderer
#end
```

---

## Setting Up `carbon.mod.json` for Allotrope

Your mod's `carbon.mod.json` needs to declare which scripts go to which side of the loader.

### Child Only (Most Common)

Targets Minecraft's own code:

```json
{
  "id": "my_allotrope_mod",
  "name": "My Allotrope Mod",
  "version": "1.0.0",
  "authors": ["your_name"],
  "minecraft_version": "1.20.1",
  "type": "allotrope",
  "allotrope": {
    "inject_parent": false,
    "inject_child": true,
    "mixins": ["src/guifix.cj"]
  }
}
```

A flat list in `"mixins"` defaults to child.

### Parent Only

Targets the Carbon Loader terminal/window:

```json
"allotrope": {
  "inject_parent": true,
  "inject_child": false,
  "mixins": {
    "parent": ["src/stubuiaddon.cj"]
  }
}
```

### Both Sides

```json
"allotrope": {
  "inject_parent": true,
  "inject_child": true,
  "mixins": {
    "parent": ["src/stubuiaddon.cj"],
    "child": ["src/guifix.cj"]
  }
}
```

If a script needs to run on both sides (for example, something that handles the Parent-Child handoff), put it in both arrays:

```json
"mixins": {
  "parent": ["src/handoff_hook.cj"],
  "child": ["src/handoff_hook.cj"]
}
```

### Classloader Validation

Carbon checks that every file in `"parent"` only targets Parent-side classes, and every file in `"child"` only targets Child-side (Minecraft) classes.

If a file targets the wrong side:
```
[Carbon] Error loading allotrope mod "my_mod":
  "src/guifix.cj" targets "#FabricMainWindow.java" which is a child-only class,
  but this file is listed under "parent" mixins.
```

The mod will not load. Move the file to the correct array.

---

## Full Example — Adding a Log Line to the Loader Window

**`src/stubuiaddon.cj`**
```
carbonjava

allotrope (#StubUi.java)
#at HEAD
  this.logPanel.appendText("[MyMod] Allotrope mod loaded successfully.");
#end

end
```

**`carbon.mod.json`**
```json
{
  "id": "stub_ui_addon",
  "name": "Stub UI Addon",
  "version": "1.0.0",
  "authors": ["your_name"],
  "minecraft_version": "1.20.1",
  "type": "allotrope",
  "allotrope": {
    "inject_parent": true,
    "inject_child": false,
    "mixins": {
      "parent": ["src/stubuiaddon.cj"]
    }
  }
}
```
