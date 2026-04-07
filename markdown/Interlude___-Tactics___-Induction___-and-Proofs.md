# Interlude: Tactics, Induction, and Proofs

Source: https://lean-lang.org/functional_programming_in_lean/Interlude___-Tactics___-Induction___-and-Proofs

# Interlude: Tactics, Induction, and Proofs[🔗](find/?domain=Verso.Genre.Manual.section&name=tactics-induction-proofs "Permalink")

인터루드: 전술, 귀납법, 그리고 증명

## A Note on Proofs and User Interfaces[🔗](find/?domain=Verso.Genre.Manual.section&name=proofs-and-uis "Permalink")

증명과 사용자 인터페이스에 대한 참고사항

This book presents the process of writing proofs as if they are written in one go and submitted to Lean, which then replies with error messages that describe what remains to be done.
The actual process of interacting with Lean is much more pleasant.
Lean provides information about the proof as the cursor is moved through it and there are a number of interactive features that make proving easier.
Please consult the documentation of your Lean development environment for more information.

이 책은 증명을 한 번에 작성하여 Lean에 제출하면 남은 작업을 설명하는 오류 메시지로 응답하는 과정을 제시합니다.
Lean과 상호 작용하는 실제 프로세스는 훨씬 더 유쾌합니다.
Lean은 커서가 증명을 통해 이동할 때 증명에 대한 정보를 제공하며, 증명을 더 쉽게 만드는 많은 인터랙티브 기능이 있습니다.
더 많은 정보는 Lean 개발 환경의 문서를 참고하십시오.

The approach in this book that focuses on incrementally building a proof and showing the messages that result demonstrates the kinds of interactive feedback that Lean provides while writing a proof, even though it is much slower than the process used by experts.
At the same time, seeing incomplete proofs evolve towards completeness is a useful perspective on proving.
As your skill in writing proofs increases, Lean's feedback will come to feel less like errors and more like support for your own thought processes.
Learning the interactive approach is very important.

이 책의 접근 방식은 증명을 점진적으로 구축하고 결과 메시지를 보여주는 데 중점을 두고 있으며, 이는 Lean이 증명을 작성할 때 제공하는 인터랙티브 피드백의 종류를 보여줍니다. 비록 전문가들이 사용하는 프로세스보다는 훨씬 느리지만요.
동시에, 불완전한 증명이 완성을 향해 진화하는 것을 보는 것은 증명에 대한 유용한 관점입니다.
증명 작성 기술이 증가하면서, Lean의 피드백은 오류처럼 느껴지는 것에서 자신의 사고 과정을 지원하는 것으로 느껴질 것입니다.
인터랙티브 접근 방식을 배우는 것은 매우 중요합니다.

## Recursion and Induction[🔗](find/?domain=Verso.Genre.Manual.section&name=recursion-vs-induction "Permalink")

재귀와 귀납법

The functions `plusR_succ_left` and `plusR_zero_left` from the preceding chapter can be seen from two perspectives.
On the one hand, they are recursive functions that build up evidence for a proposition, just as other recursive functions might construct a list, a string, or any other data structure.
On the other, they also correspond to proofs by *mathematical induction*.

이전 장의 함수 `plusR_succ_left`와 `plusR_zero_left`는 두 가지 관점에서 볼 수 있습니다.
한편으로는 다른 재귀 함수가 리스트, 문자열 또는 다른 데이터 구조를 구성할 수 있는 것처럼 명제에 대한 증거를 구축하는 재귀 함수입니다.
다른 한편으로는 *수학적 귀납법*에 의한 증명에 해당합니다.

Mathematical induction is a proof technique where a statement is proven for *all* natural numbers in two steps:

1. The statement is shown to hold for `0`. This is called the *base case*.
2. Under the assumption that the statement holds for some arbitrarily chosen number `n`, it is shown to hold for `n + 1`. This is called the *induction step*. The assumption that the statement holds for `n` is called the *induction hypothesis*.

Because it's impossible to check the statement for *every* natural number, induction provides a means of writing a proof that could, in principle, be expanded to any particular natural number.
For example, if a concrete proof were desired for the number 3, then it could be constructed by using first the base case and then the induction step three times, to show the statement for 0, 1, 2, and finally 3.
Thus, it proves the statement for all natural numbers.

수학적 귀납법은 명제를 두 단계로 *모든* 자연수에 대해 증명하는 증명 기법입니다:

1. 명제가 `0`에 대해 성립함을 보입니다. 이를 *기저 사례*라고 합니다.
2. 명제가 임의로 선택된 어떤 수 `n`에 대해 성립한다는 가정 하에, 명제가 `n + 1`에 대해 성립함을 보입니다. 이를 *귀납 단계*라고 합니다. 명제가 `n`에 대해 성립한다는 가정을 *귀납 가설*이라고 합니다.

