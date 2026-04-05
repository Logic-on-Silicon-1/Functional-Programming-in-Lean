# Proving Equivalence

Source: https://lean-lang.org/functional_programming_in_lean/Programming___-Proving___-and-Performance/Proving-Equivalence

Programs that have been rewritten to use tail recursion and an accumulator can look quite different from the original program.
The original recursive function is often much easier to understand, but it runs the risk of exhausting the stack at run time.
After testing both versions of the program on examples to rule out simple bugs, proofs can be used to show once and for all that the programs are equivalent.

## 8.2.1. Proving `sum` Equal[🔗](find/?domain=Verso.Genre.Manual.section&name=proving-sum-equal "Permalink")

To prove that both versions of `sum` are equal, begin by writing the theorem statement with a stub proof:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := unsolved goals
⊢ NonTail.sum = Tail.sumby⊢ NonTail.sum = Tail.sum
skip⊢ NonTail.sum = Tail.sum`

As expected, Lean describes an unsolved goal:

```
unsolved goals
⊢ NonTail.sum = Tail.sum
```

The `rfl` tactic cannot be applied here because `NonTail.sum` and `Tail.sum` are not definitionally equal.
Functions can be equal in more ways than just definitional equality, however.
It is also possible to prove that two functions are equal by proving that they produce equal outputs for the same input.
In other words, `f = g` can be proved by proving that `f(x) = g(x)` for all possible inputs `x`.
This principle is called *function extensionality*.
Function extensionality is exactly the reason why `NonTail.sum` equals `Tail.sum`: they both sum lists of numbers.

In Lean's tactic language, function extensionality is invoked using `funext`, followed by a name to be used for the arbitrary argument.
The arbitrary argument is added as an assumption to the context, and the goal changes to require a proof that the functions applied to this argument are equal:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := unsolved goals
hxs:List Nat⊢ NonTail.sum xs = Tail.sum xsby⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs`

```
unsolved goals
hxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
```

This goal can be proved by induction on the argument `xs`.
Both `sum` functions return `0` when applied to the empty list, which serves as a base case.
Adding a number to the beginning of the input list causes both functions to add that number to the result, which serves as an induction step.
Invoking the `induction` tactic results in two goals:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := by⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
induction xs with
| nil unsolved goals
h.nil⊢ NonTail.sum [] = Tail.sum []=> skiph.nil⊢ NonTail.sum [] = Tail.sum []
| cons y ys ih unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)=> skiph.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)`

```
unsolved goals
h.nil⊢ NonTail.sum [] = Tail.sum []
```

```
unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)
```

The base case for `nil` can be solved using `rfl`, because both functions return `0` when passed the empty list:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := by⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
induction xs with
| nil =>h.nil⊢ NonTail.sum [] = Tail.sum [] rflAll goals completed! 🐙
| cons y ys ih unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)=> skiph.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)`

The first step in solving the induction step is to simplify the goal, asking `simp` to unfold `NonTail.sum` and `Tail.sum`:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := by⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
induction xs with
| nil =>h.nil⊢ NonTail.sum [] = Tail.sum [] rflAll goals completed! 🐙
| cons y ys ih unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + NonTail.sum ys = Tail.sumHelper 0 (y :: ys)=>
simp [NonTail.sum, Tail.sum]h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + NonTail.sum ys = Tail.sumHelper 0 (y :: ys)h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)`

```
unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + NonTail.sum ys = Tail.sumHelper 0 (y :: ys)
```

Unfolding `Tail.sum` revealed that it immediately delegates to `Tail.sumHelper`, which should also be simplified:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := by⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
induction xs with
| nil =>h.nil⊢ NonTail.sum [] = Tail.sum [] rflAll goals completed! 🐙
| cons y ys ih unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + NonTail.sum ys = Tail.sumHelper y ys=>
simp [NonTail.sum, Tail.sum, Tail.sumHelper]h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + NonTail.sum ys = Tail.sumHelper y ysh.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)`

