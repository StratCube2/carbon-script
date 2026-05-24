# Carbon Java — Formal Language Specification

This document is the technical reference for the Carbon Java compiler and runtime. It defines every keyword, grammar rule, and behaviour of the language. This is intended for the developers building the compiler and Carbon Lib mod — not for end users.

---

## 1. File Format

- Extension: `.cj`
- Encoding: UTF-8
- Every file must begin with the token `carbonjava` on line 1 with no leading whitespace.
- Lines are newline-separated (`\n` or `\r\n`).
- Indentation is optional and ignored by the compiler.

---

## 2. Tokens

| Token | Pattern | Notes |
|-------|---------|-------|
| HEADER | `carbonjava` | Must be line 1 |
| COMMENT | `@ ...` to end of line | Stripped at lex time |
| KEYWORD | `add`, `end`, `on`, `run`, `if`, `anif`, `else`, `not`, `or`, `loop`, `stop`, `input`, `modifier`, `trigger`, `action`, `spawn`, `fire`, `effect`, `damage`, `playsound`, `teleport`, `recipe`, `drop`, `copy`, `execute`, `argument` | Reserved |
| BLOCK_TYPE | `item`, `mob`, `block`, `command`, `effect`, `action`, `behaviour`, `drops` | Used after `add` |
| IDENTIFIER | `[a-z_][a-z0-9_]*` | Mod IDs, variable names, property names |
| NS_IDENTIFIER | `[a-z_]+:[a-z_]+` | Namespaced IDs, e.g. `minecraft:zombie` |
| SELECTOR | `#p`, `#s`, `#a`, `#e`, `#w`, `#mod`, `#b` followed optionally by `.identifier` | Persistent data / targets |
| STRING | `"..."` | Text values |
| INTEGER | `[0-9]+` | Whole numbers |
| FLOAT | `[0-9]+\.[0-9]+` | Decimal numbers |
| BOOLEAN | `true` or `false` | |
| LPAREN | `(` | |
| RPAREN | `)` | |
| EQUALS | `=` | Assignment |
| AUGMENTED_ASSIGN | `+=`, `-=`, `*=`, `/=` | Compound assignment |
| OP | `+`, `-`, `*`, `/`, `<`, `>`, `<=`, `>=` | Math and comparison |
| ALLOTROPE_OPEN | `#at` | Allotrope injection point |
| ALLOTROPE_CLOSE | `#end` | Closes injection |

---

## 3. Grammar (Simplified BNF)

```
file         := "carbonjava" NEWLINE statement*

statement    := add_block
              | on_block
              | if_block
              | loop_block
              | assignment
              | run_stmt
              | allotrope_block
              | input_decl

add_block    := "add" BLOCK_TYPE NEWLINE property* statement* "end"

property     := IDENTIFIER "(" value ")" NEWLINE
              | "property" IDENTIFIER "(" value ")" NEWLINE

on_block     := "on" "(" IDENTIFIER ")" NEWLINE (condition_line)* run_stmt NEWLINE

condition_line := ("if" | "anif") ["not"] "(" expr ")" ["or" "(" expr ")"] NEWLINE

run_stmt     := "run" "(" path ["," expr] ")" ["and" "(" path ")"]* NEWLINE

if_block     := condition_line+ run_stmt [NEWLINE "else" NEWLINE run_stmt]

loop_block   := "loop" ["(" INTEGER ")"] NEWLINE statement* "end"

assignment   := IDENTIFIER "=" expr NEWLINE
              | SELECTOR "=" expr NEWLINE
              | SELECTOR AUGMENTED_ASSIGN expr NEWLINE

expr         := value (OP value)*

value        := INTEGER | FLOAT | STRING | BOOLEAN | IDENTIFIER
              | NS_IDENTIFIER | SELECTOR | "nearest_entity"
              | "target_entity" | "null"

input_decl   := "input" "(" IDENTIFIER ":" type_name ")" NEWLINE

type_name    := "string" | "integer" | "float" | "boolean" | "selector"

path         := IDENTIFIER ("/" IDENTIFIER)* ".cj"

allotrope_block := "allotrope" "(" "#" IDENTIFIER ".java" ")" NEWLINE
                   ("#at" injection_point NEWLINE java_code "#end" NEWLINE)+
                   "end"

injection_point := "HEAD" | "TAIL" | "RETURN" | "INVOKE" ["(" NS_IDENTIFIER ")"]
                 | "FIELD" | "NEW" | "JUMP" | "THROW" | "SHIFT" | "SLICE" | "ON"
```

