# List

<iframe width="791" height="445" src="https://www.youtube.com/embed/x8oLIEtSRBs" title="Lists | OCaml Programming | Chapter 3 Video 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 列表是具有相同类型的值序列。它们以单链表的形式实现。这些列表在语言中拥有一流的地位：有特殊支持可以轻松创建和处理列表。这是 OCaml 与许多其他函数式语言共享的特征。主流的命令式语言，如 Python，现在也具有这样的支持。也许这是因为程序员发现直接与列表作为语言的一流部分一起工作如此愉快，而不必通过库（如 C 和 Java）来实现。


## 构建列表

<iframe width="791" height="445" src="https://www.youtube.com/embed/I9u4kFPM7YI" title="List Syntax and Semantics | OCaml Programming | Chapter 3 Video 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

**句法**。有三种句法形式用于构建列表：

```ocaml
[]
e1 :: e2
[e1; e2; ...; en]
```

空列表写作 `[]` ，发音为“`nil`”，这个名称来自于 Lisp。给定一个列表 `lst` 和元素 `elt` ，我们可以通过写 `elt :: lst` 将 `elt` 添加到 `lst` 的前面。双冒号运算符发音为“`cons`”，这个名称来自于 Lisp 中用于在内存中构造对象的运算符。“`Cons`”也可以用作动词，比如“我将一个元素添加到列表中”。列表的第一个元素通常被称为头部，其余的元素（如果有的话）被称为尾部。

方括号语法方便但不必要。 任何列表 `[e1; e2; ...; en]` 都可以用更原始的 `nil` 和 `cons` 语法来代替： `e1 :: e2 :: ... :: en :: []` 。 当可以用更原始的语法在语言中定义出一种愉快的语法时，我们称这种愉快的语法为语法糖：它使语言变得“更甜”。 将甜美的语法转换为更原始的语法称为去糖化。

由于列表的元素可以是任意表达式，因此列表可以嵌套得很深，例如， `[[[]]; [[1; 2; 3]]]` 。

**动态语义学**。

- `[]` 已经是一个值。
- 如果 `e1` 评估为 `v1` ，并且如果 `e2` 评估为 `v2` ，那么 `e1 :: e2` 评估为 `v1 :: v2` 。

由于这些规则以及如何对列表的方括号表示进行去糖化，我们得到了以下推导规则：

- 如果 `ei` 对于 `1..n` 中的所有 `i` 都评估为 `vi` ，那么 `[e1; ...; en]` 将评估为 `[v1; ...; vn]` 。

开始在所有评估规则中写“评估为”变得乏味了。因此，让我们引入一个更简短的表示法。我们将写 `e ==> v` 来表示 `e` 评估为 `v` 。请注意， `==>` 不是 OCaml 语法的一部分。相反，它是我们在语言描述中使用的一种表示法，有点像元变量。利用这种表示法，我们可以重写上述的后两条规则：

- 如果 `e1 ==> v1` ，并且如果 `e2 ==> v2` ，那么 `e1 :: e2 ==> v1 :: v2` 。
- 如果对于 `1..n` 中的所有 `i` 都有 `ei ==> vi` ，那么 `[e1; ...; en] ==> [v1; ...; vn]` 。

**静态语义**。

列表的所有元素必须具有相同的类型。如果该元素类型是 `t` ，那么列表的类型是 `t list` 。您应该从右向左阅读这些类型： `t list` 是 `t` 的列表， `t list list` 是 `t` 的列表的列表，等等。这里的单词 `list` 本身不是一种类型：无法构建具有简单类型 `list` 的 OCaml 值。相反， `list` 是一种类型构造器：给定一个类型，它会产生一个新类型。例如，给定 `int` ，它会产生类型 `int list` 。您可以将类型构造器视为操作类型的函数，而不是操作值的函数。

类型检查规则：

- `[] : 'a list`
- 如果 `e1 : t` 和 `e2 : t list` ，那么 `e1 :: e2 : t list` 。如果冒号及其优先级令人困惑，后者意味着 `(e1 :: e2) : t list`

