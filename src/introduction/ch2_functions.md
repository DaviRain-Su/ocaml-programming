# 函数

由于 OCaml 是一种函数式语言，关于函数有很多内容需要涵盖。让我们开始吧。

> 方法和函数并不是相同的概念。方法是对象的一个组成部分，它隐含地具有一个接收者，通常可以通过类似 this 或 self 的关键字访问。OCaml 函数不是方法：它们不是对象的组成部分，也没有接收者。
>
> 有人可能会说所有的方法都是函数，但并非所有的函数都是方法。有些人甚至可能会对此提出异议，区分函数和过程。后者将是不返回任何有意义值的函数，比如在 Java 中的 void 返回类型或 Python 中的 None 返回值。
>
>
> 所以，如果您来自面向对象的背景，请注意术语。这里的一切严格来说都是一个函数，而不是一个方法。

## 函数定义

<iframe width="791" height="445" src="https://www.youtube.com/embed/vCxIlagS7kA" title="Named Functions | OCaml Programming | Chapter 2 Video 11" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

以下代码

```
let x = 42
```

在其中有一个表达式（ 42 ），但它本身不是一个表达式。相反，它是一个定义。定义将值绑定到名称，本例中值 42 被绑定到名称 x 。[OCaml 手册](https://ocaml.org/manual/modules.html)描述了定义（请参阅该页面上标题为“definition”的第三个主要分组），但该手册页面主要用于参考而非学习。定义不是表达式，表达式也不是定义——它们是不同的语法类别。

目前，让我们专注于一种特定类型的定义，即函数定义。非递归函数的定义如下：

```
let f x = ...
```

<iframe width="791" height="445" src="https://www.youtube.com/embed/_x82qitu2R8" title="Recursive Functions | OCaml Programming | Chapter 2 Video 12" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

递归函数的定义如下：

```
let rec f x = ...
```

区别只是 rec 关键字。也许有点令人惊讶的是，您必须明确添加一个关键字才能使函数递归，因为大多数语言默认情况下都是这样假设的。不过，OCaml 并不做出这种假设。（Scheme 语言族也是如此。）

最著名的递归函数之一是阶乘函数。在 OCaml 中，它可以写成如下形式：

```
(** [fact n] is [n!].
    Requires: [n >= 0]. *)
let rec fact n = if n = 0 then 1 else n * fact (n - 1)
```

```
val fact : int -> int = <fun>
```

我们在函数上方提供了一个规范注释，用于记录函数的前置条件（ Requires ）和后置条件（ is ）。

请注意，与许多语言一样，OCaml 整数并非“数学”整数，而是限制在固定位数上。[手册](https://ocaml.org/manual/5.2/values.html#sss:values:integer)规定（有符号）整数至少为 31 位，但它们可能更宽。随着架构的发展，这个大小也在增加。在当前的实现中，OCaml 整数为 63 位。因此，如果您对足够大的输入进行测试，您可能会开始看到奇怪的结果。问题在于机器算术，而不是 OCaml。（对于感兴趣的读者：为什么是 31 或 63 而不是 32 或 64？OCaml 垃圾收集器需要区分整数和指针。因此，这些的运行时表示会窃取一位来标记一个字是整数还是指针。）


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

- 由于 if 表达式可以在 then 分支中返回 1 ，根据 if 的类型规则，我们知道整个 if 表达式的类型为 int 。
- 由于 if 表达式的类型为 int ，函数的返回类型必须为 int 。
- 由于 y 与 0 使用相等运算符进行比较， y 必须是一个 int 。
- 由于 x 与另一个表达式使用 * 运算符相乘， x 必须是一个 int 。

如果我们出于某种原因想要写下这些类型，我们可以这样做：

```
let rec pow (x : int) (y : int) : int = ...
```

括号在我们为 x 和 y 编写类型注释时是必需的。通常我们会省略这些注释，因为让编译器推断它们会更简单。还有其他时候，您可能希望明确写下类型。一个特别有用的时机是当您从编译器那里得到一个您不理解的类型错误时。显式注释类型可以帮助调试这样的错误消息。


**语法**。函数定义的语法：

```
let rec f x1 x2 ... xn = e
```

f 是一个元变量，表示正在用作函数名的标识符。这些标识符必须以小写字母开头。有关小写标识符的其余规则可以在[手册](https://ocaml.org/manual/5.2/lex.html#lowercase-ident)中找到。名称 x1 到 xn 是表示参数标识符的元变量。这些遵循与函数标识符相同的规则。如果 f 是递归函数，则必须使用关键字 rec ；否则可以省略。

请注意，函数定义的语法实际上比 OCaml 实际允许的要简化。在接下来的几周中，我们将学习更多关于函数定义的增强语法。但是现在，这个简化版本将帮助我们集中精力。

可以使用 and 关键字来定义相互递归的函数：

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

t 和 u 是表示类型的元变量。类型 t -> u 是一个接受类型为 t 的输入并返回类型为 u 的输出的函数的类型。我们可以将 t1 -> t2 -> u 看作是一个接受两个输入的函数的类型，第一个输入类型为 t1 ，第二个输入类型为 t2 ，并返回类型为 u 的输出。类似地，对于一个接受 n 个参数的函数。

**动态语义**。函数定义没有动态语义。没有什么需要被评估。OCaml 只是记录名称 f 绑定到具有给定参数 x1..xn 和给定主体 e 的函数。只有在稍后应用函数时，才会有一些需要评估的内容。

**静态语义**。函数定义的静态语义：

- 对于非递归函数：如果假设 x1 : t1 和 x2 : t2 和…和 xn : tn ，我们可以得出 e : u 的结论，那么 f : t1 -> t2 -> ... -> tn -> u 。
- 对于递归函数：如果假设 x1 : t1 和 x2 : t2 和…和 xn : tn 和 f : t1 -> t2 -> ... -> tn -> u ，我们可以得出 e : u ，那么 f : t1 -> t2 -> ... -> tn -> u 。

注意递归函数的类型检查规则是假设函数标识符 f 具有特定类型，然后检查函数体在该假设下是否是良型的。这是因为 f 在函数体内部是可见的（就像参数在作用域内一样）。

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

我们已经知道我们可以拥有不绑定到名称的值。例如，整数 42 可以在顶层输入而不给它一个名称：

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

同样，OCaml 函数不必有名称；它们可以是匿名的。例如，这是一个将其输入递增的匿名函数： fun x -> x + 1 。这里， fun 是一个指示匿名函数的关键字， x 是参数， -> 将参数与函数体分隔开来。

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

匿名函数也被称为 lambda 表达式，这个术语来自λ演算，它是一种计算模型，类似于图灵机是一种计算模型。在λ演算中， fun x -> e 会被写成 lambda x.e ，其中 lambda 是希腊字母 λ 的英文名。 λ 表示一个匿名函数。

现在或许有点神秘，为什么我们要使用没有名称的函数。别担心；在课程的后面，我们会看到它们的好处，特别是当我们学习所谓的“高阶编程”时。特别是，我们经常会创建匿名函数并将它们作为输入传递给其他函数。

**句法**。

```
fun x1 ... xn -> e
```

**静态语义**

- 如果假设 x1 : t1 和 x2 : t2 和...和 xn : tn ，我们可以得出结论 e : u ，那么 fun x1 ... xn -> e : t1 -> t2 -> ... -> tn -> u 。

**动态语义学**。匿名函数已经是一个值。没有需要执行的计算。

## 函数应用

<iframe width="791" height="445" src="https://www.youtube.com/embed/fgCTDhXAYnQ" title="Function Application | OCaml Programming | Chapter 2 Video 10" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

这里我们介绍了一种相对简化的函数应用语法，与 OCaml 实际允许的语法相比。

**句法**

```
e0 e1 e2 ... en
```

第一个表达式 e0 是函数，并且它被应用于参数 e1 到 en 。请注意，不需要在参数周围加括号来表示函数应用，就像在 C 家族语言（包括 Java）中一样。

**静态语义**
- 如果 e0 : t1 -> ... -> tn -> u 和 e1 : t1 和...和 en : tn ，那么 e0 e1 ... en : u 。

**动态语义**

评估 e0 e1 ... en

- 将 e0 评估为一个函数。同时将参数表达式 e1 到 en 评估为值 v1 到 vn 。

  对于 e0 ，结果可能是一个匿名函数 fun x1 ... xn -> e 或一个名称 f 。在后一种情况下，我们需要找到 f 的定义，我们可以假设其形式为 let rec f x1 ... xn = e 。无论哪种方式，我们现在知道参数名称 x1 到 xn 和主体 e 。

- 将函数体 e 中的每个值 vi 替换为相应的参数名称 xi 。该替换会产生一个新表达式 e' 。
- 评估 e' 的值为 v ，这是评估 e0 e1 ... en 的结果。

如果您将这些评估规则与 let 表达式的规则进行比较，您会注意到它们都涉及替换。这并非偶然。事实上，在程序中任何 let x = e1 in e2 出现的地方，我们都可以用 (fun x -> e2) e1 替换它。它们在语法上有所不同，但在语义上是等价的。实质上， let 表达式只是匿名函数应用的语法糖。

## Pipeline

<iframe width="791" height="445" src="https://www.youtube.com/embed/arS9kEqCFEU" title="Application Operators | OCaml Programming | Chapter 2 Video 17" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 中有一个内置的中缀运算符用于函数应用，称为管道运算符，写作 |> 。想象它表示一个指向右侧的三角形。这个比喻是值从左到右通过管道发送。例如，假设我们有上面的增量函数 inc 以及一个将其输入平方的函数 square ：

```
let square x = x * x
```

```
val square : int -> int = <fun>
```

这里有两种等效的方法来求 6 的平方：

```
- : int = 36
```

```
- : int = 36
```

后者使用管道运算符将 5 发送到 inc 函数，然后将该结果发送到 square 函数。这是在 OCaml 中表达计算的一种不错的成语方式。前者的方式可能不那么优雅：它涉及编写额外的括号，并要求读者的眼睛在各处跳动，而不是从左到右线性移动。后者的方式在应用的函数数量增加时可以很好地扩展，而前者的方式则需要越来越多的括号。

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

由于 e1 |> e2 只是另一种写法 e2 e1 ，我们不需要说明 |> 的语义：它与函数应用相同。这两个程序是另一个例子，表达式在语法上不同但在语义上等效。


## 多态函数

<iframe width="791" height="445" src="https://www.youtube.com/embed/UWmxYBEKzN8" title="Polymorphic Functions | OCaml Programming | Chapter 2 Video 15" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

id函数是一个简单地返回其输入的函数：

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

'a 是一种类型变量：它代表一个未知类型，就像常规变量代表一个未知值一样。类型变量总是以单引号开头。常用的类型变量包括 'a ， 'b 和 'c ，OCaml 程序员通常用希腊语发音：alpha，beta 和 gamma。


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

由于您可以将 id 应用于许多类型的值，它是一个多态函数：它可以应用于许多（多）形式（形态）。


通过手动类型注释，可以为多态函数提供比编译器自动推断的类型更为严格的类型。例如：

```
let id_int (x : int) : int = x
```

```
val id_int : int -> int = <fun>
```

这与 id 的功能相同，只是有两个手动类型的注释。因为这些原因，我们不能像对待 id 一样将 id_int 应用于 bool ：

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

另一种写法 id_int 的方式是用 id 来表示：

```
let id_int' : int -> int = id
```

```
val id_int' : int -> int = <fun>
```

实际上，我们取了一个类型为 'a -> 'a 的值，并将其绑定到一个手动指定为 int -> int 的名称上。你可能会问，为什么会起作用呢？毕竟，它们并不是相同的类型。


一种思考这个问题的方式是从行为角度来看。 id_int 的类型指定了其行为的一个方面：给定一个 int 作为输入，它承诺会产生一个 int 作为输出。事实证明， id 也做出了同样的承诺：给定一个 int 作为输入，它也会返回一个 int 作为输出。现在 id 也做出了许多其他承诺，比如：给定一个 bool 作为输入，它将返回一个 bool 作为输出。因此，通过将 id 绑定到一个更为严格的类型 int -> int ，我们已经丢弃了所有这些额外的承诺，因为它们是无关紧要的。当然，这意味着丢失了信息，但至少不会违背承诺。当我们需要的是一个 int -> int 类型的函数时，使用一个 'a -> 'a 类型的函数总是安全的。


相反的情况并不成立。如果我们需要一个类型为 'a -> 'a 的函数，但却尝试使用一个类型为 int -> int 的函数，一旦有人传入另一种类型的输入，比如 bool ，我们就会陷入麻烦之中。为了避免这种麻烦，OCaml 对以下代码做了一些潜在的令人惊讶的处理：


```
let id' : 'a -> 'a = fun x -> x + 1
```

```
val id' : int -> int = <fun>
```

函数 id' 实际上是增量函数，而不是身份函数。因此，将其传递给 bool 或 string 或一些复杂的数据结构是不安全的；它可以安全操作的唯一数据是整数。因此，OCaml 将类型变量 'a 实例化为 int ，从而防止我们将 id' 应用于非整数：


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

这将引导我们思考另一种更机械化的方式来考虑所有这些与应用相关的问题。我们指的是函数应用到参数的相同概念：在评估应用 id 5 时，参数 x 将被实例化为值 5 。同样， id 类型中的 'a 在该应用中将被实例化为类型 int 。因此，如果我们写下


```
let id_int' : int -> int = id
```


```
val id_int' : int -> int = <fun>
```

事实上，我们正在使用类型 id 中的 'a 实例化类型 int 。就像没有办法“取消应用”函数一样，例如，给定 5 ，我们无法向后计算到 id 5 ，我们也无法取消应用该类型实例化并将 int 更改回 'a 。


为了准确起见，假设我们有一个 let 定义[或表达]：

```
let x = e [in e']
```

OCaml 推断 x 具有类型 t ，其中包括一些类型变量 'a ， 'b 等。然后我们可以实例化这些类型变量。我们可以通过将函数应用于揭示类型实例化应该是什么的参数（如 id 5 中所示）或通过类型注释（如 id_int' 中所示）等方式来实现。但我们必须在实例化时保持一致。例如，我们不能将类型为 'a -> 'b -> 'a 的函数实例化为类型 int -> 'b -> string ，因为 'a 的实例化在其出现的两个位置上不是相同的类型：


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

函数的类型和名称通常会让你对参数应该是什么有一个很好的想法。然而，对于有许多参数的函数（特别是相同类型的参数），给它们贴上标签可能会很有用。例如，你可能会猜到函数 String.sub 返回给定字符串的子字符串（你的猜测是正确的）。你可以输入 String.sub 来查找它的类型：

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

## 部分应用

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

函数 addx 接受一个整数 x 作为输入，并返回一个类型为 int -> int 的函数，该函数将 x 添加到传递给它的任何内容

addx 的类型是 int -> int -> int 。 add 的类型也是 int -> int -> int 。因此从它们的类型的角度来看，它们是相同的函数。但 addx 的形式暗示了一些有趣的东西：我们可以将它应用于单个参数。

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

原来 add 也可以这样做：

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

我们刚刚做的是部分应用：我们将函数 add 部分应用于一个参数，尽管你通常会认为它是一个多参数函数。这样做是因为以下三个函数在语法上不同但在语义上是等价的。也就是说，它们是表达相同计算的不同方式：

```
let add x y = x + y
let add x = fun y -> x + y
let add = fun x -> (fun y -> x + y)
```

所以 add 实际上是一个接受参数 x 并返回函数 (fun y -> x + y) 的函数。这让我们得出一个深刻的真理...
