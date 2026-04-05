# Additional Conveniences

Source: https://lean-lang.org/functional_programming_in_lean/Monad-Transformers/Additional-Conveniences

## 6.5.1. Pipe Operators[🔗](find/?domain=Verso.Genre.Manual.section&name=pipe-operators "Permalink")

Functions are normally written before their arguments.
When reading a program from left to right, this promotes a view in which the function's *output* is paramount—the function has a goal to achieve (that is, a value to compute), and it receives arguments to support it in this process.
But some programs are easier to understand in terms of an input that is successively refined to produce the output.
For these situations, Lean provides a *pipeline* operator which is similar to the that provided by F#.
Pipeline operators are useful in the same situations as Clojure's threading macros.

The pipeline `E₁ |> E₂` is short for `E₂ E₁`.
For example, evaluating:

함수는 일반적으로 인수 앞에 작성됩니다.
프로그램을 왼쪽에서 오른쪽으로 읽을 때, 이는 함수의 *출력*이 가장 중요한 관점을 나타냅니다. 즉, 함수는 달성할 목표(즉, 계산할 값)를 가지고 있으며 이 과정을 지원하기 위해 인수를 받습니다.
하지만 어떤 프로그램은 입력이 연속적으로 정제되어 출력을 생산하는 관점에서 이해하기가 더 쉽습니다.
이러한 상황에서 Lean은 F#에서 제공하는 것과 유사한 *파이프라인(pipeline)* 연산자를 제공합니다.
파이프라인 연산자는 Clojure의 스레딩 매크로와 동일한 상황에서 유용합니다.

파이프라인 `E₁ |> E₂`는 `E₂ E₁`의 축약형입니다.
예를 들어, 평가하면:

`"(some 5)"#eval some 5 |> toString`

results in:

```
"(some 5)"
```

While this change of emphasis can make some programs more convenient to read, pipelines really come into their own when they contain many components.

With the definition:

`def times3 (n : Nat) : Nat := n * 3`

the following pipeline:

`"It is 15"#eval 5 |> times3 |> toString |> ("It is " ++ ·)`

yields:

```
"It is 15"
```

More generally, a series of pipelines `E₁ |> E₂ |> E₃ |> E₄` is short for nested function applications `E₄ (E₃ (E₂ E₁))`.

이러한 강조의 변화는 어떤 프로그램을 더 편리하게 읽을 수 있게 만들 수 있지만, 파이프라인은 많은 구성 요소를 포함할 때 진정으로 제 역할을 합니다.

정의를 사용하면:

`def times3 (n : Nat) : Nat := n * 3`

다음의 파이프라인:

`"It is 15"#eval 5 |> times3 |> toString |> ("It is " ++ ·)`

는 다음을 산출합니다:

```
"It is 15"
```

더 일반적으로, 일련의 파이프라인 `E₁ |> E₂ |> E₃ |> E₄`는 중첩된 함수 적용 `E₄ (E₃ (E₂ E₁))`의 축약형입니다.

Pipelines may also be written in reverse.
In this case, they do not place the subject of data transformation first; however, in cases where many nested parentheses pose a challenge for readers, they can clarify the steps of application.
The prior example could be equivalently written as:

`”It is 15”#eval (“It is “ ++ ·) <| toString <| times3 <| 5`

which is short for:

`”It is 15”#eval (“It is “ ++ ·) (toString (times3 5))`

Lean's method dot notation that uses the name of the type before the dot to resolve the namespace of the operator after the dot serves a similar purpose to pipelines.
Even without the pipeline operator, it is possible to write `[1, 2, 3].reverse` instead of `List.reverse [1, 2, 3]`.
However, the pipeline operator is also useful for dotted functions when using many of them.
`([1, 2, 3].reverse.drop 1).reverse` can also be written as `[1, 2, 3] |> List.reverse |> List.drop 1 |> List.reverse`.
This version avoids having to parenthesize expressions simply because they accept arguments, and it recovers the convenience of a chain of method calls in languages like Kotlin or C#.
However, it still requires the namespace to be provided by hand.
As a final convenience, Lean provides the “pipeline dot” operator, which groups functions like the pipeline but uses the name of the type to resolve namespaces.
With “pipeline dot”, the example can be rewritten to `[1, 2, 3] |>.reverse |>.drop 1 |>.reverse`.