*모든* 자연수에 대해 명제를 확인하는 것은 불가능하므로, 귀납법은 원칙적으로 특정 자연수로 확장할 수 있는 증명을 작성하는 수단을 제공합니다.
예를 들어, 3에 대한 구체적인 증명이 필요하다면, 먼저 기저 사례를 사용한 후 귀납 단계를 세 번 사용하여 0, 1, 2, 그리고 마지막으로 3에 대한 명제를 보임으로써 구성할 수 있습니다.
따라서 이는 모든 자연수에 대한 명제를 증명합니다.

## The Induction Tactic[🔗](find/?domain=Verso.Genre.Manual.section&name=induction-tactic "Permalink")

귀납 전술

Writing proofs by induction as recursive functions that use helpers such as `congrArg` does not always do a good job of expressing the intentions behind the proof.
While recursive functions indeed have the structure of induction, they should probably be viewed as an *encoding* of a proof.
Furthermore, Lean's tactic system provides a number of opportunities to automate the construction of a proof that are not available when writing the recursive function explicitly.
Lean provides an induction *tactic* that can carry out an entire proof by induction in a single tactic block.
Behind the scenes, Lean constructs the recursive function that corresponds the use of induction.

귀납법으로 증명을 작성하되 `congrArg`와 같은 헬퍼를 사용하는 재귀 함수로 작성하는 것은 항상 증명 뒤의 의도를 잘 표현하지 못합니다.
재귀 함수는 확실히 귀납법의 구조를 가지고 있지만, *증명의 인코딩*으로 봐야 할 것입니다.
더욱이, Lean의 전술 시스템은 재귀 함수를 명시적으로 작성할 때는 사용할 수 없는 증명 구성의 자동화 기회를 많이 제공합니다.
Lean은 전체 귀납 증명을 단일 전술 블록에서 수행할 수 있는 귀납 *전술*을 제공합니다.
배경에서 Lean은 귀납법의 사용에 해당하는 재귀 함수를 구성합니다.

To prove `plusR_zero_left` with the `induction` tactic, begin by writing its signature (using `theorem`, because this really is a proof).
Then, use `by induction k` as the body of the definition:

`induction` 전술로 `plusR_zero_left`를 증명하려면, 먼저 서명을 작성하고 시작합니다(`theorem`을 사용합니다. 이것이 정말로 증명이기 때문입니다).
그런 다음, `by induction k`를 정의의 본문으로 사용합니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := unsolved goals
zero⊢ 0 = Nat.plusR 0 0

succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1)byk:Nat⊢ k = Nat.plusR 0 k
induction kzero⊢ 0 = Nat.plusR 0 0succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1)`

The resulting message states that there are two goals:

```
unsolved goals
zero⊢ 0 = Nat.plusR 0 0

succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1)
```

결과 메시지는 두 개의 목표가 있음을 나타냅니다:

```
미해결 목표
zero⊢ 0 = Nat.plusR 0 0

succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1)
```

A tactic block is a program that is run while the Lean type checker processes a file, somewhat like a much more powerful C preprocessor macro.
The tactics generate the actual program.

전술 블록은 Lean 타입 체커가 파일을 처리하는 동안 실행되는 프로그램으로, 훨씬 더 강력한 C 전처리기 매크로와 유사합니다.
전술은 실제 프로그램을 생성합니다.

In the tactic language, there can be a number of goals.
Each goal consists of a type together with some assumptions.
These are analogous to using underscores as placeholders—the type in the goal represents what is to be proved, and the assumptions represent what is in-scope and can be used.
In the case of the goal `case zero`, there are no assumptions and the type is `Nat.zero = Nat.plusR 0 Nat.zero`—this is the theorem statement with `0` instead of `k`.
In the goal `case succ`, there are two assumptions, named `n✝` and `n_ih✝`.
Behind the scenes, the `induction` tactic creates a dependent pattern match that refines the overall type, and `n✝` represents the argument to `Nat.succ` in the pattern.
The assumption `n_ih✝` represents the result of calling the generated function recursively on `n✝`.
Its type is the overall type of the theorem, just with `n✝` instead of `k`.
The type to be fulfilled as part of the goal `case succ` is the overall theorem statement, with `Nat.succ n✝` instead of `k`.

전술 언어에서는 여러 목표가 있을 수 있습니다.
각 목표는 타입과 일부 가정으로 구성됩니다.
이는 밑줄을 자리표시자로 사용하는 것과 유사합니다. 목표의 타입은 증명할 것을 나타내고, 가정은 범위 내에 있고 사용할 수 있는 것을 나타냅니다.
목표 `case zero`의 경우, 가정이 없고 타입은 `Nat.zero = Nat.plusR 0 Nat.zero`입니다. 이는 정리 명제에서 `k` 대신 `0`을 사용한 것입니다.
목표 `case succ`에서, `n✝`과 `n_ih✝`이라는 두 개의 가정이 있습니다.
배경에서, `induction` 전술은 전체 타입을 구체화하는 종속 패턴 매칭을 생성하며, `n✝`은 패턴의 `Nat.succ`에 대한 인수를 나타냅니다.
가정 `n_ih✝`은 `n✝`에서 생성된 함수를 재귀적으로 호출한 결과를 나타냅니다.
그 타입은 정리의 전체 타입과 같지만, `k` 대신 `n✝`을 사용합니다.
목표 `case succ`의 일부로 충족해야 할 타입은 전체 정리 명제이며, `k` 대신 `Nat.succ n✝`을 사용합니다.

The two goals that result from the use of the `induction` tactic correspond to the base case and the induction step in the description of mathematical induction.
The base case is `case zero`.
In `case succ`, `n_ih✝` corresponds to the induction hypothesis, while the whole of `case succ` is the induction step.

The next step in writing the proof is to focus on each of the two goals in turn.
Just as `pure ()` can be used in a `do` block to indicate “do nothing”, the tactic language has a statement `skip` that also does nothing.
This can be used when Lean's syntax requires a tactic, but it's not yet clear which one should be used.
Adding `with` to the end of the `induction` statement provides a syntax that is similar to pattern matching:

`induction` 전술의 사용으로 인한 두 목표는 수학적 귀납법의 설명에서 기저 사례와 귀납 단계에 해당합니다.
기저 사례는 `case zero`입니다.
`case succ`에서, `n_ih✝`은 귀납 가설에 해당하고, `case succ` 전체는 귀납 단계입니다.

증명을 작성할 때의 다음 단계는 두 목표 각각에 차례로 집중하는 것입니다.
`do` 블록에서 “아무것도 하지 않음”을 나타내기 위해 `pure ()`를 사용할 수 있는 것처럼, 전술 언어에는 역시 아무것도 하지 않는 `skip` 문이 있습니다.
Lean의 구문에서 전술이 필요하지만 어떤 것을 사용할지 아직 명확하지 않을 때 사용할 수 있습니다.
`induction` 문의 끝에 `with`를 추가하면 패턴 매칭과 유사한 구문을 제공합니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero unsolved goals
zero⊢ 0 = Nat.plusR 0 0=> skipzero⊢ 0 = Nat.plusR 0 0
| succ n ih unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)=> skipsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)`

Each of the two `skip` statements has a message associated with it.
The first shows the base case:

```
unsolved goals
zero⊢ 0 = Nat.plusR 0 0
```

The second shows the induction step:

```
unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)
```

In the induction step, the inaccessible names with daggers have been replaced with the names provided after `succ`, namely `n` and `ih`.

The cases after `induction``...``with` are not patterns: they consist of the name of a goal followed by zero or more names.
The names are used for assumptions introduced in the goal; it is an error to provide more names than the goal introduces:

두 `skip` 문 각각에는 관련 메시지가 있습니다.
첫 번째는 기저 사례를 보여줍니다:

```
미해결 목표
zero⊢ 0 = Nat.plusR 0 0
```

두 번째는 귀납 단계를 보여줍니다:

```
미해결 목표
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)
```

귀납 단계에서, 대거가 있는 접근 불가능한 이름이 `succ` 뒤에 제공된 이름 `n`과 `ih`로 대체되었습니다.

`induction``...``with` 뒤의 사례는 패턴이 아닙니다. 목표의 이름 다음에 0개 이상의 이름으로 구성됩니다.
이름은 목표에서 도입된 가정에 사용되며, 목표가 도입한 것보다 더 많은 이름을 제공하는 것은 오류입니다:

`` theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero unsolved goals
zero⊢ 0 = Nat.plusR 0 0=> skipzero⊢ 0 = Nat.plusR 0 0
Too many variable names provided at alternative `succ`: 5 provided, but 2 expected| succ n ih lots of names unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)=> skipsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1) ``

```
Too many variable names provided at alternative `succ`: 5 provided, but 2 expected
```

```
대안 `succ`에 너무 많은 변수 이름이 제공되었습니다: 5개 제공, 2개 예상
```

Focusing on the base case, the `rfl` tactic works just as well inside of the `induction` tactic as it does in a recursive function:

기저 사례에 집중하면, `rfl` 전술은 `induction` 전술 내에서 재귀 함수에서처럼 똑같이 잘 작동합니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)=> skipsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)`

In the recursive function version of the proof, a type annotation made the expected type something that was easier to understand.
In the tactic language, there are a number of specific ways to transform a goal to make it easier to solve.
The `unfold` tactic replaces a defined name with its definition:

증명의 재귀 함수 버전에서, 타입 주석은 예상 타입을 더 이해하기 쉬운 것으로 만들었습니다.
전술 언어에서, 목표를 더 쉽게 해결할 수 있도록 변환하는 여러 가지 방법이 있습니다.
`unfold` 전술은 정의된 이름을 그 정의로 바꿉니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 n + 1=>
unfold Nat.plusRsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 n + 1succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)`

Now, the right-hand side of the equality in the goal has become `Nat.plusR 0 n + 1` instead of `Nat.plusR 0 (Nat.succ n)`:

```
unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 n + 1
```

Instead of appealing to functions like `congrArg` and operators like `▸`, there are tactics that allow equality proofs to be used to transform proof goals.
One of the most important is `rw`, which takes a list of equality proofs and replaces the left side with the right side in the goal.
This almost does the right thing in `plusR_zero_left`:

이제 목표의 등식의 우변이 `Nat.plusR 0 (Nat.succ n)` 대신 `Nat.plusR 0 n + 1`이 되었습니다:

```
미해결 목표
succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 n + 1
```

`congrArg`와 같은 함수나 `▸`와 같은 연산자에 호소하는 대신, 등식 증명을 증명 목표를 변환하는 데 사용할 수 있는 전술이 있습니다.
가장 중요한 것 중 하나는 `rw`로, 등식 증명 목록을 가져가며 목표에서 좌변을 우변으로 바꿉니다.
이는 `plusR_zero_left`에서 거의 올바른 일을 합니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ Nat.plusR 0 n + 1 = Nat.plusR 0 (Nat.plusR 0 n) + 1=>
unfold Nat.plusRsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 n + 1
rw [ih]succn:Natih:n = Nat.plusR 0 n⊢ Nat.plusR 0 n + 1 = Nat.plusR 0 (Nat.plusR 0 n) + 1succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)`

However, the direction of the rewrite was incorrect.
Replacing `n` with `Nat.plusR 0 n` made the goal more complicated rather than less complicated:

```
unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ Nat.plusR 0 n + 1 = Nat.plusR 0 (Nat.plusR 0 n) + 1
```

This can be remedied by placing a left arrow before `ih` in the call to `rw`, which instructs it to replace the right-hand side of the equality with the left-hand side:

그러나 다시 쓰기의 방향이 올바르지 않았습니다.
`n`을 `Nat.plusR 0 n`으로 바꾸면 목표가 덜 복잡해지기는커녕 더 복잡해졌습니다:

```
미해결 목표
succn:Natih:n = Nat.plusR 0 n⊢ Nat.plusR 0 n + 1 = Nat.plusR 0 (Nat.plusR 0 n) + 1
```

`rw` 호출에서 `ih` 앞에 왼쪽 화살표를 배치하여 이를 수정할 수 있으며, 이는 등식의 우변을 좌변으로 바꾸도록 지시합니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih =>succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)
unfold Nat.plusRsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 n + 1
rw [←ih]All goals completed! 🐙`

This rewrite makes both sides of the equation identical, and Lean takes care of the `rfl` on its own.
The proof is complete.

이 다시 쓰기는 등식의 양쪽을 동일하게 만들고, Lean은 스스로 `rfl`을 처리합니다.
증명이 완료되었습니다.

## Tactic Golf[🔗](find/?domain=Verso.Genre.Manual.section&name=tactic-golf "Permalink")

전술 골프

So far, the tactic language has not shown its true value.
The above proof is no shorter than the recursive function; it's merely written in a domain-specific language instead of the full Lean language.
But proofs with tactics can be shorter, easier, and more maintainable.
Just as a lower score is better in the game of golf, a shorter proof is better in the game of tactic golf.

The induction step of `plusR_zero_left` can be proved using the simplification tactic `simp`.
Using `simp` on its own does not help:

지금까지 전술 언어는 그 진정한 가치를 보여주지 못했습니다.
위의 증명은 재귀 함수보다 짧지 않습니다. 단지 전체 Lean 언어 대신 도메인 특정 언어로 작성되었을 뿐입니다.
하지만 전술을 사용한 증명은 더 짧고, 더 쉽고, 더 유지보수하기 쉬울 수 있습니다.
골프 게임에서 낮은 점수가 더 좋은 것처럼, 전술 골프 게임에서 더 짧은 증명이 더 좋습니다.

