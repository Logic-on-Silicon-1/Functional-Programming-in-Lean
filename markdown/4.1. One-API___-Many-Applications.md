# One API, Many Applications

Source: https://lean-lang.org/functional_programming_in_lean/Monads/One-API___-Many-Applications

All these features and more can be implemented in library code as instances of a common API called `Monad`.
Lean provides dedicated syntax that makes this API convenient to use, but can also get in the way of understanding what is going on behind the scenes.
This chapter begins with the nitty-gritty presentation of manually nesting null checks, and builds from there to the convenient, general API.
Please suspend your disbelief in the meantime.

## 4.1.1. Checking for `none`: Don't Repeat Yourself[🔗](find/?domain=Verso.Genre.Manual.section&name=example-option-monad "Permalink")

In Lean, pattern matching can be used to chain checks for null.
Getting the first entry from a list can just use the optional indexing notation:

`def first (xs : List α) : Option α :=
xs[0]?`

The fundamental problem with this code is that it addresses two concerns: extracting the numbers and checking that all of them are present.
The second concern is addressed by copying and pasting the code that handles the `none` case.
It is often good style to lift a repetitive segment into a helper function:

`def andThen (opt : Option α) (next : α → Option β) : Option β :=
match opt with
| none => none
| some x => next x`

This helper, which is used similarly to `?.` in C# and Kotlin, takes care of propagating `none` values.
It takes two arguments: an optional value and a function to apply when the value is not `none`.
If the first argument is `none`, then the helper returns `none`.
If the first argument is not `none`, then the function is applied to the contents of the `some` constructor.

Now, `firstThird` can be rewritten to use `andThen` instead of pattern matching:

`def firstThird (xs : List α) : Option (α × α) :=
andThen xs[0]? fun first =>
andThen xs[2]? fun third =>
some (first, third)`

In Lean, functions don't need to be enclosed in parentheses when passed as arguments.
The following equivalent definition uses more parentheses and indents the bodies of functions:

`def firstThird (xs : List α) : Option (α × α) :=
andThen xs[0]? (fun first =>
andThen xs[2]? (fun third =>
some (first, third)))`

The `andThen` helper provides a sort of “pipeline” through which values flow, and the version with the somewhat unusual indentation is more suggestive of this fact.
Improving the syntax used to write `andThen` can make these computations even easier to understand.

### 4.1.1.1. Infix Operators[🔗](find/?domain=Verso.Genre.Manual.section&name=defining-infix-operators "Permalink")

In Lean, infix operators can be declared using the `infix`, `infixl`, and `infixr` commands, which create (respectively) non-associative, left-associative, and right-associative operators.
When used multiple times in a row, a *left associative* operator stacks up the opening parentheses on the left side of the expression.
The addition operator `+` is left associative, so `w + x + y + z` is equivalent to `(((w + x) + y) + z)`.
The exponentiation operator `^` is right associative, so `w ^ x ^ y ^ z` is equivalent to `w ^ (x ^ (y ^ z))`.
Comparison operators such as `<` are non-associative, so `x < y < z` is a syntax error and requires manual parentheses.

The following declaration makes `andThen` into an infix operator:

`infixl:55 " ~~> " => andThen`

The number following the colon declares the *precedence* of the new infix operator.
In ordinary mathematical notation, `x + y * z` is equivalent to `x + (y * z)` even though both `+` and `*` are left associative.
In Lean, `+` has precedence 65 and `*` has precedence 70.
Higher-precedence operators are applied before lower-precedence operators.
According to the declaration of `~~>`, both `+` and `*` have higher precedence, and thus apply first.
Typically, figuring out the most convenient precedences for a group of operators requires some experimentation and a large collection of examples.

Following the new infix operator is a double arrow `=>`, which specifies the named function to be used for the infix operator.
Lean's standard library uses this feature to define `+` and `*` as infix operators that point at `HAdd.hAdd` and `HMul.hMul`, respectively, allowing type classes to be used to overload the infix operators.
Here, however, `andThen` is just an ordinary function.

Having defined an infix operator for `andThen`, `firstThird` can be rewritten in a way that brings the “pipeline” feeling of `none`-checks front and center:

`def firstThirdInfix (xs : List α) : Option (α × α) :=
xs[0]? ~~> fun first =>
xs[2]? ~~> fun third =>
some (first, third)`

