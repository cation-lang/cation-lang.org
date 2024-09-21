+++
description = "Cation Language Design"
template = "page.html"
+++

# Cation Language Design

We design Cation as a pure categorical programming language for practical tasks.

In real-world computing, unlike mathematics, everything is bounded: resources, computational time; there are no
infinities, no arbitrary-precision rational or irrational numbers (but just their partial estimates). Most of existing
programming languages ignored this simple fact, resulting in hundreds of pitfalls and <q>footguns</q> for the developers.
Cation tries to avoid this, and ensures that everything is bound and measurable at compile-time. It does this using
instruments of category theory, and as a result each Cation program can pass a termination analysis and formaly
prove maximum amount of resources it takes to execute.

## Language features

Cation is made for practical tasks: we attempt to make it easy-to-read and write, avoiding as much of boilerplate code
as possible. There are just five built-in keywords; monadic programming is effortless: one does not need to declare set
of possible error types ahead. Cation has indentation-based syntax, which makes it much more visually readable and sets
it aside from curly-braced languages like C/C++, Rust, Scala... and makes it similar to Idris, Haskell, Python.

It has the following distinguishing features, which taken together set it aside from other existing languages:

1. Termination analysis and eager evaluation, implying compile-time-defined bounds for data types
2. Generalized algebraic data types and dependent types
3. Support of metaprogramming with `@`-attributes operating like macros
4. Parallel computation by default ("multi-way computing")
5. Doesn't require a runtime, doesn't use a garbage collector
6. Data types, functions and variable instances have no difference: everything is an expression
7. Each expression corresponds to a natural transformation
8. Program compilation is an evaluation of expressions
9. Designed for the creation of domain-specific languages

### Mutability and racing conditions

There are no variables, just values.

### Exception handling

### Memory addressing

## Program structure

On the high level, the Cation program consists of (optionally annotated) **statements**, which can be either
**specifiers** or **expressions**.

<aside>
  <p>Since specifiers are evaluated during compilation to expressions, everything is an expression, and each expression
  resolves to a natural transformation. Natural transformations always compose with each other, any program in Cation
  evaluates to just a single natural transformation, i.e. program compilation is equal to the process of evaluating all
  expressions.</p>
</aside>

Specifiers are evaluated by the compiler to sets of expressions, serving the primary purpose of reducing boilerplate
code and simplifying human readability and writability of the language.

### Specifiers

Specifiers consist of a **declaration**, optionally followed by a **definition**, which may contain **type** and
**body** information. Definition bodies, when present, are made of a sequence of statements.

Declaration starts with one of keywords, either build-in (`data`, `fx`, `infx`, `let`, `alias`) or added as language
extensions by domain-specific languages on top of Cation (like [Contractum]). The declaration keyword is followed by
a declaration name, which can be used later as a part of expressions throughout Cation code.

<aside>
  <p>Declaration name can be any valid Unicode string; however, it must always start with a non-numeric character, must not
  contain any already defined names made of math operators (for instance <code>some+method</code> would be an invalid
  name if the name <code>+</code> was defined before with some other meaning) and must put names made with math
  operators in backquotes.</p>
</aside>

The overall structure of a specifier is the following: `<KEYWORD> <NAME> <TYPE>? <BODY>?`. Type, when present, is
separated from the name with a semicolon operator; body, when present, is separated from the previous parts either with
`:=` operator, when a body is given on the same line, or a newline character, with all body statements indented.

Some examples of specifiers are:
- data type specifier: 
  ```
  data Coord2D: x U64, y U64
  ```
  where `data Coord2D` is a *data type declaration*, separated with colon from the type information, 
  which is `x U64, y U65`. Data type specifiers contain no body.
- value specifier:
  ```
  let value: U8 := 5
  ```
  where `let value` is a *value declaration*, separated with colon from the type information, which is `U8`. Next,
  the body contains the value assigned to the named value instance. Value specifications may have data type omitted in
  situations when it can be inferred; for instance, we can also write the specifier above as `let value := 5#U8`, where
  `#U8` is a type annotation.