在 `[]` 的规则中，回想一下 `'a` 是一个类型变量：它代表一个未知类型。因此，空列表是一个元素具有未知类型的列表。如果我们将一个 `int` 连接到它上面，比如说 `2 :: []` ，那么编译器会推断对于这个特定列表， `'a` 必须是 `int` 。但是如果在另一个地方我们将一个 `bool` 连接到它上面，比如说 `true :: []` ，那么编译器会推断对于这个特定列表， `'a` 必须是 `bool` 。

## 访问列表

<iframe width="791" height="445" src="https://www.youtube.com/embed/AkrlDpHN_zE" title="Pattern Matching | OCaml Programming | Chapter 3 Video 7" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

> **NOTE**:
>
> 上面链接的视频也使用记录和元组作为示例。这些内容在本书的[后面部分](./ch3_records_and_tuples.md)进行了讨论。

<iframe width="791" height="445" src="https://www.youtube.com/embed/sO9wxUxajS4" title="Pattern Matching with Lists | OCaml Programming | Chapter 3 Video 8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

构建列表的方法实际上只有两种，即使用 `nil` 和 `cons`。因此，如果我们想要将列表拆分为其组成部分，我们必须说明当列表为空时该怎么办，以及当列表非空时该怎么办（即将一个元素的 `cons` 连接到另一个列表上）。我们使用一种称为模式匹配的语言特性来实现这一点。


这里是使用模式匹配来计算列表总和的示例：

```ocaml
let rec sum lst =
  match lst with
  | [] -> 0
  | h :: t -> h + sum t
```

```ocaml
val sum : int list -> int = <fun>
```

该函数表示获取输入 `lst` ，并查看其形状是否与空列表相同。如果是，则返回 `0`。否则，如果其形状与列表 `h :: t` 相同，则让 `h` 成为 `lst` 的第一个元素，让 `t` 成为 `lst` 的其余元素，并返回 `h + sum t` 。这里选择的变量名意在暗示“头”和“尾”，这是一种常见习语，但如果需要的话我们也可以使用其他名称。另一个常见习语是：

```ocaml
let rec sum xs =
  match xs with
  | [] -> 0
  | x :: xs' -> x + sum xs'
```

```ocaml
val sum : int list -> int = <fun>
```

也就是说，输入列表是一个 `xs`（发音为 EX-uhs）的列表，头元素是 `x`，尾部是 `xs'`（发音为 EX-uhs prime）。

从句法上讲，没有必要使用这么多行来定义 `sum` 。我们可以在一行上完成所有操作：

```ocaml
let rec sum xs = match xs with | [] -> 0 | x :: xs' -> x + sum xs'
```

```ocaml
val sum : int list -> int = <fun>
```

或者，注意到在我们使用多少行之后，第 with 个 `|` 是可选的，我们也可以这样写：

```ocaml
let rec sum xs = match xs with [] -> 0 | x :: xs' -> x + sum xs'
```

```ocaml
val sum : int list -> int = <fun>
```

多行格式是我们在本书中通常使用的格式，因为它有助于人眼更好地理解语法。然而，OCaml 代码格式化工具正在朝着单行格式发展，每当代码足够短以适合一行时。

这里是另一个使用模式匹配来计算列表长度的示例：

```ocaml
let rec length lst =
  match lst with
  | [] -> 0
  | h :: t -> 1 + length t
```

```ocaml
val length : 'a list -> int = <fun>
```

注意一下，在模式匹配的右侧我们实际上并不需要变量 `h` 。当我们想要指示模式中某个值的存在，但又不想给它起名字时，可以写成 `_` （下划线字符）：

```ocaml
let rec length lst =
  match lst with
  | [] -> 0
  | _ :: t -> 1 + length t
```

```ocaml
val length : 'a list -> int = <fun>
```