This style is much more concise when writing larger functions:

`def firstThirdFifthSeventh (xs : List α) : Option (α × α × α × α) :=
xs[0]? ~~> fun first =>
xs[2]? ~~> fun third =>
xs[4]? ~~> fun fifth =>
xs[6]? ~~> fun seventh =>
some (first, third, fifth, seventh)`

## 4.1.2. Propagating Error Messages[🔗](find/?domain=Verso.Genre.Manual.section&name=example-except-monad "Permalink")

Pure functional languages such as Lean have no built-in exception mechanism for error handling, because throwing or catching an exception is outside of the step-by-step evaluation model for expressions.
However, functional programs certainly need to handle errors.
In the case of `firstThirdFifthSeventh`, it is likely relevant for a user to know just how long the list was and where the lookup failed.

This is typically accomplished by defining a datatype that can be either an error or a result, and translating functions with exceptions into functions that return this datatype:

`inductive Except (ε : Type) (α : Type) where
| error : ε → Except ε α
| ok : α → Except ε α
deriving BEq, Hashable, Repr`

The type variable `ε` stands for the type of errors that can be produced by the function.
Callers are expected to handle both errors and successes, which makes the type variable `ε` play a role that is a bit like that of a list of checked exceptions in Java.

Once again, a common pattern can be factored out into a helper.
Each step through the function checks for an error, and only proceeds with the rest of the computation if the result was a success.
A new version of `andThen` can be defined for `Except`:

`def andThen (attempt : Except e α) (next : α → Except e β) : Except e β :=
match attempt with
| Except.error msg => Except.error msg
| Except.ok x => next x`

Just as with `Option`, this version of `andThen` allows a more concise definition of `firstThird'`:

`def firstThird' (xs : List α) : Except String (α × α) :=
andThen (get xs 0) fun first =>
andThen (get xs 2) fun third =>
Except.ok (first, third)`

In both the `Option` and `Except` case, there are two repeating patterns: there is the checking of intermediate results at each step, which has been factored out into `andThen`, and there is the final successful result, which is `some` or `Except.ok`, respectively.
For the sake of convenience, success can be factored out into a helper called `ok`:

`def ok (x : α) : Except ε α := Except.ok x`

Similarly, failure can be factored out into a helper called `fail`:

`def fail (err : ε) : Except ε α := Except.error err`

Using `ok` and `fail` makes `get` a little more readable:

`def get (xs : List α) (i : Nat) : Except String α :=
match xs[i]? with
| none => fail s!"Index {i} not found (maximum is {xs.length - 1})"
| some x => ok x`

After adding the infix declaration for `andThen`, `firstThird` can be just as concise as the version that returns an `Option`:

`infixl:55 " ~~> " => andThen``def firstThird (xs : List α) : Except String (α × α) :=
get xs 0 ~~> fun first =>
get xs 2 ~~> fun third =>
ok (first, third)`

The technique scales similarly to larger functions:

`def firstThirdFifthSeventh (xs : List α) : Except String (α × α × α × α) :=
get xs 0 ~~> fun first =>
get xs 2 ~~> fun third =>
get xs 4 ~~> fun fifth =>
get xs 6 ~~> fun seventh =>
ok (first, third, fifth, seventh)`

## 4.1.3. Logging[🔗](find/?domain=Verso.Genre.Manual.section&name=logging "Permalink")

A number is even if dividing it by 2 leaves no remainder:

`def isEven (i : Int) : Bool :=
i % 2 == 0`

The function `sumAndFindEvens` computes the sum of a list while remembering the even numbers encountered along the way:

`def sumAndFindEvens : List Int → List Int × Int
| [] => ([], 0)
| i :: is =>
let (moreEven, sum) := sumAndFindEvens is
(if isEven i then i :: moreEven else moreEven, sum + i)`

This function is a simplified example of a common pattern.
Many programs need to traverse a data structure once, while both computing a main result and accumulating some kind of tertiary extra result.
One example of this is logging: a program that is an `IO` action can always log to a file on disk, but because the disk is outside of the mathematical world of Lean functions, it becomes much more difficult to prove things about logs based on `IO`.
Another example is a function that computes the sum of all the nodes in a tree with an inorder traversal, while simultaneously recording each nodes visited:

