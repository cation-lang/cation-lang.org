+++
description = "Cation Language in Terms of Category Theory"
template = "page.html"
+++

# The Categorical Language

Explaining Cation language in terms of Category Theory

Cation is a categorical language, meaning a strong equivalence between Category theory and language constructions.
This article dives into the details of such equivalences, which are made into a core of the language design.

## Basics

In Cation language everything – data types, functions, expressions, values, literals – corresponds to categories,
functors and objects. Since categories can be seen as objects in a higher-order category, all of them need just
two main language constructions: **types** and **values**, introduced and explained in this section.

### Types and Values

The very fundamental concept of a Cation language is *type*. Each <dfn>type</dfn> is a *category* in itself. 
Contravariantly, a *category* in Cation language is named *type*.

<aside>
    <p>Types in Cation are declared using a <code>type</code> keyword followed by a type name:</p>
    <pre><code>type TypeName</code></pre>
</aside>

This is a full and sufficient definition of what *type* is; everything else about types in Cation and Cation
itself are just consequences of this definition. For instance, saying that Cation is a language of operations
with types defines Cation as a categorical language.

Important to note that all Cation types are small categories and can be represented as finite sets, inhabited by their
*values*. Hence, a <dfn>value</dfn> in Cation is an object in one of the *type* categories.

<aside>
    <p>Values are defined using <code>val</code> keyword followed by an optional type annotation and a literal or an
    expression which evaluation results in the value from the type:</p>
    <pre><code>val valName: TypeName := expression</code></pre>
</aside>

As all small categories can be seen as objects in the category of all small categories <nobr>$\mathbf{Cat}$,</nobr>
all Cation *types* as categories may also be seen as objects in a category of all types of Cation language – 
<nobr>$\mathbf{Cation}$.</nobr> As a category, $\mathbf{Cation}$ is small category with infinite number of members.

### Expressions, functions, programs

A Cation <dfn>expression</dfn> maps a type <nobr>($A$)</nobr> (named <dfn>argument type</dfn>) to a type
<nobr>($R$)</nobr> (named <dfn>return type</dfn>: it can be the same as the argument type, or a different type).
Thus, an expression in Cation is always a functor <nobr>$F: A \rightarrow R$.</nobr>

<aside>
    <p>One can guess that an expression can also be seen as a morphism in the <nobr>$\mathbf{Cation}$.</nobr></p>
</aside>

Evaluation of an <dfn>expression</dfn> selects a *value* <nobr>($a$)</nobr> from the *argument type* <nobr>($A$)</nobr>
and maps it to another *value* <nobr>($r$)</nobr> in the *return type* <nobr>($R$).</nobr> Thus, it is an application
of the *functor* <nobr>($F$)</nobr> of the *expression* to an object <nobr>($a$):</nobr>

$$\mathsf{eval}\ F\ a \doteqdot a \xrightarrow{F} r$$

<aside>
    <p>Cation uses syntax close to math notation: a function type and expression evaluation are written as</p>
    <pre><code>type F: A -> R
val r := F a</code></pre>
    <p>You can even use Cation compiler to prove that `r` has the type `R`:</p>
    <pre><code>val r := (F a)#R</code></pre>
</aside>

The signature of an expression $F: A \rightarrow R$ is [a type by itself][1]: this is a <dfn>function type</dfn>. It
can also be defined via the evaluation, as shown above, applied to all objects of the argument category:

$$F\ a = r \in R\ |\ \forall a \in A$$

In Cation, each <dfn>function</dfn> is a named expression – and each expression is a function (which maybe anonymous).
All expressions and functions are
- functors,
- morphisms in <nobr>$\mathbf{Cation}$,</nobr>
- objects in $\mathrm{hom}(\mathbf{Cation})$

<aside>
    <p>Named functions are defined from expressions</p>

```
type name: A -> R := expr
```

<p>or, with a multi-line syntax,</p>

```
type name: A -> R
   expression(s)
```
</aside>

A Cation <dfn>program</dfn> is a composition of expressions, which is an expression itself (as a composition of
functors is always a functor). <dfn>Execution of program</dfn> corresponds to an evaluation of the expression: an
application of the composed functor to a program argument giving a result.


### Data types

<dfn>Data types</dfn> is another example of Cation *types*, such that data types are the first-class language citizen
alongside functions and expressions.

There are only two foundational data types, named *unit* and *uninhabited*. <dfn>Unit</dfn> type is an *initial object*
in the $\mathbf{Cation}$ category and is written as `(,)`. <dfn>Uninhabited</dfn> type is a *terminal object* in the
$\mathbf{Cation}$ category and is written as `(|)`. All other data types are derived from these two types via ... yes,
expressions!

<aside>
    <p>As will be seen later, <em>unit</em> and <em>uninhabited</em> types are just empty product and co-product types</p>
</aside>

Data type is defined as an expression composition of other types. As with any algebraic data type system there are two
forms of composition: *product* and *co-product*.

<aside>
    <p>Here, we are taking advantage of all Cation types being small categories and thus sets; so a cartesian product
    and unit operations over two types can be always defined in terms of sets.</p>
</aside>

*Product data types* are bi-functors from categories representing types $L$ and $R$ to a new category $P$; where
$P$ is a cartesian product of sets of $L$ and $R$ objects: $P \doteqdot L \times R$. This also defines two
<dfn>projections</dfn> as functors from the type $P$ to the original types $L$ and $R$. Cation expresses product with a
comma operator (`,`) and the new type may be defined in the following way:

```
data Prod: A, B

-- using the new Prod type
val new := (valueA, valueB)#Prod
val projA := new.a
val projB := new.b

-- testing that the result is the same as the original values:
projA =?= valueA && projB =?= valueB !! mustBeEqual 
```

<aside>
<p>In fact, this is a syntactic sugar: <code>data</code> is a macro coming with the standard library, which expends into
this code, defining type, constructor function and two projections:</p>

```
type Prod
type `#Prod`: a A, b B @op -> Prod
  (a, b)
type `.a`: self Prod -> A
  val ($, _) := self
type `.b`: self Prod -> B
  val (_, $) := self
```
</aside>

*Co-product data types* are bi-functors corresponding to the morphism in the $\mathbf{Cation^\mathrm{op}}$: they
map a union of sets representing all possible values in $L$ and $R$ to a new category $S$: $S \doteqdot L + R$. Cation
union with a pipe operator (`|`) and the new type may be defined in the following way:

```
data Either: A | B

-- using the new Either type
let either := a#A -- `a` is the value from the type `A`
either >|
  (a)#A |? print "I am A"
  (b)#B |? print "I am B"
```

<aside>
<p>Again, this is a syntactic sugar for</p>

```
type Either
-- two constructurs:
type `#Either`: a A -> Either := a
type `#Either`: b B -> Either := b
```
</aside>


## Advanced topics

### Currying

Since each data type constructor is a functor, and each functor takes exactly one argument, the multi-argument function
type `type fn: A, B -> C` can be seen as a functor of a single argument of an unnamed product type `(A, B)` – or as a
composition of two unnamed functors `type fn: A -> (B -> C)` – similar to the Haskell language.


### Lambda expressions

### Collections

### Generics

Now it is time to introduce Cation language constructs build using the last component of the Category theory:
*natural transformations*.

### Monads


## Expert topics

Here we show how all language constructs can be interpreted as natural transformations, such that their composability
properties are used for terminal analysis and parallel computations without creation of racing conditions.

### Naturality conditions

### Termination analysis

### Rho expressions


[1]: https://bartoszmilewski.com/2015/03/13/function-types/