`plusR_zero_left`의 귀납 단계는 단순화 전술 `simp`를 사용하여 증명할 수 있습니다.
`simp`를 단독으로 사용하는 것은 도움이 되지 않습니다:

`` theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih =>succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)
`simp` made no progresssimpsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1) ``

```
`simp` made no progress
```

```
`simp`은 진전을 만들지 못했습니다
```

However, `simp` can be configured to make use of a set of definitions.
Just like `rw`, these arguments are provided in a list.
Asking `simp` to take the definition of `Nat.plusR` into account leads to a simpler goal:

그러나 `simp`는 정의 집합을 사용하도록 구성할 수 있습니다.
`rw`처럼 이 인수들은 목록으로 제공됩니다.
`simp`에게 `Nat.plusR`의 정의를 고려하도록 요청하면 더 간단한 목표가 나옵니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n = Nat.plusR 0 n=>
simp [Nat.plusR]succn:Natih:n = Nat.plusR 0 n⊢ n = Nat.plusR 0 nsuccn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)`

```
unsolved goals
succn:Natih:n = Nat.plusR 0 n⊢ n = Nat.plusR 0 n
```

```
미해결 목표
succn:Natih:n = Nat.plusR 0 n⊢ n = Nat.plusR 0 n
```

In particular, the goal is now identical to the induction hypothesis.
In addition to automatically proving simple equality statements, the simplifier automatically replaces goals like `Nat.succ A = Nat.succ B` with `A = B`.
Because the induction hypothesis `ih` has exactly the right type, the `exact` tactic can indicate that it should be used:

특히, 목표는 이제 귀납 가설과 동일합니다.
단순 등식 명제를 자동으로 증명하는 것 외에도, 단순화기는 `Nat.succ A = Nat.succ B`와 같은 목표를 `A = B`로 자동으로 바꿉니다.
귀납 가설 `ih`가 정확히 올바른 타입을 가지고 있으므로, `exact` 전술이 사용되어야 함을 나타낼 수 있습니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih =>succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)
simp [Nat.plusR]succn:Natih:n = Nat.plusR 0 n⊢ n = Nat.plusR 0 n
exact ihAll goals completed! 🐙`

However, the use of `exact` is somewhat fragile.
Renaming the induction hypothesis, which may happen while “golfing” the proof, would cause this proof to stop working.
The `assumption` tactic solves the current goal if *any* of the assumptions match it:

그러나 `exact`의 사용은 다소 취약합니다.
증명을 “골프”하는 동안 발생할 수 있는 귀납 가설의 이름을 바꾸면 이 증명이 작동을 멈출 것입니다.
`assumption` 전술은 *어떤* 가정이라도 그것과 일치하면 현재 목표를 해결합니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction k with
| zero =>zero⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
| succ n ih =>succn:Natih:n = Nat.plusR 0 n⊢ n + 1 = Nat.plusR 0 (n + 1)
simp [Nat.plusR]succn:Natih:n = Nat.plusR 0 n⊢ n = Nat.plusR 0 n
assumptionAll goals completed! 🐙`

This proof is no shorter than the prior proof that used unfolding and explicit rewriting.
However, a series of transformations can make it much shorter, taking advantage of the fact that `simp` can solve many kinds of goals.
The first step is to drop the `with` at the end of `induction`.
For structured, readable proofs, the `with` syntax is convenient.
It complains if any cases are missing, and it shows the structure of the induction clearly.
But shortening proofs can often require a more liberal approach.

이 증명은 언폴딩과 명시적 다시 쓰기를 사용한 이전 증명보다 짧지 않습니다.
그러나 일련의 변환이 `simp`가 많은 종류의 목표를 해결할 수 있다는 사실을 활용하여 훨씬 더 짧게 만들 수 있습니다.
첫 번째 단계는 `induction` 끝의 `with`를 제거하는 것입니다.
구조화되고 읽기 쉬운 증명의 경우 `with` 구문이 편합니다.
누락된 사례가 있으면 불평하고, 귀납법의 구조를 명확하게 보여줍니다.
하지만 증명을 단축하는 것은 종종 더 자유로운 접근 방식이 필요합니다.

Using `induction` without `with` simply results in a proof state with two goals.
The `case` tactic can be used to select one of them, just as in the branches of the `induction``...``with` tactic.
In other words, the following proof is equivalent to the prior proof:

`with` 없이 `induction`을 사용하면 단순히 두 개의 목표가 있는 증명 상태가 됩니다.
`case` 전술을 사용하여 그 중 하나를 선택할 수 있으며, `induction``...``with` 전술의 분기에서와 마찬가지로 사용할 수 있습니다.
즉, 다음 증명은 이전 증명과 동등합니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction kzero⊢ 0 = Nat.plusR 0 0succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1)
case zero =>⊢ 0 = Nat.plusR 0 0 rflAll goals completed! 🐙
case succ n ih =>n:Natih:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1)
simp [Nat.plusR]n:Natih:n✝ = Nat.plusR 0 n✝⊢ n = Nat.plusR 0 n
assumptionAll goals completed! 🐙`

