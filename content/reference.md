+++
description = "Cation Language Reference"
template = "page.html"
+++

# Cation Language Reference

## Lexical structure

The lexical structure of Cation describes what sequence of characters form valid tokens of the language. These valid
tokens form the lowest-level building blocks of the language and are used to describe the rest of the language in
subsequent chapters. A token consists of an identifier, keyword, punctuation, literal, or operator.

In most cases, tokens are generated from the characters of a Cation source file by considering the longest possible
substring from the input text, within the constraints of the grammar that are specified below. This behavior is referred
to as longest match or maximal munch.

### Whitespace and comments

Whitespace has three uses:
- to separate tokens in the source file;
- to separate statements with indentation;
- to distinguish between prefix and infix operators (see Operators);

The following characters are considered whitespace: space (U+0020), line feed (U+000A), carriage return (U+000D),
horizontal tab (U+0009), vertical tab (U+000B), form feed (U+000C) and null (U+0000).

Comments are treated as whitespace by the compiler. Single line comments begin with `--` and continue until a line feed
(U+000A) or carriage return (U+000D). Multiline comments begin with `{-` and end with `-}`. Nesting multiline comments
is allowed, but the comment markers must be balanced.

### Identifiers

Identifiers begin with an uppercase or lowercase letter A through Z, an underscore (`_`), a noncombining alphanumeric 
Unicode character in the Basic Multilingual Plane, or a character outside the Basic Multilingual Plane that isn’t in
a Private Use Area. After the first character, digits and combining Unicode characters are also allowed.