---

## 4. Type Inference Rules

The compiler infers types at assignment time. No type annotations are visible to the user.

| Value pattern | Inferred type | JVM type |
|---------------|--------------|----------|
| Integer literal | `int` | `int` |
| Float literal | `float` | `double` |
| String literal | `string` | `java.lang.String` |
| `nearest_entity`, `target_entity`, `#p`, `#s` | `entity` | `net.minecraft.entity.Entity` |
| `true` / `false` | `boolean` | `boolean` |
| Variable reference | Inherits from source variable | — |

---

## 5. Persistent Data Prefixes

| Prefix | Scope | Storage |
|--------|-------|---------|
| `#p.` | Per player | `PlayerEntity` NBT |
| `#a.` | All players | Broadcast to all `PlayerEntity` instances |
| `#e.` | Per entity | `Entity` NBT |
| `#b.` | Per block | `BlockEntity` NBT |
| `#w.` | Per world | `PersistentState` on `ServerWorld` |
| `#mod.` | Global to mod | `PersistentState` on server |

---

## 6. `run` Semantics

- `run (script.cj)` — Compiles and executes script. Next line waits for completion.
- `run (a.cj) and (b.cj)` — Executes both scripts concurrently via separate threads. The next `run` after this line waits for both to finish.
- Arguments passed via `run (script.cj, arg1, arg2)` are matched to `input` declarations in order.

---

## 7. `add` Block Types and Required Properties

| Block Type | Required Properties |
|------------|-------------------|
| `item` | `name`, `type` |
| `mob` | `name`, `health`, `damage`, `hostility` |
| `block` | `name` |
| `command` | `name`, `permission`, `execute` + at least one `run` |
| `effect` | `name`, `levels`, `run` |
| `action` | At least one action line |
| `behaviour` | At least one goal or `trigger` + `action` |
| `drops` | At least one `drop` line |

---

## 8. Error Reporting Format

All errors are reported to the Carbon Loader console and the in-game chat (if the game is running) in this format:

```
[Carbon] Error in <file_path> line <N>:
  <message>. <suggestion if available>
```

The compiler must:
1. Report the exact file path relative to the mod zip root.
2. Report the exact line number (1-indexed).
3. Provide a human-readable message in plain English.
4. Where the error is a typo, run Levenshtein distance against the valid keyword list and suggest the closest match if distance ≤ 2.
5. Continue parsing after errors where possible to collect all errors in one pass.
6. On any error, do NOT load the mod. Report all collected errors then abort.

---

## 9. Mod Zip Loading Sequence

1. Locate and validate `carbon.mod.json` — abort if missing or malformed.
2. Check `minecraft_version` matches current game version — warn if mismatch, abort if major version differs.
3. Check `dependencies` are all loaded — abort with list of missing deps if not.
4. Unzip to a temp directory under Carbon's working folder.
5. Load `resources/` — register textures, models, and sounds with Minecraft's resource system.
6. Scan and lex all `.cj` files under `src/`.
7. Parse and compile all `.cj` files via ASM. Collect all errors. If any errors exist, abort and report.
8. Register all compiled `add item`, `add block`, `add mob`, etc. with Fabric's registry.
9. Register all `add command` blocks with the server command dispatcher.
10. Attach all `on (event)` handlers to the relevant Fabric event buses.
11. Report successful load to the console: `[Carbon] Loaded mod: <name> v<version>`

