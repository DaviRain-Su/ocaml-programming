# Summary

列表是 OCaml 中非常有用的内置数据结构。该语言提供了一种轻量级的语法来构建它们，而不是要求您使用库。访问列表的部分利用了模式匹配，这是一个非常强大的特性（正如您可能从其相当冗长的语义中期待的那样）。随着课程的进行，我们将看到模式匹配的更多用途。

这些内置列表是作为单链表实现的。当您的需求超出小到中等大小的列表时，这一点很重要。在长列表上使用递归函数将占用大量堆栈空间，因此尾递归变得重要。如果您尝试处理非常庞大的列表，您可能根本不想使用链表，而是使用一种能更好地利用内存局部性的数据结构。

OCaml 提供了变体（一种类型）、元组和乘积（每种类型）以及选项（可能类型）的数据类型。模式匹配可用于访问这些数据类型的值。模式匹配可用于 let 表达式和函数中。

关联列表结合了列表和元组，创建了字典的轻量级实现。

变体是一种强大的语言特性。它们是在函数式语言中表示数据的主力军。OCaml 变体实际上将几个理论上独立的语言特性合并为一个：和类型、积类型、递归类型以及参数化（多态）类型。其结果是能够表达许多种类的数据，包括列表、选项、树甚至异常。

## 术语和概念

- algebraic data type 代数数据类型
- append 追加
- association list 关联列表
- binary trees as variants 二叉树作为变体
- binding 绑定
- branch 分支
- carried data 传输数据
- catch-all cases 全包式案例
- cons 缺点
- constant constructor 常量构造函数
- constructor 构造函数
- copying 复制
- desugaring 解糖
- each-of type 每个类型
- exception 例外
- exception as variants 异常作为变体
- exception packet 异常数据包
- exception pattern 异常模式
- exception value 异常值
- exhaustiveness 详尽性
- field 领域
- head 头部
- induction 归纳
- leaf 叶
- list 清单
- lists as variants 列为变体
- maybe type 也许类型
- mutually recursive functions 相互递归函数
- natural numbers as variants 自然数作为变量
- nil 空
- node 节点
- non-constant constructor 非常量构造函数
- one-of type 独特类型
- options 选项
- options as variants 选项作为变体
- order of evaluation 评估顺序
- pair 对
- parameterized variant 参数化变体
- parametric polymorphism 参数多态性
- pattern matching 模式匹配
- prepend 添加
- product type 产品类型
- record 记录
- recursion 递归
- recursive variant 递归变体
- sharing 分享
- stack frame 栈帧
- sum type 求和类型
- syntactic sugar 语法糖
- tag 标签
- tail 尾部
- tail call 尾调用
- tail recursion 尾递归
- test-driven development (TDD) 测试驱动开发（TDD）
- triple 三重
- tuple 元组
- type constructor 类型构造函数
- type synonym 类型同义词
- variant 变体
- wildcard 通配符

## 进一步阅读

- Objective Caml 简介，章节 4，5.2，5.3，5.4，6，7，8.1
- OCaml 从最初开始，第 3、4、5、7、8、10、11 章
- 现实世界中的 OCaml，第 3、5、6、7 章
