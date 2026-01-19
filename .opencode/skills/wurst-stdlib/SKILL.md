---
name: wurst-stdlib
description: WurstScript standard library reference including closures, data structures, vectors, object editing, dummy units, and utility packages
---

# WurstScript Standard Library

The standard library provides essential packages for Warcraft III map development.

Source: https://github.com/wurstscript/WurstStdlib2

## Core Packages (_wurst)

### Printing
```wurst
import Printing

print("Hello")           // Display message to all players
printTimed("Hi", 5.0)    // Display for 5 seconds
```

### Error Handling
```wurst
import ErrorHandling

// Throws error and stops execution
error("Something went wrong")

// Assertions
if condition
    error("Condition failed")
```

### Assets
Contains paths to built-in WC3 models and icons.

```wurst
import Assets

let fx = addEffect(Abilities.thunderClapCaster, pos)
```

### Wurstunit
Unit testing framework:

```wurst
import Wurstunit

@Test function testAddition()
    1 + 1 .assertEquals(2)

@Test function testUnit()
    let u = createUnit(...)
    u.assertNotNull()
```

## Closures

### ClosureTimers
```wurst
import ClosureTimers

// Run once after delay
doAfter(5.0) ->
    print("5 seconds passed")

// Run periodically
doPeriodically(1.0) cb ->
    print("Every second")
    if shouldStop
        destroy cb

// Run periodically with data
doPeriodicallyCounted(0.5, 10) (cb, count) ->
    print("Tick " + count.toString())

// Null timer (next frame)
nullTimer() ->
    print("Next frame")
```

### ClosureForGroups
```wurst
import ClosureForGroups

// Iterate units in range
forUnitsInRange(pos, 500) u ->
    u.damage(100)

// Iterate units of player
forUnitsOfPlayer(player) u ->
    u.kill()

// Iterate units in rect
forUnitsInRect(rect) u ->
    processUnit(u)

// Iterate with filter
forUnitsInRange(pos, 500, Filter(() -> GetFilterUnit().isAlive())) u ->
    // only alive units
```

### ClosureEvents
```wurst
import ClosureEvents

// Spell cast events
EventListener.onCast(ABILITY_ID) caster ->
    let target = EventData.getSpellTargetUnit()

EventListener.onPointCast(ABILITY_ID) (caster, targetPos) ->
    flashEffect(FX, targetPos)

EventListener.onTargetCast(ABILITY_ID) (caster, target) ->
    caster.damageTarget(target, 100)

// Unit events
EventListener.add(EVENT_PLAYER_UNIT_DEATH) ->
    let dying = GetTriggerUnit()

// Player-specific
EventListener.add(player, EVENT_PLAYER_UNIT_ATTACKED) ->
    // only for this player
```

### Execute
```wurst
import Execute

// Execute heavy computation in chunks to avoid op limit
execute() ->
    for i = 0 to 10000
        // heavy work
```

## Data Structures

### LinkedList
```wurst
import LinkedList

let list = new LinkedList<int>()
list.add(1)
list.add(2)
list.add(3)

// Iteration
for elem in list
    print(elem.toString())

// Operations
list.size()
list.get(0)
list.remove(2)
list.has(1)
list.clear()

// Functional
list.filter(x -> x > 1)
list.map(x -> x * 2)
list.foldl(0, (acc, x) -> acc + x)

destroy list
```

### HashMap
```wurst
import HashMap

let map = new HashMap<unit, int>()
map.put(someUnit, 100)
let value = map.get(someUnit)
map.has(someUnit)
map.remove(someUnit)

// Iteration
for key in map
    let val = map.get(key)

destroy map
```

### HashList
Combination of HashMap and LinkedList for ordered unique elements:

```wurst
import HashList

let hashList = new HashList<unit>()
hashList.add(unit1)
hashList.add(unit2)
hashList.has(unit1)  // O(1) lookup
hashList.remove(unit1)
```

### BitSet
```wurst
import BitSet

var bits = bitset(0)
bits = bits.add(3)      // set bit 3
bits = bits.add(5)      // set bit 5
bits.contains(3)        // true
bits = bits.remove(3)   // unset bit 3
```

## Math