In the resulting goal, `sumHelper` has taken a step of computation and added `y` to the accumulator:

```
unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + NonTail.sum ys = Tail.sumHelper y ys
```

Rewriting with the induction hypothesis removes all mentions of `NonTail.sum` from the goal:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := by⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
induction xs with
| nil =>h.nil⊢ NonTail.sum [] = Tail.sum [] rflAll goals completed! 🐙
| cons y ys ih unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + Tail.sum ys = Tail.sumHelper y ys=>
simp [NonTail.sum, Tail.sum, Tail.sumHelper]h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + NonTail.sum ys = Tail.sumHelper y ys
rw [ih]h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + Tail.sum ys = Tail.sumHelper y ysh.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ NonTail.sum (y :: ys) = Tail.sum (y :: ys)`

```
unsolved goals
h.consy:Natys:List Natih:NonTail.sum ys = Tail.sum ys⊢ y + Tail.sum ys = Tail.sumHelper y ys
```

This new goal states that adding some number to the sum of a list is the same as using that number as the initial accumulator in `sumHelper`.
For the sake of clarity, this new goal can be proved as a separate theorem:

`theorem helper_add_sum_accum (xs : List Nat) (n : Nat) :
n + Tail.sum xs = Tail.sumHelper n xs := unsolved goals
xs:List Natn:Nat⊢ n + Tail.sum xs = Tail.sumHelper n xsbyxs:List Natn:Nat⊢ n + Tail.sum xs = Tail.sumHelper n xs
skipxs:List Natn:Nat⊢ n + Tail.sum xs = Tail.sumHelper n xs`

```
unsolved goals
xs:List Natn:Nat⊢ n + Tail.sum xs = Tail.sumHelper n xs
```

Once again, this is a proof by induction where the base case uses `rfl`:

`theorem helper_add_sum_accum (xs : List Nat) (n : Nat) :
n + Tail.sum xs = Tail.sumHelper n xs := byxs:List Natn:Nat⊢ n + Tail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>niln:Nat⊢ n + Tail.sum [] = Tail.sumHelper n [] rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consn y:Natys:List Natih:n + Tail.sum ys = Tail.sumHelper n ys⊢ n + Tail.sum (y :: ys) = Tail.sumHelper n (y :: ys)=> skipconsn:Naty:Natys:List Natih:n + Tail.sum ys = Tail.sumHelper n ys⊢ n + Tail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
consn y:Natys:List Natih:n + Tail.sum ys = Tail.sumHelper n ys⊢ n + Tail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
```

Because this is an inductive step, the goal should be simplified until it matches the induction hypothesis `ih`.
Simplifying, using the definitions of `Tail.sum` and `Tail.sumHelper`, results in the following:

`theorem helper_add_sum_accum (xs : List Nat) (n : Nat) :
n + Tail.sum xs = Tail.sumHelper n xs := byxs:List Natn:Nat⊢ n + Tail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>niln:Nat⊢ n + Tail.sum [] = Tail.sumHelper n [] rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consn y:Natys:List Natih:n + Tail.sum ys = Tail.sumHelper n ys⊢ n + Tail.sumHelper y ys = Tail.sumHelper (y + n) ys=>
simp [Tail.sum, Tail.sumHelper]consn:Naty:Natys:List Natih:n + Tail.sum ys = Tail.sumHelper n ys⊢ n + Tail.sumHelper y ys = Tail.sumHelper (y + n) ysconsn:Naty:Natys:List Natih:n + Tail.sum ys = Tail.sumHelper n ys⊢ n + Tail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
consn y:Natys:List Natih:n + Tail.sum ys = Tail.sumHelper n ys⊢ n + Tail.sumHelper y ys = Tail.sumHelper (y + n) ys
```

Ideally, the induction hypothesis could be used to replace `Tail.sumHelper (y + n) ys`, but they don't match.
The induction hypothesis can be used for `Tail.sumHelper n ys`, not `Tail.sumHelper (y + n) ys`.
In other words, this proof is stuck.

## 8.2.2. A Second Attempt[🔗](find/?domain=Verso.Genre.Manual.section&name=proving-sum-equal-again "Permalink")

Rather than attempting to muddle through the proof, it's time to take a step back and think.
Why is it that the tail-recursive version of the function is equal to the non-tail-recursive version?
Fundamentally speaking, at each entry in the list, the accumulator grows by the same amount as would be added to the result of the recursion.
This insight can be used to write an elegant proof.
Crucially, the proof by induction must be set up such that the induction hypothesis can be applied to *any* accumulator value.

Discarding the prior attempt, the insight can be encoded as the following statement:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := unsolved goals
xs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xsbyxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
skipxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs`

