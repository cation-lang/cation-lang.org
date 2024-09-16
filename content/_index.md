+++
description = "Cation Language"
+++

# Cation Language

Cation is a modern general-purpose programming language made with category theory in mind. It is purely functional
and uses statical and strong generalized algebraic data types known as [strict types][strict types].

The language design and compiler is a work in progress. The compiler will compile the code into LLVM and [AluVM](AluVM)
targets, and a special cross-compiler from Cation to Rust will be available.

## Language objectives

1. ![Applied Category Theory](/symbols/act.svg)
   Be the (first) categorical programming language with notations close to mathematics
2. ![Formal analysis](/symbols/analysis.svg)
   Support termination analysis, generalized algebraic data types and formal verification
3. ![Fun to program](/symbols/fun.svg)
   Be easy to read and fun to program, avoid visual clutter and boilerplate

<div></div>

- ## Language Design

  Read more on language design in the documentation.

  <a href="/design" class="button">Language design docs</a>

- ## Language Reference

  Reference contains a list of Cation keywords and statements.

  <a href="/reference" class="button">Language Reference</a>

- ## Code Examples
  
  See how Cation code looks like in most typical tasks.

  <a href="/examples" class="button">Code examples</a>

---

Cation is a work in progress project. Its development is lead by [Dr Maxim Orlovsky][orlovsky] at [UBIDECO Labs],
[Institute of Deterministic and Cognitive Systems][InDCS], Lugano, Switzerland with a broad participation of community.

[strict types]: https://www.strict-types.org
[AluVM]: https://www.aluvm.org
[orlovsky]: https://dr.orlovsky.ch
[UBIDECO Labs]: https://www.ubideco.org
[InDCS]: https://indcs.org