In a context with a single goal (namely, `k = Nat.plusR 0 k`), the `induction k` tactic yields two goals.
In general, a tactic will either fail with an error or take a goal and transform it into zero or more new goals.
Each new goal represents what remains to be proved.
If the result is zero goals, then the tactic was a success, and that part of the proof is done.

The `<;>` operator takes two tactics as arguments, resulting in a new tactic.
`T1` `<;>` `T2` applies `T1` to the current goal, and then applies `T2` in *all* goals created by `T1`.
In other words, `<;>` enables a general tactic that can solve many kinds of goals to be used on multiple new goals all at once.
One such general tactic is `simp`.

단일 목표(즉, `k = Nat.plusR 0 k`)가 있는 컨텍스트에서, `induction k` 전술은 두 개의 목표를 생성합니다.
일반적으로 전술은 오류와 함께 실패하거나 목표를 가져와 0개 이상의 새 목표로 변환합니다.
각 새 목표는 증명하려는 것의 나머지를 나타냅니다.
결과가 0개의 목표이면, 전술은 성공했고 증명의 해당 부분은 완료됩니다.

`<;>` 연산자는 두 개의 전술을 인수로 가져가며, 새 전술을 생성합니다.
`T1` `<;>` `T2`는 현재 목표에 `T1`을 적용한 다음, `T1`로 생성된 *모든* 목표에 `T2`를 적용합니다.
즉, `<;>`은 많은 종류의 목표를 해결할 수 있는 일반 전술을 여러 새 목표에 한 번에 사용할 수 있게 합니다.
이러한 일반 전술 중 하나는 `simp`입니다.

Because `simp` can both complete the proof of the base case and make progress on the proof of the induction step, using it with `induction` and `<;>` shortens the proof:

`simp`은 기저 사례 증명을 완료할 수 있고 귀납 단계 증명에서 진전을 만들 수 있으므로, `induction` 및 `<;>`과 함께 사용하면 증명이 단축됩니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := unsolved goals
succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ = Nat.plusR 0 n✝byk:Nat⊢ k = Nat.plusR 0 k
induction kzero⊢ 0 = Nat.plusR 0 0succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1) <;>zero⊢ 0 = Nat.plusR 0 0succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1) simp [Nat.plusR]succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ = Nat.plusR 0 n✝`

This results in only a single goal, the transformed induction step:

```
unsolved goals
succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ = Nat.plusR 0 n✝
```

결과는 하나의 목표, 변환된 귀납 단계만 됩니다:

```
미해결 목표
succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ = Nat.plusR 0 n✝
```

Running `assumption` in this goal completes the proof:

이 목표에서 `assumption`을 실행하면 증명이 완료됩니다:

`theorem plusR_zero_left (k : Nat) : k = Nat.plusR 0 k := byk:Nat⊢ k = Nat.plusR 0 k
induction kzero⊢ 0 = Nat.plusR 0 0succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1) <;>zero⊢ 0 = Nat.plusR 0 0succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ + 1 = Nat.plusR 0 (n✝ + 1) simp [Nat.plusR]succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ = Nat.plusR 0 n✝ <;>succn✝:Nata✝:n✝ = Nat.plusR 0 n✝⊢ n✝ = Nat.plusR 0 n✝ assumptionAll goals completed! 🐙`

Here, `exact` would not have been possible, because `ih` was never explicitly named.

For beginners, this proof is not easier to read.
However, a common pattern for expert users is to take care of a number of simple cases with powerful tactics like `simp`, allowing them to focus the text of the proof on the interesting cases.
Additionally, these proofs tend to be more robust in the face of small changes to the functions and datatypes involved in the proof.
The game of tactic golf is a useful part of developing good taste and style when writing proofs.

여기서는 `ih`가 명시적으로 명명되지 않았으므로 `exact`은 불가능했을 것입니다.

초보자의 경우 이 증명은 읽기 더 쉽지 않습니다.
그러나 전문가 사용자의 일반적인 패턴은 `simp`와 같은 강력한 전술로 여러 가지 간단한 사례를 처리하여 증명 텍스트를 재미있는 사례에 집중할 수 있도록 하는 것입니다.
또한 이러한 증명은 증명에 관련된 함수 및 데이터 타입의 작은 변화에 대해 더 견고한 경향이 있습니다.
전술 골프 게임은 증명을 작성할 때 좋은 취향과 스타일을 개발하는 데 유용한 부분입니다.

## Induction on Other Datatypes[🔗](find/?domain=Verso.Genre.Manual.section&name=induction-other-types "Permalink")

다른 데이터타입에서의 귀납

Mathematical induction proves a statement for natural numbers by providing a base case for `Nat.zero` and an induction step for `Nat.succ`.
The principle of induction is also valid for other datatypes.
Constructors without recursive arguments form the base cases, while constructors with recursive arguments form the induction steps.
The ability to carry out proofs by induction is the very reason why they are called *inductive* datatypes.

수학적 귀납법은 `Nat.zero`에 대한 기저 사례와 `Nat.succ`에 대한 귀납 단계를 제공하여 자연수에 대한 명제를 증명합니다.
귀납법의 원리는 다른 데이터타입에도 유효합니다.
재귀 인수가 없는 생성자는 기저 사례를 형성하고, 재귀 인수를 가진 생성자는 귀납 단계를 형성합니다.
귀납법으로 증명을 수행할 수 있는 능력이 이들이 *귀납* 데이터타입이라고 불리는 바로 그 이유입니다.

One example of this is induction on binary trees.
Induction on binary trees is a proof technique where a statement is proven for *all* binary trees in two steps:

1. The statement is shown to hold for `BinTree.leaf`. This is called the base case.
2. Under the assumption that the statement holds for some arbitrarily chosen trees `l` and `r`, it is shown to hold for `BinTree.branch l x r`, where `x` is an arbitrarily-chosen new data point. This is called the *induction step*. The assumptions that the statement holds for `l` and `r` are called the *induction hypotheses*.

한 예는 이진 트리에 대한 귀납입니다.
이진 트리에 대한 귀납은 명제를 두 단계로 *모든* 이진 트리에 대해 증명하는 증명 기법입니다:

1. 명제가 `BinTree.leaf`에 대해 성립함을 보입니다. 이를 기저 사례라고 합니다.
2. 명제가 임의로 선택된 어떤 트리 `l`과 `r`에 대해 성립한다는 가정 하에, 명제가 `BinTree.branch l x r`에 대해 성립함을 보입니다. 여기서 `x`는 임의로 선택된 새로운 데이터 포인트입니다. 이를 *귀납 단계*라고 합니다. 명제가 `l`과 `r`에 대해 성립한다는 가정을 *귀납 가설*이라고 합니다.

`BinTree.count` counts the number of branches in a tree:

`def BinTree.count : BinTree α → Nat
| .leaf => 0
| .branch l _ r =>
1 + l.count + r.count`

`BinTree.count`는 트리의 분기 수를 세십시오:

`def BinTree.count : BinTree α → Nat
| .leaf => 0
| .branch l _ r =>
1 + l.count + r.count`

[Mirroring a tree](Monads/Additional-Conveniences/#leading-dot-notation) does not change the number of branches in it.
This can be proven using induction on trees.
The first step is to state the theorem and invoke `induction`:

[트리를 미러링](Monads/Additional-Conveniences/#leading-dot-notation)하는 것은 그 안의 분기 수를 변경하지 않습니다.
이는 트리에 대한 귀납을 사용하여 증명할 수 있습니다.
첫 번째 단계는 정리를 명시하고 `induction`을 호출하는 것입니다:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction t with
| leaf unsolved goals
leafα:Type⊢ leaf.mirror.count = leaf.count=> skipleafα:Type⊢ leaf.mirror.count = leaf.count
| branch l x r ihl ihr unsolved goals
branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count=> skipbranchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count`

The base case states that counting the mirror of a leaf is the same as counting the leaf:

```
unsolved goals
leafα:Type⊢ leaf.mirror.count = leaf.count
```

The induction step allows the assumption that mirroring the left and right subtrees won't affect their branch counts, and requests a proof that mirroring a branch with these subtrees also preserves the overall branch count:

```
unsolved goals
branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count
```

The base case is true because mirroring `leaf` results in `leaf`, so the left and right sides are definitionally equal.
This can be expressed by using `simp` with instructions to unfold `BinTree.mirror`:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction t with
| leaf =>leafα:Type⊢ leaf.mirror.count = leaf.count simp [BinTree.mirror]All goals completed! 🐙
| branch l x r ihl ihr unsolved goals
branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count=> skipbranchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count`

In the induction step, nothing in the goal immediately matches the induction hypotheses.
Simplifying using the definitions of `BinTree.count` and `BinTree.mirror` reveals the relationship:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction t with
| leaf =>leafα:Type⊢ leaf.mirror.count = leaf.count simp [BinTree.mirror]All goals completed! 🐙
| branch l x r ihl ihr unsolved goals
branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.mirror.count + l.mirror.count = 1 + l.count + r.count=>
simp [BinTree.mirror, BinTree.count]branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.mirror.count + l.mirror.count = 1 + l.count + r.countbranchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count`