<aside>
  <p>Here we can see several things:</p>
  <ul>
    <li>declaration name use mathematical symbol of addition and thus backquoted;</li>
    <li>type information consists of input and return type, separated with <code>-></code> operator.</li>
  </ul>
</aside>

- function specifier (infix variant):

  ```
  infx `+`: a Coord2D, b Coord2D -> Coord2D
    let x := a.x + b.x
    let y := a.y + b.y
    x, y
  ```
 
  which can be also written as a one-liner 
  ```
  infx `+`: a Coord2D, b Coord2D -> Coord2D := a.x + b.x, a.y + b.y
  ```

### Expressions

Everything which is not a specifier is an expression. Expressions can be seen as mathematical statements, or a natural
transformations.

In Cation, data types, values and functions are all first-class citizens, such that all of them may participate
expressions.

Expressions are made of named entities, composed together according to the compatibility rules using functions.
Expressions are separated either with a line feed character (U+000A), or with a semicolon `;` if put on one line one
after the other.


## Language syntax

### Fundamental operators

<aside>
  <p>Operator is a function which name is made of mathematical symbols.</p>
  
  <p>Below we will discuss more about fundamental
  operators which are a backbone of the expression syntax.</p>
</aside>

Built-in operators are called **fundamental operators**. They are used for basic expression composability and constitute
a backbone of the expression syntax.

There are three sets of fundamental operators: categorical limits, projection and injection operators, iterating and
composition operators. These operators are used to structure execution flow: branching, looping, working with
collections etc.

#### Categorical limits

There are two fundamental operators defining categorical limits: product operator `,` (comma) and sum operator 
`|` (pipe). These operators are used to compose most of the code; for instance, arguments of a data type definition is
just an expression composing other data types as a categorical product; for instance, `data Coord2D: x U32, y U32`
makes 2D coordinate as a product of two 32-bit integers. Likewise, a co-product (*enum*) data types can be defined as
`data Maybe: none | some(X)`.

#### Projection operators

Projection operators <code>&nbsp;</code> and `.` allow to call a function providing it with arguments. The use of these
two forms depends on the function type and a number of its arguments. There are two types of functions, which affect the
operator: prefix functions (declared using `fx` keyword) and infix functions (declared with `infx` keyword). For prefix
operators one calls a function (i.e. does projection) by putting function argument after the function name, separating
them with a space: `someFn argument`. In fact, any prefix function can be seen as a function with just a single argument,
made of anonymous data type, composing all arguments together. Thus, a function of two arguments can be used in the
following manner:
```
fx twoArgsFn: x U32, y U32 -> U32

let coord := x U32, y U32
twoArgsFn coord 
```

As one may see, a data type can be seen as a function constructing an instance of that type, which emphasises that in 
Cation there is no difference between data types and functions.

The call of the prefix function can be reversed using a dot projection operator `.`: `coord.twoArgsFn`.

<aside>
  <p>One may notice that Cation language logo is made with <code>(+)</code> expression :)</p>
</aside>

Infix functions are put after their first argument, which allows more natural way of using mathematical operators and
alike. For instance, above we have declared ``infx `+` `` operator which takes two arguments and adds them together:
`a + b`. One may also reverse the order of the arguments passed to the infix operator by putting the call expression 
into parentheses: `(+ a, b)` or even `(+) a, b`.

#### Injection operators

Injection operators `>|`, `|?`, `|:` represent a way how to de-compose a categorical co-product into one of its source
components and pattern-match against them. In other words, injection operators are the way to branch the code execution;
they are the way how *if-else* statements and pattern matching is handled in the language.

There are several ways of doing branching constructions using injection operators. First, one can use `value >|` in
combination with `pattern |?` expressions to match the value against a set of patterns and handle a default variant
with `|:`:

<aside>
  <p>In comments we provide Rust equivalents</p>
</aside>