In this statement, it's very important that `n` is part of the type that's after the colon.
The resulting goal begins with `∀ (n : Nat)`, which is short for “For all `n`”:

```
unsolved goals
xs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
```

Using the induction tactic results in goals that include this “for all” statement:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil unsolved goals
nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []=> skipnil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)=> skipconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

In the `nil` case, the goal is:

```
unsolved goals
nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
```

For the induction step for `cons`, both the induction hypothesis and the specific goal contain the “for all `n`”:

```
unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
```

In other words, the goal has become more challenging to prove, but the induction hypothesis is correspondingly more useful.

A mathematical proof for a statement that beings with “for all `x`” should assume some arbitrary `x`, and prove the statement.
“Arbitrary” means that no additional properties of `x` are assumed, so the resulting statement will work for *any* `x`.
In Lean, a “for all” statement is a dependent function: no matter which specific value it is applied to, it will return evidence of the proposition.
Similarly, the process of picking an arbitrary `x` is the same as using `fun x => ...`.
In the tactic language, this process of selecting an arbitrary `x` is performed using the `intro` tactic, which produces the function behind the scenes when the tactic script has completed.
The `intro` tactic should be provided with the name to be used for this arbitrary value.

Using the `intro` tactic in the `nil` case removes the `∀ (n : Nat),` from the goal, and adds an assumption `n : Nat`:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil unsolved goals
niln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []=> intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)=> skipconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
niln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []
```

Both sides of this propositional equality are definitionally equal to `n`, so `rfl` suffices:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []
rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)=> skipconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

The `cons` goal also contains a “for all”:

```
unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
```

This suggests the use of `intro`.

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []
rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)=>
intro nconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
```

The proof goal now contains both `NonTail.sum` and `Tail.sumHelper` applied to `y :: ys`.
The simplifier can make the next step more clear:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []
rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + (y + NonTail.sum ys) = Tail.sumHelper (y + n) ys=>
intro nconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
simp [NonTail.sum, Tail.sumHelper]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + (y + NonTail.sum ys) = Tail.sumHelper (y + n) ysconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + (y + NonTail.sum ys) = Tail.sumHelper (y + n) ys
```

This goal is very close to matching the induction hypothesis.
There are two ways in which it does not match:

* The left-hand side of the equation is `n + (y + NonTail.sum ys)`, but the induction hypothesis needs the left-hand side to be a number added to `NonTail.sum ys`.
  In other words, this goal should be rewritten to `(n + y) + NonTail.sum ys`, which is valid because addition of natural numbers is associative.
* When the left side has been rewritten to `(y + n) + NonTail.sum ys`, the accumulator argument on the right side should be `n + y` rather than `y + n` in order to match.
  This rewrite is valid because addition is also commutative.

The associativity and commutativity of addition have already been proved in Lean's standard library.
The proof of associativity is named `Nat.add_assoc`, and its type is `(n m k : Nat) → (n + m) + k = n + (m + k)`, while the proof of commutativity is called `Nat.add_comm` and has type `(n m : Nat) → n + m = m + n`.
Normally, the `rw` tactic is provided with an expression whose type is an equality.
However, if the argument is instead a dependent function whose return type is an equality, it attempts to find arguments to the function that would allow the equality to match something in the goal.
There is only one opportunity to apply associativity, though the direction of the rewrite must be reversed because the right side of the equality in `(n + m) + k = n + (m + k)` is the one that matches the proof goal:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []
rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (y + n) ys=>
intro nconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
simp [NonTail.sum, Tail.sumHelper]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + (y + NonTail.sum ys) = Tail.sumHelper (y + n) ys
rw [←Nat.add_assoc]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (y + n) ysconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (y + n) ys
```

