# Carbon Java Script — Compiler & Runtime Requirements

This document is intended for the developer building the Carbon Lib mod and
the CJ compiler. It describes everything the compiler and runtime must do.

---

## Overview

The Carbon Java Script compiler is built into Carbon Lib (a Fabric mod).
It runs entirely at mod-load time — modders never run a compiler themselves.
The compiler takes `.cj` source files and produces JVM bytecode via ASM.

---

## 1. Mod Zip Loader

On startup (and on hot-reload), Carbon Lib must:

1. Scan the mods directory for `.zip` files.
2. For each zip, read and validate `carbon.mod.json` at the root.
3. Unzip the mod into a temp directory.
4. Resolve and fetch any missing dependencies from Modrinth.
5. Run the Trust Audit (check mod against whitelist for XP eligibility).
6. Load resources from `resources/` into the game's resource pipeline.
7. Compile and load all `.cj` files from `src/`.
8. Register all items, blocks, mobs, commands, effects defined in definitions/.

### Load Order
1. `src/definitions/` — items, blocks, mobs, potions (in any order within folder)
2. `src/commands/` — command registration
3. `src/recipes/` — recipe registration
4. `src/scripts/` — action and behaviour scripts (loaded on demand, not all upfront)

---

## 2. Lexer

The lexer tokenizes raw `.cj` text into a stream of tokens.

### Token Types
- `HEADER` — the word `carbonjava` on line 1
- `KEYWORD` — `add`, `end`, `on`, `run`, `if`, `anif`, `else`, `not`, `or`,
  `loop`, `stop`, `input`, `modifier`, `action`, `fire`, `effect`,
  `allotrope`, `copy`, `trigger`, `execute`, `recipe`
- `IDENTIFIER` — any name like `ruby_sword`, `weak_skeleton`
- `TYPE_NAME` — known block types: `item`, `mob`, `block`, `command`,
  `behaviour`, `action`, `effect`, `potion`
- `PROPERTY` — known property names: `name`, `health`, `damage`, `type`, etc.
- `VALUE` — content inside `( )` parentheses
- `TARGET` — `#p`, `#s`, `#a`, `#w`, `#mod`, `#e`, `#b` and dot-access
- `SELECTOR` — `nearest_entity`, `target_entity`
- `NUMBER` — integer or float literal
- `STRING` — quoted string literal
- `BOOL` — `true` or `false`
- `OPERATOR` — `+`, `-`, `*`, `/`, `%`, `=`, `+=`, `-=`, `*=`, `/=`,
  `<`, `>`, `<=`, `>=`
- `COMMENT` — everything after `@` on a line (discarded)
- `NEWLINE` — significant for block separation
- `EOF`

---

## 3. Parser

The parser builds an Abstract Syntax Tree (AST) from the token stream.

### Top-Level Structure
```
Program
  ├── Header ("carbonjava")
  └── Statement[]
        ├── AddBlock
        ├── Assignment
        ├── IfStatement
        ├── LoopStatement
        ├── RunStatement
        ├── OnBlock
        ├── AllotropeBlock
        └── InputDeclaration
```

### Key Parsing Rules
- First line must be `carbonjava` or the file is rejected.
- `add [type]` opens a block. All indented or following lines until `end`
  belong to that block.
- `on (event)` blocks end at the next `on`, the next `add`, or `end`.
- Empty lines separate `on` blocks within an `add` block.
- `if` / `anif` / `else` do not use `end` — they end at the next statement.
- `loop` uses `end` to close.
- `allotrope` blocks contain raw Java between `#at` and `#end` — this section
  must be passed through to ASM without further parsing.
- Recipe files do not start with `carbonjava` and use their own simple parser.

---

## 4. Type Inference

The compiler infers variable types from their assigned values:

| Value pattern | Inferred type |
|---------------|--------------|
| Integer literal (`10`) | `int` |
| Float literal (`1.5`) | `float` |
| Quoted string (`"text"`) | `String` |
| `true` / `false` | `boolean` |
| `nearest_entity`, `target_entity`, `#p`, `#s` | `EntitySelector` |

Types are tracked in a symbol table per file scope. Persistent data (`#p.`, `#w.`, etc.)
is stored in a Carbon-managed persistent store and typed on first assignment.

---

## 5. ASM Code Generation

The compiler uses ASM to generate JVM bytecode from the AST.

### Each `add` block compiles to:
- A registered Fabric object (Item, Block, EntityType, etc.)
- Event listener registrations for each `on` block
- Behaviour/AI goal registrations for mob `add behaviour` blocks

### Each script file compiles to:
- A Java class with a main `execute()` method
- Input parameters become method arguments
- Variables become local variables or persistent store reads/writes
- `run` calls become method invocations on compiled script classes
- `loop` compiles to a while loop or a scheduled tick handler
- `if`/`anif`/`else` compile to standard Java conditionals

### Allotrope blocks:
- Raw Java between `#at` and `#end` is injected via ASM at the specified
  injection point in the target class.

---

## 6. Error Reporting

Errors must be clear, friendly, and actionable. Format:

```
[Carbon] Error in src/definitions/items.cj line 6:
  "durabilty" is not recognised. Did you mean "durability"?
```

### Error Types
- **Unknown keyword** — suggest closest match (Levenshtein distance ≤ 2)
- **Missing `end`** — tell the user which `add` block was not closed
- **Wrong value type** — e.g. `damage ("hello")` — damage expects a number
- **Missing required property** — e.g. `add item` with no `name`
- **File not found** — `run (missing.cj)` — file does not exist in src/
- **Wrong classloader target** — Allotrope mixin in wrong parent/child array

On any error:
- Print the error with file, line number, and suggestion.
- Do not load the mod.
- Continue loading other mods unaffected.

---

## 7. Sandboxing

Scripts must not be able to:
- Call arbitrary Java classes outside the Carbon API
- Access `System.exit`, `ProcessBuilder`, file I/O outside the mod zip
- Access Parent classloader secrets (auth tokens, IPC keys)
- Load or instantiate arbitrary classes via reflection

The ASM code generator must whitelist which Java packages compiled scripts
can reference. Any attempt to reference a non-whitelisted class is a compile
error.

---

## 8. Hot Reload

When a mod zip is modified:
1. Unregister all items/blocks/mobs/events from the previous version.
2. Discard the old compiled classes.
3. Re-run the full load pipeline on the new zip.
4. Re-register everything.
5. Report success or error to the Carbon Loader terminal and HUD.

---

## 9. Resource Pipeline

Files in `resources/` are handled as follows:

| Type | Handling |
|------|---------|
| `.png` | Registered as Minecraft texture (item, block, entity) |
| `.glb` | Loaded as 3D model, registered for the item/mob/block that references it |
| `.obj` | Same as `.glb` |
| `.mp3` / `.ogg` | Registered as a Minecraft sound event |

Resources are passed to the client. Carbon Lib handles the Fabric resource
pack injection automatically — the modder does not need to write any
resource pack JSON.
