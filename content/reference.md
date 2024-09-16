+++
description = "Cation Language Reference"
template = "page.html"
+++

## Keywords

- `data`: defines new data type
- `fx`: defines new function (prefix function)
- `infx`: defines new infix function
- `let`: defines new instance of a data type
- `class`: defines a new type class

## Build-in operators

### Grouping

Grouping operator is `(`..`)` constructs an anonymous data type.

### Details

Operator `:`

Defines a details of a previous statement.

### Composition and categorical product

Operator `,`

### Categorical co-product

Operator `|`

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

where instead of 2 any other constant can be given

### Context value

Operator `_` means context default value, like the one kept in a stack from
a previous function call or a decomposition

### Result value

Operator `$` means <q>result of the current expression or a function</q>. It
can be used to assign an output value of a function, like in `$ <- value`,
or to access the results within iterations from the previous cycles of
iterations, like with `$-1`, accessing the previous iteration result, or
`$3` accessing the result of the third iteration from the beginning.

### Mappings

Operators `->` `<-` define mappings: they do project each of the members of
an object (a category: collection, iterator or a data type) resulting from
an expression on the one side to the statement given on the other.

For instance, when used in function definition, it projects all possible
values of a function input as data type (which may be a composite anonymous
type) to the set of return values defined by the return data type:

    fx some: input Type -> output Type

Operator `->` is also used inside collections of key-value maps, separating
keys and their corresponding values.

### Generic mapping

Operator `=>` is used to introduce generic arguments as a natural
transformation.

### Iterating operators

Operators `|>` and `<|` used to build iterators: it maps of the items
of an iterable collection into a new value:

    x <- 0..<100 |> mulTry x, 2

which will result in a new iterator over doubled values in the range from
0 to 99. See how `<-` is looking like $\in$ mathematical symbol?

One may skip te initial `x <-` assignment; in this case the input value of
the current iteration will be put into the first argument of the next expression:

    0..<100 |> mulTry 2

one may also use the context operator `_`:

    0..<100 |> _ mulTry 2

or reverse the order of the expressions:

    mulTry 2 <| 0..<100

### Branching

Operators `.. >| .. |? .. |? .. |: ..` are called branching operators.
They correspond to `match { .. -> .., .. -> .. }` construction.

Operator `.. |? .. |: ..` is also the ternary operator, corresponding to
`if .. then .. else` construction. It also has a form of
`condition1 |? statement1 |: condition2 |? statement2 |: statementElse`,
corresponding to 
`if condition1 then statement1 elseIf condition2 then statement2 else statementElse`.

### Monadic operators

Operators `!`, `!!`, `?` and `??` are used with monad types (like
optionals/maybes or result types). First, `!` and `?` marks a part of the
expression which should be tested against unwrapped monad value; if the unwrap
procedure fails, it will default to the expression put after `!!` or `??` 
operator at the end of the same line. For instance, if `value` is of `Maybe` 
type we can write `value ?? 0`; or `value !! noValue`; the first will default
expression to zero, and the second convert return type of the function to a
result type, create an enum `Error` with variant `noValue` and will return
that value if the maybe monad in `value` doesn't contain an actual value.

### Tagging

Operator `#` adds context tags to the values; for instance it is used to add
integer tag to co-product type variants (like in `data Bool: false#0 | true#1`)
or to enforce some literal to be of a specific data type (like in `..100#U8`,
where the range is enforced to be over `U8` type).

### Annotation

Operator `@` is used to annotate Cation statements. Annotations are a way of
metaprogramming: they are similar to procedural macros in Rust or annotations
in Java.
