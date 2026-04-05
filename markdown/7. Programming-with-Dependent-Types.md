# Programming with Dependent Types

Source: https://lean-lang.org/functional_programming_in_lean/Programming-with-Dependent-Types

[←6.6. Summary](Monad-Transformers/Summary/#monad-transformer-summary "6.6. Summary")[7.1. Indexed Families→](Programming-with-Dependent-Types/Indexed-Families/#indexed-families "7.1. Indexed Families")

# 7. Programming with Dependent Types

In most statically-typed programming languages, there is a hermetic seal between the world of types and the world of programs.
Types and programs have different grammars and they are used at different times.
Types are typically used at compile time, to check that a program obeys certain invariants.
Programs are used at run time, to actually perform computations.
When the two interact, it is usually in the form of a type-case operator like an “instance-of” check or a casting operator that provides the type checker with information that was otherwise unavailable, to be verified at run time.
In other words, the interaction consists of types being inserted into the world of programs, where they gain some limited run-time meaning.

Lean does not impose this strict separation.
In Lean, programs may compute types and types may contain programs.
Placing programs in types allows their full computational power to be used at compile time, and the ability to return types from functions makes types into first-class participants in the programming process.

*Dependent types* are types that contain non-type expressions.
A common source of dependent types is a named argument to a function.
For example, the function `natOrStringThree` returns either a natural number or a string, depending on which `Bool` it is passed:

`def natOrStringThree (b : Bool) : if b then Nat else String :=
match b with
| true => (3 : Nat)
| false => "three"`

Further examples of dependent types include:

Dependent types vastly increase the power of a type system.
The flexibility of return types that branch on argument values enables programs to be written that cannot easily be given types in other type systems.
At the same time, dependent types allow a type signature to restrict which values may be returned from a function, enabling strong invariants to be enforced at compile time.

However, programming with dependent types can be quite complex, and it requires a whole set of skills above and beyond functional programming.
Expressive specifications can be complicated to fulfill, and there is a real risk of tying oneself in knots and being unable to complete the program.
On the other hand, this process can lead to new understanding, which can be expressed in a refined type that can be fulfilled.
While this chapter scratches the surface of dependently typed programming, it is a deep topic that deserves an entire book of its own.

1. [7.1. Indexed Families](Programming-with-Dependent-Types/Indexed-Families/#indexed-families)
2. [7.2. The Universe Design Pattern](Programming-with-Dependent-Types/The-Universe-Design-Pattern/#universe-pattern)
3. [7.3. Worked Example: Typed Queries](Programming-with-Dependent-Types/Worked-Example___-Typed-Queries/#typed-queries)
4. [7.4. Indices, Parameters, and Universe Levels](Programming-with-Dependent-Types/Indices___-Parameters___-and-Universe-Levels/#indices-parameters-universe-levels)
5. [7.5. Pitfalls of Programming with Dependent Types](Programming-with-Dependent-Types/Pitfalls-of-Programming-with-Dependent-Types/#dependent-type-pitfalls)
6. [7.6. Summary](Programming-with-Dependent-Types/Summary/#dependent-types-summary)

[←6.6. Summary](Monad-Transformers/Summary/#monad-transformer-summary "6.6. Summary")[7.1. Indexed Families→](Programming-with-Dependent-Types/Indexed-Families/#indexed-families "7.1. Indexed Families")