---
name: wurst-community
description: WurstScript community resources including example maps, libraries, frameworks, and project showcases for Warcraft 3 modding
---

# WurstScript Community Resources

Discover community projects, libraries, and example maps built with WurstScript.

## Official Resources

### Documentation
- Main Site: https://wurstlang.org
- Manual: https://wurstlang.org/manual.html
- Standard Library: https://wurstlang.org/stdlib
- Tutorials: https://wurstlang.org/tutorials.html

### Source Repositories
- WurstScript Compiler: https://github.com/wurstscript/WurstScript
- Standard Library: https://github.com/wurstscript/WurstStdlib2
- Documentation Site: https://github.com/wurstscript/wurstscript.github.io

### Community
- Discord: https://discord.gg/mSHZpWcadz
- Issue Tracker: https://github.com/wurstscript/WurstScript/issues

## Popular Libraries

### Frentity
Lightweight entity framework for game objects.
```yaml
dependencies:
  - https://github.com/Frotty/Frentity
```

Features:
- Entity lifecycle management
- Component-based architecture
- Automatic cleanup

### Wurst Lodash
Functional programming utilities inspired by Lodash.
```yaml
dependencies:
  - https://github.com/theQuazz/wurst-lodash
```

Features:
- Map, filter, reduce
- Collection utilities
- Functional helpers

### Wurst Table Layout
Frame-based UI system without the complexity.
```yaml
dependencies:
  - https://github.com/Frotty/wurst-table-layout
```

Features:
- Grid-based layouts
- Easy positioning
- Responsive design

### Wurst Item Shop
Complete item shop system.
```yaml
dependencies:
  - https://github.com/Frotty/wurst-item-shop
```

Features:
- Category-based shops
- Custom UI
- Transaction handling

### Bounty Controller
Control bounty rewards and behavior.
```yaml
dependencies:
  - https://github.com/HerlySQR/Bounty_Controller
```

## Example Maps (Open Source)

### Zombie Defense
PvE survival base building with randomized terrain.
- Type: Castle Defense
- Repository: Community release
- Link: https://maps.w3reforged.com/maps/categories/castle-defense/Zombie%20Defense%20by%20Eejin%20%26%20Frotty

### The Last Stand
Multiplayer post-apocalyptic survival game.
- Type: Survival
- Repository: https://github.com/jlfarris91/TheLastStand
- Features: Day/night cycle, base building

### Island Troll Tribes
Classic survival PvP game.
- Type: Survival/PvP
- Repository: https://github.com/island-troll-tribes/island-troll-tribes
- Features: Crafting, hunting, tribal warfare

### Forest Defense
Cooperative tower defense with survival elements.
- Type: Tower Defense
- Repository: https://github.com/Frotty/ForestDef
- Features: Wall building, wave survival

### Escape Builder Reloaded
In-game maze builder and escape game.
- Type: Escape/Builder
- Repository: https://github.com/Frotty/EBR
- Features: Level editor, custom escapes

### Snowball Fight
Team-based winter skirmish game.
- Type: Arena
- Repository: https://github.com/Frotty/Snowball-Fight
- Features: Simple mechanics, team play

### Escape the Crab
Escape game set in ancient Japan.
- Type: Escape
- Repository: https://github.com/Frotty/EscapeTheCrab
- Features: Boss mechanics, cooperative play

### Microrunner TD
Tower defense with micro-controlled runner.
- Type: Tower Defense
- Repository: https://bitbucket.org/Cokemonkey11/microrunnertd/src
- Features: Micro control, TD hybrid

### Battle Planes (The Gorge)
Battleships-style AoS game.
- Type: AoS
- Repository: https://bitbucket.org/Cokemonkey11/the-gorge/src
- Features: Auto-weapons, vehicle combat

### Gold Rush
Tag-style FFA collection game.
- Type: Mini-game
- Repository: https://github.com/HannesHaglund/Gold-Rush
- Features: Role switching, gold collection

