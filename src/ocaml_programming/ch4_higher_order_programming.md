# 高阶编程

<iframe width="791" height="445" src="https://www.youtube.com/embed/rTbinjZ9-oc" title="Higher-Order Functions | OCaml Programming | Chapter 4 Video 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


函数在 OCaml 中就像任何其他值一样。这究竟意味着什么？这意味着我们可以将函数作为参数传递给其他函数，我们可以将函数存储在数据结构中，我们可以从其他函数中返回函数作为结果，等等。

高阶函数要么接受其他函数作为输入，要么返回其他函数作为输出（或两者兼有）。高阶函数也被称为函数式，因此使用它们进行编程可以被称为函数式编程——这表明像 OCaml 这样的语言中编程的核心是什么。

高阶函数是从函数式语言中最近引入主流语言的概念之一。Java 8 Streams 库和 Python 2.3 的 itertools 模块就是其中的例子；C++ 也至少从 2011 年开始增加了对高阶函数的支持。

> NOTE:
>
> C 语言的专家可能会反对采用这种方法并不是最近才有的。毕竟，C 语言长期以来一直通过函数指针具有进行高阶编程的能力。但是，这种能力还取决于通过传递额外的环境参数来传递函数中变量的值的编程模式，以便通过指针调用函数。正如我们将在后面关于解释器的章节中看到的那样，在函数式语言中（高阶）函数的本质是它们实际上是一种称为闭包的东西，这消除了对额外参数的需求。请记住，问题不在于一种语言中可能计算什么——毕竟一切最终都被编译成机器代码，所以我们可以专门写那种代码——而在于什么是愉快的计算。

在本章中，我们将看到所有的喧嚣是关于什么的。高阶函数使得编写美观、通用、可重用的代码成为可能。