`def inorderSum : BinTree Int → List Int × Int
| BinTree.leaf => ([], 0)
| BinTree.branch l x r =>
let (leftVisited, leftSum) := inorderSum l
let (hereVisited, hereSum) := ([x], x)
let (rightVisited, rightSum) := inorderSum r
(leftVisited ++ hereVisited ++ rightVisited,
leftSum + hereSum + rightSum)`

Both `sumAndFindEvens` and `inorderSum` have a common repetitive structure.
Each step of computation returns a pair that consists of a list of data that have been saved along with the primary result.
The lists are then appended, and the primary result is computed and paired with the appended lists.
The common structure becomes more apparent with a small rewrite of `sumAndFindEvens` that more cleanly separates the concerns of saving even numbers and computing the sum:

`def sumAndFindEvens : List Int → List Int × Int
| [] => ([], 0)
| i :: is =>
let (moreEven, sum) := sumAndFindEvens is
let (evenHere, ()) := (if isEven i then [i] else [], ())
(evenHere ++ moreEven, sum + i)`

For the sake of clarity, a pair that consists of an accumulated result together with a value can be given its own name:

`structure WithLog (logged : Type) (α : Type) where
log : List logged
val : α`

Similarly, the process of saving a list of accumulated results while passing a value on to the next step of a computation can be factored out into a helper, once again named `andThen`:

`def andThen (result : WithLog α β) (next : β → WithLog α γ) : WithLog α γ :=
let {log := thisOut, val := thisRes} := result
let {log := nextOut, val := nextRes} := next thisRes
{log := thisOut ++ nextOut, val := nextRes}`

In the case of errors, `ok` represents an operation that always succeeds.
Here, however, it is an operation that simply returns a value without logging anything:

`def ok (x : β) : WithLog α β := {log := [], val := x}`

Just as `Except` provides `fail` as a possibility, `WithLog` should allow items to be added to a log.
This has no interesting return value associated with it, so it returns `Unit`:

`def save (data : α) : WithLog α Unit :=
{log := [data], val := ()}`

`WithLog`, `andThen`, `ok`, and `save` can be used to separate the logging concern from the summing concern in both programs:

`def sumAndFindEvens : List Int → WithLog Int Int
| [] => ok 0
| i :: is =>
andThen (if isEven i then save i else ok ()) fun () =>
andThen (sumAndFindEvens is) fun sum =>
ok (i + sum)``def inorderSum : BinTree Int → WithLog Int Int
| BinTree.leaf => ok 0
| BinTree.branch l x r =>
andThen (inorderSum l) fun leftSum =>
andThen (save x) fun () =>
andThen (inorderSum r) fun rightSum =>
ok (leftSum + x + rightSum)`

And, once again, the infix operator helps put focus on the correct steps:

`infixl:55 " ~~> " => andThen``def sumAndFindEvens : List Int → WithLog Int Int
| [] => ok 0
| i :: is =>
(if isEven i then save i else ok ()) ~~> fun () =>
sumAndFindEvens is ~~> fun sum =>
ok (i + sum)
def inorderSum : BinTree Int → WithLog Int Int
| BinTree.leaf => ok 0
| BinTree.branch l x r =>
inorderSum l ~~> fun leftSum =>
save x ~~> fun () =>
inorderSum r ~~> fun rightSum =>
ok (leftSum + x + rightSum)`

## 4.1.4. Numbering Tree Nodes[🔗](find/?domain=Verso.Genre.Manual.section&name=numbering-tree-nodes "Permalink")

An *inorder numbering* of a tree associates each data point in the tree with the step it would be visited at in an inorder traversal of the tree.
For example, consider `aTree`:

`open BinTree in
def aTree :=
branch
(branch
(branch leaf "a" (branch leaf "b" leaf))
"c"
leaf)
"d"
(branch leaf "e" leaf)`

Its inorder numbering is:

```
BinTree.branch
  (BinTree.branch
    (BinTree.branch (BinTree.leaf) (0, "a") (BinTree.branch (BinTree.leaf) (1, "b") (BinTree.leaf)))
    (2, "c")
    (BinTree.leaf))
  (3, "d")
  (BinTree.branch (BinTree.leaf) (4, "e") (BinTree.leaf))
```

