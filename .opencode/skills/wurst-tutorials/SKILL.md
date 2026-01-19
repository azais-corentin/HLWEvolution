---
name: wurst-tutorials
description: WurstScript tutorials covering spell creation, legacy map migration, save/load systems, hot code reload, and advanced techniques
---

# WurstScript Tutorials

Practical guides for common Warcraft III modding tasks.

## Spell Creation

### Basic Targeted Spell
```wurst
package LightningBolt

import ClosureEvents
import DamageSystem

public constant LIGHTNING_BOLT_ID = 'A001'
constant DAMAGE = 150.
constant FX_PATH = "Abilities\\Spells\\Human\\Thunderclap\\ThunderclapCaster.mdl"

init
    EventListener.onTargetCast(LIGHTNING_BOLT_ID) (caster, target) ->
        flashEffect(FX_PATH, target.getPos())
        caster.damageTarget(target, DAMAGE, ATTACK_TYPE_MAGIC)
```

### Area of Effect Spell
```wurst
package NovaSpell

import ClosureEvents
import ClosureForGroups
import DamageSystem

public constant NOVA_ID = 'A002'
constant DAMAGE = 100.
constant RADIUS = 300.

init
    EventListener.onCast(NOVA_ID) caster ->
        let pos = caster.getPos()
        flashEffect("Abilities\\Spells\\Undead\\FrostNova\\FrostNovaTarget.mdl", pos)
        
        forUnitsInRange(pos, RADIUS) target ->
            if target.isEnemyOf(caster.getOwner()) and target.isAlive()
                caster.damageTarget(target, DAMAGE, ATTACK_TYPE_MAGIC)
```

### Channeled/Timed Spell
```wurst
package RainOfFire

import ClosureEvents
import ClosureTimers
import ClosureForGroups

public constant RAIN_OF_FIRE_ID = 'A003'
constant TICK_DAMAGE = 25.
constant RADIUS = 200.
constant DURATION = 5.
constant TICK_INTERVAL = 0.5

init
    EventListener.onPointCast(RAIN_OF_FIRE_ID) (caster, targetPos) ->
        var elapsed = 0.
        
        doPeriodically(TICK_INTERVAL) cb ->
            elapsed += TICK_INTERVAL
            
            flashEffect("Abilities\\Spells\\Demon\\RainOfFire\\RainOfFireTarget.mdl", targetPos)
            
            forUnitsInRange(targetPos, RADIUS) u ->
                if u.isEnemyOf(caster.getOwner())
                    caster.damageTarget(u, TICK_DAMAGE)
            
            if elapsed >= DURATION
                destroy cb
```

### Projectile Spell
```wurst
package Fireball

import ClosureEvents
import ClosureTimers
import DamageSystem

public constant FIREBALL_ID = 'A004'
constant SPEED = 800.
constant DAMAGE = 200.
constant COLLISION = 80.

init
    EventListener.onPointCast(FIREBALL_ID) (caster, targetPos) ->
        let startPos = caster.getPos()
        let direction = startPos.angleTo(targetPos)
        var currentPos = startPos
        
        let fx = addEffect("Abilities\\Weapons\\RedDragonBreath\\RedDragonMissile.mdl", startPos)
            ..setYaw(direction)
        
        doPeriodically(0.03) cb ->
            currentPos = currentPos.polarOffset(direction, SPEED * 0.03)
            fx.setPos(currentPos)
            
            // Check collision
            let hit = findNearestEnemy(currentPos, COLLISION, caster.getOwner())
            if hit != null
                flashEffect("Abilities\\Spells\\Other\\Incinerate\\IncinerateBuff.mdl", currentPos)
                caster.damageTarget(hit, DAMAGE, ATTACK_TYPE_MAGIC)
                fx.destr()
                destroy cb
            else if currentPos.distanceTo(startPos) > 1200
                fx.destr()
                destroy cb
```

## Legacy Map Migration

### Working with Existing Jass Maps

1. **Create wurst project** in map's directory
2. **Keep existing Jass** - Wurst compiles alongside
3. **Gradually migrate** code to Wurst packages

### Calling Jass from Wurst
```wurst
package Interop

// Call existing Jass functions directly
native MyJassFunction takes integer i returns nothing

// Reference Jass globals
@extern var myJassGlobal: int

init
    MyJassFunction(5)
    myJassGlobal = 10
```

### Migrating Triggers
```wurst
// Old Jass trigger
// function Trig_Init_Actions takes nothing returns nothing
//     call DisplayTimedTextToPlayer(...)
// endfunction

// Wurst equivalent
package MigratedTrigger

init
    CreateTrigger()
        ..registerAnyUnitEvent(EVENT_PLAYER_UNIT_DEATH)
        ..addAction() ->
            let dying = GetTriggerUnit()
            // trigger logic
```

### vJass to Wurst

| vJass | Wurst |
|-------|-------|
| `struct` | `class` |
| `library` | `package` |
| `scope` | `package` (separate file) |
| `method` | `function` |
| `Table` | `HashMap<K, V>` |
| `TimerUtils` | `ClosureTimers` |
| `GroupUtils` | `ClosureForGroups` |
| `RegisterPlayerUnitEvent` | `EventListener` |