### Vectors
```wurst
import Vectors

// 2D vectors
let v2 = vec2(100, 200)
v2.x
v2.y
v2.length()
v2.norm()           // normalized
v2.rotate(angle(45))

// 3D vectors
let v3 = vec3(100, 200, 50)
v3.toVec2()         // drop z
v3.withZ(100)       // change z

// Operations
v2 + v2
v2 - v2
v2 * 2.0            // scale
v2.dot(other)
v2.angleTo(other)
v2.distanceTo(other)

// Conversions
v2.toVec3()
v2.withZ(height)
```

### Angle
```wurst
import Angle

let a = angle(90)           // degrees
let a2 = (PI/2).asAngleRadians()  // radians

a.degrees()
a.radians()
a.sin()
a.cos()
a.toVec(length)     // direction vector

// Operations
a + a2
a - a2
a.normalize()       // 0-360 range
```

### Quaternion
```wurst
import Quaternion

let q = quat(axis, angle)
let rotated = q.rotate(vec3)
```

## Dummy Units

### DummyRecycler
```wurst
import DummyRecycler

// Get a recycled dummy
let dummy = DummyRecycler.get(pos, angle)

// Return when done
DummyRecycler.recycle(dummy)
```

### DummyCaster
```wurst
import DummyCaster

// Cast ability instantly
new DummyCaster()
    ..owner(player)
    ..origin(casterPos)
    ..castTarget(ABILITY_ID, 1, target)

// Cast at point
new DummyCaster()
    ..owner(player)
    ..origin(casterPos)
    ..castPoint(ABILITY_ID, 1, targetPos)

// Immediate (no projectile)
new DummyCaster()
    ..owner(player)
    ..origin(casterPos)
    ..castImmediate(ABILITY_ID, 1)
```

## Object Editing

Create WC3 objects in code at compile time:

### Units
```wurst
import UnitObjEditing

@compiletime function createUnit()
    new UnitDefinition('u001', 'hfoo')
        ..setName("Custom Footman")
        ..setHitPointsMaximumBase(500)
        ..setAttack1DamageBase(15)
        ..setMoveSpeed(300)
```

### Abilities
```wurst
import AbilityObjEditing

@compiletime function createAbility()
    new AbilityDefinitionFireBolt('A001')
        ..setName("Super Bolt")
        ..setDamage(1, 150)
        ..setCooldown(1, 8)
        ..setManaCost(1, 75)
        ..setCastRange(1, 600)
```

### Items
```wurst
import ItemObjEditing

@compiletime function createItem()
    new ItemDefinition('I001', 'rst1')
        ..setName("Magic Sword")
        ..setAbilities("A001")
        ..setGoldCost(500)
```

### Buffs
```wurst
import BuffObjEditing

@compiletime function createBuff()
    new BuffDefinition('B001', 'Bslo')
        ..setName("Slowed")
        ..setTooltipNormal(1, "Slowed")
        ..setIcon(Icons.bTNSlow)
```

### Upgrades
```wurst
import UpgradeObjEditing

@compiletime function createUpgrade()
    new UpgradeDefinition('R001')
        ..setName(1, "Better Weapons")
        ..addEffectAttackDamageBonus(0, 10)
        ..setGoldCostBase(100)
```

## Utility Packages

### DialogBox
```wurst
import DialogBox

let dialog = new DialogBox("Choose wisely")
dialog.addButton("Option A") ->
    print("Chose A")
dialog.addButton("Option B") ->
    print("Chose B")
dialog.display(player, true)
```

### Simulate3dSound
```wurst
import Simulate3dSound

// Play sound with 3D positioning
let sound = new Sound(...)
sound.playOnPoint(pos.withTerrainZ())
```

### Colors
```wurst
import Colors

let c = color(255, 0, 0)          // RGB
let c2 = colorA(255, 0, 0, 128)   // RGBA
player.getColor().toColor()
"|cffff0000Red Text|r"
```

### String Utilities
```wurst
import StringUtils

"hello".charAt(0)
"hello".substring(1, 3)
"hello".contains("ell")
"  hello  ".trim()
123.toString()
"123".toInt()
```

## Best Practices

1. **Destroy objects**: Call `destroy` on classes when done
2. **Use closures**: Prefer ClosureTimers over raw timer callbacks
3. **Recycle dummies**: Always recycle dummy units
4. **Prefer stdlib**: Use stdlib wrappers over raw natives
5. **Follow conventions**: Check stdlib source for patterns

## Resources

- Documentation: https://wurstlang.org/stdlib
- Source Code: https://github.com/wurstscript/WurstStdlib2
- Coding Conventions: https://wurstlang.org/manual.html#coding-conventions
