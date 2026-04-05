# Introduction

Source: https://lean-lang.org/functional_programming_in_lean/Introduction

# Introduction

Lean is an interactive theorem prover based on dependent type theory.
Originally developed at Microsoft Research, development now takes place at the [Lean FRO](https://lean-fro.org).
Dependent type theory unites the worlds of programs and proofs; thus, Lean is also a programming language.
Lean takes its dual nature seriously, and it is designed to be suitable for use as a general-purpose programming language—Lean is even implemented in itself.
This book is about writing programs in Lean.

Lean은 종속 타입 이론을 기반으로 한 인터랙티브 정리 증명기입니다.
원래 Microsoft Research에서 개발되었으며, 현재 개발은 [Lean FRO](https://lean-fro.org)에서 이루어지고 있습니다.
종속 타입 이론은 프로그램과 증명의 세계를 통합합니다. 따라서 Lean은 또한 프로그래밍 언어입니다.
Lean은 이러한 이중적 성질을 진지하게 받아들이며, 범용 프로그래밍 언어로 사용하기에 적합하도록 설계되었습니다. Lean은 심지어 자기 자신으로 구현되어 있습니다.
이 책은 Lean으로 프로그램을 작성하는 것에 관한 것입니다.

When viewed as a programming language, Lean is a strict pure functional language with dependent types.
A large part of learning to program with Lean consists of learning how each of these attributes affects the way programs are written, and how to think like a functional programmer.
*Strictness* means that function calls in Lean work similarly to the way they do in most languages: the arguments are fully computed before the function's body begins running.
*Purity* means that Lean programs cannot have side effects such as modifying locations in memory, sending emails, or deleting files without the program's type saying so.
Lean is a *functional* language in the sense that functions are first-class values like any other and that the execution model is inspired by the evaluation of mathematical expressions.
*Dependent types*, which are the most unusual feature of Lean, make types into a first-class part of the language, allowing types to contain programs and programs to compute types.

프로그래밍 언어로 보았을 때, Lean은 종속 타입을 가진 엄격한 순수 함수형 언어입니다.
Lean으로 프로그래밍하는 방법을 배우는 큰 부분은 이러한 각 속성이 프로그램을 작성하는 방식에 어떻게 영향을 미치는지, 그리고 함수형 프로그래머처럼 생각하는 방법을 배우는 것입니다.
*엄격성*이란 Lean의 함수 호출이 대부분의 언어에서와 비슷하게 작동한다는 의미입니다. 인수는 함수의 본문이 실행되기 전에 완전히 계산됩니다.
*순수성*은 Lean 프로그램이 메모리 위치를 수정하거나, 이메일을 보내거나, 파일을 삭제하는 것과 같은 부작용을 가질 수 없다는 의미입니다. 프로그램의 타입이 명시하지 않는 한 말입니다.
Lean은 함수가 다른 값들과 마찬가지로 일급 값이며, 실행 모델이 수학식 계산의 평가에서 영감을 받은 의미에서 *함수형* 언어입니다.
Lean의 가장 특이한 특징인 *종속 타입*은 타입을 언어의 일급 부분으로 만들어, 타입이 프로그램을 포함할 수 있고 프로그램이 타입을 계산할 수 있도록 합니다.

This book is intended for programmers who want to learn Lean, but who have not necessarily used a functional programming language before.
Familiarity with functional languages such as Haskell, OCaml, or F# is not required.
On the other hand, this book does assume knowledge of concepts like loops, functions, and data structures that are common to most programming languages.
While this book is intended to be a good first book on functional programming, it is not a good first book on programming in general.

이 책은 Lean을 배우고 싶지만, 이전에 함수형 프로그래밍 언어를 사용하지 않은 프로그래머를 대상으로 합니다.
Haskell, OCaml, F#과 같은 함수형 언어에 대한 친숙함이 필요하지 않습니다.
반면에, 이 책은 대부분의 프로그래밍 언어에 공통적인 루프, 함수, 데이터 구조와 같은 개념에 대한 지식을 가정합니다.
이 책이 함수형 프로그래밍에 대한 좋은 첫 번째 책이 되도록 의도되었지만, 일반적으로 프로그래밍에 대한 좋은 첫 번째 책은 아닙니다.

Mathematicians who are using Lean as a proof assistant will likely need to write custom proof automation tools at some point.
This book is also for them.
As these tools become more sophisticated, they begin to resemble programs in functional languages, but most working mathematicians are trained in languages like Python and Mathematica.
This book can help bridge the gap, empowering more mathematicians to write maintainable and understandable proof automation tools.

증명 보조기로 Lean을 사용하는 수학자들은 어떤 시점에서 사용자 정의 증명 자동화 도구를 작성해야 할 가능성이 높습니다.
이 책도 그들을 위한 것입니다.
이러한 도구가 더 정교해지면서, 함수형 언어의 프로그램과 유사해지기 시작합니다. 하지만 대부분의 실무 수학자는 Python과 Mathematica 같은 언어로 훈련받았습니다.
이 책은 그 간격을 좁히는 데 도움이 될 수 있으며, 더 많은 수학자들이 유지 관리 가능하고 이해하기 쉬운 증명 자동화 도구를 작성할 수 있도록 힘을 실어줄 수 있습니다.

This book is intended to be read linearly, from the beginning to the end.
Concepts are introduced one at a time, and later sections assume familiarity with earlier sections.
Sometimes, later chapters will go into depth on a topic that was only briefly addressed earlier on.
Some sections of the book contain exercises.
These are worth doing, in order to cement your understanding of the section.
It is also useful to explore Lean as you read the book, finding creative new ways to use what you have learned.

이 책은 처음부터 끝까지 선형으로 읽혀지도록 의도되었습니다.
개념은 한 번에 하나씩 소개되며, 이후 섹션은 이전 섹션에 대한 친숙함을 가정합니다.
때로 이후 장은 이전에 간단히 다루었던 주제를 깊이 있게 설명할 것입니다.
책의 일부 섹션에는 연습 문제가 포함되어 있습니다.
섹션에 대한 이해를 확실히 하기 위해 이들을 하는 것이 좋습니다.
책을 읽으면서 Lean을 탐색하고, 배운 내용을 사용하는 창의적인 새로운 방법을 찾는 것도 유용합니다.

## Getting Lean[🔗](find/?domain=Verso.Genre.Manual.section&name=getting-lean "Permalink")

Before writing and running programs written in Lean, you'll need to set up Lean on your own computer.
The Lean tooling consists of the following:

* `elan` manages the Lean compiler toolchains, similarly to `rustup` or `ghcup`.
* `lake` builds Lean packages and their dependencies, similarly to `cargo`, `make`, or Gradle.
* `lean` type checks and compiles individual Lean files as well as providing information to programmer tools about files that are currently being written.
  Normally, `lean` is invoked by other tools rather than directly by users.
* Plugins for editors, such as Visual Studio Code or Emacs, that communicate with `lean` and present its information conveniently.

Please refer to the [Lean manual](https://lean-lang.org/lean4/doc/quickstart.html) for up-to-date instructions for installing Lean.

Lean으로 작성된 프로그램을 작성하고 실행하기 전에, 자신의 컴퓨터에 Lean을 설정해야 합니다.
Lean 도구는 다음으로 구성됩니다:

* `elan`은 `rustup` 또는 `ghcup`과 유사하게 Lean 컴파일러 도구 체인을 관리합니다.
* `lake`는 `cargo`, `make`, 또는 Gradle과 유사하게 Lean 패키지와 그 종속성을 빌드합니다.
* `lean`은 개별 Lean 파일의 타입 검사와 컴파일을 수행하며, 현재 작성 중인 파일에 대한 정보를 프로그래머 도구에 제공합니다.
  일반적으로 `lean`은 사용자가 직접이 아니라 다른 도구에 의해 호출됩니다.
* Visual Studio Code 또는 Emacs와 같은 편집기의 플러그인으로, `lean`과 통신하고 그 정보를 편리하게 표시합니다.

Lean을 설치하기 위한 최신 지침은 [Lean 설명서](https://lean-lang.org/lean4/doc/quickstart.html)를 참조하세요.

## Typographical Conventions[🔗](find/?domain=Verso.Genre.Manual.section&name=typographical-conventions "Permalink")

Code examples that are provided to Lean as *input* are formatted like this:

`def add1 (n : Nat) : Nat := n + 1``#eval add1 7`

The last line above (beginning with `#eval`) is a command that instructs Lean to calculate an answer.
Lean's replies are formatted like this:

```
8
```

Error messages returned by Lean are formatted like this:

```
Application type mismatch: The argument
  "seven"
has type
  String
but is expected to have type
  Nat
in the application
  add1 "seven"
```

Warnings are formatted like this:

```
declaration uses 'sorry'
```

Lean에 *입력*으로 제공되는 코드 예제는 다음과 같이 포맷됩니다:

`def add1 (n : Nat) : Nat := n + 1``#eval add1 7`

위의 마지막 줄(`#eval`로 시작)은 Lean이 답을 계산하도록 지시하는 명령입니다.
Lean의 응답은 다음과 같이 포맷됩니다:

```
8
```

Lean이 반환하는 오류 메시지는 다음과 같이 포맷됩니다:

```
Application type mismatch: The argument
  "seven"
has type
  String
but is expected to have type
  Nat
in the application
  add1 "seven"
```

경고는 다음과 같이 포맷됩니다:

```
declaration uses 'sorry'
```

## Unicode[🔗](find/?domain=Verso.Genre.Manual.section&name=unicode "Permalink")

Idiomatic Lean code makes use of a variety of Unicode characters that are not part of ASCII.
For instance, Greek letters like `α` and `β` and the arrow `→` both occur in the first chapter of this book.
This allows Lean code to more closely resemble ordinary mathematical notation.

With the default Lean settings, both Visual Studio Code and Emacs allow these characters to be typed with a backslash (`\`) followed by a name.
For example, to enter `α`, type `\alpha`.
To find out how to type a character in Visual Studio Code, point the mouse at it and look at the tooltip.
In Emacs, use `C-c C-k` with point on the character in question.

관용적인 Lean 코드는 ASCII의 일부가 아닌 다양한 유니코드 문자를 사용합니다.
예를 들어, `α`, `β`와 같은 그리스 문자와 화살표 `→`는 이 책의 첫 번째 장에서 나타납니다.
이를 통해 Lean 코드는 일반적인 수학 표기법과 더 유사하게 보일 수 있습니다.

기본 Lean 설정에서 Visual Studio Code와 Emacs 모두 역슬래시(`\`)와 이름을 입력하여 이러한 문자를 입력할 수 있습니다.
예를 들어, `α`를 입력하려면 `\alpha`를 입력합니다.
Visual Studio Code에서 문자를 입력하는 방법을 알아내려면, 마우스를 올려놓고 도구 설명을 확인하세요.
Emacs에서는 해당 문자에 포인트를 두고 `C-c C-k`를 사용합니다.

## Release history[🔗](find/?domain=Verso.Genre.Manual.section&name=release-history "Permalink")

### October, 2025

The book has been updated to the latest stable Lean release (version 4.23.0), and now describes functional induction and the `grind` tactic.

### August, 2025

This is a maintenance release to resolve an issue with copy-pasting code from the book.

### July, 2025

The book has been updated for version 4.21 of Lean.

### June, 2025

The book has been reformatted with Verso.

### April, 2025

The book has been extensively updated and now describes Lean version 4.18.

### January, 2024

This is a minor bugfix release that fixes a regression in an example program.

### October, 2023

In this first maintenance release, a number of smaller issues were fixed and the text was brought up to date with the latest release of Lean.

### May, 2023

The book is now complete! Compared to the April pre-release, many small details have been improved and minor mistakes have been fixed.

### April, 2023

This release adds an interlude on writing proofs with tactics as well as a final chapter that combines discussion of performance and cost models with proofs of termination and program equivalence.
This is the last release prior to the final release.

### March, 2023

This release adds a chapter on programming with dependent types and indexed families.

### January, 2023

This release adds a chapter on monad transformers that includes a description of the imperative features that are available in `do`-notation.

### December, 2022

This release adds a chapter on applicative functors that additionally describes structures and type classes in more detail.
This is accompanied with improvements to the description of monads.
The December 2022 release was delayed until January 2023 due to winter holidays.

### November, 2022

This release adds a chapter on programming with monads. Additionally, the example of using JSON in the coercions section has been updated to include the complete code.

### October, 2022

This release completes the chapter on type classes. In addition, a short interlude introducing propositions, proofs, and tactics has been added just before the chapter on type classes, because a small amount of familiarity with the concepts helps to understand some of the standard library type classes.

### September, 2022

This release adds the first half of a chapter on type classes, which are Lean's mechanism for overloading operators and an important means of organizing code and structuring libraries. Additionally, the second chapter has been updated to account for changes in Lean's stream API.

### August, 2022

This third public release adds a second chapter, which describes compiling and running programs along with Lean's model for side effects.

### July, 2022

The second public release completes the first chapter.

### June, 2022

This was the first public release, consisting of an introduction and part of the first chapter.

## About the Author[🔗](find/?domain=Verso.Genre.Manual.section&name=about-the-author "Permalink")

David Thrane Christiansen has been using functional languages for twenty years, and dependent types for ten.
Together with Daniel P. Friedman, he wrote [*The Little Typer*](https://thelittletyper.com/), an introduction to the key ideas of dependent type theory.
He has a Ph.D. from the IT University of Copenhagen.
During his studies, he was a major contributor to the first version of the Idris language.
Since leaving academia, he has worked as a software developer at Galois in Portland, Oregon and Deon Digital in Copenhagen, Denmark, and he was the Executive Director of the Haskell Foundation.
At the time of writing, he is employed at the [Lean Focused Research Organization](https://lean-fro.org) working full-time on Lean.

David Thrane Christiansen는 20년 동안 함수형 언어를 사용해 왔으며, 10년 동안 종속 타입을 사용해 왔습니다.
Daniel P. Friedman과 함께, 그는 종속 타입 이론의 핵심 개념을 소개하는 [*The Little Typer*](https://thelittletyper.com/)를 저술했습니다.
그는 코펜하겐 IT 대학에서 박사 학위를 받았습니다.
그의 연구 기간 동안, 그는 Idris 언어의 첫 번째 버전에 대한 주요 기여자였습니다.
학계를 떠난 이후, 그는 미국 오리건주 포틀랜드의 Galois에서 소프트웨어 개발자로, 그리고 덴마크 코펜하겐의 Deon Digital에서 근무했으며, Haskell Foundation의 Executive Director였습니다.
저술 당시에 그는 [Lean Focused Research Organization](https://lean-fro.org)에 고용되어 Lean을 풀타임으로 작업했습니다.

## License[🔗](find/?domain=Verso.Genre.Manual.section&name=license "Permalink")

[![Creative Commons License](https://i.creativecommons.org/l/by/4.0/88x31.png)](http://creativecommons.org/licenses/by/4.0/)  
This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

The original version of the book was written by David Thrane Christiansen on contract to Microsoft Corporation, who generously released it under a Creative Commons Attribution 4.0 International License.
The current version has been modified by the author from the original version to account for changes in newer versions of Lean.
A detailed account of the changes can be found in the book's [source code repository](https://github.com/leanprover/fp-lean/).

[![Creative Commons License](https://i.creativecommons.org/l/by/4.0/88x31.png)](http://creativecommons.org/licenses/by/4.0/)  
이 작품은 [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/)에 따라 허가됩니다.

이 책의 원본은 Microsoft Corporation과의 계약에 따라 David Thrane Christiansen이 작성했으며, Microsoft는 이를 Creative Commons Attribution 4.0 International License에 따라 관대하게 공개했습니다.
현재 버전은 저자가 원본 버전을 수정하여 최신 버전의 Lean의 변경 사항을 반영하고 있습니다.
변경 사항에 대한 자세한 내용은 이 책의 [소스 코드 저장소](https://github.com/leanprover/fp-lean/)에서 찾을 수 있습니다.