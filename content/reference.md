+++
description = "Cation Language Reference"
template = "page.html"
+++

## Keywords

- `data`: defines new data type
- `fx`: defines new function (prefix function)
- `infx`: defines new infix function
- `let`: defines new instance of a data type

## Build-in operators

### Grouping

Grouping operator is `(`..`)` constructs an anonymous data type.

### Details

Operator `:`

Defines a details of a previous statement.

### Categorical sum

Operator `|`

### Composition and categorical product

Operator `,`

### Decomposition assignment

Operator `:=` is called <dfn>decomposition assignment</dfn>

### Call

Call of a prefix function `fn` is `fn args`.

`args` can be a named or unnamed data type matching arguments in the
function declarator. They can be kapt in `(`..`)`, or without them,
but must be separated with a comma `,`.

Thus, a function always takes a single argument, which can be a named or
unnamed data type.

Prefix functions may be called using an infix notation: `(args).fn`, or via
a decomposition `arg0.fn arg1, .., argLast`.

Call of an infix function `infn` is `arg0 infn arg1, .., argLast`. It can
be reverted via use of `(infn arg0, .., argLast)` form.

### Collection/iterator comprehension

Operators `[`..`]`, `{`..`}` and `{`..`->`..`}`

### Range operators

Operators `..=` and `..<` help to create iterators or collections over
ranges. They may be combined with a step size information in form of

    0<=2x<=100

Where instead of 2 any other constant can be given

### Context value

Operator `_` means context default value, like the one kept in a stack from
a previous function call or a decomposition

### Projection

Operators `->` `<-` define functors: they do project each of the members of
an object (a category: collection, iterator or a data type) resulting from
an expression on the one side to the statement given on the other.

For instance, when used in function definition, it projects all possible
values of a function input as data type (which may be a composite anonymous
type) to the set of return values defined by the return data type:

    fx some: input Type -> output Type

When provided inside iterator decomposition it projects each of the items
of the iterator into a new value:

    x <- 0..<100 -> mulTry x, 2

which will result in a new iterator over doubled values in the range from
0 to 99. See how `<-` is looking like $\in$ mathematical symbol?

Operators has a second form `|>` and `<|` which allows to skip use of named
values; it brings the result to the first argument of the next call - or to
the position specified by context operator `_`:

    0..<100 |> mulTry 2

or

    mulTry 2 <| 0..<100

When provided inside branching context it projects all values in a provided
branch into the result of a branch execution:

    data MaybyU8: none | some(U8)

    let val: MaybyU8 := {- some assigned value -}
    let res := val =>
        |? none -> {- do something -}
        |? some(x) -> {- do something else -}

All projection operators may be used for constructing loops and cycles.

### Previous value

Operator `$` defines a value yielded by a previous call of a generator.
When suffixed with a signed integer specifies depth of the call, for instance
`$-1` means a value returned two calls back (thus simple form `$` stays for
`$-0`).

### Branching

Operators `.. => .. |? .. |? .. |: ..` are called branching operators.
It corresponds to `match { .. -> .., .. -> .. }` construction.

Operator `.. ?: .. |: ..` is  ternary operator, corresponding to
`if .. then .. else` construction. It also has a form of
`.. ?: .. |..?: .. ?: ..` corresponding to
`if .. then .. elseIf .. then .. else ..`.

Operator `.. ?? ..`

### Exception

Operator `!!`

### Tagging

Operator `#`

### Annotation

Operator `@`
