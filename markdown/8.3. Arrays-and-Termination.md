# Arrays and Termination

Source: https://lean-lang.org/functional_programming_in_lean/Programming___-Proving___-and-Performance/Arrays-and-Termination

Because different types have different notions of ordering, inequality is governed by two type classes, called `LE` and `LT`.
The table in the section on [standard type classes](Overloading-and-Type-Classes/Standard-Classes/#equality-and-ordering) describes how these classes relate to the syntax:

### 8.3.1.1. Inductively-Defined Propositions, Predicates, and Relations[🔗](find/?domain=Verso.Genre.Manual.section&name=inductive-props "Permalink")

`Nat.le` is an *inductively-defined relation*.
Just as `inductive` can be used to create new datatypes, it can be used to create new propositions.
When a proposition takes an argument, it is referred to as a *predicate* that may be true for some, but not all, potential arguments.
Propositions that take multiple arguments are called *relations*.

Each constructor of an inductively defined proposition is a way to prove it.
In other words, the declaration of the proposition describes the different forms of evidence that it is true.
A proposition with no arguments that has a single constructor can be quite easy to prove:

`inductive EasyToProve : Prop where
| heresTheProof : EasyToProve`

The proof consists of using its constructor:

`theorem fairlyEasy : EasyToProve := by⊢ EasyToProve
constructorAll goals completed! 🐙`

In fact, the proposition `True`, which should always be easy to prove, is defined just like `EasyToProve`:

`inductive True : Prop where
| intro : True`

Inductively-defined propositions that don't take arguments are not nearly as interesting as inductively-defined datatypes.
This is because data is interesting in its own right—the natural number `3` is different from the number `35`, and someone who has ordered 3 pizzas will be upset if 35 arrive at their door 30 minutes later.
The constructors of a proposition describe ways in which the proposition can be true, but once a proposition has been proved, there is no need to know *which* underlying constructors were used.
This is why most interesting inductively-defined types in the `Prop` universe take arguments.

The inductively-defined predicate `IsThree` states that its argument is three:

`inductive IsThree : Nat → Prop where
| isThree : IsThree 3`

The mechanism used here is just like [indexed families such as `HasCol`](Programming-with-Dependent-Types/Worked-Example___-Typed-Queries/#column-pointers), except the resulting type is a proposition that can be proved rather than data that can be used.

Using this predicate, it is possible to prove that three is indeed three:

`theorem three_is_three : IsThree 3 := by⊢ IsThree 3
constructorAll goals completed! 🐙`

Similarly, `IsFive` is a predicate that states that its argument is `5`:

`inductive IsFive : Nat → Prop where
| isFive : IsFive 5`

If a number is three, then the result of adding two to it should be five.
This can be expressed as a theorem statement:

`theorem three_plus_two_five : IsThree n → IsFive (n + 2) := unsolved goals
n:Nat⊢ IsThree n → IsFive (n + 2)byn:Nat⊢ IsThree n → IsFive (n + 2)
skipn:Nat⊢ IsThree n → IsFive (n + 2)`

The resulting goal has a function type:

```
unsolved goals
n:Nat⊢ IsThree n → IsFive (n + 2)
```

Thus, the `intro` tactic can be used to convert the argument into an assumption:

`theorem three_plus_two_five : IsThree n → IsFive (n + 2) := unsolved goals
n:Natthree:IsThree n⊢ IsFive (n + 2)byn:Nat⊢ IsThree n → IsFive (n + 2)
intro threen:Natthree:IsThree n⊢ IsFive (n + 2)`

```
unsolved goals
n:Natthree:IsThree n⊢ IsFive (n + 2)
```

Given the assumption that `n` is three, it should be possible to use the constructor of `IsFive` to complete the proof:

`` theorem three_plus_two_five : IsThree n → IsFive (n + 2) := byn:Nat⊢ IsThree n → IsFive (n + 2)
intro threen:Natthree:IsThree n⊢ IsFive (n + 2)
Tactic `constructor` failed: no applicable constructor found

n:Natthree:IsThree n⊢ IsFive (n + 2)constructorn:Natthree:IsThree n⊢ IsFive (n + 2) ``

However, this results in an error:

```
Tactic `constructor` failed: no applicable constructor found

n:Natthree:IsThree n⊢ IsFive (n + 2)
```

This error occurs because `n + 2` is not definitionally equal to `5`.
In an ordinary function definition, dependent pattern matching on the assumption `three` could be used to refine `n` to `3`.
The tactic equivalent of dependent pattern matching is `cases`, which has a syntax similar to that of `induction`:

`theorem three_plus_two_five : IsThree n → IsFive (n + 2) := byn:Nat⊢ IsThree n → IsFive (n + 2)
intro threen:Natthree:IsThree n⊢ IsFive (n + 2)
cases three with
| isThree unsolved goals
isThree⊢ IsFive (3 + 2)=> skipisThree⊢ IsFive (3 + 2)`

In the remaining case, `n` has been refined to `3`:

```
unsolved goals
isThree⊢ IsFive (3 + 2)
```

Because `3 + 2` is definitionally equal to `5`, the constructor is now applicable:

`theorem three_plus_two_five : IsThree n → IsFive (n + 2) := byn:Nat⊢ IsThree n → IsFive (n + 2)
intro threen:Natthree:IsThree n⊢ IsFive (n + 2)
cases three with
| isThree =>isThree⊢ IsFive (3 + 2) constructorAll goals completed! 🐙`

The standard false proposition `False` has no constructors, making it impossible to provide direct evidence for.
The only way to provide evidence for `False` is if an assumption is itself impossible, similarly to how `nomatch` can be used to mark code that the type system can see is unreachable.
As described in [the initial Interlude on proofs](Interlude___-Propositions___-Proofs___-and-Indexing/#connectives), the negation `Not A` is short for `A → False`.
`Not A` can also be written `¬A`.

It is not the case that four is three:

`theorem four_is_not_three : ¬ IsThree 4 := unsolved goals
⊢ ¬IsThree 4by⊢ ¬IsThree 4
skip⊢ ¬IsThree 4`

The initial proof goal contains `Not`:

```
unsolved goals
⊢ ¬IsThree 4
```

The fact that it's actually a function type can be exposed using `unfold`:

`theorem four_is_not_three : ¬ IsThree 4 := unsolved goals
⊢ IsThree 4 → Falseby⊢ ¬IsThree 4
unfold Not⊢ IsThree 4 → False`

```
unsolved goals
⊢ IsThree 4 → False
```

Because the goal is a function type, `intro` can be used to convert the argument into an assumption.
There is no need to keep `unfold`, as `intro` can unfold the definition of `Not` itself:

`theorem four_is_not_three : ¬ IsThree 4 := unsolved goals
h:IsThree 4⊢ Falseby⊢ ¬IsThree 4
intro hh:IsThree 4⊢ False`

```
unsolved goals
h:IsThree 4⊢ False
```

In this proof, the `cases` tactic solves the goal immediately:

`theorem four_is_not_three : ¬ IsThree 4 := by⊢ ¬IsThree 4
intro hh:IsThree 4⊢ False
cases hAll goals completed! 🐙`

Just as a pattern match on a `Vect String 2` doesn't need to include a case for `Vect.nil`, a proof by cases over `IsThree 4` doesn't need to include a case for `isThree`.

### 8.3.1.2. Inequality of Natural Numbers[🔗](find/?domain=Verso.Genre.Manual.section&name=inequality-of-natural-numbers "Permalink")

The definition of `Nat.le` has a parameter and an index:

`inductive Nat.le (n : Nat) : Nat → Prop
| refl : Nat.le n n
| step : Nat.le n m → Nat.le n (m + 1)`

The parameter `n` is the number that should be smaller, while the index is the number that should be greater than or equal to `n`.
The `refl` constructor is used when both numbers are equal, while the `step` constructor is used when the index is greater than `n`.

From the perspective of evidence, a proof that `n \leq k` consists of finding some number `d` such that `n + d = m`.
In Lean, the proof then consists of a `Nat.le.refl` constructor wrapped by `d` instances of `Nat.le.step`.
Each `step` constructor adds one to its index argument, so `d` `step` constructors adds `d` to the larger number.
For example, evidence that four is less than or equal to seven consists of three `step`s around a `refl`:

`theorem four_le_seven : 4 ≤ 7 :=
open Nat.le in
step (step (step refl))`

The strict less-than relation is defined by adding one to the number on the left:

`def Nat.lt (n m : Nat) : Prop :=
Nat.le (n + 1) m
instance : LT Nat where
lt := Nat.lt`

Evidence that four is strictly less than seven consists of two `step`'s around a `refl`:

`theorem four_lt_seven : 4 < 7 :=
open Nat.le in
step (step refl)`

This is because `4 < 7` is equivalent to `5 ≤ 7`.