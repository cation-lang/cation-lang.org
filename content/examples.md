+++
description = "Cation Language Examples"
template = "page.html"
+++

# Cation Language Examples

## Example programs

### Fibonacci numbers

```
fx fib: n U64 -> U128
    0..=n |>
        $-1? + $-2? ?? 1
```

or, as a shortened one-liner:
```
fx fib: n U64 -> U128 := ..=n |> $-1? + $-2? ?? 1
```

### Hello world

```
fx main := println "Hello world"
```

## Use of specific statements

### Data type definitions

```
data Byte: U8
data Bool: false#0 | true#1

data Year: I16

data ShortMonthDay: #1 | .. | #30
data FullMonthDay: #1 | .. | #31
data FebMonthDay: #1 | .. | #29

data MonthDay: jan(FullMonthDay)  | feb(FebMonthDay)   | mar(FullMonthDay)
             | apr(ShortMonthDay) | may(FullMonthDay)  | jun(ShortMonthDay)
             | jul(FullMonthDay)  | aug(FullMonthDay)  | sep(ShortMonthDay)
             | oct(FullMonthDay)  | nov(ShortMonthDay) | dec(FullMonthDay)

data Date: year Year, monthDay MonthDay
```

Using generics:
```
data Maybe: T any => none | some(T)
data Result: T any, E error => ok(T) | err(E)
```

### Functions

```
infx `+`: a Coord2D, b Coord2D -> Coord2D
  val x := a.x + b.x
  val y := a.y + b.y
  x, y
```

### Code branching

```
data WorldDirection: north | south | west | east

val sample: WorldDirection = random

sample >|
  north |? println("I am northman") 
  south |? println("I am at south!")
        |: println("I am nowhere :)")
```

### Collections and ranges

```
data String256 = [Char ^ ..=0xFF]

data Bytes32 = [Byte ^ 32]
data ByteList256 = [Byte ^ ..=0xFF]
data ByteList64kb = [Byte ^ ..=0xFFFF]
data ByteListNonEmpty256 = [Byte ^ 1..=0xFF]

data SpecialDates = {MonthDay ^ ..=365}
-- maps dates to a sets of up to 256 friend names who has the birthday that date
data Birthdays = { MonthDay -> ^ ..=365 {String256 ^ ..=0xFF} }
```

### Iterators

```
class iter: item any
    fx next: _ -> item?

infx findFirst: V eq, I iter(item eq) => iter I, value V -> U64?
    enumerate iter |> fst =?= value |? $ := lst _

infx hasUnique: V eq, I iter(item eq) => iter I, value V -> Bool
    val index := iter findFirst value !! false
    (iter[index..] |> =?= value |? $ := false) ?? true
    
[0, 1, 2, 3, 4] hasUnique 1 -- true
```

### Collection comprehensions

```
val congratulations :=
    [ "Happy birthday, \(_)" <| birthdays.values.flatten ]
```

### Monadic operators

```
  fx sumOrZero: x U32, y U32 -> U32
    x +? y ?? 0 -- here we return zero on overflow

  val x, y: U32, U32 := random, random
  val sum = x +? y !! overflow -- here we return Err(overflow) on overflow
  sumOrZero sum, x
```

### Type classes

```
class ord
  infx `>?`: Self, Self -> Bool
  
  @final
  infx `<?`: a Self, b Self -> Bool
    ~ a >? b

class eq: ord
  infx `=?=`: Self, Self -> Bool

  @final
  infx `=/=`: a Self, b Self -> Bool
    ~ (a =?= b)
  
  @final
  infx `>?=`: a Self, b Self -> Bool
    a =?= b |? true |: a >? b
  
  @final
  infx `<?=`: a Self, b Self -> Bool
    a =?= b |? true |: a <? b
```