## Save/Load System

### Basic Save System
```wurst
package SaveLoad

import Serialization
import StringUtils
import FileIO

constant SAVE_FILENAME = "MyMapSave"

public class PlayerData
    int level
    int gold
    int array[6] items
    
    function serialize() returns string
        var data = level.toString() + "|"
        data += gold.toString() + "|"
        for i = 0 to 5
            data += items[i].toString() + "|"
        return data
    
    static function deserialize(string data) returns PlayerData
        let parts = data.split("|")
        let pd = new PlayerData()
        pd.level = parts.get(0).toInt()
        pd.gold = parts.get(1).toInt()
        for i = 0 to 5
            pd.items[i] = parts.get(i + 2).toInt()
        return pd

public function savePlayer(player p, PlayerData data)
    let encoded = data.serialize()
    // Add checksum/encryption here
    FileIO.write(SAVE_FILENAME + p.getId().toString(), encoded)

public function loadPlayer(player p) returns PlayerData
    let content = FileIO.read(SAVE_FILENAME + p.getId().toString())
    if content != null and content != ""
        return PlayerData.deserialize(content)
    return new PlayerData()
```

### Codeless Save (Hash-based)
```wurst
package CodeSave

import Charsets

// Generate save code from data
public function generateCode(int level, int gold, int checksum) returns string
    var code = ""
    code += encodeValue(level, 2)
    code += encodeValue(gold, 4)
    code += encodeValue(checksum, 2)
    return code

function encodeValue(int value, int digits) returns string
    var result = ""
    var remaining = value
    for i = 0 to digits - 1
        let digit = remaining mod CHARSET.length()
        result = CHARSET.charAt(digit) + result
        remaining = remaining div CHARSET.length()
    return result
```

## Hot Code Reload (JHCR)

### Setup
1. Install JHCR: https://github.com/lep/jhcr
2. Add to `wurst.build`:
```yaml
buildMapData:
  jhcr: true
```

### Usage
1. Run map normally
2. Edit code
3. Save file
4. Game reloads code automatically

### Limitations
- Can't add new global variables
- Can't change class structures
- Function signatures must stay same
- Init blocks don't re-run

### Hot-Reload Friendly Code
```wurst
package HotReloadFriendly

// Use functions instead of init globals
function getDamageAmount() returns real
    return 100.  // Can change this value

// Config in functions
function setupSpell()
    EventListener.onCast(SPELL_ID) caster ->
        caster.damageTarget(target, getDamageAmount())
```

## Common Patterns

### Singleton Pattern
```wurst
package MySingleton

public class GameManager
    private static GameManager instance = null
    
    static function getInstance() returns GameManager
        if instance == null
            instance = new GameManager()
        return instance
    
    private construct()
        // private constructor
```

### Factory Pattern
```wurst
package UnitFactory

interface UnitSpawner
    function spawn(vec2 pos) returns unit

class FootmanSpawner implements UnitSpawner
    override function spawn(vec2 pos) returns unit
        return createUnit(players[0], 'hfoo', pos, angle(0))

class KnightSpawner implements UnitSpawner
    override function spawn(vec2 pos) returns unit
        return createUnit(players[0], 'hkni', pos, angle(0))
```

### Observer Pattern
```wurst
package EventSystem

interface GameEventListener
    function onEvent(string eventType, int data)

let listeners = new LinkedList<GameEventListener>()

public function addListener(GameEventListener listener)
    listeners.add(listener)

public function fireEvent(string eventType, int data)
    for listener in listeners
        listener.onEvent(eventType, data)
```

### State Machine
```wurst
package StateMachine

enum UnitState
    IDLE
    MOVING
    ATTACKING
    DEAD

class StatefulUnit
    unit u
    UnitState state = IDLE
    
    function update()
        switch state
            case IDLE
                if hasTarget()
                    state = MOVING
            case MOVING
                moveToTarget()
                if inRange()
                    state = ATTACKING
            case ATTACKING
                attack()
                if targetDead()
                    state = IDLE
            case DEAD
                skip
```

## Performance Tips

### Avoid Leaks
```wurst
// Bad - leaks group
forUnitsInRange(pos, 500) u ->
    // ...

// Good - use stdlib which handles cleanup
import ClosureForGroups
forUnitsInRange(pos, 500) u ->
    // properly cleaned up
```

### Batch Operations
```wurst
// Bad - many timer instances
for i = 0 to 100
    doAfter(1.0) ->
        doThing(i)

// Good - single timer
doPeriodically(0.03) cb ->
    // batch process
```

### Object Pooling
```wurst
class Projectile
    static LinkedList<Projectile> pool = new LinkedList<Projectile>()
    
    static function get() returns Projectile
        if pool.isEmpty()
            return new Projectile()
        return pool.pop()
    
    function release()
        pool.push(this)
```

## Resources

- Full Tutorial List: https://wurstlang.org/tutorials.html
- Beginner Guide: https://wurstlang.org/tutorials/wurstbeginner.html
- Legacy Maps Guide: https://wurstlang.org/tutorials/legacymaps.html
- Discord: https://discord.gg/mSHZpWcadz
