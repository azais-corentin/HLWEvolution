# AGENTS.md - HLWEvolution

This document provides guidance for AI coding agents working on this Warcraft III Hero Defense map project.

## Project Overview

- **Language**: WurstScript (compiles to Jass for Warcraft III)
- **Framework**: Wurst standard library (`wurstStdlib2`) and `frentity` (Entity-Component-System)
- **Project Type**: Warcraft III custom map (Hero Line Wars variant)
- **Map File**: `HLWEvolution.w3m`

## Build/Compile/Test Commands

This project uses the Wurst compiler, typically via VS Code extension.

### Building
- **VS Code**: Use command `Wurst: Build Map`
- **Compiler flags** are in `wurst_run.args`:
  - `-runcompiletimefunctions` - Execute @compiletime functions
  - `-injectobjects` - Inject generated objects into map
  - `-stacktraces` - Enable stack traces for debugging

### Running the Map
- **VS Code**: Use command `Wurst: Run Map` (compiles and launches Warcraft III)

### Testing
- **Run All Tests**: VS Code command `Wurst: Run all tests`
- **Run Single Test**: Click "Run Test" code lens above any `@test` annotated function
- Tests use Wurst's built-in unit testing framework with `@test` annotation

### Linting
- Built into the Wurst language server (no separate command)

## Project Structure

```
wurst/                    # Primary source code
  Data/                   # Static data definitions (Units/, GameRegions.wurst)
  Logic/                  # Core game mechanics
    Hero/                 # Hero selection, revival, leveling
    Monsters/             # Spawning, movement, goal logic
    Player/               # Income, teams, scoreboards
    Commands.wurst        # Chat command handling
    Constants.wurst       # Game configuration values
  Generators/             # Compile-time generation scripts
  Utility/                # Shared helper functions
  Initialization.wurst    # Lifecycle annotation definitions
_build/                   # Compiled output and dependencies
```

## Configuration Files

- `wurst.build` - Project manifest (YAML): dependencies, map metadata
- `wurst_run.args` - Compiler arguments

## Code Style Guidelines

### Initialization System

Use custom annotations instead of standard `init` blocks when order matters:

```wurst
@loadinit    // Called during loading
@earlyinit   // Called right after map start
@lateinit    // Called after map initialization (use for most triggers)
@lastinit    // Called after everything else
@compiletime // Executed at compile time for data generation
```

### Naming Conventions

| Element | Convention | Examples |
|---------|------------|----------|
| Packages | PascalCase | `package HeroSelection` |
| Classes | PascalCase | `class MonsterData`, `class Team` |
| Functions | camelCase | `handleCommand()`, `initTeamPlayers()` |
| Variables | camelCase | `playerHero`, `goldToLumber` |
| Constants | camelCase or snake_case | `monsterPerKeep`, `message_duration` |
| Global flags | SCREAMING_SNAKE_CASE | `DEBUG_MODE` |
| Configurable constants | camelCase with @configurable | `@configurable public constant real priceC1` |

### Import Ordering

1. Standard Wurst libraries (`ClosureTimers`, `HashMap`, `Players`, etc.)
2. Project initialization package (`Initialization`)
3. Project data/logic packages (sorted alphabetically within group)

```wurst
package Commands

import StringUtils
import ClosureEvents
import LinkedList

import Initialization
import Constants
import IncomeValues
```

### Type Usage

- Use `let` for immutable values, `var` for mutable
- Use `constant` for compile-time constants
- Use `tuple` for lightweight data structures: `tuple ButtonPosition(int x, int y)`
- Use `class` for complex objects with behavior
- Prefer explicit types for public API, inference for local variables

### Trigger, Event, and Timer Patterns

```wurst
// Preferred: EventListener from standard library
EventListener.add(EVENT_PLAYER_UNIT_SELECTED) ->
  let u = GetTriggerUnit()
  let p = GetTriggerPlayer()

// Raw triggers with cascade operator
let t = CreateTrigger()
  ..registerEnterRegion(someRegion, null)
  ..addAction() ->
    // handle action

// Delayed execution
doAfter(0.1) ->
  callFunctionsWithAnnotation("@lateinit")
```

### Error Handling

- Use early returns with player-facing messages for validation:
```wurst
if possibleAttributeTotal == 0
  p.print("Not enough money!", message_duration)
  return
```
- Use `print()` for debug output during development
- Check `DEBUG_MODE` flag for conditional debug behavior

### Compile-Time Generation

Use `@compiletime` for generating game data at build time:

```wurst
public var monsterData = compiletime(new LinkedList<MonsterData>)

@compiletime function initMonsterData()
  for id = 1 to monsterCount - 1
    monsterData.push(new MonsterData(...))
```

### Formatting

- Indentation: 2 spaces (consistent within files)
- Single blank line between imports and first code block
- Use `//` for single-line comments
- Group related constants with comment headers

### String Formatting

Use `.format()` for string interpolation:
```wurst
p.print("Unknown command '{0}'".format(cmd), message_duration)
```

### Switch Statements

Use `|` for multiple case matching:
```wurst
switch cmd
  case "zoom" | "cam"
    // handle both commands
  default
    p.print("Unknown command")
```

## Key Patterns to Follow

1. **Use appropriate init annotation** - Most game logic uses `@lateinit`
2. **Compile-time for data** - Generate unit stats, tooltips at compile time
3. **Player feedback** - Always inform players of command results via `p.print()`
4. **Constants in Constants.wurst** - Centralize configuration values
5. **Type-safe collections** - Use `HashMap<player, unit>` over raw arrays

## Dependencies

Defined in `wurst.build`:
- `wurstStdlib2` - Standard library (ClosureTimers, HashMap, Players, etc.)
- `frentity` - Entity-Component-System framework