### Hunter's Hall
Team-oriented objective-based PvP.
- Type: PvP Arena
- Repository: https://bitbucket.org/Cokemonkey11/hunters-hall/
- Features: Short rounds, objectives

### Sheep Tag Relapse
Modern Sheep Tag implementation.
- Type: Tag Game
- Repository: https://github.com/W3Madhatters/Sheep-Tag-Relapse

### Gods' Arena
PvE hero survival against gods.
- Type: Boss Arena
- Repository: https://github.com/Overkane/gods-arena
- Features: Boss fights, item shop

### Twilight's Eve Final Fixxed
Reworked classic map.
- Type: RPG
- Repository: https://github.com/Code-Fixxers/teve_final_fixxed
- Website: https://tever.xyz/tevef

## Code Patterns from Community

### Entity System (Frentity style)
```wurst
import Entity

class Missile extends Entity
    angle direction
    real speed
    
    construct(vec3 pos, angle dir, real spd)
        super(pos)
        direction = dir
        speed = spd
    
    override function update()
        pos += direction.toVec(speed * ANIMATION_PERIOD).withZ(0)
        if outOfBounds()
            terminate()
```

### Event-Driven Architecture
```wurst
import ClosureEvents

// Centralized event handling
init
    EventListener.add(EVENT_PLAYER_UNIT_DEATH) ->
        onUnitDeath(GetTriggerUnit(), GetKillingUnit())
    
    EventListener.add(EVENT_PLAYER_UNIT_PICKUP_ITEM) ->
        onItemPickup(GetTriggerUnit(), GetManipulatedItem())

function onUnitDeath(unit dying, unit killer)
    // Handle death globally
    
function onItemPickup(unit picker, item itm)
    // Handle item pickup
```

### Configuration Pattern
```wurst
package SpellConfig

// Configurable values
@configurable public constant DAMAGE = 100.
@configurable public constant COOLDOWN = 10.
@configurable public constant RANGE = 600.

// Config package
package SpellConfig_config
@config public constant DAMAGE = 150.  // Override default
```

### Unit Indexing
```wurst
import HashMap

let unitData = new HashMap<unit, UnitData>()

class UnitData
    unit u
    int customValue
    
    construct(unit u)
        this.u = u
        unitData.put(u, this)
    
    ondestroy
        unitData.remove(u)

function getUnitData(unit u) returns UnitData
    return unitData.get(u)
```

## Contributing to Community

### Create a Library
1. Create GitHub repository
2. Add `wurst.build` with no dependencies (or minimal)
3. Document API with hotdoc comments
4. Share on Discord

### Share Your Map
1. Make repository public
2. Include clear README
3. Post on Discord or Hive Workshop
4. Submit to wurstscript.github.io

### Report Issues
- Compiler bugs: https://github.com/wurstscript/WurstScript/issues
- Stdlib issues: https://github.com/wurstscript/WurstStdlib2/issues
- Docs improvements: https://github.com/wurstscript/wurstscript.github.io/issues

### Help Others
- Answer questions on Discord
- Review pull requests
- Write tutorials
- Create example code

## Getting Help

### Discord (Recommended)
https://discord.gg/mSHZpWcadz
- #help channel for questions
- #showcase for sharing work
- #development for advanced topics

### Hive Workshop
https://www.hiveworkshop.com/
- Map hosting
- Community forums
- Resource sharing

### GitHub Discussions
https://github.com/wurstscript/WurstScript/discussions
- Long-form questions
- Feature requests
- General discussion

## Learning Path

1. **Start**: Complete beginner guide
2. **Learn**: Read manual sections as needed
3. **Practice**: Modify example maps
4. **Build**: Create simple spells/systems
5. **Study**: Read stdlib source code
6. **Share**: Post work on Discord
7. **Contribute**: Help others, submit PRs