```
data WorldDirection: north | south | west | east

let sample: WorldDirection

sample >|                   – match sample {
  north |? doSomething1     –   north => {} 
  south |? doSomething2     –   south => {}
        |: doSomething3     –   _ => {}
                            – }
```

<aside>
  <p>Boolean type in Cation is a co-product type, thus any boolean condition can use injection for conditional branching</p>
  <p>One may clearly see similarities with ternary operator <code> .. ? .. : .. </code> &nbsp;in C++; however, in Cation
  the operator clearly maintains similarity with co-product categorical composition operator <code>|</code> and
  signifies injections of the co-product value into each of its components.</p>
</aside>

Next, one can use a boolean expression followed with `|?` to express *if* semantics, and use `|:` for handling *else*
condition. Let's assume we have an expression `test` which results in a boolean value, and two expressions, `ifTrue` and
`ifFalse` which we'd like to execute basing on the `test` value. To do so we simply write `test |? ifTrue |: ifElse`. 

```
sample =? north             – if sample == north
|? doSomething1
|: doSomething2             – else

sample =? north             – if sample == north
|? doSomething1
|: sample =? south          – else if sample == south
|? doSomething2             
|: doSomething3             – else
```

#### Composition operators

Operators `( )`, `[ ]` and `{ }` are called composition operators; they are used to create types via categorical
composition of existing types, i.e. act as natural transformations defining new functors (as type constructors) from
existing ones.

<aside>
  <p><code>(,)</code> is the initial object in the category $\mathbf{Cation}$ of all Cation data types. It corresponds to
  *unit* type in other languages, like <code>()</code> in Rust or <code>void</code> in C/C++. <code>(|)</code> is the
  terminal object corresponding to *never* type or *uninhabited* enums in other languages, like <code>!</code> or
  <code>enum Impossible {}</code> in Rust.</p>
</aside>

Round parenthesis can be used for grouping expressions, such that the boundaries of composition operation are
explicitly provided. When used with no values, they also can be used to represent initial object (unit) `(,)`, as
well as terminal object (impossible type) `(|)`.

Square brackets are used for creating ordered non-unique **collection types**. Collection type is a type composed
dynamically (via production operation) of some elementary data types – which in other languages is called a *list*
or an *array*. Finally, curly braces are used for creating ordered unique collection types – *sets* and *maps*.

In the type definition each of the collection types is required to have a specification of the minimum and maximum
allowed number of elements – a mechanism by which compiler can perform a termination analysis and enforce boundaries
and compile-time. This is done with **power operator** `^` and **range operators** `..=` and `..<`. Power operator
denotes that the data type of collection element is potentiated, and the range operator specifies limits of how
many times the self-product of the element type may happen. For instance, `[ U8 ^ ..<256 ]` is an array which may
contain from zero up to 255 elements of unsigned 8-bit integer type.

#### Iterating operators

<aside>
  <p>In Cation a function and an operator may have multiple aliases. For instance, mathematical and fundamental
  operators have an ASCII and Unicode aliases, and both can be used in code. For iterating operators, Unicode
  version is <code>|></code>, while ASCII is 
  <code style="padding-right: 0">|</code><code style="padding-left: 0.5pt">></code></p>
</aside>

Iterating operators are used not just for data type specifications, but also for enabling code repetitions
(cycles and loops), iterators and generators. For this purpose iterating operators `<|` and `|>` are used.
The semantic meaning of the expression becomes <q>take each element of a collection and bring it as a value to the other
expression</q>. The collection for `<|` should be put right-side and expression processing its values left-side,
while for `|>` the order is reversed. Thus, `0..<100#U8 |> print` will iterate over range of unsigned 8-bit integers
from zero to 100 and print each of them. When the iterating operator is combined with a composition operator, this means
<q>put the resulting values from the expression into a new collection</q>: for instance `[0..<100#U8 |> pow 2]` would
collect all squared numbers from 0 to 99 into a new list.

