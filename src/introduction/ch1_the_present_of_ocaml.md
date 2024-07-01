# OCaml 的现在

<iframe width="791" height="445" src="https://www.youtube.com/embed/JTEwC3HihFc" title="Functional Programming - What | OCaml Programming | Chapter 1 Video 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


OCaml 是一种函数式编程语言。函数式语言的关键语言抽象是数学函数。函数将一个输入映射到一个输出；对于相同的输入，它总是产生相同的输出。也就是说，数学函数是无状态的：它们不保留任何额外信息或在函数使用之间保持的状态。函数是第一类的：您可以将它们用作其他函数的输入，并将函数作为输出。用函数来表达一切使得编程模型更加统一和简单，比其他语言族中找到的过程和方法更容易推理。

命令式编程语言如 C 和 Java 涉及可变状态，在执行过程中会发生变化。命令指定如何通过破坏性地改变状态来计算。过程（或方法）可能具有会更新状态的副作用，除了产生返回值。

可变性的幻想是很容易推理的：机器做这个，然后这个，依此类推。

可变性的现实是，尽管机器擅长对状态进行复杂操作，但人类并不擅长理解它。这种情况为什么会发生的本质是，可变性破坏了引用透明性：即能够用其值替换表达式而不影响计算结果的能力。在数学中，如果 \\( f(x) = y \\)，那么你可以在任何地方替换 \\( y \\)  为 \\( f(x) \\) 。在命令式语言中，你不能这样做：f 可能会有副作用，所以在时间 \\( t \\) 计算 \\( f(x) \\) 可能会得出与时间 \\( t' \\) 不同的值。

诱人的想法是相信机器操纵一个单一状态，并且机器一次只做一件事。计算机系统竭尽全力试图提供这种幻觉。但事实上，有许多状态分布在线程、核心、处理器和网络计算机之间。机器同时做许多事情。可变性使得对分布状态和并发执行的推理变得极其困难。

不可变性可以使程序员摆脱这些顾虑。它提供了构建正确和并发程序的强大方式。OCaml 主要是一种不可变语言，就像大多数函数式语言一样。它支持具有可变状态的命令式编程，但在本书的许多章节中我们不会使用这些特性——部分原因是因为我们根本不需要它们，另一部分原因是为了让你戒除你可能不知道自己有的依赖。这种摆脱可变性的自由是 OCaml 可以带给你的最大视角变化之一。

## OCaml 的特点

<iframe width="791" height="445" src="https://www.youtube.com/embed/T-DIW1dhYzo" title="OCaml | OCaml Programming | Chapter 1 Video 5" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 是一种静态类型和类型安全的编程语言。静态类型语言在编译时检测类型错误；如果检测到类型错误，语言将不允许程序执行。类型安全语言限制了可以对哪种类型的数据执行哪种操作。在实践中，这可以防止许多愚蠢的错误（例如，将整数视为函数）并且还可以防止许多安全问题：在计算机应急响应团队（CERT，负责网络安全的美国政府机构）报告的一半以上入侵事件是由于缓冲区溢出，这在类型安全的语言中是不可能发生的。

一些语言，比如 Python 和 Racket，是类型安全的但是动态类型的。也就是说，类型错误只在运行时捕获。其他语言，比如 C 和 C++，是静态类型的但不是类型安全的：它们检查一些类型错误，但不能保证所有类型错误的缺失。也就是说，不能保证类型错误不会在运行时发生。还有其他语言，比如 Java，使用静态和动态类型结合来实现类型安全。。

OCaml 支持许多高级功能，其中一些您可能以前遇到过，而另一些可能是新的：

- 代数数据类型：您可以在 OCaml 中轻松构建复杂的数据结构，无需处理指针和内存管理。模式匹配是一项功能，我们很快将了解到，它使得检查数据结构的形状变得更加方便。
- 类型推断：您不必在每个地方都写下类型信息。编译器会自动推断大多数类型。这可以使代码更易于阅读和维护。
- 参数多态性：函数和数据结构可以针对类型进行参数化。这对于能够重用代码至关重要。
- 垃圾回收：自动内存管理使您摆脱了内存分配和释放的负担，这是诸如 C 语言中常见的错误源。
- 模块：OCaml 通过使用模块，使得构建大型系统变得简单。模块用于封装接口背后的实现。OCaml 通过提供可以操作模块的函数（称为 functors），在模块方面的功能远远超出大多数语言的范围。

## 工业中的 OCaml

<iframe width="791" height="445" src="https://www.youtube.com/embed/eNLm5Xbgmd0" title="Functional Programming - Why Part 2 | OCaml Programming | Chapter 1 Video 4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 和其他函数式语言远不及 Python、C 或 Java 那样受欢迎。OCaml 真正的优势在于语言操作（即编译器、分析器、验证器、证明器等）。这并不令人意外，因为 OCaml 起源于定理证明领域。

这并不是说函数式语言没有在工业中使用。有[许多行业项目](https://ocaml.org/industrial-users)使用 OCaml 和 [Haskell](https://wiki.haskell.org/Haskell_in_industry) 等语言。 Yaron Minsky（康奈尔大学博士 02）甚至写了一篇关于在[金融行业](http://www.journals.cambridge.org/abstract_S095679680800676X)使用 OCaml 的论文。它解释了 OCaml 的功能如何使其成为快速构建可用的复杂软件的良好选择。