该功能实际上作为 OCaml 标准库 List 模块的一部分内置。它在那里的名称是 `List.length` 。该“点”表示模块 List 内名为 length 的函数，类似于许多其他语言中使用的点表示法。

这里是第三个示例，将一个列表附加到另一个列表的开头：

```ocaml
let rec append lst1 lst2 =
  match lst1 with
  | [] -> lst2
  | h :: t -> h :: append t lst2
```

```ocaml
val append : 'a list -> 'a list -> 'a list = <fun>
```

例如， `append [1; 2] [3; 4]` 是 `[1; 2; 3; 4]` 。该函数实际上作为内置运算符 `@` 可用，因此我们可以改为写成 `[1; 2] @ [3; 4]` 。

<iframe width="791" height="445" src="https://www.youtube.com/embed/VDRTatjSl0E" title="Cons vs Append | OCaml Programming | Chapter 3 Video 10" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

作为最后一个例子，我们可以编写一个函数来确定一个列表是否为空：

```ocaml
let empty lst =
  match lst with
  | [] -> true
  | h :: t -> false
```

```ocaml
val empty : 'a list -> bool = <fun>
```

但是有一种更好的方法可以编写相同的函数，而不需要模式匹配：

```ocaml
let empty lst =
  lst = []
```

```ocaml
val empty : 'a list -> bool = <fun>
```

注意以上所有递归函数与对自然数进行归纳证明的相似之处：每个自然数要么是 `0`，要么比另一个自然数`n`
大 `1`，因此归纳证明有一个基本情况为 `0` 和一个归纳情况为 `n + 1`
。同样，我们所有的函数都有一个空列表的基本情况和一个比另一个列表多一个元素的递归情况。这种相似性并非偶然。归纳和递归之间存在着深刻的关系；我们将在本书后面更详细地探讨这种关系。

顺便提一下，有两个库函数 `List.hd` 和 `List.tl` ，它们返回列表的头部和尾部。在 OCaml 中，直接将它们应用于列表是不好的习惯。问题在于，当应用于空列表时，它们会引发异常，而你必须记得处理该异常。相反，你应该使用模式匹配：这样你将被迫匹配空列表和非空列表（至少），这将防止异常被引发，从而使你的程序更加健壮。

## 不变异列表

列表是不可变的。无法将列表中的一个元素从一个值更改为另一个值。相反，OCaml 程序员会从旧列表中创建新列表。例如，假设我们想要编写一个函数，该函数返回与其输入列表相同的列表，但第一个元素（如果有的话）增加一。我们可以这样做：

```ocaml
let inc_first lst =
  match lst with
  | [] -> []
  | h :: t -> h + 1 :: t
```

现在您可能会担心我们是否在浪费空间。毕竟，编译器至少有两种方式可以实现上述代码：

- 在与 cons 模式匹配时，当创建新列表时，复制整个尾列表 `t` ，使得内存使用量增加的数量与 `t` 的长度成比例增加。
- 在旧列表和新列表之间共享尾部列表 `t` ，以确保内存使用量不会增加——除了存储 `h + 1` 所需的额外内存之外。

事实上，编译器执行后者。所以不需要担心。编译器实现共享是相当安全的原因在于列表元素是不可变的。如果它们是可变的，那么我们将开始担心我拥有的列表是否与你拥有的列表共享，以及我所做的更改是否会在你的列表中可见。因此，不可变性使得对代码进行推理更容易，并使编译器执行优化变得更安全。

## 使用列表进行模式匹配

我们已经看到如何使用模式匹配来访问列表。让我们更仔细地看看这个特性。

**句法**。

```ocaml
match e with
| p1 -> e1
| p2 -> e2
| ...
| pn -> en
```

每个子句 `pi -> ei` 被称为模式匹配的一个分支或一个情况。整个模式匹配中的第一个竖线是可选的。

这里的 `p` 是一种称为模式的新句法形式。目前，模式可能是：