When working with loops using iterating operators one may need to access the current value from the iterator in explicit
form, as well as to use a results from previous rounds of the iterator. This can be done with `_` and `$` operators:
`_` is the **context-defined value** operator holding the current input for the iteration, while `$` is the **result
operator** giving access to the output values of the previous iterations. For instance, to access the previous
result one needs to write `$-1`, and to access the value result of the very first iteration, `$0` (`$1` will give
result of the second iteration etc.). Result operator `$` can be also used to assign an output of the iteration or a
function by writing `$ <- value`.

#### Tags and annotations

Operator `#` adds context tags to the values; for instance it is used to add integer tag to co-product type variants
(like in `data Bool: false#0 | true#1`) or to enforce some literal to be of a specific data type (like in `..100#U8`,
where the range is enforced to be over `U8` type).

Operator `@` is used to annotate Cation statements. Annotations are a way of metaprogramming: they are similar to
procedural macros in Rust or annotations in Java.


### Data types

Data types are made with GADT and category theory:

* primitive data types all are countable and finite sets;
* compositional data types are products (using `,` operator) or coproducts (`|` operator) of primitive types (sets).

Data type names are capitalized; their definition starts with a `data` keyword, followed by the type name and
compositional semantic after a colon, which can be seen as a constructor definition or as a product/coproduct
functor over categories creating new given data type as a new category. For instance, `data Coord : x U32, y U32`
defines category $Coord$ (2D coordinate) as a self-product of categories $U32$ (all 32-bit natural numbers)
$Coord = U32 \times U32$ having two corresponding projective morphisms $x: Coord \mapsto U32$ and $y: Coord \mapsto U32$

Cation comes with a very small set of built-in data types; the most of the types used in everyday programming are
defined in the standard library.

#### Built-in types

There are four main classes of built-in types: 

<aside>
  <p>&nbsp;</p><p>You can learn more about type classes below</p>
</aside>

- **unit** `(,)` and **never** type `(|)`, which we have covered above;
- **integer numbers**: signed, unsigned and non-zero unsigned<br/>(type classes `int`, `unsigned`, `signed` and `nonzero`);
- **floating-point numbers** (type class `float`);
- **Unicode character** `Char` type;
- **range** types.

<aside>
  <p>Unicode character type <code>Char</code> is the only type having variable bit length, which is caused by the
  Unicode standard. Its length varies from 8 bits to 32 bits; with 8 bit step.</p>
</aside>
 
Integers and floats offer many times with different bit length; float types also offer different memory encodings.
Unicode character has variable bit length.

Supported bit length for **integer types** are:

<aside>
  <p>&nbsp;</p>
  <p><sup>*</sup> Rust <a href="https://crates.io/amplify"><code>amplify</code></a> library offers many of the integer
  types present in the Cation language</p>
</aside>

| Bits      | Bytes     | Unsigned |  Signed | Non-zero | C equivalents            | Rust equivalents *            | 
|-----------|-----------|---------:|--------:|---------:|--------------------------|-------------------------------|
| 8 bits    | 1 byte    |     `U8` |    `I8` |     `N8` | `(unsigned)` `char`      | `u8`, `i8`, `NonZeroU8`       |
| 16 bits   | 2 bytes   |    `U16` |   `I16` |    `N16` | `(unsigned)` `short`     | `u16`, `i16`, `NonZeroU16`    |
| 24 bits   | 3 bytes   |    `U24` |   `I24` |    `N24` | n/a                      | n/a                           |
| 32 bits   | 4 bytes   |    `U32` |   `I32` |    `N32` | `(unsigned)` `long`      | `u32`, `i32`, `NonZeroU32`    |
| 40 bits   | 5 bytes   |    `U40` |   `I40` |    `N40` | n/a                      | n/a                           |
| 48 bits   | 6 bytes   |    `U48` |   `I48` |    `N48` | n/a                      | n/a                           |
| 56 bits   | 7 bytes   |    `U56` |   `I56` |    `N56` | n/a                      | n/a                           |
| 64 bits   | 8 bytes   |    `U64` |   `I64` |    `N64` | `(unsigned)` `long long` | `u64`, `i64`, `NonZeroU64`    |
| 80 bits   | 10 bytes  |    `U80` |   `I80` |    `N80` | n/a                      | n/a                           |
| 96 bits   | 12 bytes  |    `U96` |   `I96` |    `N96` | n/a                      | n/a                           |
| 112 bits  | 14 bytes  |   `U112` |  `I112` |   `N112` | n/a                      | n/a                           |
| 128 bits  | 16 bytes  |   `U128` |  `I128` |   `N128` | n/a                      | `u128`, `i128`, `NonZeroU128` |
| 256 bits  | 32 bytes  |   `U256` |  `I256` |   `N256` | n/a                      | n/a                           |
| 512 bits  | 64 bytes  |   `U512` |  `I512` |   `N512` | n/a                      | n/a                           |
| 1024 bits | 128 bytes |  `U1024` | `I1024` |  `N1024` | n/a                      | n/a                           |

