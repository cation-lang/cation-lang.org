+++
description = "Cation Language Design"
template = "page.html"
+++

We design Cation as a pure categorical programming language for practical programming.

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

The main differences between Cation and other modern programming languages with strong functional properties are given
in the table:

|          | Absent in the language (but present in Cation)                                       | Features used in Cation                              |
|----------|--------------------------------------------------------------------------------------|------------------------------------------------------|
| Haskell  | Termination analysis, parallel computing, GADT, "infinite types", absence of runtime | Monads                                               |
| Idris v2 | Parallel computing, absence of runtime                                               | GADT, termination analysis, monads, eager evaluation |
| Scala v2 | Termination analysis, absence of runtime, types are not first-class citizens         | GADT, eager evaluation, monads                       |
| Rust     | Termination analysis, parallel computing, GADT, types are not first-class citizens   | No runtime                                           |

Cation has indentation-based syntax, which makes it much more visually readable and sets it aside from curly-braced
languages like C/C++, Rust, Scala... and makes it similar to Idris, Haskell, Python.

## Program structure

On the high level, the Cation program consists of (optionally annotated) **statements**, which can be either
**specifiers** or **expressions**.

Specifiers are evaluated by the compiler to sets of expressions, serving the primary purpose of reducing boilerplate
code and simplifying human readability and writability of the language.

Since specifiers are evaluated during compilation to expressions, thus, everything is an expression, and each 
expression resolves to a natural transformation. Natural transformations always compose with each other, any program in
Cation evaluates to just a single natural transformation, i.e. program compilation is equal to the process of evaluating
all expressions.

### Specifiers

Specifiers consist of a **declaration**, optionally followed by a **definition**, which may contain **type** and
**body** information. Definition bodies, when present, are made of a sequence of statements.

Declaration starts with one of keywords, either build-in (`data`, `fx`, `infx`, `let`, `alias`) or added as language
extensions by domain-specific languages on top of Cation (like [Contractum]). The declaration keyword is followed by
a declaration name, which can be used later as a part of expressions throughout Cation code.

The overall structure of a specifier is the following: `<KEYWORD> <NAME> <TYPE>? <BODY>?`. Type, when present, is
separated from the name with a semicolon operator; body, when present, is separated from the previous parts either with
`:=` operator, when a body is given on the same line, or a newline character, with all body statements indented.

Declaration name can be any valid Unicode string; however, it must always start with a non-numeric character, must not
contain any already defined names made of math operators (for instance `some+method` would be an invalid name if a 
name `+` was defined before with some other meaning) and must put names made with math operators in backquotes.

Some examples of specifiers are:
- data type specifier: 
  ```
  data Coord2D: x U64, y U64
  ```
  where `data Coord2D` is a *data type declaration*, separated with
  semicolon from the type information, which is `x U64, y U65`. Data type specifiers contain no body.
- function specifier (infix variant):

  ```idris
  infx `+`: a Coord2D, b Coord2D -> Coord2D
    let x := a.x + b.x
    let y := a.y + b.y
    x, y
  ```
  
  which can be also written as a one-liner 
  ```idris
  infx `+`: a Coord2D, b Coord2D -> Coord2D := a.x + b.x, a.y + b.y
  ```

  here we can see several things:
  - an example of variable (actually a constant) specification using `let` keyword. Variable specifications may have
    data type omitted in situations when it can be inferred; for instance, we can also write `x` specification with an
    explicit data type: `let x: U64 := a.x + b.x`
  - declaration name use mathematical symbol of addition and thus backquoted;
  - type information consists of input and return type, separated with `->` operator.

### Expressions

Everything which is not a specifier is an expression. Expressions can be seen as mathematical statements, or a natural
transformation.

In Cation, data types, values and functions are all first-class citizens, such that all of them may participate
expressions.

Expressions are made of named entities, composed together according to the compatibility rules using functions.

Functions made of mathematical symbols are also named **operators**. 

#### Fundamental operators

Built-in operators are called **fundamental operators**. They are used for basic expression composability.

There are three sets of fundamental operators: categorical limits, projection and injection operators, and comprehension
operators.

There are two fundamental operators defining categorical limits: product operator `,` (comma) and sum operator 
`|` (pipe). These operators are used to compose most of the code; for instance, arguments of a data type definition is
just an expression composing other data types as a categorical product (`data Coord2D: x U32, y U32` makes 2D coordinate
a product of two 23-bit integers). Thus, enum data types can be defined as `data Maybe: none | some(value X)`.

Projection operator allows to call a function providing it with arguments and has several forms, depending on the 
function type and a number of its arguments. There are two types of functions, which affect the operator: prefix
functions (declared using `fx` keyword) and infix functions (declared with `infx` keyword). For prefix operators
one calls a function (i.e. does projection) by putting function argument after the function name, separating them with
a space: `someFn argument`. In fact, any prefix function can be seen as a function with just a single argument, made
of anonymous data type, composing all arguments together. Thus, a function of two arguments can be used in the following
manner:
```idris
fx twoArgsFn: x U32, y U32 -> U32

let coord := x U32, y U32
twoArgsFn coord 
```

As one may see, a data type can be seen as a function constructing an instance of that type, which emphasises that in 
Cation there is no difference between data types and functions.

The call of the prefix function can be reversed using a dot projection operator `.`: `coord.twoArgsFn`


----

#### Data types

Data types are made with GADT and category theory:

* primitive data types are countable and finite sets;
* compositional data types are products (using `,` operator) or coproducts (`|` operator) of primitive types (sets).

Data type names are capitalized; their definition starts with a `data` keyword, followed by the type name and
compositional semantic after a double colon, which can be seen as a constructor definition or as a product/coproduct of
sets creating a given data type. For instance, `data Coord : x U32, y U32` defines set $\mathbf{Coord}$ (2D coordinate)
as a self-product of set $U32$ (all 32-bit natural numbers) $Coord = U32 \times U32$
having two corresponding projective morphisms  $x: Coord \mapsto U32$ and $y: Coord \mapsto U32$.
Thus, data type definition is a comprehension defining all of the above methods.

Data types use Strict types [Data type algebra](https://app.gitbook.com/s/-McPRmdXp1jTEY27B57G/type-system/data-primitives "mention")
and can be generic over its parameters. For example, `data Coord2D: x Num#1, y Num#1` defines a version of the coord
type which is generic over a specific type of the coordinate. `#1` is an example of tags, used in Cation expressions;
here this tag binds the concrete type used in the first argument to the concrete type of the second argument (i.e. both
coordinates must be of the same numeric type).


[Contractum]: https://www.contractum.org
