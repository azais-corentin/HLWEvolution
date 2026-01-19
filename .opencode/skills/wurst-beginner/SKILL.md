---
name: wurst-beginner
description: Getting started with WurstScript for Warcraft 3 map development including project structure, first map, and basic spell creation
---

# WurstScript Beginner Guide

This guide helps you get started with WurstScript for Warcraft III map development.

## Prerequisites

1. Install VSCode: https://code.visualstudio.com/
2. Install the Wurst extension from the marketplace
3. The extension auto-installs the compiler and CLI

## Creating a New Project

1. Open VSCode command palette (F1)
2. Run `>wurst: New Wurst Project`
3. Select a folder for your project

Or use CLI:
```
grill generate my-wurst-project
```

## Project Structure

```
my-project/
  _build/           # Generated content (don't edit)
  .vscode/          # VSCode settings
  imports/          # Files to import into map
  wurst/            # Your .wurst code files
  .gitignore
  ExampleMap.w3x    # Your terrain map
  wurst_run.args    # Run configuration
  wurst.build       # Project configuration
  wurst.dependencies # Generated dependency links
```

## Hello World

Create `wurst/Hello.wurst`:

```wurst
package Hello

init
    print("Hello World")
```

Run with F1 -> `>runmap`

## Package Structure

Every `.wurst` file contains exactly one package:

```wurst
package MyPackage

import SomeLibrary  // import other packages

// Global variables
let myGlobal = 42

// Functions
function myFunction()
    print("Hello")

// Init block runs on map start
init
    myFunction()
```

## Using the Standard Library

The stdlib provides wrappers and utilities. Example:

```wurst
package SpellExample

import ClosureTimers
import ClosureForGroups
import ClosureEvents

init
    EventListener.onPointCast(MY_SPELL_ID) (caster, targetPos) ->
        flashEffect("Abilities\\Spells\\Human\\ThunderClap\\ThunderClapCaster.mdl", targetPos)
        
        doAfter(0.5) ->
            forUnitsInRange(targetPos, 300) u ->
                if u.isEnemyOf(caster)
                    caster.damageTarget(u, 100)
```

## Coding Conventions

### Use Extension Functions
```wurst
// Jass style (avoid)
local integer id = GetPlayerId(GetOwningPlayer(GetTriggerUnit()))

// Wurst style (preferred)
let id = GetTriggerUnit().getOwner().getId()
```

### Use Cascade Operator
```wurst
// Instead of
let u = createUnit(player, UNIT_ID, pos, angle)
SetUnitX(u, newX)
SetUnitY(u, newY)
u.addAbility(ABILITY_ID)

// Use cascade
createUnit(player, UNIT_ID, pos, angle)
    ..setX(newX)
    ..setY(newY)
    ..addAbility(ABILITY_ID)
```

### Use Type Inference
```wurst
// Explicit (verbose)
unit u = CreateUnit(...)

// Inferred (preferred)
let u = createUnit(...)
var health = u.getHP()
```

## Vector Math

Wurst has built-in vector types:

```wurst
import Vectors

let pos = vec2(100, 200)
let pos3d = vec3(100, 200, 50)

// Operations
let sum = pos + vec2(10, 10)
let scaled = pos * 2
let length = pos.length()
let normalized = pos.norm()
let angle = pos.angleTo(otherPos)
```

## Creating Spells

### Basic Spell Structure
```wurst
package MySpell

import ClosureEvents
import DamageSystem

public constant MY_SPELL_ID = 'A000'

init
    EventListener.onCast(MY_SPELL_ID) caster ->
        let target = EventData.getSpellTargetUnit()
        caster.damageTarget(target, 100, ATTACK_TYPE_MAGIC)
```

### Spell with Timer
```wurst
package TimedSpell

import ClosureTimers
import ClosureEvents

init
    EventListener.onPointCast(SPELL_ID) (caster, pos) ->
        var ticks = 0
        doPeriodically(0.5) cb ->
            ticks++
            flashEffect(FX_PATH, pos)
            if ticks >= 10
                destroy cb
```

## Object Editing in Code

Create WC3 objects without the World Editor:

```wurst
package MyObjects

import AbilityObjEditing

@compiletime function createSpell()
    new AbilityDefinitionFireBolt('A001')
        ..setName("Super Bolt")
        ..setTooltipNormal(1, "Super Bolt")
        ..setTooltipNormalExtended(1, "Deals 100 damage")
        ..setDamage(1, 100)
        ..setCooldown(1, 10)
        ..setManaCost(1, 50)
```

## Building and Running

### Run Map (Testing)
- F1 -> `>runmap`
- Compiles and launches WC3 with your map

### Build Map (Release)
- F1 -> `>buildmap`
- Creates compiled map in `_build/` folder

## Managing Dependencies

Add a library:
```
grill install https://github.com/some/library
```

Update dependencies:
```
grill install
```

## Debugging Tips

1. Use `print()` for quick debugging
2. Use `Log.info()`, `Log.warn()`, `Log.error()` for structured logging
3. Check VSCode's Problems panel for compile errors
4. Use breakpoints in generated Jass (advanced)

## Common Patterns

### Event Handling
```wurst
import ClosureEvents

init
    EventListener.add(EVENT_PLAYER_UNIT_DEATH) ->
        let dying = GetTriggerUnit()
        let killer = GetKillingUnit()
        // handle death
```

### Periodic Actions
```wurst
import ClosureTimers

init
    doPeriodically(1.0) cb ->
        // runs every second

    doAfter(5.0) ->
        // runs once after 5 seconds
```

### Data Storage
```wurst
import HashMap

let unitData = new HashMap<unit, MyData>()

function saveData(unit u, MyData data)
    unitData.put(u, data)

function getData(unit u) returns MyData
    return unitData.get(u)
```

## Resources

- Full Manual: https://wurstlang.org/manual.html
- Standard Library: https://wurstlang.org/stdlib
- Discord Community: https://discord.gg/mSHZpWcadz
- GitHub: https://github.com/wurstscript/WurstScript