All integer types in Cation are co-product types, made with all their allowed values. This makes it possible to use
[injection operators](#injection-operators) to match them against patterns and ranges.

Supported bit length and encodings for **floating-point types** are:

| Type name | Bytes | Encoding          | Underlynig Rust type               |
|-----------|-------|-------------------|------------------------------------|
| `F16B`    | 2     | bfloat16          | `bfloat::bf16`                     |
| `F16`     | 2     | IEEE Half         | `apfloat::ieee::Half`              |
| `F32`     | 4     | IEEE Single       | `apfloat::ieee::Single`            |
| `F64`     | 8     | IEEE Double       | `apfloat::ieee::Double`            |
| `F80`     | 10    | IEEE X87 Extended | `apfloat::ieee::X87DoubleExtended` |
| `F128`    | 16    | IEEE Quad         | `apfloat::ieee::Quad`              |
| `F256`    | 32    | IEEE Oct          | `apfloat::ieee::Oct`               |

**Range types** simplify creation of [collection types](#iterable-collections), described below, as well as are an
efficient tool in building [cycles and iterators](#iterating-operators). Cation comes with the following set of
range types, each of which can be instantiated using a shorthand **range operator**.

| Type name         | Range operator |
|-------------------|----------------|
| `RangeAll`        | `..`           |
| `RangeTo`         | `..<N`         |
| `RangeToIncl`     | `..=N`         |
| `RangeFrom`       | `M..`          |
| `RangeFromTo`     | `M..<N`        |
| `RangeFromToIncl` | `M..=N`        |

#### Data type composition

New data types in Cation are constructed via composition of built-in types using categorical product and co-product
operators `,` and `|` described above. Here are the requirements for the syntax of these operators: 

| Name                             | Syntax form                  | Max no of fields or variants |
|----------------------------------|------------------------------|------------------------------|
| Product types (structure, tuple) | `• , •` _or_ `(• , •)`       | 256                          |
| Sum types <br>(union, enum)      | `• `\|` •` _or_ `(• `\|` •)` | 256                          |


#### Iterable collections

One may use [composition operators](#composition-operators) to build arrays and dynamic collections, which may be
iterated over using [iterating operations](#iterating-operators). Cation provides built-in support for the following 
collections:

| Name         | Syntax form      | Max no of elements / fields / variants |
|--------------|------------------|----------------------------------------|
| Fixed array  | `[•^N]`          | `N`, up to 2^16                        |
| Dynamic list | `[• ^D..<U]`     | From `D` to `U`, up to 2^64            |
| Dynamic set  | `{• ^D..<U}`     | From `D` to `U`, up to 2^64            |
| Dynamic map  | `{• -> ^D..U •}` | From `D` to `U`, up to 2^64            |

Range constriction `^D..=U` used in type expression specifying minimum and maximum size of a dynamic collection is 
called **confinement bounds**. It can be seen as an upper and lower indexes on the possible number of elements, i.e.
type definition `[Byte ^ 1..20]` can be read as $\bigotimes^{20}_1 byte$ and means product type with dynamic number of
fields, from 1 to 20 max, where each field is a byte – or, in more common terms, a byte array of dynamic size which
can't have less than one byte – and can't grow larger than 20 bytes.

For simplifying syntax strict encoding provides comprehensions and defaults for specifying the confinement bounds:

<aside>
  <p>Please note that type expression of <code>[•^N..=N]</code> is not allowed, since it means <q>dynamic</q> collection
  with a fixed number of items, which is nonsense, so please use <code>[•^N]</code> instead.</p>
</aside>

| Comprehension         | Expands to         | Comment                                                                 |
|-----------------------|--------------------|-------------------------------------------------------------------------|
| `[•]`                 | `[• ^ 0..=0xFFFF]` | Default number of elements in confined collections is from zero to 2^16 |
| `[•+]` _or_ `[•^1..]` | `[• ^ 1..=0xFFFF]` | Collection which must contain at least one item                         |
| `[•^N]`               | `[• ^ N..=0xFFFF]` | Collection with minimum of `N` items                                    |
| `[•^..=N]`            | `[• ^ 0..=N]`      | Collection having maximum of `N` items                                  |

#### Standard library types

Standard library offers many types composed of the built-ins, including (but not limiting to):
- Restricted-bit-length integers, like `U1`, `U2`, `U3`, `U4`, `U5`, `U6`, `U7`;
- ASCII char subsets `AsciiChar`, `AsciiPrintable` and others;
- Strings: Unicode strings `String`, ASCII string `AsciiString`;
- Bytes, fixed and dynamic byte arrays: `Byte`, `Bytes16`, `Bytes32`, `Blob256`, `Blob64kB`, `Blob16MB` `Blob4GB` etc;
- Commonly-used monads `Maybe`, `Either`, `Result`, `Ternary`.

### Lambda expressions

<aside>
  One of Cation's main features is an ability to treat data, values and functions the same way, making all of them
  first-class citizens.
</aside>

<dfn>Lambda expressions</dfn> allow a combination of statements, capturing locally-defined values,
to be treated as a function. They simplify passing functions as arguments to other functions, saving
from boilerplate code.

Lambda expressions have several forms. The canonical one is a specifier form, which starts with a `lambda` keyword,
followed by a value name, colon, argument and return type definition and body:

```
let local: U8 = random
lambda sq: x U8 -> U32
    pow 2 + local
```

As any other specifier it can be put into a single line:
```
let local: U8 = random
lambda sq: x U8 -> U32 := pow 2 + local
```

<aside>
  The <code>.\</code> operator is shaped after Greek letter $\lambda$.
</aside>

The second form is an operator form using `.\` or Greek `λ`; it allows anonymous lambda definitions which may span
a single or multiple lines. The specific syntax of this form depends on whether the lambda is the last expression in
the line:

```
-- for the last expression in the line
.\pow 2
-- or even shorter
.\^2
```

If a lambda expression has to be followed by other expressions, one need to put the expression into a parenthesis:
```
.\(pow 2), _
```

Lambda expressions may have own explicit arguments, separated from the function body using double-column. The return
type may be omitted, and in this case if is inferred by the compiler from the body of the expression:
```
.\x U8 -> U16: x pow 2

-- With a type omission:
.\x U8: x pow 2

-- If the expression is not at the end of a line
.\(x U8: x pow 2), _
```

Finally, there is the multiline forms of lambda expressions:
```
let lambdaFn := .\
    statement1
    statement2
```

In can be used the same way as single-line expressions, with the only difference – there is no colon between the lambda
declaration and the body:
```
.\ x U8
   x pow 2

.\ x U8 -> U16
   x pow 2

-- Here we can use multiline in the middle of other expression
someFn arg1, .\(x U8 -> U16
   x pow 2
), argLast
```

The type of the lambda expression is `.\ input -> output`, and this type may be used in type annotations:
```
let squared: .\U8 -> U16 := .\pow 2
```

<!-- TODO: add on currying:
Functions with multiple arguments is the same as a function with single argument made of anonymous data type composing
the list of the arguments. -->


### Generics

Cation supports parametric polymorphism via type classes, generics and confinement constraints.

#### Type classes

Type classes are the same as classes in Haskell or traits in Rust: they provide a simple way to introduce generics into
programming, without the need for complex inheritance rules.

Type classes are declared with `class` keyword followed by a lowercase name; here is an example how `ord` and `eq`
classes are defined in the standard library:

<aside>
  <p>Cation tries to use semantically and mathematically correct operator symbols. Thus, instead of using simple
  <code>></code> operator, which states that the left value is greater than one, we add a question mart to it,
  applying semantic of test.</p>
</aside>

```
class ord
  infx `>?`: Self, Self -> Bool
  
  @final
  infx `<?`: a Self, b Self -> Bool
    ~ a >? b

class eq: ord
  infx `=?`: Self, Self -> Bool

  @final
  infx `=!`: a Self, b Self -> Bool
    ~ a =? b
  
  @final
  infx `>=?`: a Self, b Self -> Bool
    a =? b |? true |: a >? b
  
  @final
  infx `<=?`: a Self, b Self -> Bool
    a =? b |? true |: a <? b
```

With declarations, all types which has infix `>?` operator taking two arguments of the same type and returning bool
will become `ord` and acquire the opposite infix operator `<?`.

#### Generic arguments

<aside>
  <p>In terms of category theory, generic arguments represent a co-product of all generic types, and `=>` operator is a
  natural transformation from the functor constructing that co-product to the function itself as a functor mapping
  category of its composed input type to category of its composed output type.</p>
</aside>

Functions and data types can be equipped with generic arguments between colon separating function name from its input
arguments, and `=>` character:

```
fx len: T any, N unsigned => collection [T ^ $N..$N] -> N
```

Each generic argument consists of generic parameter name – usually capitalized – and a constraint expressed as a
type class name to which the type must belong. Multiple generic parameters should be separated by a comma.

#### Confinement constraints

<aside>
  <p>Confinement constraints make Cation to fully support generalized algebraic data types (GADT) and ensure at
  compile-time absence of stack overflows and out-of-memory bounds access</p>
</aside>

When collections are used inside functions sometimes it is important to make sure that the concrete collection types
provided to the function in different input or output arguments match their dimensions. This is achieved with a
special form of generics named **confinement constrains**:

```
fx append: N unsigned => 
  list [String ^ ..$N], item String -> [String ^ ..$N+1]
```

##  Computational parallelism

Cation compiler will be designed to reason about the code logic at compile-time, which is possible due to constraints
and bounds on all resources used by the language. During compilation, it deduces the graph of all relations between
values used in the program, and uses that graph to automatically parallelize all computations which do not access the
same values at the same time. Thus, most of the parallelism will come for free, with no effort from the programmer.

<aside>
  <p>Specific syntax for channel-based concurrency programming and computational parallelism is still work in progress</p>
</aside>

When an explicit parallelism is needed, Cation offers channel-based parallelism, made with rho-calculus. Thus, instead
of a need to differentiate async and non-async functions one uses non-blocking channels to send data by value. Since
there is no difference between data, types, functions and everything is just a natural transformations, channels can
send also functions between threads. This significantly simplifies programming patterns and reduces the number of 
possible pitfalls,


## Language comparison

The main differences between Cation and other modern programming languages with strong functional properties are given
in the table:

| Language | Absent in the language<br/>(but present in Cation)                                   | Features used in Cation                              |
|----------|--------------------------------------------------------------------------------------|------------------------------------------------------|
| Haskell  | Termination analysis, parallel computing, GADT, "infinite types", absence of runtime | Monads                                               |
| Idris v2 | Parallel computing, absence of runtime                                               | GADT, termination analysis, monads, eager evaluation |
| Scala v2 | Termination analysis, absence of runtime, types are not first-class citizens         | GADT, eager evaluation, monads                       |
| Rust     | Termination analysis, parallel computing, GADT, types are not first-class citizens   | No runtime                                           |



[Contractum]: https://www.contractum.org
