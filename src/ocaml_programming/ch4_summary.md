# Summary


本章是本书中最重要的章节之一。它没有涵盖任何新的语言特性。相反，我们学习了如何以可能是新的、令人惊讶的或具有挑战性的方式使用一些现有特性。高阶编程和抽象原则是两个概念，将帮助您成为任何语言中更好的程序员，而不仅仅是 OCaml。当然，各种语言在支持这些概念的程度上有所不同，有些语言在编写高阶代码时提供的帮助明显较少，这也是我们在本课程中使用 OCaml 的原因之一。


地图、过滤器、折叠和其他函数式正逐渐被广泛认可为优秀的计算结构化方式。部分原因在于它们将对数据结构的迭代与对每个元素进行的计算分离开来。诸如 Python、Ruby 和 Java 8 等语言现在已经支持这种迭代方式。

## 术语和概念

- Abstraction Principle 抽象原则
- accumulator 累加器
- apply 应用
- associative 关联的
- compose 组成
- factor 因素
- filter 过滤器
- first-order function 一阶函数
- fold 折叠
- functional 功能性
- generalized fold operation 广义折叠操作
- higher-order function 高阶函数
- map 地图
- pipeline 管道
- pipelining 流水线化

## 进一步阅读

- Introduction to Objective Caml -- Objective Caml 简介，第 3.1.3 章，第 5.3 章
- OCaml from the Very Beginning --  OCaml 从零开始，第 6 章
- More OCaml: Algorithms, Methods, and Diversions --《更多 OCaml：算法、方法和娱乐》，第 1 章，约翰·惠廷顿著。这本书是《OCaml from the Very Beginning -- OCaml 从零开始》的续集。
- Real World OCaml --- 现实世界的 OCaml，第 3 章（请注意，这本书的 Core 库与标准库的 List 模块不同， map 和 fold 的类型也与我们在这里看到的不同）
- “Higher Order Functions”，《函数式编程：实践与理论》第 6 章。Bruce J. MacLennan，Addison-Wesley，1990 年。我们对高阶函数和抽象原则的讨论受到了这一章的启发。
- “编程能否摆脱冯·诺依曼风格？一种函数式风格及其程序代数。”约翰·巴克斯（John Backus）1977 年图灵奖演讲的详细形式，作为一篇发表的[文章](https://dl.acm.org/doi/pdf/10.1145/359576.359579)。
- 《[斯坦福哲学百科全书](http://plato.stanford.edu/entries/logic-higher-order/)》中的“二阶和高阶逻辑”。