- 变量名称，例如， `x`
- 下划线字符 `_` ，也被称为通配符
- 空列表 `[]`
- `p1 :: p2`
- `[p1; ...; pn]`

在模式中，变量名不得出现超过一次。例如，模式 `x :: x` 是非法的。通配符可以出现任意次数。

随着我们对 OCaml 中可用的数据结构了解更多，我们将扩展模式可能性。

**动态语义学**

<iframe width="791" height="445" src="https://www.youtube.com/embed/sz72NP4u4DQ" title="Pattern Matching Syntax and Semantics | OCaml Programming | Chapter 3 Video 11" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

模式匹配涉及两个相互关联的任务：确定模式是否与值匹配，以及确定值的哪些部分应与模式中的哪些变量名相关联。前一个任务直观地是关于确定模式和值是否具有相同的形状。后一个任务是关于确定模式引入的变量绑定。例如，考虑以下代码：

```ocaml
match 1 :: [] with
| [] -> false
| h :: t -> h >= 1 && List.length t = 0
```

```ocaml
- : bool = true
```

在评估第二分支的右侧时， `h` 绑定到 `1` ， `t` 绑定到 `[]` 。让我们写 `h->1` 表示变量绑定，即 `h` 的值为 `1` ；这不是 OCaml 语法的一部分，而是我们用来推理语言的符号。因此，第二分支产生的变量绑定将是 `h->1, t->[]` 。

利用该符号表示法，以下是模式何时与值匹配以及匹配产生的绑定的定义：

- 模式 `x` 匹配任何值 `v` 并生成变量绑定 `x->v` 。
- 模式 `_` 匹配任何值并且不生成任何绑定。
- 模式 `[]` 匹配值 `[]` 并且不生成任何绑定。
- 如果 `p1` 匹配 `v1` 并产生一组绑定`b1`，并且如果 `p2` 匹配 `v2` 并产生一组绑定 `b1 U b2`
，那么 `p1 :: p2` 匹配 `v1 :: v2` 并产生一组绑定。请注意 `v2` 必须是一个列表（因为它在 :: 的右侧），并且可以具有任意长度：`0` 个元素，`1` 个元素或多个元素。请注意绑定的并集  `b1 U b2` 永远不会出现一个问题，即同一个变量在 `b1` 和  `b2` 中分别绑定，因为语法限制要求模式中的变量名不得多次出现。
- 如果对于 `1..n` 中的所有 `i` ，它成立 `pi` 匹配 `vi` 并产生绑定集  `bi` ，那么 `[p1; ...; pn]` 匹配 `[v1; ...; vn]` 并产生绑定集 `Uibi`。请注意，此模式指定列表必须具有的确切长度。

现在我们可以说如何评估 `match e with p1 -> e1 | ... | pn -> en` ：

- 评估 `e` 的值为 `v` 。
- 尝试将 `v` 与 `p1` 匹配，然后与 `p2` 匹配，依此类推，按照它们在匹配表达式中出现的顺序进行匹配。
- 如果 `v` 与任何模式都不匹配，则匹配表达式的评估会引发 `Match_failure` 异常。我们还没有讨论 OCaml 中的异常，但您肯定从其他语言中熟悉它们。在本章末尾，我们将回到异常讨论，之前我们会介绍 OCaml 中的一些其他内置数据结构。
- 否则，让 `pi` 成为第一个匹配的模式，并让 `b` 成为通过将 `v` 与 `pi` 匹配产生的变量绑定。
- 替换那些在 `ei` 内部的绑定 `b` ，生成一个新表达式 `e'` 。
- 评估 `e'` 的值为 `v'` 。
- 整个匹配表达式的结果是 `v'` 。

例如，这是如何评估这个匹配表达式的方式：

```ocaml
match 1 :: [] with
| [] -> false
| h :: t -> h = 1 && t = []
```

```ocaml
- : bool = true
```