Trees are most naturally processed with recursive functions, but the usual pattern of recursion on trees makes it difficult to compute an inorder numbering.
This is because the highest number assigned anywhere in the left subtree is used to determine the numbering of a node's data value, and then again to determine the starting point for numbering the right subtree.
In an imperative language, this issue can be worked around by using a mutable variable that contains the next number to be assigned.
The following Python program computes an inorder numbering using a mutable variable:

```
class Branch:
    def __init__(self, value, left=None, right=None):
        self.left = left
        self.value = value
        self.right = right
    def __repr__(self):
        return f'Branch({self.value!r}, left={self.left!r}, right={self.right!r})'

def number(tree):
    num = 0
    def helper(t):
        nonlocal num
        if t is None:
            return None
        else:
            new_left = helper(t.left)
            new_value = (num, t.value)
            num += 1
            new_right = helper(t.right)
            return Branch(left=new_left, value=new_value, right=new_right)

    return helper(tree)
```

The numbering of the Python equivalent of `aTree` is:

```
a_tree = Branch("d",
                left=Branch("c",
                            left=Branch("a", left=None, right=Branch("b")),
                            right=None),
                right=Branch("e"))
```

and its numbering is:

`>>> number(a_tree)`

```
Branch((3, 'd'), left=Branch((2, 'c'), left=Branch((0, 'a'), left=None, right=Branch((1, 'b'), left=None, right=None)), right=None), right=Branch((4, 'e'), left=None, right=None))
```

Even though Lean does not have mutable variables, a workaround exists.
From the point of view of the rest of the world, the mutable variable can be thought of as having two relevant aspects: its value when the function is called, and its value when the function returns.
In other words, a function that uses a mutable variable can be seen as a function that takes the mutable variable's starting value as an argument, returning a pair of the variable's final value and the function's result.
This final value can then be passed as an argument to the next step.

Just as the Python example uses an outer function that establishes a mutable variable and an inner helper function that changes the variable, a Lean version of the function uses an outer function that provides the variable's starting value and explicitly returns the function's result along with an inner helper function that threads the variable's value while computing the numbered tree:

`def number (t : BinTree α) : BinTree (Nat × α) :=
let rec helper (n : Nat) : BinTree α → (Nat × BinTree (Nat × α))
| BinTree.leaf => (n, BinTree.leaf)
| BinTree.branch left x right =>
let (k, numberedLeft) := helper n left
let (i, numberedRight) := helper (k + 1) right
(i, BinTree.branch numberedLeft (k, x) numberedRight)
(helper 0 t).snd`

This code, like the `none`-propagating `Option` code, the `error`-propagating `Except` code, and the log-accumulating `WithLog` code, commingles two concerns: propagating the value of the counter, and actually traversing the tree to find the result.
Just as in those cases, an `andThen` helper can be defined to propagate state from one step of a computation to another.
The first step is to give a name to the pattern of taking an input state as an argument and returning an output state together with a value:

`def State (σ : Type) (α : Type) : Type :=
σ → (σ × α)`

In the case of `State`, `ok` is a function that returns the input state unchanged, along with the provided value:

`def ok (x : α) : State σ α :=
fun s => (s, x)`

When working with a mutable variable, there are two fundamental operations: reading the value and replacing it with a new one.
Reading the current value is accomplished with a function that places the input state unmodified into the output state, and also places it into the value field:

`def get : State σ σ :=
fun s => (s, s)`

Writing a new value consists of ignoring the input state, and placing the provided new value into the output state:

`def set (s : σ) : State σ Unit :=
fun _ => (s, ())`

Finally, two computations that use state can be sequenced by finding both the output state and return value of the first function, then passing them both into the next function:

`def andThen (first : State σ α) (next : α → State σ β) : State σ β :=
fun s =>
let (s', x) := first s
next x s'
infixl:55 " ~~> " => andThen`

Using `State` and its helpers, local mutable state can be simulated:

`def number (t : BinTree α) : BinTree (Nat × α) :=
let rec helper : BinTree α → State Nat (BinTree (Nat × α))
| BinTree.leaf => ok BinTree.leaf
| BinTree.branch left x right =>
helper left ~~> fun numberedLeft =>
get ~~> fun n =>
set (n + 1) ~~> fun () =>
helper right ~~> fun numberedRight =>
ok (BinTree.branch numberedLeft (n, x) numberedRight)
(helper t 0).snd`

Because `State` simulates only a single local variable, `get` and `set` don't need to refer to any particular variable name.