```
unsolved goals
branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.mirror.count + l.mirror.count = 1 + l.count + r.count
```

Both induction hypotheses can be used to rewrite the left-hand side of the goal into something almost like the right-hand side:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction t with
| leaf =>leafα:Type⊢ leaf.mirror.count = leaf.count simp [BinTree.mirror]All goals completed! 🐙
| branch l x r ihl ihr unsolved goals
branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.count + l.count = 1 + l.count + r.count=>
simp [BinTree.mirror, BinTree.count]branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.mirror.count + l.mirror.count = 1 + l.count + r.count
rw [ihl, ihr]branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.count + l.count = 1 + l.count + r.countbranchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count`

```
unsolved goals
branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.count + l.count = 1 + l.count + r.count
```

The `simp` tactic can use additional arithmetic identities when passed the `+arith` option.
It is enough to prove this goal, yielding:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction t with
| leaf =>leafα:Type⊢ leaf.mirror.count = leaf.count simp [BinTree.mirror]All goals completed! 🐙
| branch l x r ihl ihr =>branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count
simp [BinTree.mirror, BinTree.count]branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.mirror.count + l.mirror.count = 1 + l.count + r.count
rw [ihl, ihr]branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ 1 + r.count + l.count = 1 + l.count + r.count
simp +arithAll goals completed! 🐙`

In addition to definitions to be unfolded, the simplifier can also be passed names of equality proofs to use as rewrites while it simplifies proof goals.
`BinTree.mirror_count` can also be written:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction t with
| leaf =>leafα:Type⊢ BinTree.leaf.mirror.count = BinTree.leaf.count simp [BinTree.mirror]All goals completed! 🐙
| branch l x r ihl ihr =>branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count
simp +arith [BinTree.mirror, BinTree.count, ihl, ihr]All goals completed! 🐙`

As proofs grow more complicated, listing assumptions by hand can become tedious.
Furthermore, manually writing assumption names can make it more difficult to re-use proof steps for multiple subgoals.
The argument `*` to `simp` or `simp +arith` instructs them to use *all* assumptions while simplifying or solving the goal.
In other words, the proof could also be written:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction t with
| leaf =>leafα:Type⊢ BinTree.leaf.mirror.count = BinTree.leaf.count simp [BinTree.mirror]All goals completed! 🐙
| branch l x r ihl ihr =>branchα:Typel:BinTree αx:αr:BinTree αihl:l.mirror.count = l.countihr:r.mirror.count = r.count⊢ (l.branch x r).mirror.count = (l.branch x r).count
simp +arith [BinTree.mirror, BinTree.count, *]All goals completed! 🐙`

Because both branches are using the simplifier, the proof can be reduced to:

`theorem BinTree.mirror_count (t : BinTree α) :
t.mirror.count = t.count := byα:Typet:BinTree α⊢ t.mirror.count = t.count
induction tleafα:Type⊢ BinTree.leaf.mirror.count = BinTree.leaf.countbranchα:Typea✝²:BinTree αa✝¹:αa✝:BinTree αa_ih✝¹:a✝².mirror.count = a✝².counta_ih✝:a✝.mirror.count = a✝.count⊢ (a✝².branch a✝¹ a✝).mirror.count = (a✝².branch a✝¹ a✝).count <;>leafα:Type⊢ BinTree.leaf.mirror.count = BinTree.leaf.countbranchα:Typea✝²:BinTree αa✝¹:αa✝:BinTree αa_ih✝¹:a✝².mirror.count = a✝².counta_ih✝:a✝.mirror.count = a✝.count⊢ (a✝².branch a✝¹ a✝).mirror.count = (a✝².branch a✝¹ a✝).count simp +arith [BinTree.mirror, BinTree.count, *]All goals completed! 🐙`

## Exercises[🔗](find/?domain=Verso.Genre.Manual.section&name=tactics-induction-proofs-exercises "Permalink")

* Prove `plusR_succ_left` using the `induction``...``with` tactic.
* Rewrite the proof of `plusR_succ_left` to use `<;>` in a single line.
* Prove that appending lists is associative using induction on lists:

  `theorem List.append_assoc (xs ys zs : List α) :
  xs ++ (ys ++ zs) = (xs ++ ys) ++ zs`