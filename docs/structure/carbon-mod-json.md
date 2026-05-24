# carbon.mod.json — Full Specification

`carbon.mod.json` is the metadata file for every mod in the Carbon ecosystem.
It must be placed at the root of the mod zip. It is also used by Fabric mods
that hook into Allotrope, making it the single standardized format across the
entire Carbon Loader ecosystem.

---

## Minimal Example (Carbon Mod)

```json
{
  "id": "ruby_mod",
  "name": "Ruby Mod",
  "version": "1.0.0",
  "authors": ["zleo"],
  "description": "Adds ruby items and a weak skeleton mob.",
  "icon": "resources/textures/icon.png",
  "minecraft_version": "1.21",
  "type": "carbon",
  "dependencies": []
}
```

---

## Full Example (Allotrope Mod — both parent and child)

```json
{
  "id": "my_allotrope_mod",
  "name": "My Allotrope Mod",
  "version": "1.0.0",
  "authors": ["yourname"],
  "description": "Injects into both the Parent and Child loaders.",
  "icon": "resources/textures/icon.png",
  "minecraft_version": "1.21",
  "type": "allotrope",
  "dependencies": [],
  "allotrope": {
    "inject_parent": true,
    "inject_child": true,
    "mixins": {
      "parent": [
        "src/stubuiaddon.cj"
      ],
      "child": [
        "src/guifix.cj"
      ]
    }
  }
}
```

---

## Allotrope Mod — Child Only (flat list shorthand)

If only injecting into the child, `mixins` can be a flat list.
Carbon defaults to child injection when no parent/child split is specified.

```json
{
  "id": "guifix",
  "name": "GUI Fix",
  "version": "1.0.0",
  "authors": ["yourname"],
  "minecraft_version": "1.21",
  "type": "allotrope",
  "allotrope": {
    "inject_parent": false,
    "inject_child": true,
    "mixins": [
      "src/guifix.cj"
    ]
  }
}
```

---

## Field Reference

| Field | Required | Description |
|-------|----------|-------------|
| `id` | Yes | Unique mod ID. Lowercase, underscores only. Used as namespace. |
| `name` | Yes | Display name shown in mod list. |
| `version` | Yes | Semantic version string, e.g. `"1.0.0"` |
| `authors` | Yes | Array of author names. |
| `description` | No | Short description of the mod. |
| `icon` | No | Path to icon image inside the zip. |
| `minecraft_version` | Yes | Target Minecraft version, e.g. `"1.21"` |
| `type` | Yes | See Type Values below. |
| `dependencies` | No | Array of mod IDs this mod requires. |
| `allotrope` | Only for allotrope type | Mixin injection config. |

---

## Type Values

| Type | Description |
|------|-------------|
| `"carbon"` | A standard Carbon Lang Script mod. |
| `"allotrope"` | Injects Java into Carbon Loader via mixins. |
| `"fabric"` | A Fabric mod that uses carbon.mod.json for Carbon ecosystem compatibility. |

---

## `allotrope` Block Fields

| Field | Description |
|-------|-------------|
| `inject_parent` | `true` if this mod injects into the ParentLoader. |
| `inject_child` | `true` if this mod injects into the ChildSession/Knot. |
| `mixins` | Either a flat array (defaults to child) or an object with `"parent"` and `"child"` arrays. |

---

## Mixin Routing Rules

- Flat array → all files routed to child.
- Object with `"parent"` and `"child"` keys → explicitly routed.
- A file can appear in both `"parent"` and `"child"` if it targets a shared handoff class.
- If a file targets the wrong classloader's classes, Carbon throws an error at
  load time and the mod does not load.

---

## Dependencies

```json
"dependencies": [
  "carbon_lib",
  "sodium",
  "my_other_mod"
]
```

Dependencies are resolved automatically. If a listed dependency is missing,
Carbon auto-fetches it from Modrinth before launching.
The key `"carbonloader"` is also accepted as a dependency identifier.
