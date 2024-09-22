+++
description = "Cation Language Overview"
template = "page.html"
+++

# Cation Language Overview

Cation as a pure categorical programming language for practical tasks.

We designed Cation to be:
- non-verbose;
- as close to mathematics notation as possible;
- extensible and usable for creation of domain-specific languages.

## Unique features

### Boundness

Cation is the first and the only language where *all types are bounded in their size and computational resources at the
compile time*. This enables two main core language properties: *formal verifiability* and *termination analysis*. It 
also makes Cation a language which can be used for writing ubiquitous deterministic programs suitable for mathematical
modelling and immutable smart contracts.

<aside>
<h3>Why not to use existing functional language?</h3>

<p>Since each one of them lacks the distinguishing features: absence of the non-terminating computations and parallelism
by default.</p>

<p>Instead of operating with a pure mathematical abstractions, which may frequent lead to paradoxes with no physical
meaning (like <a href="https://en.wikipedia.org/wiki/Banach%E2%80%93Tarski_paradox"></a>Banach–Tarski paradox</a>),
Cation assumes real physical model for the computing: there are no infinities; any computation is always bound in its
resources. This allows Cation to achieve things which can't be achieved with any other existing language,
functional or not.</p>
</aside>

By types being *bound* we mean that any type is a finite set of a known size, with all possible values known to the
compiler.

- Only two foundational types: unit (initial object) and uninhabitable type (empty set or a terminal object);
- All integers are enumerations made with unit type (Peano arithmetics);
- All rational numbers, including rational numbers (like results of division of integers),
  arithmetic reals (like square root of 2) and irrational numbers (like *pi* or *e*) are explicit
  decimal approximates expressed as functions running for a number of steps known at compile-time;
- All other types are composed as a product or co-product categories from unit and uninhabitable type;
- Dynamic collections and iterators always have bounds on the minimum and maximal number of elements enforced at
  compile-time;
- There is no bottom type (non-terminating computations).

### Parallelism by default

Cation compiller converts a program as a set of expressions into a composition of natural transformations. This allows
static analysis at compile-time of which branches of computations depends on which values from other branches; the
compiler uses this information to parallize all computing wherever is possible with no programmer or syntax overhead.

When needed, a developer can use an explicit parallelism using asynchronous channels, avoiding racing conditions. These
channels can be used to send values, lambda-functions and even other channels between execution threads using
rho-calculus: an extension of lambda-calculus for parallel computations.

### No RAM, no mutability

- Absence of mutable variables;
- Abstracted memory management: language is suitable for architectures with no RAM access, like memristors and cellular
  automation;

## Notable features

- Generic system
- Collections
- Iterators
- Collection comprehensions
- Extensible set of literals
- Domain-specific languages
- No runtime
- Tiny set of compiler built-ins
- Moands
- Side-effect control
- Termination analysis
- Extensible infix operators
- Close-to-mathematics

## Syntax

### Declarations

Cation has only two keywords: `type`, for defining a type, and `val`, for instantiating a value.
