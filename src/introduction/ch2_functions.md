# 函数

由于 OCaml 是一种函数式语言，关于函数有很多内容需要涵盖。让我们开始吧。

> 方法和函数并不是相同的概念。方法是对象的一个组成部分，它隐含地具有一个接收者，通常可以通过类似 `this` 或 `self` 的关键字访问。OCaml 函数不是方法：它们不是对象的组成部分，也没有接收者。
>
> 有人可能会说所有的方法都是函数，但并非所有的函数都是方法。有些人甚至可能会对此提出异议，区分函数和过程。后者将是不返回任何有意义值的函数，比如在 Java 中的 `void` 返回类型或 Python 中的 `None` 返回值。
>
>
> 所以，如果您来自面向对象的背景，请注意术语。这里的一切严格来说都是一个函数，而不是一个方法。

## 函数定义

<iframe width="791" height="445" src="https://www.youtube.com/embed/vCxIlagS7kA" title="Named Functions | OCaml Programming | Chapter 2 Video 11" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

以下代码

```
let x = 42
```

在其中有一个表达式（ `42` ），但它本身不是一个表达式。相反，它是一个定义。定义将值绑定到名称，本例中值 `42` 被绑定到名称 `x` 。[OCaml 手册](https://ocaml.org/manual/modules.html)描述了定义（请参阅该页面上标题为“definition”的第三个主要分组），但该手册页面主要用于参考而非学习。定义不是表达式，表达式也不是定义——它们是不同的语法类别。

目前，让我们专注于一种特定类型的定义，即函数定义。非递归函数的定义如下：

```
let f x = ...
```

<iframe width="791" height="445" src="https://www.youtube.com/embed/_x82qitu2R8" title="Recursive Functions | OCaml Programming | Chapter 2 Video 12" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

递归函数的定义如下：

```
let rec f x = ...
```

区别只是 `rec` 关键字。也许有点令人惊讶的是，您必须明确添加一个关键字才能使函数递归，因为大多数语言默认情况下都是这样假设的。不过，OCaml 并不做出这种假设。（Scheme 语言族也是如此。）

最著名的递归函数之一是阶乘函数。在 OCaml 中，它可以写成如下形式：

```
(** [fact n] is [n!].
    Requires: [n >= 0]. *)
let rec fact n = if n = 0 then 1 else n * fact (n - 1)
```

```
val fact : int -> int = <fun>
```

我们在函数上方提供了一个规范注释，用于记录函数的前置条件（ `Requires` ）和后置条件（ `is` ）。

请注意，与许多语言一样，OCaml 整数并非“数学”整数，而是限制在固定位数上。[手册](https://ocaml.org/manual/5.2/values.html#sss:values:integer)规定（有符号）整数至少为 `31` 位，但它们可能更宽。随着架构的发展，这个大小也在增加。在当前的实现中，OCaml 整数为 `63` 位。因此，如果您对足够大的输入进行测试，您可能会开始看到奇怪的结果。问题在于机器算术，而不是 OCaml。（对于感兴趣的读者：为什么是 `31` 或 `63` 而不是 `32` 或 `64`？OCaml 垃圾收集器需要区分整数和指针。因此，这些的运行时表示会窃取一位来标记一个字是整数还是指针。）


这是另一个递归函数：

```
(** [pow x y] is [x] to the power of [y].
     Requires: [y >= 0]. *)
let rec pow x y = if y = 0 then 1 else x * pow x (y - 1)
```

```
val pow : int -> int -> int = <fun>
```

注意我们在这两个函数中都没有写任何类型：OCaml 编译器会自动为我们推断类型。编译器通过算法解决了这个类型推断问题，但我们也可以自己做。这就像一个谜团，可以通过我们的推理能力来解决：

- 由于 `if` 表达式可以在 `then` 分支中返回 `1` ，根据 `if` 的类型规则，我们知道整个 `if` 表达式的类型为 `int` 。
- 由于 `if` 表达式的类型为 `int` ，函数的返回类型必须为 `int` 。
- 由于 `y` 与 `0` 使用相等运算符进行比较， `y` 必须是一个 `int` 。
- 由于 `x` 与另一个表达式使用 `*` 运算符相乘， `x` 必须是一个 `int` 。

如果我们出于某种原因想要写下这些类型，我们可以这样做：

```
let rec pow (x : int) (y : int) : int = ...
```

括号在我们为 `x` 和 `y` 编写类型注释时是必需的。通常我们会省略这些注释，因为让编译器推断它们会更简单。还有其他时候，您可能希望明确写下类型。一个特别有用的时机是当您从编译器那里得到一个您不理解的类型错误时。显式注释类型可以帮助调试这样的错误消息。


**语法**。函数定义的语法：

```
let rec f x1 x2 ... xn = e
```

`f` 是一个元变量，表示正在用作函数名的标识符。这些标识符必须以小写字母开头。有关小写标识符的其余规则可以在[手册](https://ocaml.org/manual/5.2/lex.html#lowercase-ident)中找到。名称 `x1` 到 `xn` 是表示参数标识符的元变量。这些遵循与函数标识符相同的规则。如果 `f` 是递归函数，则必须使用关键字 `rec` ；否则可以省略。

请注意，函数定义的语法实际上比 OCaml 实际允许的要简化。在接下来的几周中，我们将学习更多关于函数定义的增强语法。但是现在，这个简化版本将帮助我们集中精力。

可以使用 `and` 关键字来定义相互递归的函数：

```
let rec f x1 ... xn = e1
and g y1 ... yn = e2
```

例如：

```
(** [even n] is whether [n] is even.
    Requires: [n >= 0]. *)
let rec even n =
  n = 0 || odd (n - 1)

(** [odd n] is whether [n] is odd.
    Requires: [n >= 0]. *)
and odd n =
  n <> 0 && even (n - 1);;
```

```
val even : int -> bool = <fun>
val odd : int -> bool = <fun>
```

<iframe width="791" height="445" src="https://www.youtube.com/embed/W0rO84YXIXo" title="Function Types | OCaml Programming | Chapter 2 Video 13" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

函数类型的语法是：

```
t -> u
t1 -> t2 -> u
t1 -> ... -> tn -> u
```

`t` 和 `u` 是表示类型的元变量。类型 `t -> u` 是一个接受类型为 `t` 的输入并返回类型为 `u` 的输出的函数的类型。我们可以将 `t1 -> t2 -> u` 看作是一个接受两个输入的函数的类型，第一个输入类型为 `t1` ，第二个输入类型为 `t2` ，并返回类型为 `u` 的输出。类似地，对于一个接受 `n` 个参数的函数。

**动态语义**。函数定义没有动态语义。没有什么需要被求值。OCaml 只是记录名称 `f` 绑定到具有给定参数 `x1..xn` 和给定主体 `e` 的函数。只有在稍后应用函数时，才会有一些需要求值的内容。

**静态语义**。函数定义的静态语义：

- 对于非递归函数：如果假设 `x1 : t1` 和 `x2 : t2` 和…和 `xn : tn` ，我们可以得出 `e : u` 的结论，那么 `f : t1 -> t2 -> ... -> tn -> u` 。
- 对于递归函数：如果假设 `x1 : t1` 和 `x2 : t2` 和…和 `xn : tn` 和 `f : t1 -> t2 -> ... -> tn -> u` ，我们可以得出`e : u` ，那么 `f : t1 -> t2 -> ... -> tn -> u` 。

注意递归函数的类型检查规则是假设函数标识符 `f` 具有特定类型，然后检查函数体在该假设下是否是良型的。这是因为 `f` 在函数体内部是可见的（就像参数在作用域内一样）。

> 要理解 OCaml 中递归函数的类型检查规则，首先需要理解 **类型推断** 和 **环境** 的概念。类型推断是指编译器在没有显式类型标注的情况下自动推导出表达式类型的过程。环境则是一个存储变量及其类型信息的映射表。
>
>在 OCaml 中，递归函数的类型检查遵循以下规则：
>
>1. **假设函数标识符具有特定类型**:  在检查递归函数的函数体时，OCaml 编译器会首先假设函数标识符 (例如, `f`) 具有程序员期望的类型。 这类似于在函数体内部，函数名 `f` 就如同一个已经定义了类型的变量，可以被函数体内部的代码使用。
>
>2. **检查函数体在假设下是否良型**:  接下来，编译器会根据环境中已有的类型信息和函数标识符的假设类型，来检查函数体中所有表达式的类型是否一致。 如果类型检查过程中发现任何类型错误，编译器就会报错。
>
>3. **函数标识符在函数体内部可见**:  递归函数的定义允许函数体内部的代码调用函数自身。 这意味着在函数体的环境中，函数标识符 `f` 是可见的，并且它的类型是之前假设的类型。 因此，编译器可以像检查普通变量一样检查递归函数对自身的调用。
>
>总而言之，OCaml 中递归函数的类型检查规则可以概括为： **假设函数类型，检查函数体，函数名可见**。 这种规则使得编译器能够在类型安全的前提下处理递归函数的定义和调用。

## 匿名函数

<iframe width="791" height="445" src="https://www.youtube.com/embed/JwoIIrj0bcM" title="Anonymous Functions | OCaml Programming | Chapter 2 Video 8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

我们已经知道我们可以拥有不绑定到名称的值。例如，整数 `42` 可以在顶层输入而不给它一个名称：

```
42
```

```
- : int = 42
```

或者我们可以将其绑定到一个名称：

```
let x = 42
```

```
val x : int = 42
```

同样，OCaml 函数不必有名称；它们可以是匿名的。例如，这是一个将其输入递增的匿名函数： `fun x -> x + 1` 。这里， `fun` 是一个指示匿名函数的关键字， `x` 是参数， `->` 将参数与函数体分隔开来。

我们现在有两种方式可以编写一个增量函数：

```
let inc x = x + 1
let inc = fun x -> x + 1
```

```
val inc : int -> int = <fun>
```

```
val inc : int -> int = <fun>
```

<iframe width="791" height="445" src="https://www.youtube.com/embed/zHHCD7MOjmw" title="Lambdas | OCaml Programming | Chapter 2 Video 9" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

匿名函数也被称为 `lambda` 表达式，这个术语来自`λ`演算，它是一种计算模型，类似于图灵机是一种计算模型。在`λ`演算中， `fun x -> e` 会被写成 `lambda x.e` ，其中 `lambda` 是希腊字母 `λ` 的英文名。 `λ` 表示一个匿名函数。

现在或许有点神秘，为什么我们要使用没有名称的函数。别担心；在课程的后面，我们会看到它们的好处，特别是当我们学习所谓的“高阶编程”时。特别是，我们经常会创建匿名函数并将它们作为输入传递给其他函数。

**句法**。

```
fun x1 ... xn -> e
```

**静态语义**

- 如果假设 `x1 : t1` 和 `x2 : t2` 和`...`和 `xn : tn` ，我们可以得出结论 `e : u` ，那么 `fun x1 ... xn -> e : t1 -> t2 -> ... -> tn -> u` 。

**动态语义学**。匿名函数已经是一个值。没有需要执行的计算。

## 函数应用

<iframe width="791" height="445" src="https://www.youtube.com/embed/fgCTDhXAYnQ" title="Function Application | OCaml Programming | Chapter 2 Video 10" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

这里我们介绍了一种相对简化的函数应用语法，与 OCaml 实际允许的语法相比。

**句法**

```
e0 e1 e2 ... en
```

第一个表达式 `e0` 是函数，并且它被应用于参数 `e1` 到 `en` 。请注意，不需要在参数周围加括号来表示函数应用，就像在 C 家族语言（包括 Java）中一样。

**静态语义**
- 如果 `e0 : t1 -> ... -> tn -> u` 和 `e1 : t1` 和`...`和 `en : tn` ，那么 `e0 e1 ... en : u` 。

**动态语义**

求值 `e0 e1 ... en`

- 将 `e0` 求值为一个函数。同时将参数表达式 `e1` 到 `en` 求值为值 `v1` 到 `vn` 。

  对于 `e0` ，结果可能是一个匿名函数 `fun x1 ... xn -> e` 或一个名称 `f` 。在后一种情况下，我们需要找到 `f` 的定义，我们可以假设其形式为 `let rec f x1 ... xn = e` 。无论哪种方式，我们现在知道参数名称 `x1` 到 `xn` 和主体 `e` 。

- 将函数体 `e` 中的每个值 `vi` 替换为相应的参数名称 `xi` 。该替换会产生一个新表达式 `e'` 。
- 求值 `e'` 的值为 `v` ，这是求值 `e0 e1 ... en` 的结果。

如果您将这些求值规则与 `let` 表达式的规则进行比较，您会注意到它们都涉及替换。这并非偶然。事实上，在程序中任何 `let x = e1 in e2` 出现的地方，我们都可以用 `(fun x -> e2) e1` 替换它。它们在语法上有所不同，但在语义上是等价的。实质上， `let` 表达式只是匿名函数应用的语法糖。

## Pipeline

<iframe width="791" height="445" src="https://www.youtube.com/embed/arS9kEqCFEU" title="Application Operators | OCaml Programming | Chapter 2 Video 17" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 中有一个内置的中缀运算符用于函数应用，称为管道运算符，写作 `|>` 。想象它表示一个指向右侧的三角形。这个比喻是值从左到右通过管道发送。例如，假设我们有上面的增量函数 `inc` 以及一个将其输入平方的函数 `square` ：

```
let square x = x * x
```

```
val square : int -> int = <fun>
```

这里有两种等效的方法来求 `6` 的平方：

```
- : int = 36
```

```
- : int = 36
```

后者使用管道运算符将 `5` 发送到 `inc` 函数，然后将该结果发送到 `square` 函数。这是在 OCaml 中表达计算的一种不错的成语方式。前者的方式可能不那么优雅：它涉及编写额外的括号，并要求读者的眼睛在各处跳动，而不是从左到右线性移动。后者的方式在应用的函数数量增加时可以很好地扩展，而前者的方式则需要越来越多的括号。

```
5 |> inc |> square |> inc |> inc |> square;;
square (inc (inc (square (inc 5))));;
```

```
- : int = 1444
```

```
- : int = 1444
```

一开始可能会感觉有点奇怪，但下次在编写一长串函数应用时，尝试在自己的代码中使用管道操作符。

由于 `e1 |> e2` 只是另一种写法 `e2 e1` ，我们不需要说明 `|>` 的语义：它与函数应用相同。这两个程序是另一个例子，表达式在语法上不同但在语义上等效。


## 多态函数

<iframe width="791" height="445" src="https://www.youtube.com/embed/UWmxYBEKzN8" title="Polymorphic Functions | OCaml Programming | Chapter 2 Video 15" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

`id`函数是一个简单地返回其输入的函数：

```
let id x = x
```

```
val id : 'a -> 'a = <fun>
```

或者等效地作为一个匿名函数:

```
let id = fun x -> x
```

```
val id : 'a -> 'a = <fun>
```

`'a` 是一种类型变量：它代表一个未知类型，就像常规变量代表一个未知值一样。类型变量总是以单引号开头。常用的类型变量包括 `'a` ， `'b` 和 `'c` ，OCaml 程序员通常用希腊语发音：`alpha`，`beta` 和 `gamma`。


我们可以将恒等函数应用于任何类型的值：

```
id 42;;
id true;;
id "bigred";;
```

```
- : int = 42
```

```
- : bool = true
```

```
- : string = "bigred"
```

由于您可以将 `id` 应用于许多类型的值，它是一个多态函数：它可以应用于许多（多）形式（形态）。


通过手动类型注释，可以为多态函数提供比编译器自动推断的类型更为严格的类型。例如：

```
let id_int (x : int) : int = x
```

```
val id_int : int -> int = <fun>
```

这与 `id` 的功能相同，只是有两个手动类型的注释。因为这些原因，我们不能像对待 `id` 一样将 `id_int` 应用于 `bool` ：

```
id_int true
```

```
File "[14]", line 1, characters 7-11:
1 | id_int true
           ^^^^
Error: This expression has type bool but an expression was expected of type
         int
```

另一种写法 `id_int` 的方式是用 `id` 来表示：

```
let id_int' : int -> int = id
```

```
val id_int' : int -> int = <fun>
```

实际上，我们取了一个类型为 `'a -> 'a` 的值，并将其绑定到一个手动指定为 `int -> int` 的名称上。你可能会问，为什么会起作用呢？毕竟，它们并不是相同的类型。


一种思考这个问题的方式是从行为角度来看。 `id_int` 的类型指定了其行为的一个方面：给定一个 `int` 作为输入，它承诺会产生一个 `int` 作为输出。事实证明， `id` 也做出了同样的承诺：给定一个 `int` 作为输入，它也会返回一个 `int` 作为输出。现在 `id` 也做出了许多其他承诺，比如：给定一个 `bool` 作为输入，它将返回一个 `bool` 作为输出。因此，通过将 `id` 绑定到一个更为严格的类型 `int -> int` ，我们已经丢弃了所有这些额外的承诺，因为它们是无关紧要的。当然，这意味着丢失了信息，但至少不会违背承诺。当我们需要的是一个 `int -> int` 类型的函数时，使用一个 `'a -> 'a` 类型的函数总是安全的。


相反的情况并不成立。如果我们需要一个类型为 `'a -> 'a` 的函数，但却尝试使用一个类型为 `int -> int` 的函数，一旦有人传入另一种类型的输入，比如 `bool` ，我们就会陷入麻烦之中。为了避免这种麻烦，OCaml 对以下代码做了一些潜在的令人惊讶的处理：


```
let id' : 'a -> 'a = fun x -> x + 1
```

```
val id' : int -> int = <fun>
```

函数 `id'` 实际上是增量函数，而不是身份函数。因此，将其传递给 `bool` 或 `string` 或一些复杂的数据结构是不安全的；它可以安全操作的唯一数据是整数。因此，OCaml 将类型变量 `'a` 实例化为 `int` ，从而防止我们将 `id'` 应用于非整数：


```
id' true
```


```
File "[17]", line 1, characters 4-8:
1 | id' true
        ^^^^
Error: This expression has type bool but an expression was expected of type
         int
```

这将引导我们思考另一种更机械化的方式来考虑所有这些与应用相关的问题。我们指的是函数应用到参数的相同概念：在求值应用 `id 5` 时，参数 `x` 将被实例化为值 `5` 。同样， `id` 类型中的 `'a` 在该应用中将被实例化为类型 `int` 。因此，如果我们写下


```
let id_int' : int -> int = id
```

```
val id_int' : int -> int = <fun>
```

事实上，我们正在使用类型 `id` 中的 `'a` 实例化类型 `int` 。就像没有办法“取消应用”函数一样，例如，给定 `5` ，我们无法向后计算到 `id 5` ，我们也无法取消应用该类型实例化并将 `int` 更改回 `'a` 。

为了准确起见，假设我们有一个 `let` 定义[或表达]：

```
let x = e [in e']
```

OCaml 推断 `x` 具有类型 `t` ，其中包括一些类型变量 `'a` ， `'b` 等。然后我们可以实例化这些类型变量。我们可以通过将函数应用于揭示类型实例化应该是什么的参数（如 `id 5` 中所示）或通过类型注释（如 `id_int'` 中所示）等方式来实现。但我们必须在实例化时保持一致。例如，我们不能将类型为 `'a -> 'b -> 'a` 的函数实例化为类型 `int -> 'b -> string` ，因为 `'a` 的实例化在其出现的两个位置上不是相同的类型：

```
let first x y = x;;
let first_int : int -> 'b -> int = first;;
let bad_first : int -> 'b -> string = first;;
```

```
val first : 'a -> 'b -> 'a = <fun>
```

```
val first_int : int -> 'b -> int = <fun>
```

```
File "[19]", line 3, characters 38-43:
3 | let bad_first : int -> 'b -> string = first;;
                                          ^^^^^
Error: This expression has type int -> 'b -> int
       but an expression was expected of type int -> 'b -> string
       Type int is not compatible with type string
```

## 标记和可选参数

函数的类型和名称通常会让你对参数应该是什么有一个很好的想法。然而，对于有许多参数的函数（特别是相同类型的参数），给它们贴上标签可能会很有用。例如，你可能会猜到函数 `String.sub` 返回给定字符串的子字符串（你的猜测是正确的）。你可以输入 `String.sub` 来查找它的类型：

```
String.sub;;
```


```
- : string -> int -> int -> string = <fun>
```

但是从类型上并不清楚如何使用它，你被迫查阅文档。


OCaml 支持函数的标记参数。您可以使用以下语法声明这种类型的函数：

```
let f ~name1:arg1 ~name2:arg2 = arg1 + arg2;;
```

```
val f : name1:int -> name2:int -> int = <fun>
```

此函数可以通过以任意顺序传递带标签的参数来调用：

```
f ~name2:3 ~name1:4
```

参数的标签通常与它们的变量名称相同。OCaml 为这种情况提供了一种简写。以下两者是等价的：

```
let f ~name1:name1 ~name2:name2 = name1 + name2
let f ~name1 ~name2 = name1 + name2
```

使用带标签的参数在很大程度上是一种品味问题。它们传达了额外的信息，但也可能使类型变得混乱。

编写带有标签参数和显式类型注释的语法是：

```
let f ~name1:(arg1 : int) ~name2:(arg2 : int) = arg1 + arg2
```

也可以将一些参数设置为可选的。在没有可选参数的情况下调用时，将提供默认值。要声明这样一个函数，请使用以下语法：

```
let f ?name:(arg1=8) arg2 = arg1 + arg2
```

```
val f : ?name:int -> int -> int = <fun>
```

您可以选择带参数或不带参数地调用一个函数：

```
f ~name:2 7
```

```
- : int = 9
```

```
f 7
```

```
- : int = 15
```

## 偏应用

<iframe width="791" height="445" src="https://www.youtube.com/embed/85xVK0wmDTw" title="Partial Application | OCaml Programming | Chapter 2 Video 14" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

我们可以将加法函数定义如下：

```
let add x y = x + y
```

```
val add : int -> int -> int = <fun>
```

这是一个相当类似的函数：

```
let addx x = fun y -> x + y
```

```
val addx : int -> int -> int = <fun>
```

函数 `addx` 接受一个整数 `x` 作为输入，并返回一个类型为 `int -> int` 的函数，该函数将 `x` 添加到传递给它的任何内容

`addx` 的类型是 `int -> int -> int` 。`add` 的类型也是 `int -> int -> int` 。因此从它们的类型的角度来看，它们是相同的函数。但 `addx` 的形式暗示了一些有趣的东西：我们可以将它应用于单个参数。

```
let add5 = addx 5
```

```
val add5 : int -> int = <fun>
```

```
add5 2
```

```
- : int = 7
```

原来 `add` 也可以这样做：

```
let add5 = add 5
```

```
val add5 : int -> int = <fun>
```

```
add5 2;;
```

```
- : int = 7
```

我们刚刚做的是偏应用：我们将函数 `add` 偏应用于一个参数，尽管你通常会认为它是一个多参数函数。这样做是因为以下三个函数在语法上不同但在语义上是等价的。也就是说，它们是表达相同计算的不同方式：

```
let add x y = x + y
let add x = fun y -> x + y
let add = fun x -> (fun y -> x + y)
```

所以 `add` 实际上是一个接受参数 `x` 并返回函数 `(fun y -> x + y)` 的函数。这让我们得出一个深刻的真理...

## 函数结合性

你准备好接受真相了吗？深呼吸。开始了…

每个 OCaml 函数都只接受一个参数。

为什么？考虑 `add` ：虽然我们可以将其写成 `let add x y = x + y` ，但我们知道这在语义上等同于 `let add = fun x -> (fun y -> x + y)` 。而且一般来说，

```
let f x1 x2 ... xn = e
```

语义上等同于

```
let f =
  fun x1 ->
    (fun x2 ->
       (...
          (fun xn -> e)...))
```

所以，即使你认为 `f` 是一个接受 `n` 个参数的函数，实际上它是一个接受 `1` 个参数并返回一个函数的函数。

这种函数的类型

```
t1 -> t2 -> t3 -> t4
```

确实意味着与

```
t1 -> (t2 -> (t3 -> t4))
```

换句话说，函数类型是右结合的：函数类型周围有隐式括号，从右到左。这里的直觉是，函数接受一个参数并返回一个期望其余参数的新函数。

函数应用，另一方面，是左结合的：函数应用周围有隐式括号，从左到右。所以

```
e1 e2 e3 e4
```

确实意味着与

```
((e1 e2) e3) e4
```

这里的直觉是，最左边的表达式将右侧的下一个表达式作为其单个参数。

## 运算符作为函数

<iframe width="791" height="445" src="https://www.youtube.com/embed/OVKOx8UiwxM" title="Operators As Functions | OCaml Programming | Chapter 2 Video 16" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

加法运算符 `+` 具有类型 `int -> int -> int` 。通常以中缀形式书写，例如， `3 + 4` 。通过在其周围加括号，我们可以将其变为前缀运算符：

```
( + )
```

```
- : int -> int -> int = <fun>
```

```
( + ) 3 4;;
```

```
- : int = 7
```

```
let add3 = ( + ) 3
```

```
val add3 : int -> int = <fun>
```

```
add3 2
```

```
- : int = 5
```

相同的技术适用于任何内置运算符。

通常空格是不必要的。我们可以写 `(+)` 或 `( + )` ，但最好包括它们。要注意乘法，必须写成 `( * )` ，因为 `(*)` 会被解析为开始注释。

我们甚至可以定义自己的新中缀运算符，例如：

```
let ( ^^ ) x y = max x y
```

现在 `2 ^^ 3` 求值为 `3` 。

可以用标点符号创建中缀运算符的规则并不一定直观。这类运算符的解析优先级也不一定直观。因此，在使用时要小心。

## 尾递归

考虑以下看似无趣的函数，它从 `1` 数到 `n` ：

```
(** [count n] is [n], computed by adding 1 to itself [n] times.  That is,
    this function counts up from 1 to [n]. *)
let rec count n =
  if n = 0 then 0 else 1 + count (n - 1)
```

```
val count : int -> int = <fun>
```

数到 `10` 没有问题：

```
count 10
```

```
- : int = 10
```

计数到 `100,000` 也不是问题：


```
count 100_000
```

```
- : int = 100000
```

但是尝试数到 `1,000,000`，你会收到以下错误：

```
Stack overflow during evaluation (looping recursion?).
```


这里发生了什么事？

**调用堆栈**。问题在于调用堆栈具有有限的大小。您可能在入门编程课程中学到，大多数编程语言使用堆栈来实现函数调用。该堆栈包含每个已启动但尚未完成的函数调用的一个元素。每个元素存储诸如本地变量的值以及当前正在执行的函数中的指令等信息。当一个函数体的求值调用另一个函数时，调用堆栈上会推送一个新元素，并在被调用函数完成时弹出。

**栈的大小通常受操作系统限制**。因此，如果栈空间耗尽，就无法再进行另一个函数调用。通常情况下，这种情况不会发生，因为在返回之前没有理由连续进行那么多次函数调用。在发生这种情况的情况下，操作系统有充分理由让程序停止运行：可能正在占用整台计算机上所有可用内存，从而影响同一台计算机上运行的其他程序。 `count` 函数不太可能做到这一点，但这个函数可能会：

```
let rec count_forever n = 1 + count_forever n
```

```
val count_forever : 'a -> int = <fun>
```

因此，出于安全考虑，操作系统限制了调用堆栈的大小。这意味着最终 `count` 将在足够大的输入上耗尽堆栈空间。请注意，这个选择实际上与编程语言无关。因此，这个问题在除了 OCaml 之外的其他语言中也会出现，包括 Python 和 Java。你在那些语言中可能没有看到这个问题的表现，因为你可能从未在那些语言中编写过如此多的递归函数。


**尾递归**。关于这个问题，盖·斯蒂尔（Guy Steele）在 1977 年关于 LISP 的一篇[论文](https://dl.acm.org/doi/pdf/10.1145/800179.810196)中描述了一个解决方案。这个解决方案，即**尾调用优化**，需要程序员和编译器之间的一些合作。程序员对函数进行一些重写，编译器会注意到并应用优化。让我们看看它是如何工作的。

假设一个递归函数 `f` 调用自身然后返回该递归调用的结果。我们的 `count` 函数不这样做：

```
let rec count n =
  if n = 0 then 0 else 1 + count (n - 1)
```

```
val count : int -> int = <fun>
```

相反，在递归调用 `count (n - 1)` 之后，还有计算任务未完成：计算机仍然需要将 `1` 添加到该调用的结果中。

但是作为程序员，我们可以重写 `count` 函数，使其在递归调用之后不需要进行任何额外的计算。诀窍在于创建一个带有额外参数的辅助函数：

```
let rec count_aux n acc =
  if n = 0 then acc else count_aux (n - 1) (acc + 1)

let count_tr n = count_aux n 0
```

```
val count_aux : int -> int -> int = <fun>
```

```
val count_tr : int -> int = <fun>
```

函数 `count_aux` 与我们原来的 `count` 几乎相同，但它添加了一个名为 `acc` 的额外参数，这是惯用语，代表“累加器”。其思想是，我们希望从函数返回的值会随着每次递归调用而在其中逐渐累积。现在，“剩余计算”——加 `1`——现在发生在递归调用之前而不是之后。当递归的基本情况最终到来时，函数现在返回 `acc` ，其中答案已经被累积


但是原始的基本情况 `0` 仍然需要在代码中存在。而且确实存在，作为传递给 `count_aux` 的 `acc` 的原始值。现在 `count_tr` （我们马上会解释为什么名称是“`tr`”）作为我们原始 `count` 的替代品。


到这一点，我们已经完成了程序员的责任，但很可能不清楚为什么要经历这样的努力。毕竟， `count_aux` 仍然会像 `count` 一样递归调用自身太多次，最终会导致堆栈溢出。

这就是编译器的责任所在。一个好的编译器（OCaml 编译器在这方面做得很好）可以注意到递归调用是否处于尾位置，这是一种技术性的说法，意思是“在它返回后没有更多的计算要做了”。对 `count_aux` 的递归调用处于尾位置；对 `count` 的递归调用则不是。以下是它们的对比：

```
let rec count n =
  if n = 0 then 0 else 1 + count (n - 1)

let rec count_aux n acc =
  if n = 0 then acc else count_aux (n - 1) (acc + 1)
```

这就是尾位置的重要性：尾位置的递归调用不需要新的堆栈帧。它可以直接重用现有的堆栈帧。这是因为在现有的堆栈帧中没有任何有用的东西了！没有剩余的计算需要进行，因此局部变量、下一条指令要执行的内容等都不再重要。再也不需要再次读取那些内存，因为该调用实际上已经完成。因此，编译器不会浪费空间来分配另一个堆栈帧，而是“回收”了先前帧使用的空间。

这是尾调用优化。即使在递归函数之外的情况下，如果调用函数的栈帧与被调用者适配，也可以应用尾调用优化。而且，这是一件大事。尾调用优化将栈空间需求从线性降低到常数。而 `count` 需要 `O(n)`个栈帧， `count_aux` 只需要 `O(1)`，因为相同的栈帧一遍又一遍地被每次递归调用重复使用。这意味着 `count_tr` 实际上可以计数到`1,000,000`。

```
count_tr 1_000_000
```

```
- : int = 1000000
```


最后，我们为什么将这个函数命名为 `count_tr` ？“`tr`”代表尾递归。尾递归函数是一个递归函数，其递归调用都处于尾位置。换句话说，这是一个函数（除非存在其他病态情况），不会耗尽堆栈。

**尾递归的重要性**。有时，初学函数式编程的人可能会过分关注它。如果你只关心编写函数的初稿，那么你可能不需要担心尾递归。如果需要的话，通过添加一个累加器参数，很容易将其改为尾递归。或者，也许你应该重新思考一下你设计函数的方式。以 `count` 为例：这有点愚蠢。但后来我们会看到一些不愚蠢的例子，比如遍历包含成千上万元素的列表。

编译器支持优化是很重要的。否则，作为程序员对代码所做的转换就毫无意义。事实上，大多数编译器都支持优化，至少作为一个选项。Java 是一个明显的例外。

**尾递归的配方**。简而言之，这是我们使一个函数成为尾递归的方法：
- 将该函数改为辅助函数。添加一个额外的参数：累加器，通常命名为 `acc` 。
- 编写一个调用辅助函数的新的“`main`”版本。它将原始基本情况的返回值作为累加器的初始值传递。
- 将辅助函数更改为在基本情况下返回累加器。
- 更改辅助函数的递归情况。现在它需要在递归调用之前对累加器参数进行额外的工作。这是唯一需要很多创造力的步骤。

一个例子：阶乘。让我们将这个阶乘函数转换为尾递归形式：

```
(** [fact n] is [n] factorial. *)
let rec fact n =
  if n = 0 then 1 else n * fact (n - 1)
```

```
val fact : int -> int = <fun>
```

首先，我们更改其名称并添加一个累加器参数：

```
let rec fact_aux n acc = ...
```

其次，我们编写一个新的“主”函数，该函数调用带有原始基本情况作为累加器的辅助函数：

```
let rec fact_tr n = fact_aux n 1
```

第三，我们将辅助函数更改为在基本情况下返回累加器：

```
if n = 0 then acc ...
```

最后，我们改变递归情况：

```
else fact_aux (n - 1) (n * acc)
```

将所有这些放在一起，我们有：

```
let rec fact_aux n acc =
  if n = 0 then acc else fact_aux (n - 1) (n * acc)

let fact_tr n = fact_aux n 1
```

```
val fact_aux : int -> int -> int = <fun>
```

```
val fact_tr : int -> int = <fun>
```

这是一个不错的练习，但也许并不值得。甚至在我们耗尽堆栈空间之前，计算就会因整数溢出而受到影响。

```
fact 50
```

```
- : int = -3258495067890909184
```

为了解决这个问题，我们转向 OCaml 的大整数库 [Zarith](https://antoinemine.github.io/Zarith/doc/latest/Z.html)。在这里，我们使用了一些 OCaml 的特性，这些特性超出了我们迄今为止见过的任何内容，但希望没有什么特别令人惊讶的地方。（如果您想跟随这段代码，首先在 OPAM 中安装 `Zarith opam install zarith` 。）

```
#require "zarith.top";;
```

```
let rec zfact_aux n acc =
  if Z.equal n Z.zero then acc else zfact_aux (Z.pred n) (Z.mul acc n);;

let zfact_tr n = zfact_aux n Z.one;;

zfact_tr (Z.of_int 50)
```

```
val zfact_aux : Z.t -> Z.t -> Z.t = <fun>
```

```
val zfact_tr : Z.t -> Z.t = <fun>
```

```
- : Z.t = 30414093201713378043612608166064768844377641568960512000000000000
```

如果你愿意，你可以使用该代码来计算 `zfact_tr 1_000_000` ，而不会出现堆栈溢出或整数溢出的问题，尽管这可能需要几分钟的时间。

关于模块的章节将详细解释我们上面使用的 OCaml 功能，但现在：

- `#require` 加载了库，该库提供了一个名为 `Z` 的模块。回想一下，
 是数学中用来表示整数的符号。

- `Z.n` 表示在 `Z` 内定义的名称 `n` 。
- 类型 `Z.t` 是大整数类型的库名称。
- 我们使用库值 `Z.equal` 进行相等比较， `Z.zero` 代表 `0`， `Z.pred` 代表前驱（即减 `1`）， `Z.mul` 代表乘法， `Z.one` 代表 `1`， `Z.of_int` 用于将原始整数转换为大整数。
