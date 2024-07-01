# OCaml 的现在

<iframe width="791" height="445" src="https://www.youtube.com/embed/JTEwC3HihFc" title="Functional Programming - What | OCaml Programming | Chapter 1 Video 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


OCaml 是一种函数式编程语言。函数式语言的关键语言抽象是数学函数。函数将输入映射到输出；对于相同的输入，它总是产生相同的输出。也就是说，数学函数是无状态的：它们不维护在函数使用之间持续存在的任何额外信息或状态。函数是一流的：您可以将它们用作其他函数的输入，并生成函数作为输出。用函数来表达一切可以实现统一且简单的编程模型，该模型比其他语言系列中的过程和方法更容易推理。

命令式编程语言（例如 C 和 Java）涉及在执行过程中发生变化的可变状态。命令指定如何通过破坏性地改变该状态来进行计算。过程（或方法）除了产生返回值之外还可能具有更新状态的副作用。

可变性的幻想在于它很容易推理：机器做这个，然后做这个，等等。

实质上的不可变性是，机器在复杂地操作状态时很好，而人类却不擅长理解它。这是因为不可变性破坏了引用透明性：可以将表达式替换为其值而不影响计算结果。在数学中，如果 \\( f(x) = y \\)，那么你可以在任何地方替换 \\( y \\)  为 \\( f(x) \\) 。在命令式语言中，你不能这样做：f 可能会有副作用，所以在时间 \\( t \\) 计算 \\( f(x) \\) 可能会得出与时间 \\( t' \\) 不同的值。

人们很容易相信机器操纵的是单一状态，并且机器一次只做一件事。计算机系统竭尽全力试图提供这种幻觉。但这只是：一种幻觉。实际上，存在许多状态，分布在线程、内核、处理器和联网计算机上。而且机器可以同时做很多事情。可变性使得分布式状态和并发执行的推理变得非常困难。

然而，不变性使程序员摆脱了这些担忧。它提供了构建正确的并发程序的强大方法。与大多数函数式语言一样，OCaml 主要是一种不可变语言。它确实支持具有可变状态的命令式编程，但是直到本书的许多章节中我们才会使用这些功能，部分原因是我们根本不需要它们，部分原因是为了让您从依赖中“突然戒掉”你可能不知道你有。这种免于可变性的自由是 OCaml 可以为您带来的最大的视角变化之一。

## OCaml 的特点

<iframe width="791" height="445" src="https://www.youtube.com/embed/T-DIW1dhYzo" title="OCaml | OCaml Programming | Chapter 1 Video 5" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 是一种静态类型和类型安全的编程语言。静态类型语言在编译时检测类型错误；如果检测到类型错误，该语言将不允许执行该程序。类型安全语言限制可以对哪种数据执行哪种操作。实际上，这可以防止许多愚蠢的错误（例如，将整数视为函数），并且还可以防止许多安全问题：计算机紧急响应小组（CERT，美国政府机构）报告的入侵事件中有一半以上负责网络安全的任务）是由于缓冲区溢出造成的，这在类型安全语言中是不可能的。

有些语言（例如 Python 和 Racket）是类型安全的，但是是动态类型的。也就是说，类型错误仅在运行时才会被捕获。其他语言（例如 C 和 C++）是静态类型的，但不是类型安全的：它们检查某些类型错误，但不保证不存在所有类型错误。也就是说，不能保证在运行时不会发生类型错误。还有其他语言，如 Java，使用静态和动态类型的组合来实现类型安全。

OCaml 支持许多高级功能，其中一些您以前会遇到过，而另一些可能是新的：

- **代数数据类型**：您可以在 OCaml 中轻松构建复杂的数据结构，而无需担心指针和内存管理。模式匹配——我们很快就会了解的一个功能，可以检查数据结构的形状——使它们变得更加方便。
- **类型推断**：您不必在任何地方都写下类型信息。编译器会自动找出大多数类型。这可以使代码更易于阅读和维护。
- **参数多态性**：函数和数据结构可以通过类型进行参数化。这对于能够重用代码至关重要。
- **垃圾收集**：自动内存管理使您摆脱内存分配和释放的负担，这是 C 等语言中错误的常见来源。
- **模块**：OCaml 通过使用模块可以轻松构建大型系统。模块用于封装接口后面的实现。 OCaml 通过提供操作模块的函数（称为函子），远远超出了大多数带有模块的语言的功能。

## 工业中的 OCaml

<iframe width="791" height="445" src="https://www.youtube.com/embed/eNLm5Xbgmd0" title="Functional Programming - Why Part 2 | OCaml Programming | Chapter 1 Video 4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 和其他函数式语言远不如 Python、C 或 Java 流行。 OCaml 的真正优势在于语言操作（即编译器、分析器、验证器、证明器等）。这并不奇怪，因为 OCaml 是从定理证明领域发展而来的。

这并不是说函数式语言没有在工业中使用。有[许多行业项目](https://ocaml.org/industrial-users)使用 OCaml 和 [Haskell](https://wiki.haskell.org/Haskell_in_industry) 等语言。 Yaron Minsky（康奈尔大学博士 02）甚至写了一篇关于在[金融行业](http://www.journals.cambridge.org/abstract_S095679680800676X)使用 OCaml 的论文。它解释了 OCaml 的功能如何使其成为快速构建可用的复杂软件的良好选择。