- `1 :: []` 已经是一个值。
- `[]` 与 `1 :: []` 不匹配。
- `h :: t` 匹配 `1 :: []` 并生成变量绑定 `{ h->1 , t->[] }`，因为：
  - `h` 匹配 `1` 并生成变量绑定 `h->1` 。
  - `t` 匹配 `[]` 并生成变量绑定 `t->[]` 。
- 将 `{ h->1 , t->[] }` 替换到 `h = 1 && t = []` 中会产生一个新表达式 `1 = 1 && [] = []` 。
- 评估 `1 = 1 && [] = []` 的结果为 `true` 。我们在这里省略了这一事实的理由，但它是根据其他内置运算符和函数应用的评估规则得出的。
- 因此，整个匹配表达式的结果是 `true` 。

**静态语义**

<iframe width="791" height="445" src="https://www.youtube.com/embed/aLQJpk9vXD4" title="Static Checking of Pattern Matching | OCaml Programming | Chapter 3 Video 12" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

除了这种类型检查规则外，编译器还会对每个匹配表达式进行另外两项检查。

首先，穷尽性：编译器会检查以确保有足够的模式来保证至少有一个模式与表达式 `e` 匹配，无论该表达式在运行时的值是什么。这确保了程序员没有遗漏任何分支。例如，下面的函数将导致编译器发出警告：

```ocaml
let head lst = match lst with h :: _ -> h
```

```ocaml
File "[12]", line 1, characters 15-41:
1 | let head lst = match lst with h :: _ -> h
                   ^^^^^^^^^^^^^^^^^^^^^^^^^^
Warning 8 [partial-match]: this pattern-matching is not exhaustive.
Here is an example of a case that is not matched:
[]
val head : 'a list -> 'a = <fun>
```

通过向程序员发出警告，编译器帮助程序员防范在运行时可能发生的 Match_failure 异常

