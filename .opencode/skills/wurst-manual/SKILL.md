---
name: wurst-manual
description: WurstScript language reference covering syntax, types, classes, modules, generics, lambdas, and all language features for Warcraft 3 modding
---

# WurstScript Language Manual

WurstScript is an imperative, object-oriented, statically-typed, beginner-friendly programming language for Warcraft III modding. It compiles to both Jass and Lua.

## Values and Types

WurstScript is statically typed with these basic types:

```wurst
boolean b = true    // true or false
int i = 1337        // integer without decimals
real r = 3.14       // floating point value
string s = "hello"  // text
```

## Syntax

WurstScript uses indentation-based syntax (similar to Python). Use spaces or tabs consistently.

```wurst
if condition
    ifStatements
nextStatements
```

Newlines are ignored:
- After `(` or `[`
- Before `)`, `]`, `.`, `..`, or `begin`
- Around `,`, `+`, `*`, `-`, `div`, `/`, `mod`, `%`, `and`, `or`, `?`, `:`

## Packages

All code must be inside a package:

```wurst
package MyPackage
import SomeOtherPackage

public int globalVar = 5

init
    print("Package initialized")
```

- Use `public` to export declarations
- Use `import` to access other packages
- Use `import public` to re-export imports
- Use `initlater` for cyclic dependencies

## Functions

```wurst
function max(int a, int b) returns int
    if a > b
        return a
    else
        return b

function printMax(int a, int b)
    print(max(a, b).toString())
```

## Variables

```wurst
constant x = 5              // immutable, type inferred
let x2 = 25                 // immutable shorthand
var y = 5                   // mutable
int z = 7                   // explicit type
int array a                 // array
int array b = [1, 2, 3]     // initialized array
```

## Expressions

```wurst
// Arithmetic: +, -, *, /, div (int division), %, mod
// Boolean: and, or, not
// Comparison: <, <=, >, >=, ==, !=
// Ternary: condition ? ifTrue : ifFalse
// Cascade: obj..method() returns receiver
// Cast: expr castTo Type
// Instance check: expr instanceof Type
```

## Control Flow

### If Statement
```wurst
if x > y
    print("greater")
else if x < y
    print("less")
else
    print("equal")
```

### Switch Statement
```wurst
switch i
    case 1
        print("one")
    case 2 | 3
        print("two or three")
    default
        print("other")
```

### Loops
```wurst
while a > b
    // while loop

for i = 0 to 10
    // for loop

for i = 10 downto 0 step 2
    // reverse with step

for u in someGroup
    // iterate (keeps items)

for u from someGroup
    // iterate and remove items
```

## Classes

```wurst
class Missile
    vec2 pos
    real speed

    construct(vec2 pos, real speed)
        this.pos = pos
        this.speed = speed

    function move()
        pos += vec2(speed, 0)

    ondestroy
        flashEffect("explosion.mdx", pos)

// Usage
let m = new Missile(vec2(0, 0), 10)
m.move()
destroy m
```

### Inheritance
```wurst
class FireballMissile extends Missile
    construct(vec2 pos)
        super(pos, 20)  // call parent constructor

    override function move()
        super.move()
        createFireTrail()
```

### Static Members
```wurst
class Counter
    static int count = 0
    
    static function increment()
        count++

// Usage
Counter.increment()
print(Counter.count.toString())
```

### Visibility
- Default: visible everywhere
- `private`: only within class
- `protected`: within package and subclasses

## Abstract Classes

```wurst
abstract class GameObject
    abstract function update()

    function tick()
        update()

class Unit extends GameObject
    override function update()
        // implementation required
```

## Interfaces

```wurst
interface Damageable
    function takeDamage(real amount)
    
    function isAlive() returns boolean  // can have default implementation
        return true

class Building implements Damageable
    function takeDamage(real amount)
        // must implement
```

## Modules

Modules provide reusable functionality without inheritance hierarchy:

```wurst
module Movable
    vec2 pos

    function move(vec2 delta)
        pos += delta

class Unit
    use Movable
    // now has pos and move()
```

## Generics

```wurst
class Stack<T>
    private LinkedList<T> items = new LinkedList<T>()

    function push(T item)
        items.add(item)

    function pop() returns T
        return items.pop()

// Usage
let intStack = new Stack<int>()
intStack.push(5)
```

### Generic Functions
```wurst
function first<T>(LinkedList<T> list) returns T
    return list.get(0)
```

## Lambdas and Closures

```wurst
// Lambda expression
let pred = (int x) -> x mod 2 == 0

// Type inference
let pred2 = x -> x mod 2 == 0

// Multi-line lambda block
doAfter(5.0) ->
    print("5 seconds passed")
    doSomething()

// Capturing variables (closure)
let threshold = 10
myList.filter(x -> x > threshold)
```

## Extension Functions

Add methods to existing types:

```wurst
public function unit.kill()
    SetUnitState(this, UNIT_STATE_LIFE, 0)

public function real.squared() returns real
    return this * this

// Usage
myUnit.kill()
let area = radius.squared() * PI
```

## Tuple Types

Lightweight value types (no allocation):

```wurst
tuple vec2(real x, real y)

let v = vec2(10, 20)
let sum = v.x + v.y
```

## Enums

```wurst
enum State
    IDLE
    MOVING
    ATTACKING

let state = State.IDLE

switch state
    case IDLE
        print("idle")
    case MOVING
        print("moving")
    case ATTACKING
        print("attacking")
```

## Vararg Functions

```wurst
function sum(vararg int numbers) returns int
    var total = 0
    for n in numbers
        total += n
    return total

sum(1, 2, 3, 4, 5)  // works with any number of args
```

## Annotations

```wurst
@configurable public function getValue() returns int
    return 5

// In config package:
@config public function getValue() returns int
    return 10
```

## Compiletime Execution

```wurst
@compiletime function generateData()
    // runs at compile time
    for i = 0 to 100
        createUnit(i)
```

## Object Editing

Generate WC3 objects in code:

```wurst
@compiletime function createAbility()
    new AbilityDefinitionFireBolt('A001')
        ..setName("Super Bolt")
        ..setDamage(1, 100)
        ..setCooldown(1, 10)
```

## Best Practices

1. Use extension functions over nested calls
2. Use cascade operator `..` for chaining
3. Prefer `let`/`var` over explicit types
4. Use lambdas for callbacks
5. Follow stdlib patterns

## Resources

- Documentation: https://wurstlang.org/manual.html
- Standard Library: https://wurstlang.org/stdlib
- Discord: https://discord.gg/mSHZpWcadz
