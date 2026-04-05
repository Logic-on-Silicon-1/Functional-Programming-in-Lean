# Getting to Know Lean

Source: https://lean-lang.org/functional_programming_in_lean/Getting-to-Know-Lean

[←Acknowledgments](Acknowledgments/#Functional-Programming-in-Lean--Acknowledgments "Acknowledgments")[1.1. Evaluating Expressions→](Getting-to-Know-Lean/Evaluating-Expressions/#evaluating "1.1. Evaluating Expressions")

# 1. Getting to Know Lean[🔗](find/?domain=Verso.Genre.Manual.section&name=getting-to-know "Permalink")

According to tradition, a programming language should be introduced by compiling and running a program that displays `"Hello, world!"` on the console.
This simple program ensures that the language tooling is installed correctly and that the programmer is able to run the compiled code.

관례에 따르면, 프로그래밍 언어는 콘솔에 `"Hello, world!"`를 표시하는 프로그램을 컴파일하고 실행하여 소개되어야 합니다.
이 간단한 프로그램은 언어 도구가 올바르게 설치되었고 프로그래머가 컴파일된 코드를 실행할 수 있음을 보장합니다.

Since the 1970s, however, programming has changed.
Today, compilers are typically integrated into text editors, and the programming environment offers feedback as the program is written.
Lean is no exception: it implements an extended version of the Language Server Protocol that allows it to communicate with a text editor and provide feedback as the user types.

하지만 1970년대 이후로 프로그래밍은 변했습니다.
오늘날 컴파일러는 일반적으로 텍스트 편집기에 통합되어 있으며, 프로그래밍 환경은 프로그램을 작성할 때 피드백을 제공합니다.
Lean도 예외가 아닙니다. Lean은 텍스트 편집기와 통신하고 사용자가 입력할 때 피드백을 제공할 수 있는 Language Server Protocol의 확장 버전을 구현합니다.

Languages as varied as Python, Haskell, and JavaScript offer a read-eval-print-loop (REPL), also known as an interactive toplevel or a browser console, in which expressions or statements can be entered.
The language then computes and displays the result of the user's input.
Lean, on the other hand, integrates these features into the interaction with the editor, providing commands that cause the text editor to display feedback integrated into the program text itself.
This chapter provides a short introduction to interacting with Lean in an editor, while [Hello, World!](Hello___-World___/#hello-world) describes how to use Lean traditionally from the command line in batch mode.

Python, Haskell, JavaScript과 같은 다양한 언어들은 read-eval-print-loop (REPL), 또는 인터랙티브 toplevel 또는 브라우저 콘솔로도 알려진 기능을 제공하며, 여기에 표현식이나 문장을 입력할 수 있습니다.
그러면 언어는 사용자의 입력 결과를 계산하고 표시합니다.
한편, Lean은 이러한 기능을 편집기와의 상호 작용에 통합하여, 텍스트 편집기가 프로그램 텍스트 자체에 통합된 피드백을 표시하게 하는 명령을 제공합니다.
이 장은 편집기에서 Lean과 상호 작용하는 방법에 대한 짧은 소개를 제공하며, [Hello, World!](Hello___-World___/#hello-world)는 배치 모드의 명령 줄에서 전통적으로 Lean을 사용하는 방법을 설명합니다.

It is best if you read this book with Lean open in your editor, following along and typing in each example. Please play with the
examples, and see what happens!

편집기에서 Lean을 열어 놓고 이 책을 읽으면서 각 예제를 따라 입력하는 것이 가장 좋습니다. 예제들을 직접 시도해 보고 어떻게 되는지 확인해 보세요!

1. [1.1. Evaluating Expressions](Getting-to-Know-Lean/Evaluating-Expressions/#evaluating)
2. [1.2. Types](Getting-to-Know-Lean/Types/#getting-to-know-types)
3. [1.3. Functions and Definitions](Getting-to-Know-Lean/Functions-and-Definitions/#functions-and-definitions)
4. [1.4. Structures](Getting-to-Know-Lean/Structures/#structures)
5. [1.5. Datatypes and Patterns](Getting-to-Know-Lean/Datatypes-and-Patterns/#datatypes-and-patterns)
6. [1.6. Polymorphism](Getting-to-Know-Lean/Polymorphism/#polymorphism)
7. [1.7. Additional Conveniences](Getting-to-Know-Lean/Additional-Conveniences/#getting-to-know-conveniences)
8. [1.8. Summary](Getting-to-Know-Lean/Summary/#getting-to-know-summary)

[←Acknowledgments](Acknowledgments/#Functional-Programming-in-Lean--Acknowledgments "Acknowledgments")[1.1. Evaluating Expressions→](Getting-to-Know-Lean/Evaluating-Expressions/#evaluating "1.1. Evaluating Expressions")