To use ASCII punctuation characters in an identifier, or a reserved word, put a backtick (`` ` ``) before and after it.
For example, `data` isn’t a valid identifier, but `` `data` `` is valid. The backticks aren’t considered part of the
identifier; `` `x` `` and `x` has the same meaning.

### Keywords

The following keywords are reserved and can’t be used as identifiers, unless they’re escaped with backticks, as
described above in [Identifiers](#identifiers). Keywords other than `let` and `lambda` can be used as field or variant
names in data types, as parameter names in a function declaration or function call without being escaped with backticks.

- `data`: defines new data type
- `class`: defines a new data type class
- `fx`: defines new function (prefix function)
- `infx`: defines new infix function
- `lambda`: defines a lambda function
- `alias`: defines a new alias for a function
- `let`: defines new value

The following tokens are reserved as built-in operators and can’t be used in custom operators: `(`, `)`, `{`, `}`, 
`[`, `]`, `.`, `,`, `:`, `;`, `#`, `=>`, `->`, `<-`, `<|`, `|>`, `>|`, `|?`, `|:`, `.\`, `` ` ``.

### Literals

A <dfn>literal</dfn> is the source code representation of a value of a type, such as a number or string.

The following are examples of literals:

```
42               -- Integer literal
42#U8            -- Tagged integer literal
3.14159          -- Floating-point literal
'A'              -- Character literal
"Hello, world!"  -- String literal
```

A literal without a [tag](#tags) doesn’t have a type on its own. Instead, Cation’s type inference attempts to infer
a type for the literal. For example, in the declaration `let x: U8 := 42`, Cation uses the explicit type information
(`: U8`) to infer that the type of the integer literal `42` is `U8`. If there isn’t suitable type information available,
Cation infers that the literal’s type is one of the default literal built-in Cation types listed in the table below. 

| Literal   | Default type |
|:----------|-------------:|
| Integer   |          U64 |
| Float     |          F64 |
| Character |         Char |
| String    |       String |

When specifying the type annotation for a literal value with a tag, the annotation’s type must be a type that can be
instantiated from that literal value.

For example, in the declaration `let str := "Hello, world"`, the default inferred type of the string literal
`"Hello, world"` is `String`. This can be changed by using type annotation or tag: both 
`let str: AsciiString := "Hello, world"` and `let str := "Hello, world"#AsciiString` will be inferred to `AsciiString`
type instead of `String`.

#### Integer literals

<dfn>Integer literals</dfn> represent integer values of some precision. By default, integer literals are expressed in
decimal; you can specify an alternate base using a prefix:
- binary literals begin with 0b,
- octal literals begin with 0o,
- and hexadecimal literals begin with 0x.

Decimal literals contain the digits 0 through 9. Binary literals contain 0 and 1, octal literals contain 0 through 7,
and hexadecimal literals contain 0 through 9 as well as A through F in upper- or lowercase.

Negative integers literals are expressed by prepending a minus sign (-) to an integer literal, as in `-42`.

Underscores (`_`) are allowed between digits for readability, but they’re ignored and therefore don’t affect the value of
the literal. Integer literals can begin with leading zeros (`0`), but they’re likewise ignored and don’t affect the base
or value of the literal.

Unless otherwise specified, the default inferred type of an integer literal is the Cation built-in type `U64`. 
Cation has built-in types for various sizes of signed, unsigned and built-in integers, as described in
[Integers](#integers).

#### Floating-point literals

<dfn>Floating-point literals</dfn> represent floating-point values of some precision.

By default, floating-point literals are expressed in decimal (with no prefix), but they can also be expressed in
hexadecimal (with a 0x prefix).

Decimal floating-point literals consist of a sequence of decimal digits followed by either a decimal fraction, a decimal
exponent, or both. The decimal fraction consists of a decimal point (`.`) followed by a sequence of decimal digits. The
exponent consists of an upper- or lowercase `e` prefix followed by a sequence of decimal digits that indicates what
power of 10 the value preceding the `e` is multiplied by. For example, `1.25e2` represents 1.25 x 10², which evaluates
to 125.0. Similarly, `1.25e-2` represents 1.25 x 10⁻², which evaluates to 0.0125.

Hexadecimal floating-point literals consist of a 0x prefix, followed by an optional hexadecimal fraction, followed by a
hexadecimal exponent. The hexadecimal fraction consists of a decimal point followed by a sequence of hexadecimal digits.
The exponent consists of an upper- or lowercase p prefix followed by a sequence of decimal digits that indicates what
power of 2 the value preceding the p is multiplied by. For example, `0xFp2` represents 15 x 2², which evaluates to 60.
Similarly, `0xFp-2` represents 15 x 2⁻², which evaluates to 3.75.

Negative floating-point literals are expressed by prepending a minus sign (-) to a floating-point literal, as in `-42.5`.

Underscores (`_`) are allowed between digits for readability, but they’re ignored and therefore don’t affect the value
of the literal. Floating-point literals can begin with leading zeros (`0`), but they’re likewise ignored and don’t
affect the base or value of the literal.

Unless otherwise specified, the default inferred type of a floating-point literal is the Cation built-in `F64`, which
represents a 64-bit floating-point number.

#### Character literal

#### String literals

#### Binary data literals

#### Date-and-time literals

#### Custom literals

### Syntactic constructs

#### Specifiers

#### Projections

Function calls are made using projection operator expressed as a space (` `); i.e. for a prefix function `fn` the way
to call it is to write `fn args`, where `args` can be a named or unnamed data type matching arguments in the function
declaration. They can be kapt in `(`..`)`, or without them, but if multiple arguments are present they must be separated
from each other with a comma `,` (product operator which composes arguments into a data type). Thus, a function always
takes a single argument, which can be a named or unnamed data type.

Prefix functions may be called using an infix notation: `(args).fn`, or via `arg0.fn arg1, .., argLast` notation.

Call of an infix function `infn` is `arg0 infn arg1, .., argLast`. It can be reverted via use of
`(infn arg0, .., argLast)` or `(infn) arg0, .., argLast` forms.

#### Injections

Constructs with `>|`, `|?`, `|:` symbols are called branching operators.
They correspond to `match { .. -> .., .. -> .. }` construction.

Operator `.. |? .. |: ..` is also the ternary operator, corresponding to
`if .. then .. else` construction. It also has a form of
`condition1 |? statement1 |: condition2 |? statement2 |: statementElse`,
corresponding to
`if condition1 then statement1 elseIf condition2 then statement2 else statementElse`.

#### Annotations

## Operators

### Built-in

#### Categorical limits

Operator `,` is a categorical product operator. Operator `|` is categorical sum operator

#### Mappings (morphisms and functors)

Operators `->` `<-` define functors: they do project an object or each of
objects in a category (value, collection, iterator or a data type) to a
different object or a category.

For instance, when used in function definition, it projects all possible
values of a function input as data type (which may be a composite anonymous
type) to the set of return values defined by the return data type:

    fx some: input Type -> output Type

Operator `->` is also used inside collections of key-value maps, separating
keys and their corresponding values.

#### Natural transformation mapping

Operator `=>` is used to introduce generic arguments as a natural transformation.

#### Iteration operators

Operators `|>` and `<|` used to build iterators: it maps of the items
of an iterable collection into a new value:

    x <- 0..<100 |> mulTry x, 2

which will result in a new iterator over doubled values in the range from
0 to 99.

One may skip te initial `x <-` assignment; in this case the input value of
the current iteration will be put into the first argument of the next expression:

    0..<100 |> mulTry 2

one may also use the context operator `_`:

    0..<100 |> _ mulTry 2

or reverse the order of the expressions:

    mulTry 2 <| 0..<100

#### Lambda operator

<dfn>Lambda operator</dfn> `.\`, with alias `λ` (Greek letter lambda) is a shorthand for creating lambda expressions.
It has four forms:
- single-line, end of line (trailing lambda):
  ```
  .\args -> ret: expr
  ```
- single-line, alongside other expressions:
  ```
  .\(args -> ret: expr), _
  ```
- multi-line, end of line (trailing lambda block):
  ```
  .\args -> ret
    expr1
    expr2
    -- ...
  ```
- multi-line, alongside other expressions:
  ```
  .\(args -> ret
    expr1
    expr2
    -- ...
  ), _
  ```

All forms may skip the return type `-> ret` part; in this case the return type is inferred by the compiler:
```
.\args: expr

.\(args: expr), _

.\args
  expr1
  expr2
  -- ...

.\(args
  expr1
  expr2
  -- ...
), _
```

If the lambda expression has no inputs, lambda operator must be simply followed by the expression itself with no
colon used:
```
.\expr

.\(expr), _

.\
  expr1
  expr2
  -- ...

.\(
  expr1
  expr2
  -- ...
), _
```

See also [lambda specifier](#lambda-specifier).

### Standard library

#### Monadic operators

Operators `!`, `!!`, `?` and `??` are used with monad types (like
optionals/maybes or result types). First, `!` and `?` marks a part of the
expression which should be tested against unwrapped monad value; if the unwrap
procedure fails, it will default to the expression put after `!!` or `??` 
operator at the end of the same line. For instance, if `value` is of `Maybe` 
type we can write `value ?? 0`; or `value !! noValue`; the first will default
expression to zero, and the second convert return type of the function to a
result type, create an enum `Error` with variant `noValue` and will return
that value if the maybe monad in `value` doesn't contain an actual value.

#### Arithmetic operators

Unlike other languages, Cation requires to explicitly handle overflow and underflow conditions in arithmetics, as well
as zero divisions, which is required for termination analysis and helps in avoiding undefined behaviours. Thus, each of
arithmetic operators has multiple forms, handling overflows and underflows differently. This approach used in Cation 
is named <dfn>checked arithmetics</dfn>.

To construct arithmetic operators, a symbol representing mathematical operation (like `+`, `-`, `*`, `/`, `%`, `^`) is
postfixed with a symbol representing the way of handling overflow, underflow or zero divisions. Such symbols are:
- `?` for converting the result of the operation into `Maybe` monad;
- `!` for converting the result of the operation into `Result::error` monad variant with details on specific condition
  which has occurred;
- `@` for wrapping a value in case of overflow;
- `^` for saturating a value with a maximum possible value in case of overflow;
- `|` for skipping the operation as whole.

Additionally, Cation allows <dfn>native arithmetic operations</dfn> when an operation itself and the resulting type 
guarantees impossibility of overflow or other exceptional conditions. These operations are:
- unsigned, signed and non-zero integer addition with `+` operator, when the bit dimension of the resulting integer type
  is equal to or exceeds the sum of the bit dimensions of the inputs;
- signed integer subtraction with `-` operator, when the bit dimension of the resulting integer type is equal to or
  exceeds the sum of the bit dimensions of the inputs;
- unsigned, signed and non-zero integer multiplication with `*` operator, when the dimension of the resulting integer
  type is equal to or exceeds the product of the bit dimensions of the inputs;
- division with `/` operator of non-zero unsigned integers;
- modulo division with `%` operator of non-zero unsigned integers;
- unsigned, signed and non-zero potentiation with `^` operator when the bit dimensions of the resulting integer type
  exceeds 

Thus, the resulting table of the arithmetic operations is the following:

| Operation       | Native | Maybe | Result | Wrapping | Saturating | Skipping |
|-----------------|--------|-------|--------|----------|------------|----------|
| Addition        | `+`    | `+?`  | `+!`   | `+@`     | `+^`       | `+`\|    |
| Subtraction     | `-`    | `-?`  | `-!`   | `-@`     | `-^`       | `-`\|    |
| Multiplication  | `*`    | `*?`  | `*!`   | `*@`     | `*^`       | `*`\|    |
| Division        | `/`    | `/?`  | `/!`   | `/@`     | `/^`       | `/`\|    |
| Modulo division | `%`    | `%?`  | `%!`   | `%@`     | `%^`       | `%`\|    |
| Potentiation    | `^`    | `^?`  | `^!`   | `^@`     | `^^`       | `^`\|    |

#### Bitwise operators

#### Boolean logic operators

#### Ternary logic operators

#### String operators

## Types

### Built-in

#### Initial and terminal

#### Integers

Integers come in signed, unsigned and non-zero unsigned classes, each of which contains types with different bit length.

Supported bit length for **integer types** are:

| Bits      | Bytes     | Unsigned |  Signed | Non-zero | C equivalents            | Rust equivalents              | 
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
[injection operators](#injections) to match them against patterns and ranges.

#### Floats

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

#### Character

Cation has just a single built-in Unicode character type `Char`. It is the only of the built-in types which has
variable bit length, due to the Unicode standard. Its length varies from 8 bits to 32 bits; with 8 bit step.

#### Ranges

**Range types** simplify creation of [collection types](#collection-comprehension), as well as are an efficient tool for
[cycles and iterators](#iteration-operators). Cation comes with the following set of range types, each of which can be
instantiated using a shorthand [range expressions](#range-expressions).

| Type name         | Range operator |
|-------------------|----------------|
| `RangeAll`        | `..`           |
| `RangeTo`         | `..<N`         |
| `RangeToIncl`     | `..=N`         |
| `RangeFrom`       | `M..`          |
| `RangeFromTo`     | `M..<N`        |

### Standard library

#### Small integers

#### String types

#### Monads

## Statements

### Expressions

Expressions are separated either with a line feed character (U+000A), or with a semicolon `;` if put on one line one
after the other.

#### Lambda expressions

Lambda expressions have two forms: [operator](#lambda-operator) and [specifier](#lambda-specifier).

#### Collection comprehension

Grouping operator is `(`..`)` constructs an anonymous data type.

Operators `[`..`]`, `{`..`}` and `{`..`->`..`}` construct collection types.

#### Range expressions

Operators `..=` and `..<` help to create iterators or collections over
ranges. They may be combined with a step size information in form of

    0<=2x<=100

where instead of 2 any other constant can be given

#### Context value

Operator `_` means context default value, like the one kept in a stack from
a previous function call or a decomposition

#### Result value

Operator `$` means <q>result of the current expression or a function</q>. It
can be used to assign an output value of a function, like in `$ <- value`,
or to access the results within iterations from the previous cycles of
iterations, like with `$-1`, accessing the previous iteration result, or
`$3` accessing the result of the third iteration from the beginning.

#### Patterns

### Specifiers

#### Function

#### Data type

#### Data class

#### Value specifier

#### Lambda specifier

<dfn>Lambda specifier</dfn> starts with a `lambda` keyword, followed by a value name, colon, argument and return type
definition and body:

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

See also [lambda operator](#lambda-operator).

## Generics

## Annotations

### Attributes

Statements starting with `@` are used to add attributes other Cation statements. Attributes are a way of
metaprogramming: they are similar to procedural macros in Rust or annotations in Java.

#### `@id`

#### `@alias`

#### `@final`

#### `@override`

#### `@private`

### Tags

Operator `#` adds context tags to the values; for instance it is used to add
integer tag to co-product type variants (like in `data Bool: false#0 | true#1`)
or to enforce some literal to be of a specific data type (like in `..100#U8`,
where the range is enforced to be over `U8` type).

#### Co-product variant tags

#### Type tags