파이프라인은 역순으로도 작성될 수 있습니다.
이 경우, 데이터 변환의 주제를 먼저 배치하지 않습니다. 그러나 많은 중첩된 괄호가 독자에게 도전이 되는 경우, 적용의 단계를 명확히 할 수 있습니다.
이전 예제는 다음과 같이 동등하게 작성될 수 있습니다:

`”It is 15”#eval (“It is “ ++ ·) <| toString <| times3 <| 5`

이는 다음의 축약형입니다:

`”It is 15”#eval (“It is “ ++ ·) (toString (times3 5))`

Lean의 메서드 점 표기법은 점 앞의 타입 이름을 사용하여 점 뒤의 연산자의 네임스페이스를 해결하므로 파이프라인과 유사한 목적을 제공합니다.
파이프라인 연산자가 없어도 `List.reverse [1, 2, 3]` 대신 `[1, 2, 3].reverse`를 작성할 수 있습니다.
그러나 파이프라인 연산자는 많은 점 처리 함수를 사용할 때도 유용합니다.
`([1, 2, 3].reverse.drop 1).reverse`는 `[1, 2, 3] |> List.reverse |> List.drop 1 |> List.reverse`로도 작성될 수 있습니다.
이 버전은 단순히 인수를 받는다는 이유로 표현식에 괄호를 붙일 필요가 없으며, Kotlin 또는 C#과 같은 언어의 메서드 호출 체인의 편의성을 회복합니다.
그러나 네임스페이스를 수동으로 제공해야 합니다.
최종 편의를 위해 Lean은 “파이프라인 점” 연산자를 제공하며, 이는 파이프라인처럼 함수를 그룹화하지만 타입의 이름을 사용하여 네임스페이스를 해결합니다.
“파이프라인 점”으로 예제는 `[1, 2, 3] |>.reverse |>.drop 1 |>.reverse`로 다시 작성될 수 있습니다.

## 6.5.2. Infinite Loops[🔗](find/?domain=Verso.Genre.Manual.section&name=infinite-loops "Permalink")

Within a `do`-block, the `repeat` keyword introduces an infinite loop.
For example, a program that spams the string `”Spam!”` can use it:

`def spam : IO Unit := do
repeat IO.println “Spam!”`

A `repeat` loop supports `break` and `continue`, just like `for` loops.

The `dump` function from the [implementation of `feline`](Hello___-World___/Worked-Example___--cat/#streams) uses a recursive function to run forever:

`partial def dump (stream : IO.FS.Stream) : IO Unit := do
let buf ← stream.read bufsize
if buf.isEmpty then
pure ()
else
let stdout ← IO.getStdout
stdout.write buf
dump stream`

This function can be greatly shortened using `repeat`:

`def dump (stream : IO.FS.Stream) : IO Unit := do
let stdout ← IO.getStdout
repeat do
let buf ← stream.read bufsize
if buf.isEmpty then break
stdout.write buf`

Neither `spam` nor `dump` need to be declared as `partial` because they are not themselves infinitely recursive.
Instead, `repeat` makes use of a type whose `ForM` instance is `partial`.
Partiality does not “infect” calling functions.

`do` 블록 내에서, `repeat` 키워드는 무한 루프를 도입합니다.
예를 들어, 문자열 `”Spam!”`을 스팸하는 프로그램은 이를 사용할 수 있습니다:

`def spam : IO Unit := do
repeat IO.println “Spam!”`

`repeat` 루프는 `for` 루프처럼 `break`와 `continue`를 지원합니다.

[`feline`의 구현](Hello___-World___/Worked-Example___--cat/#streams)의 `dump` 함수는 무한으로 실행되기 위해 재귀 함수를 사용합니다:

`partial def dump (stream : IO.FS.Stream) : IO Unit := do
let buf ← stream.read bufsize
if buf.isEmpty then
pure ()
else
let stdout ← IO.getStdout
stdout.write buf
dump stream`

이 함수는 `repeat`를 사용하여 크게 단축될 수 있습니다:

`def dump (stream : IO.FS.Stream) : IO Unit := do
let stdout ← IO.getStdout
repeat do
let buf ← stream.read bufsize
if buf.isEmpty then break
stdout.write buf`

`spam`과 `dump` 둘 다 그 자체로 무한 재귀적이지 않기 때문에 `partial`로 선언할 필요가 없습니다.
대신, `repeat`는 `ForM` 인스턴스가 `partial`인 타입을 사용합니다.
부분성은 호출 함수를 “감염”시키지 않습니다.