---

## 10. Hot-Reload Sequence

When a mod zip is modified while the game is running:

1. Detect file change via watcher on the mods directory.
2. Deregister all items, blocks, mobs, commands, and event handlers from the current version.
3. Unload the compiled classes from the classloader.
4. Re-run the full loading sequence (steps 1–11 above).
5. Report result: `[Carbon] Hot-reloaded: <name>` or list of errors.

---

## 11. Sandboxing Rules

ASM-generated bytecode must not reference:
- `java.lang.System` (exit, in, out)
- `java.io.*` (file system access outside the mod zip's resource folder)
- `java.lang.reflect.*`
- `java.net.*`
- `sun.*` or `com.sun.*`
- Carbon Loader internal classes (`carbonloader.*`, `allotrope.*`) — except via the sanctioned `carbon.*` API

Any attempt to reference these should be caught at compile time and reported as an error.

---

## 12. Compilation Pipeline (Option B — Transpile to Java)

Carbon Java uses a transpile-then-compile approach. `.cj` files are never directly converted to bytecode. Instead they go through a pipeline: `.cj` → token list → AST → `.java` source → `.class` bytecode → live JVM class.

This makes the compiler easier to build, easier to debug, and produces readable intermediate output at every step.

---

### Step 1 — Lexer

**Class:** `CarbonLexer`
**Input:** Raw `.cj` file as a String
**Output:** `List<Token>`

The lexer reads the file character by character and emits a flat list of typed tokens. It strips comments (everything after `@` to end of line) before tokenising.

**Token enum — all valid types:**
```java
public enum TokenType {
    HEADER,           // "carbonjava"
    KEYWORD_ADD,      // "add"
    KEYWORD_END,      // "end"
    KEYWORD_ON,       // "on"
    KEYWORD_RUN,      // "run"
    KEYWORD_IF,       // "if"
    KEYWORD_ANIF,     // "anif"
    KEYWORD_ELSE,     // "else"
    KEYWORD_NOT,      // "not"
    KEYWORD_OR,       // "or"
    KEYWORD_AND,      // "and"
    KEYWORD_LOOP,     // "loop"
    KEYWORD_STOP,     // "stop"
    KEYWORD_INPUT,    // "input"
    KEYWORD_MODIFIER, // "modifier"
    KEYWORD_TRIGGER,  // "trigger"
    KEYWORD_ACTION,   // "action"
    KEYWORD_COPY,     // "copy"
    KEYWORD_EXECUTE,  // "execute"
    KEYWORD_ARGUMENT, // "argument"
    KEYWORD_FIRE,     // "fire"
    KEYWORD_EFFECT,   // "effect"
    KEYWORD_DAMAGE,   // "damage"
    KEYWORD_PLAYSOUND,// "playsound"
    KEYWORD_TELEPORT, // "teleport"
    KEYWORD_SPAWN,    // "spawn"
    KEYWORD_DROP,     // "drop"
    KEYWORD_RECIPE,   // "recipe"
    KEYWORD_PROPERTY, // "property"
    KEYWORD_TIER,     // "tier"
    KEYWORD_MATERIAL, // "material"
    BLOCK_TYPE,       // item, mob, block, command, effect, action, behaviour, drops
    IDENTIFIER,       // any lowercase_underscore word
    NS_IDENTIFIER,    // namespace:name
    SELECTOR,         // #p, #s, #a, #e, #w, #mod, #b
    SELECTOR_DOT,     // #p.clicks etc
    STRING,           // "quoted text"
    INTEGER,          // 123
    FLOAT,            // 1.5
    BOOLEAN,          // true / false
    LPAREN,           // (
    RPAREN,           // )
    EQUALS,           // =
    AUG_ASSIGN,       // +=  -=  *=  /=
    OPERATOR,         // + - * / < > <= >=
    COLON,            // :
    COMMA,            // ,
    NEWLINE,
    EOF
}
```

Each token carries: `TokenType type`, `String value`, `int line`.

**Lexer rules:**
- `carbonjava` on line 1 → `HEADER`. If not present, emit an error and stop.
- `@` → discard everything to end of line.
- Words are matched against the keyword list first, then BLOCK_TYPE list, then fall through to IDENTIFIER.
- `namespace:name` pattern → NS_IDENTIFIER.
- `#p`, `#s`, `#a`, `#e`, `#w`, `#b`, `#mod` → SELECTOR. If followed by `.identifier` → SELECTOR_DOT.
- Quoted strings → STRING (strip quotes from value).
- Digits with `.` → FLOAT. Digits only → INTEGER.
- `+=`, `-=`, `*=`, `/=` → AUG_ASSIGN (check two chars before single char operators).
- Blank lines are significant — emit NEWLINE for each line break.

---

### Step 2 — AST Node Classes

One Java class per node type. All extend a base `AstNode` that carries `int line`.

```java
abstract class AstNode { int line; }

class FileNode extends AstNode {
    List<AstNode> statements;
}

class AddBlockNode extends AstNode {
    String blockType;           // "item", "mob", "block", etc.
    List<AstNode> children;     // properties, on-blocks, nested add-blocks
}

class PropertyNode extends AstNode {
    String key;
    Object value;               // String, Integer, Double, Boolean
}

class OnEventNode extends AstNode {
    String event;
    List<ConditionNode> conditions;
    RunNode run;
}

class ConditionNode extends AstNode {
    boolean negated;            // true if "not"
    boolean isAnd;              // true if "anif", false if "if" or "or"
    ExprNode expr;
}

class RunNode extends AstNode {
    List<String> paths;         // supports "and" — multiple paths
    List<ExprNode> args;
}

class IfNode extends AstNode {
    List<ConditionNode> conditions;
    RunNode thenRun;
    RunNode elseRun;            // null if no else
}

class LoopNode extends AstNode {
    ExprNode count;             // null = infinite (every tick)
    List<AstNode> body;
}

class AssignmentNode extends AstNode {
    String target;              // variable name or selector
    String operator;            // "=", "+=", "-=", "*=", "/="
    ExprNode value;
}

class ExprNode extends AstNode {
    Object left;                // value or nested ExprNode
    String operator;            // null if no operation
    Object right;               // null if no operation
}

class InputDeclNode extends AstNode {
    String name;
    String type;                // "string", "integer", "float", "boolean", "selector"
}

class ActionLineNode extends AstNode {
    String action;              // "effect", "fire", "playsound", "spawn", "damage", "teleport"
    List<Object> args;
}

class ModifierNode extends AstNode {
    String target;
    String operation;           // "+", "-", "*", "/", or plain value
    Object value;
}

class TriggerNode extends AstNode {
    ExprNode condition;
    List<AstNode> actions;
}

class BehaviourNode extends AstNode {
    List<String> goals;         // "wander", "panic", etc.
    TriggerNode trigger;        // null if no trigger
}

class DropsNode extends AstNode {
    List<DropEntryNode> drops;
}

class DropEntryNode extends AstNode {
    String item;
    double chance;
    int min;
    int max;
}

class AllotropeNode extends AstNode {
    String targetClass;
    List<AllotropeInjectionNode> injections;
}

class AllotropeInjectionNode extends AstNode {
    String point;               // HEAD, TAIL, RETURN, etc.
    String pointArg;            // for INVOKE(method), FIELD(name), etc.
    String javaCode;            // raw Java between #at and #end
}
```

---

### Step 3 — Parser

**Class:** `CarbonParser`
**Input:** `List<Token>`
**Output:** `FileNode`

The parser is a recursive descent parser. It reads tokens one at a time and builds the AST.

**Key parsing rules:**

- Consume `HEADER` first. If missing → error.
- `add BLOCK_TYPE` → parse an `AddBlockNode`. Recurse for nested `add` blocks. Consume `end` to close.
- `on (event)` → parse an `OnEventNode`. Read any following `if`/`anif` lines, then the `run` line. A blank line (double NEWLINE) terminates the on-block.
- `if`/`anif` lines before a `run` → build a list of `ConditionNode`. The `run` becomes an `IfNode`.
- `loop (N)` or `loop` → parse a `LoopNode`. Recurse body until `end`.
- `IDENTIFIER = expr` or `SELECTOR = expr` → `AssignmentNode`.
- `SELECTOR AUG_ASSIGN expr` → `AssignmentNode` with compound operator.
- `input (name : type)` → `InputDeclNode`.
- `allotrope (#Class.java)` → parse `AllotropeNode`. Read `#at POINT` ... `#end` blocks until `end`.
- `add behaviour` → parse `BehaviourNode`. Goals are bare keywords. `trigger (expr)` starts a trigger section.
- `add drops` → parse `DropsNode`. Each `drop (item, chance, min, max)` → `DropEntryNode`.

**Error collection:**
- Parser must NOT throw on first error. Use a `List<CarbonError>` collector.
- On an unexpected token, record the error, then skip tokens until a known recovery point (`end`, `add`, `on`, newline) and continue.
- After full parse, if `errors` is non-empty → abort compilation and report all errors.

**CarbonError format:**
```java
class CarbonError {
    String filePath;
    int line;
    String message;
    String suggestion;  // null if no suggestion available
}
```

Suggestion generation: for unrecognised IDENTIFIER tokens in keyword positions, run Levenshtein distance against the full keyword list. If closest match has distance ≤ 2, set suggestion to `"Did you mean \"" + closest + "\"?"`.

---

### Step 4 — Transpiler

**Class:** `CarbonTranspiler`
**Input:** `FileNode`
**Output:** A `.java` source file as a String

The transpiler walks the AST and emits Java source code that calls the Carbon API (see Step 6).

**Output class structure:**
```java
public class Carbon_<modid>_<scriptname> implements CarbonScript {
    @Override
    public void register(CarbonRegistry registry) {
        // generated registration code
    }

    @Override
    public void attachEvents(CarbonEventBus events) {
        // generated event handler code
    }
}
```

**Mapping rules — AST node to Java output:**

| AST Node | Java output |
|----------|------------|
| `AddBlockNode(item)` | `registry.registerItem(new CarbonItemBuilder()...build())` |
| `AddBlockNode(mob)` | `registry.registerMob(new CarbonMobBuilder()...build())` |
| `AddBlockNode(block)` | `registry.registerBlock(new CarbonBlockBuilder()...build())` |
| `AddBlockNode(command)` | `registry.registerCommand(new CarbonCommandBuilder()...build())` |
| `AddBlockNode(effect)` | `registry.registerEffect(new CarbonEffectBuilder()...build())` |
| `PropertyNode` | `.propertyName(value)` on the enclosing builder |
| `OnEventNode` | `events.on(EventType.X, (ctx) -> { ... })` |
| `RunNode` | `CarbonRuntime.run("path", args)` |
| `IfNode` | Standard Java `if` / `else` |
| `LoopNode(count)` | `for (int i = 0; i < N; i++)` |
| `LoopNode(null)` | `events.onTick((ctx) -> { ... })` |
| `AssignmentNode` | Java variable declaration or assignment |
| `ActionLineNode(effect)` | `CarbonActions.applyEffect(target, effectId, level, duration)` |
| `ActionLineNode(fire)` | `CarbonActions.fireProjectile(projectileId, modifiers)` |
| `ActionLineNode(playsound)` | `CarbonActions.playSound(soundPath)` |
| `ActionLineNode(spawn)` | `CarbonActions.spawnMob(mobId, location)` |
| `ActionLineNode(damage)` | `CarbonActions.damage(target, amount)` |
| `ActionLineNode(teleport)` | `CarbonActions.teleport(entity, destination)` |
| `DropsNode` | `.drops(new CarbonDropTable()...build())` on mob builder |
| `BehaviourNode` | `.behaviour(new CarbonBehaviourBuilder()...build())` on mob builder |

**Type inference at transpile time:**
- Integer literal → `int`
- Float literal → `double`
- String literal → `String`
- `nearest_entity`, `#p`, `#s`, `target_entity` → `Entity`
- Variable that was assigned an Entity → `Entity`
- All others → infer from first assignment, carry type through subsequent uses

**Selector mapping:**
| Carbon selector | Java |
|----------------|------|
| `#p` | `ctx.getPlayer()` |
| `#s` | `ctx.getSelf()` |
| `#a` | `ctx.getAllPlayers()` |
| `nearest_entity` | `ctx.getNearestEntity()` |
| `target_entity` | `ctx.getTarget()` |
| `#p.field` | `CarbonPersistence.getPlayer(ctx.getPlayer(), "field")` |
| `#w.field` | `CarbonPersistence.getWorld("field")` |
| `#mod.field` | `CarbonPersistence.getMod("field")` |

---

### Step 5 — Runtime Compiler

**Class:** `CarbonLangCompiler`
**Input:** `.java` source file path
**Output:** `.class` bytecode file

Uses `javax.tools.JavaCompiler` which is built into the JDK. The Minecraft classpath and Carbon Lib classpath must be on the compiler's classpath so the generated code can reference them.

```java
public class CarbonLangCompiler {
    public boolean compile(Path javaFile, Path outputDir, List<Path> classpath) {
        JavaCompiler compiler = ToolProvider.getSystemJavaCompiler();
        StandardJavaFileManager fm = compiler.getStandardFileManager(null, null, null);

        fm.setLocation(StandardLocation.CLASS_OUTPUT, List.of(outputDir.toFile()));
        fm.setLocation(StandardLocation.CLASS_PATH, classpath.stream()
            .map(Path::toFile).toList());

        Iterable<? extends JavaFileObject> units =
            fm.getJavaFileObjects(javaFile.toFile());

        DiagnosticCollector<JavaFileObject> diagnostics = new DiagnosticCollector<>();
        boolean success = compiler.getTask(null, fm, diagnostics, null, null, units).call();

        if (!success) {
            // translate javac errors back into CarbonError format
            // map line numbers back to original .cj file using line map from transpiler
        }
        return success;
    }
}
```

The transpiler must emit `// CJ_LINE:<N>` comments in the generated Java so that javac errors can be mapped back to the original `.cj` line numbers for user-facing error messages.

---

### Step 6 — Carbon API (Carbon Lib)

This is the Fabric mod (`carbon-lib`) that the transpiler's output calls into. It provides a clean Java API so the transpiler never needs to emit raw Fabric or Minecraft API calls directly.

**Required classes in Carbon Lib:**

```
CarbonScript          interface — register() + attachEvents()
CarbonRegistry        wraps Fabric Registry
CarbonEventBus        wraps Fabric event buses
CarbonRuntime         handles run(), hot-reload, script loading
CarbonContext         passed to event handlers — getPlayer(), getSelf(), getTarget(), etc.
CarbonPersistence     handles #p. #w. #mod. #e. #b. persistent data via NBT / PersistentState
CarbonActions         static methods — applyEffect(), fireProjectile(), playSound(), etc.
CarbonItemBuilder     builder for items
CarbonMobBuilder      builder for mobs
CarbonBlockBuilder    builder for blocks
CarbonCommandBuilder  builder for commands
CarbonEffectBuilder   builder for custom effects
CarbonBehaviourBuilder builder for mob AI
CarbonDropTable       builder for mob drop tables
```

Each builder maps directly to the properties defined in the language spec. Every property in section 7 of this spec must have a corresponding builder method.

---

### Step 7 — Class Loader

**Class:** `CarbonClassLoader`

Loads the compiled `.class` files into the JVM at runtime. Uses a child-first `URLClassLoader` so mod classes are isolated from each other.

```java
public class CarbonClassLoader {
    private URLClassLoader loader;

    public CarbonScript load(Path classDir, String className) throws Exception {
        loader = new URLClassLoader(
            new URL[]{classDir.toUri().toURL()},
            CarbonClassLoader.class.getClassLoader()
        );
        Class<?> cls = loader.loadClass(className);
        return (CarbonScript) cls.getDeclaredConstructor().newInstance();
    }

    public void unload() throws Exception {
        if (loader != null) loader.close();
    }
}
```

`unload()` is called during hot-reload before recompiling.

---

### Step 8 — Hot-Reload Watcher

**Class:** `CarbonWatcher`

Watches the mods directory for changes to `.zip` files using Java's `WatchService`. When a change is detected:

1. Call `CarbonClassLoader.unload()` for the affected mod.
2. Deregister all items, mobs, blocks, commands, and event handlers registered by that mod.
3. Re-run the full pipeline (Steps 1–7) for the changed zip.
4. Re-register everything.
5. Report result to console: `[Carbon] Hot-reloaded: <modid>` or list errors.

Hot-reload must not crash the game. Wrap the entire reload sequence in a try-catch. On failure, leave the previous version loaded and report the error.

---

### Full Build Order for the Coding Agent

Build and test in this order. Each step can be unit tested independently before moving to the next.

| Order | Class | Depends on | Can test without Minecraft? |
|-------|-------|-----------|----------------------------|
| 1 | `TokenType` enum | nothing | Yes |
| 2 | `CarbonLexer` | TokenType | Yes |
| 3 | AST node classes | nothing | Yes |
| 4 | `CarbonParser` | Lexer + AST nodes | Yes |
| 5 | `CarbonError` + suggestion engine | nothing | Yes |
| 6 | `CarbonScript` interface | nothing | Yes |
| 7 | `Carbon*Builder` classes | Fabric API | No — needs Fabric |
| 8 | `CarbonRegistry` | Fabric API | No |
| 9 | `CarbonEventBus` | Fabric API | No |
| 10 | `CarbonContext` | Fabric API | No |
| 11 | `CarbonPersistence` | Fabric API | No |
| 12 | `CarbonActions` | Fabric API | No |
| 13 | `CarbonTranspiler` | Parser + Builder classes | Yes (mock builders) |
| 14 | `CarbonLangCompiler` | javax.tools | Yes |
| 15 | `CarbonClassLoader` | compiled classes | Yes |
| 16 | `CarbonRuntime` | all of the above | No |
| 17 | `CarbonWatcher` | CarbonRuntime | No |

---

## 13. Extended Variable and Selector System

### 13.1 Writing Persistent Data to a Specific Player

To write persistent data to a player other than the current player (`#p`), use the `#player(variable)` syntax:

```
#player(target).field = value
```

Where `target` is any variable or selector holding an entity reference. This emits a call to `CarbonPersistence.setPlayer(target, "field", value)` in the transpiler.

Grammar addition:
```
SELECTOR_PLAYER := "#player" "(" expr ")" "." IDENTIFIER
```

This is valid on both the left and right side of an assignment.

---

### 13.2 Built-in Read-Only Player Properties

The following `#p.` properties are built-in and read-only. Attempting to assign to them is a compile-time error.

**Player (`#p.` or `#player(var).`):**

| Property | Type | Description |
|----------|------|-------------|
| `name` | string | Player's username |
| `uuid` | string | Player's UUID |
| `health` | float | Current health points |
| `max_health` | float | Maximum health points |
| `hunger` | integer | Current hunger level (0–20) |
| `position` | position | Current XYZ coordinates |
| `dimension` | string | Current dimension: `overworld`, `nether`, `end` |
| `gamemode` | string | `survival`, `creative`, `adventure`, `spectator` |
| `xp_level` | integer | Current XP level |

**Entity (`#s.`):**

| Property | Type | Description |
|----------|------|-------------|
| `name` | string | Entity type name |
| `health` | float | Current health |
| `max_health` | float | Maximum health |
| `position` | position | Current XYZ coordinates |

The transpiler maps these to the appropriate Minecraft API getters via `CarbonContext`. Any attempt to assign to a read-only property emits:
```
[Carbon] Error in <file> line <N>:
  "#p.name" is read-only and cannot be assigned to.
```

---

### 13.3 Early Script Exit — `exit`

`exit` immediately halts execution of the current script. It is valid anywhere in a script body.

```
if (#p.tpa_requester == "null")
run (scripts/no_request.cj)
exit

@ Only reached if the if condition was false
requester = find_player (#p.tpa_requester)
```

Distinction from `stop`:
- `stop` — exits the current `loop` block only. Invalid outside a loop.
- `exit` — exits the entire script. Valid anywhere.

Grammar addition:
```
exit_stmt := "exit" NEWLINE
```

Transpiler output: `return;` inside the generated method.

---

### 13.4 Player Lookup by Name — `find_player`

`find_player` looks up an online player by their username string and returns an entity reference, or `null` if the player is not currently online.

```
requester = find_player (#p.tpa_requester)

if (requester == null)
run (scripts/player_offline.cj)
exit
```

Grammar addition:
```
find_player_expr := "find_player" "(" expr ")"
```

This is valid anywhere a value or entity expression is expected.

Transpiler output: `CarbonActions.findPlayer(name)` which calls `server.getPlayerManager().getPlayer(name)` and returns `null` if not found.

`null` is now a valid value token and can be used in equality comparisons:
```
if (requester == null)
```

---

## 14. Mod-Level Persistent Variables

### 14.1 Declaration in `carbon.mod.json`

Mods can declare named persistent variables that survive server restarts and are accessible from any script via `#mod.`. They are declared in `carbon.mod.json` under the `persistent_vars` key:

```json
{
  "id": "ruby_mod",
  "name": "Ruby Mod",
  "version": "1.0.0",
  "authors": ["your_name"],
  "minecraft_version": "1.20.1",
  "type": "carbon",
  "dependencies": [],
  "persistent_vars": {
    "total_rubies_mined": "integer",
    "server_boss_killed": "boolean",
    "top_player": "string",
    "difficulty_multiplier": "float"
  }
}
```

### 14.2 Valid Types

| Type in JSON | JVM type | Default value |
|--------------|----------|--------------|
| `"integer"` | `int` | `0` |
| `"float"` | `double` | `0.0` |
| `"boolean"` | `boolean` | `false` |
| `"string"` | `String` | `""` |

### 14.3 Rules

- Carbon creates storage for every declared var at mod load time using `PersistentState` on the server.
- Default values are written on first load if no saved value exists.
- Using `#mod.field` in a script where `field` is not declared in `persistent_vars` is a **compile-time error**:
```
[Carbon] Error in src/scripts/example.cj line 4:
  "#mod.unknown_var" is not declared in persistent_vars in carbon.mod.json.
```
- `#p.` and `#w.` fields do NOT require pre-declaration since player and world sets are dynamic.
- `persistent_vars` is optional. Mods that don't use `#mod.` don't need it.

### 14.4 Usage in Scripts

```
@ Increment a global counter
#mod.total_rubies_mined += 1

@ Check a global flag
if (#mod.server_boss_killed == false)
run (scripts/spawn_boss.cj)

@ Store a string
#mod.top_player = #p.name
```

Transpiler maps all `#mod.field` reads and writes to `CarbonPersistence.getMod("field")` and `CarbonPersistence.setMod("field", value)`.