Rewriting directly with `rw [Nat.add_comm]`, however, leads to the wrong result.
The `rw` tactic guesses the wrong location for the rewrite, leading to an unintended goal:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []
rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ NonTail.sum ys + (n + y) = Tail.sumHelper (y + n) ys=>
intro nconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
simp [NonTail.sum, Tail.sumHelper]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + (y + NonTail.sum ys) = Tail.sumHelper (y + n) ys
rw [←Nat.add_assoc]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (y + n) ys
rw [Nat.add_comm]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ NonTail.sum ys + (n + y) = Tail.sumHelper (y + n) ysconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ NonTail.sum ys + (n + y) = Tail.sumHelper (y + n) ys
```

This can be fixed by explicitly providing `y` and `n` as arguments to `Nat.add_comm`:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n []
intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []
rflAll goals completed! 🐙
| cons y ys ih unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (n + y) ys=>
intro nconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
simp [NonTail.sum, Tail.sumHelper]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + (y + NonTail.sum ys) = Tail.sumHelper (y + n) ys
rw [←Nat.add_assoc]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (y + n) ys
rw [Nat.add_comm y n]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (n + y) ysconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)`

```
unsolved goals
consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (n + y) ys
```

The goal now matches the induction hypothesis.
In particular, the induction hypothesis's type is a dependent function type.
Applying `ih` to `n + y` results in exactly the desired type.
The `exact` tactic completes a proof goal if its argument has exactly the desired type:

`theorem non_tail_sum_eq_helper_accum (xs : List Nat) :
(n : Nat) → n + NonTail.sum xs = Tail.sumHelper n xs := byxs:List Nat⊢ ∀ (n : Nat), n + NonTail.sum xs = Tail.sumHelper n xs
induction xs with
| nil =>nil⊢ ∀ (n : Nat), n + NonTail.sum [] = Tail.sumHelper n [] intro nniln:Nat⊢ n + NonTail.sum [] = Tail.sumHelper n []; rflAll goals completed! 🐙
| cons y ys ih =>consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ys⊢ ∀ (n : Nat), n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
intro nconsy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + NonTail.sum (y :: ys) = Tail.sumHelper n (y :: ys)
simp [NonTail.sum, Tail.sumHelper]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + (y + NonTail.sum ys) = Tail.sumHelper (y + n) ys
rw [←Nat.add_assoc]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (y + n) ys
rw [Nat.add_comm y n]consy:Natys:List Natih:∀ (n : Nat), n + NonTail.sum ys = Tail.sumHelper n ysn:Nat⊢ n + y + NonTail.sum ys = Tail.sumHelper (n + y) ys
exact ih (n + y)All goals completed! 🐙`

The actual proof requires only a little additional work to get the goal to match the helper's type.
The first step is still to invoke function extensionality:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := unsolved goals
hxs:List Nat⊢ NonTail.sum xs = Tail.sum xsby⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs`

```
unsolved goals
hxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
```

The next step is unfold `Tail.sum`, exposing `Tail.sumHelper`:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := unsolved goals
hxs:List Nat⊢ NonTail.sum xs = Tail.sumHelper 0 xsby⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
simp [Tail.sum]hxs:List Nat⊢ NonTail.sum xs = Tail.sumHelper 0 xs`