> NOTE:
>
> 抱歉，上面单元格的输出在 HTML 中被分成多行。这是目前使用来构建本书的 [JupyterBook](https://github.com/executablebooks/jupyter-book/issues/973) 框架中的一个未解决问题。

第二，未使用的分支：编译器会检查是否有任何分支永远不会匹配，因为之前的某个分支已经被保证会成功。例如，下面的函数将导致编译器发出警告：

```ocaml
let rec sum lst =
  match lst with
  | h :: t -> h + sum t
  | [ h ] -> h
  | [] -> 0
```

```ocaml
File "[13]", line 4, characters 4-9:
4 |   | [ h ] -> h
        ^^^^^
Warning 11 [redundant-case]: this match case is unused.
val sum : int list -> int = <fun>
```

第二分支未被使用，因为第一分支将匹配第二分支匹配的任何内容。

未使用的匹配情况通常表明程序员编写的内容与其本意不符。因此，通过提供该警告，编译器帮助程序员检测其代码中潜在的错误。

这里是一个导致未使用匹配情况警告的最常见错误的示例。理解这个错误也是检查您对匹配表达式的动态语义理解的好方法：

```ocaml
let length_is lst n =
  match List.length lst with
  | n -> true
  | _ -> false
```

```ocaml
File "[14]", line 4, characters 4-5:
4 |   | _ -> false
        ^
Warning 11 [redundant-case]: this match case is unused.
val length_is : 'a list -> 'b -> bool = <fun>
```

程序员认为如果 `lst` 的长度等于 `n` ，那么这个函数将返回 `true` ，否则将返回 `false` 。但实际上这个函数总是返回 `true` 。为什么？因为模式变量 `n` 与函数参数 `n` 是不同的。假设 `lst` 的长度为 `5`。那么模式匹配变成了： `match 5 with n -> true | _ -> false` 。 `n` 匹配 `5` 吗？是的，根据上述规则：变量模式匹配任何值，并在这里生成绑定 `n->5` 。然后评估将该绑定应用于 `true` ，用 `5` 替换 `true` 中的所有 `n` 。嗯，这里没有这样的出现。所以我们完成了，评估的结果就是 `true` 。

程序员真正想要写的是：

```ocaml
let length_is lst n =
  match List.length lst with
  | m -> m = n
```

```ocaml
val length_is : 'a list -> int -> bool = <fun>
```

或者更好的是:

```ocaml
let length_is lst n =
  List.length lst = n
```

```ocaml
val length_is : 'a list -> int -> bool = <fun>
```

## 深度模式匹配

模式可以嵌套。这样做可以让您的代码深入查看列表的结构。例如：

- `_ :: []` 匹配所有只有一个元素的列表
- `_ :: _` 匹配至少包含一个元素的所有列表
- `_ :: _ :: []` 匹配所有恰好有两个元素的列表
- `_ :: _ :: _ :: _` 匹配至少包含三个元素的所有列表


## 立即匹配

<iframe width="791" height="445" src="https://www.youtube.com/embed/VgVP8Tin6yY" title="The Function Keyword | OCaml Programming | Chapter 3 Video 9" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

当您有一个立即对其最终参数进行模式匹配的函数时，有一个很好的语法糖可以帮助您避免编写额外的代码。这里有一个例子：而不是

```ocaml
let rec sum lst =
  match lst with
  | [] -> 0
  | h :: t -> h + sum t
```

```ocaml
val sum : int list -> int = <fun>
```

你可以写

```ocaml
let rec sum = function
  | [] -> 0
  | h :: t -> h + sum t
```

```ocaml
val sum : int list -> int = <fun>
```

`function` 是一个关键字。请注意，我们可以省略包含 `match` 的那一行，以及参数的名称，该名称在其他任何地方都没有被使用过。在这种情况下，尤其重要的是在函数的规范注释中记录该参数应该是什么，因为代码不再给出它的描述性名称。

## OCamldoc 和列表语法


OCamldoc 是类似于 Javadoc 的文档生成器。它从源代码中提取注释并生成 HTML（以及其他输出格式）。例如，[List 模块的标准库 Web 文档](https://ocaml.org/manual/5.2/api/List.html)是由 OCamldoc 从该模块的[标准库源代码](https://github.com/ocaml/ocaml/blob/trunk/stdlib/list.mli)生成的。

> WARNING:
>
> 在 OCamldoc 中，有一个与列表有关的方括号的句法约定，可能会让人感到困惑。
>
> 在 OCamldoc 注释中，源代码被方括号括起来。该代码将以打字机字体呈现，并在输出的 HTML 中进行语法高亮显示。在这种情况下，方括号并不表示一个列表。

例如，在标准库源代码中，这是对 `List.hd` 的注释:

```ocaml
(** Return the first element of the given list. Raise
   [Failure "hd"] if the list is empty. *)
```

[Failure "hd"] 并不意味着包含异常 `Failure "hd"` 的列表。相反，它意味着将表达式 `Failure "hd"` 排版为源代码，正如您在这里所[看到的](https://ocaml.org/manual/5.2/api/List.html)。

这在你想要讨论列表作为文档的一部分时可能会变得特别令人困惑。例如，这是我们可以重写该评论的一种方式：

```ocaml
(** [hd lst] returns the first element of [lst].
    Raises [Failure "hd"] if [lst = []]. *)
```

在 `[lst = []]` 中，外部方括号表示源代码作为注释的一部分，而内部方括号表示空列表。

## 列表推导

一些语言，包括 Python 和 Haskell，具有一种称为推导的语法，允许列表的编写方式有点类似数学中的集合推导。推导的最早例子似乎是函数式语言 NPL，它设计于 1977 年。

OCaml 没有内置的语法支持推导式。虽然一些扩展被开发出来，但似乎已经不再受支持。推导式所完成的主要任务（过滤掉一些元素，转换其他元素）实际上已经被高阶编程很好地支持，我们将在后面的章节中学习到，以及我们已经学习过的管道操作符。因此，推导式的额外语法实际上从未真正需要。

## 尾递归

请回忆一下，如果一个函数在递归调用自身后不执行任何计算，而是在递归调用返回后立即将递归调用的值返回给调用者，则该函数是尾递归的。考虑这两种列表求和的实现 `sum` 和 `sum_tr` ：

```ocaml
let rec sum (l : int list) : int =
  match l with
  | [] -> 0
  | x :: xs -> x + (sum xs)

let rec sum_plus_acc (acc : int) (l : int list) : int =
  match l with
  | [] -> acc
  | x :: xs -> sum_plus_acc (acc + x) xs

let sum_tr : int list -> int =
  sum_plus_acc 0
```

```ocaml
val sum : int list -> int = <fun>
```

```ocaml
val sum_plus_acc : int -> int list -> int = <fun>
```

```ocaml
val sum_tr : int list -> int = <fun>
```

观察上述 `sum` 和 `sum_tr` 函数之间的区别：在 `sum` 函数中，它不是尾递归的，在递归调用返回其值后，我们将 `x` 添加到其中。在尾递归 `sum_tr` 中，或者更确切地说，在 `sum_plus_acc` 中，在递归调用返回后，我们立即返回该值，而无需进一步计算。

如果你要在非常长的列表上编写函数，尾递归对性能非常重要。因此，当你在使用尾递归和非尾递归函数之间做选择时，最好在非常长的列表上使用尾递归函数以实现空间效率。出于这个原因，List 模块记录了哪些函数是尾递归的，哪些不是。

但这并不意味着尾递归实现就一定更好。例如，尾递归函数可能更难阅读。（考虑 `sum_plus_acc` 。）此外，有些情况下，实现尾递归函数需要进行预处理或后处理以反转列表。在小到中等大小的列表上，反转列表的开销（无论是时间还是为反转列表分配内存）可能会使尾递归版本的时间效率较低。这里的“小”和“大”是什么意思？很难说，但根据 List 模块的[标准库文档](https://ocaml.org/manual/5.2/api/List.html)，也许 10,000 是一个很好的估计。

```ocaml
(** [from i j l] is the list containing the integers from [i] to [j],
    inclusive, followed by the list [l].
    Example:  [from 1 3 [0] = [1; 2; 3; 0]] *)
let rec from i j l = if i > j then l else from i (j - 1) (j :: l)

(** [i -- j] is the list containing the integers from [i] to [j], inclusive. *)
let ( -- ) i j = from i j []

let long_list = 0 -- 1_000_000
```

```ocaml
val from : int -> int -> int list -> int list = <fun>
```

```ocaml
val ( -- ) : int -> int -> int list = <fun>
```

```ocaml
val long_list : int list =
  [0; 1; 2; 3; 4; 5; 6; 7; 8; 9; 10; 11; 12; 13; 14; 15; 16; 17; 18; 19; 20;
   21; 22; 23; 24; 25; 26; 27; 28; 29; 30; 31; 32; 33; 34; 35; 36; 37; 38;
   39; 40; 41; 42; 43; 44; 45; 46; 47; 48; 49; 50; 51; 52; 53; 54; 55; 56;
   57; 58; 59; 60; 61; 62; 63; 64; 65; 66; 67; 68; 69; 70; 71; 72; 73; 74;
   75; 76; 77; 78; 79; 80; 81; 82; 83; 84; 85; 86; 87; 88; 89; 90; 91; 92;
   93; 94; 95; 96; 97; 98; 99; 100; 101; 102; 103; 104; 105; 106; 107; 108;
   109; 110; 111; 112; 113; 114; 115; 116; 117; 118; 119; 120; 121; 122; 123;
   124; 125; 126; 127; 128; 129; 130; 131; 132; 133; 134; 135; 136; 137; 138;
   139; 140; 141; 142; 143; 144; 145; 146; 147; 148; 149; 150; 151; 152; 153;
   154; 155; 156; 157; 158; 159; 160; 161; 162; 163; 164; 165; 166; 167; 168;
   169; 170; 171; 172; 173; 174; 175; 176; 177; 178; 179; 180; 181; 182; 183;
   184; 185; 186; 187; 188; 189; 190; 191; 192; 193; 194; 195; 196; 197; 198;
   199; 200; 201; 202; 203; 204; 205; 206; 207; 208; 209; 210; 211; 212; 213;
   214; 215; 216; 217; 218; 219; 220; 221; 222; 223; 224; 225; 226; 227; 228;
   229; 230; 231; 232; 233; 234; 235; 236; 237; 238; 239; 240; 241; 242; 243;
   244; 245; 246; 247; 248; 249; 250; 251; 252; 253; 254; 255; 256; 257; 258;
   259; 260; 261; 262; 263; 264; 265; 266; 267; 268; 269; 270; 271; 272; 273;
   274; 275; 276; 277; 278; 279; 280; 281; 282; 283; 284; 285; 286; 287; 288;
   289; 290; 291; 292; 293; 294; 295; 296; 297; 298; ...]
```

值得研究 `--` 的定义，以确保您理解

- (i)它是如何工作的，以及
- (ii)为什么它是尾递归的。

将来您可能会决定再次创建这样的列表。与其记住这个定义的位置，然后将其复制到您的代码中，不如使用内置库函数轻松创建相同的列表的方法：

```ocaml
List.init 1_000_000 Fun.id
```

```ocaml
- : int list =
[0; 1; 2; 3; 4; 5; 6; 7; 8; 9; 10; 11; 12; 13; 14; 15; 16; 17; 18; 19; 20;
 21; 22; 23; 24; 25; 26; 27; 28; 29; 30; 31; 32; 33; 34; 35; 36; 37; 38; 39;
 40; 41; 42; 43; 44; 45; 46; 47; 48; 49; 50; 51; 52; 53; 54; 55; 56; 57; 58;
 59; 60; 61; 62; 63; 64; 65; 66; 67; 68; 69; 70; 71; 72; 73; 74; 75; 76; 77;
 78; 79; 80; 81; 82; 83; 84; 85; 86; 87; 88; 89; 90; 91; 92; 93; 94; 95; 96;
 97; 98; 99; 100; 101; 102; 103; 104; 105; 106; 107; 108; 109; 110; 111; 112;
 113; 114; 115; 116; 117; 118; 119; 120; 121; 122; 123; 124; 125; 126; 127;
 128; 129; 130; 131; 132; 133; 134; 135; 136; 137; 138; 139; 140; 141; 142;
 143; 144; 145; 146; 147; 148; 149; 150; 151; 152; 153; 154; 155; 156; 157;
 158; 159; 160; 161; 162; 163; 164; 165; 166; 167; 168; 169; 170; 171; 172;
 173; 174; 175; 176; 177; 178; 179; 180; 181; 182; 183; 184; 185; 186; 187;
 188; 189; 190; 191; 192; 193; 194; 195; 196; 197; 198; 199; 200; 201; 202;
 203; 204; 205; 206; 207; 208; 209; 210; 211; 212; 213; 214; 215; 216; 217;
 218; 219; 220; 221; 222; 223; 224; 225; 226; 227; 228; 229; 230; 231; 232;
 233; 234; 235; 236; 237; 238; 239; 240; 241; 242; 243; 244; 245; 246; 247;
 248; 249; 250; 251; 252; 253; 254; 255; 256; 257; 258; 259; 260; 261; 262;
 263; 264; 265; 266; 267; 268; 269; 270; 271; 272; 273; 274; 275; 276; 277;
 278; 279; 280; 281; 282; 283; 284; 285; 286; 287; 288; 289; 290; 291; 292;
 293; 294; 295; 296; 297; 298; ...]
```

表达式 `List.init len f` 创建列表 `[f 0; f 1; ...; f (len - 1)]` ，如果 `len` 大于 10,000，则以尾递归方式执行。函数 `Fun.id` 简单地是标识函数 `fun x -> x` 。
