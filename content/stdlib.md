+++
description = "Cation Standard Library"
template = "page.html"
+++

# Cation Standard Library

## Type classes

- `num`
- `int`
- `unsigned`
- `signed`
- `nonzero`
- `float`
 
- `chr`
- `str`
- `ascii`
- `unicode`

- `pord`
- `ord`
- `eqcls`
- `eq`

- `debug`
- `display`
- `parse`
- `error`

- `prod`
- `coprod`
- `iter`
- `try`
  
- `read`
- `write`

## Monads

```idris
data Maybe: T => none | some(T)

data Result: T, E => err(E) | ok(T)

data Either: L, R => left(L) | right(R)

data Tern: N, P => neg(N)#0xff | zero#0 | pos(p)#1

data MayError: R, E => ok T, err Maybe E

fx map: T, U, F: T -> U => Maybe T, F -> Maybe U
   none, _ => none
   some(val), f => some(f val)

infx `=?=`: T eq => _ Maybe T, _ Maybe T -> Bool
    none, none => true
    some(_), none | none, some(_) => false
    some(v), some(w) => v =?= w

impl eq: T eq => Maybe T
```