```
unsolved goals
hxs:List Nat⊢ NonTail.sum xs = Tail.sumHelper 0 xs
```

Having done this, the types almost match.
However, the helper has an additional addend on the left side.
In other words, the proof goal is `NonTail.sum xs = Tail.sumHelper 0 xs`, but applying `non_tail_sum_eq_helper_accum` to `xs` and `0` yields the type `0 + NonTail.sum xs = Tail.sumHelper 0 xs`.
Another standard library proof, `Nat.zero_add`, has type `(n : Nat) → 0 + n = n`.
Applying this function to `NonTail.sum xs` results in an expression with type `0 + NonTail.sum xs = NonTail.sum xs`, so rewriting from right to left results in the desired goal:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := unsolved goals
hxs:List Nat⊢ 0 + NonTail.sum xs = Tail.sumHelper 0 xsby⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
simp [Tail.sum]hxs:List Nat⊢ NonTail.sum xs = Tail.sumHelper 0 xs
rw [←Nat.zero_add (NonTail.sum xs)]hxs:List Nat⊢ 0 + NonTail.sum xs = Tail.sumHelper 0 xs`

```
unsolved goals
hxs:List Nat⊢ 0 + NonTail.sum xs = Tail.sumHelper 0 xs
```

Finally, the helper can be used to complete the proof:

`theorem non_tail_sum_eq_tail_sum : NonTail.sum = Tail.sum := by⊢ NonTail.sum = Tail.sum
funext xshxs:List Nat⊢ NonTail.sum xs = Tail.sum xs
simp [Tail.sum]hxs:List Nat⊢ NonTail.sum xs = Tail.sumHelper 0 xs
rw [←Nat.zero_add (NonTail.sum xs)]hxs:List Nat⊢ 0 + NonTail.sum xs = Tail.sumHelper 0 xs
exact non_tail_sum_eq_helper_accum xs 0All goals completed! 🐙`

This proof demonstrates a general pattern that can be used when proving that an accumulator-passing tail-recursive function is equal to the non-tail-recursive version.
The first step is to discover the relationship between the starting accumulator argument and the final result.
For instance, beginning `Tail.sumHelper` with an accumulator of `n` results in the final sum being added to `n`, and beginning `Tail.reverseHelper` with an accumulator of `ys` results in the final reversed list being prepended to `ys`.
The second step is to write down this relationship as a theorem statement and prove it by induction.
While the accumulator is always initialized with some neutral value in practice, such as `0` or `[]`, this more general statement that allows the starting accumulator to be any value is what's needed to get a strong enough induction hypothesis.
Finally, using this helper theorem with the actual initial accumulator value results in the desired proof.
For example, in `non_tail_sum_eq_tail_sum`, the accumulator is specified to be `0`.
This may require rewriting the goal to make the neutral initial accumulator values occur in the right place.

## 8.2.3. Functional Induction[🔗](find/?domain=Verso.Genre.Manual.section&name=fun-induction "Permalink")

The proof of `non_tail_sum_eq_helper_accum` follows the implementation of `Tail.sumHelper` closely.
There is not, however, a perfect match between the implementation and the structure expected by mathematical induction, which makes it necessary to manage the assumption `n` carefully.
This is a small amount of work in the case of `non_tail_sum_eq_helper_accum`, but proofs about functions whose definitions are further from the structure expected by `induction` require more bookkeeping.

In addition to proving theorems about recursive functions by induction on one of the arguments, Lean supports proofs by induction on the recursive call structure of functions.
This *functional induction* results in a base case for each branch of the function's control flow that does not include a recursive call, and inductive steps for each branch that does.
A proof by functional induction should demonstrate that the theorem holds for the non-recursive branches, and that if the theorem holds for the result of each recursive call, then it also holds for the result of the recursive branch.