# Carbon Java Script — Allotrope Mixin System

Allotrope is the advanced injection system built into Carbon Loader. It allows
mods to inject raw Java code into Carbon Loader's Parent or Child classloader.

**This is not required for normal Carbon mods.** A beginner making items, mobs,
and blocks does not need to touch Allotrope at all. It is intended for advanced
developers who need to modify the loader itself.

---

## How It Works

An Allotrope `.cj` file uses the `allotrope` block instead of `add`. Inside
the block, you target a Java class and specify an injection point using `#at`.
The raw Java code between `#at` and `#end` is injected at that point.

```
carbonjava

allotrope (#FabricMainWindow.java)
#at HEAD
// Your Java code here
int x = 10;
System.out.println("Injected at HEAD: " + x);
#end
end
```

---

## Multiple Injections in One File

A single `.cj` file can contain multiple `allotrope` blocks targeting different
Java classes.

```
carbonjava

allotrope (#FabricMainWindow.java)
#at HEAD
// Java code
#end

allotrope (#KnotClassLoader.java)
#at TAIL
// Java code
#end
end
```

---

## Injection Points

These match the standard Mixin injection points:

| Point | Description |
|-------|-------------|
| `HEAD` | At the very start of the method, before anything else. |
| `TAIL` | At the very end of the method, before it returns. |
| `RETURN` | At every return statement in the method. |
| `INVOKE` | Before or after a specific method call inside the target method. |
| `FIELD` | At a field access (read or write). |
| `NEW` | At object instantiation (`new SomeClass()`). |
| `JUMP` | At a branch instruction (if/else in bytecode). |
| `THROW` | Before a `throw` statement. |

---

## Parent vs Child Routing

Allotrope files are routed in `carbon.mod.json`. A file must be in the correct
array or Carbon will throw an error and refuse to load the mod.

| Target classes | Which array |
|----------------|-------------|
| `ParentLoader`, `StubUi`, Carbon Loader internals | `"parent"` |
| `FabricMainWindow`, `KnotClassLoader`, Minecraft classes | `"child"` |
| Handoff/bridge classes used by both | Both `"parent"` and `"child"` |

### Example: Parent only
```json
"allotrope": {
  "inject_parent": true,
  "inject_child": false,
  "mixins": {
    "parent": ["src/stubuiaddon.cj"]
  }
}
```

### Example: Child only (flat list shorthand)
```json
"allotrope": {
  "inject_parent": false,
  "inject_child": true,
  "mixins": ["src/guifix.cj"]
}
```

### Example: Both
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

### Example: Same file in both (handoff class)
```json
"allotrope": {
  "inject_parent": true,
  "inject_child": true,
  "mixins": {
    "parent": ["src/handoff_patch.cj"],
    "child": ["src/handoff_patch.cj"]
  }
}
```

---

## Security

- Carbon Loader validates that the target class matches the assigned classloader.
- If a `parent` mixin targets a child-only class, the mod is rejected at load
  time with an error.
- The Child (Minecraft/Knot) classloader never has access to the Auth Token,
  database keys, or other Parent secrets — Allotrope cannot bypass this.
- The Mod Auditor (Phase 14 of Carbon Loader) scans Allotrope files for
  dangerous signatures (`System.exit`, `ProcessBuilder`) before they are loaded.

---

## Who Should Use Allotrope?

- Developers building Carbon Loader extensions or Allotrope API modules.
- Fabric mod authors who want to integrate with Carbon Loader's Parent UI or CLI.
- Advanced modders adding features to StubUi or the IPC bridge.

If you are just making items, mobs, blocks, or commands — you do not need this.
