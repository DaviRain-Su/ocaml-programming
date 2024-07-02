# 表达式

OCaml 语法的主要部分是表达式。就像命令式语言中的程序主要由命令构建而成一样，函数式语言中的程序主要由表达式构建而成。表达式的例子包括 `2+2` 和 `increment 21` 。

[OCaml 手册](https://ocaml.org/manual/expr.html)对语言中所有表达式进行了完整定义。尽管该页面以相当神秘的概述开始，但如果您向下滚动，您将看到一些英文解释。现在不必担心研究该页面；只需知道它可供参考。

函数式语言中计算的主要任务是将表达式评估为一个值。值是一个表达式，对于它而言没有剩余的计算需要执行。因此，所有的值都是表达式，但并非所有的表达式都是值。值的示例包括 `2` ， `true` 和 `"yay!"` 。


[OCaml 手册](https://ocaml.org/manual/5.2/values.html)中也包含所有值的定义，尽管那一页主要用于参考而非学习。

有时候一个表达式可能无法求值为一个值。这种情况可能有两个原因：

- 表达式的评估引发了异常。
- 表达式的评估永远不会终止（例如，它进入了“无限循环”）。

## 原始类型和值

原始类型是内置和最基本的类型：整数、浮点数、字符、字符串和布尔值。它们将被识别为类似于其他编程语言中的原始类型。

类型 `int` ：整数。OCaml 整数通常写作： `1` ， `2` ，等等。常见运算符可用： `+` ， `-` ， `*` ， `/` ，和 `mod` 。后两者为整数除法和取模：

```
65 / 60
```

```
- : int = 1
```

```
65 mod 60
```

```
- : int = 5
```

```
65 / 0
```

```
Exception: Division_by_zero.
Raised by primitive operation at unknown location
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

OCaml 整数在现代平台上的范围从
 到
 。它们使用 64 位机器字来实现，这是 64 位处理器上寄存器的大小。但其中一个比特被 OCaml 实现“窃取”，导致 63 位表示。该比特在运行时用于区分整数和指针。对于需要真正的 64 位整数的应用程序，在标准库中有一个 [`Int64` 模块](https://ocaml.org/manual/5.2/api/Int64.html)。对于需要任意精度整数的应用程序，有一个单独的 [Zarith 库](https://github.com/ocaml/Zarith)。但对于大多数情况，内置的 `int` 类型足够并提供最佳性能。

类型 `float` ：浮点数。OCaml 浮点数是 IEEE 754 双精度浮点数。从语法上讲，它们必须始终包含一个点—例如， `3.14` 或 `3.0` 或甚至 `3.` 。最后一个是 `float` ；如果你将其写成 `3` ，那么它实际上是一个 `int` 。

```
3.
```

```
- : float = 3.
```

```
3
```

```
- : int = 3
```

OCaml 故意不支持运算符重载，浮点数的算术运算在其后加一个点来表示。例如，浮点数乘法写作 `*.` 而不是 `*` ：

```
3.14 *. 2.
```

```
- : float = 6.28
```

```
3.14 * 2.
```

```
File "[7]", line 1, characters 0-4:
1 | 3.14 * 2.
    ^^^^
Error: This expression has type float but an expression was expected of type
         int
```

OCaml 不会自动在 `int` 和 `float` 之间转换。如果您想要转换，有两个内置函数可用于此目的： `int_of_float` 和 `float_of_int` 。

```
3.14 *. (float_of_int 2)
```

```
- : float = 6.28
```

与任何语言一样，浮点表示是近似的。这可能导致舍入误差。

```
0.1 +. 0.2
```

```
- : float = 0.300000000000000044
```

相同的行为也可以在 Python 和 Java 中观察到。如果您以前没有遇到过这种现象，这里有一个关于[浮点表示的基本指南](https://floating-point-gui.de/basic/)，您可能会喜欢阅读。


类型 `bool` ：布尔值。布尔值用 `true` 和 `false` 表示。通常的短路逻辑与 `&&` 和或 `||` 运算符可用。

类型 `char` ：字符。字符用单引号写成，如 `'a'` ， `'b'` 和 `'c'` 。它们以字节形式表示——即 8 位整数——在 ISO 8859-1“Latin-1”编码中。该范围内的字符的前半部分是标准 ASCII 字符。您可以使用 `char_of_int` 和 `int_of_char` 将字符转换为整数。

类型 `string` ：字符串。字符串是字符序列。它们用双引号表示，例如 `"abc"` 。字符串连接运算符是 `^` ：

```
"abc" ^ "def"
```

```
- : string = "abcdef"
```

面向对象的语言通常提供一个可重写的方法来将对象转换为字符串，比如 Java 中的 `toString()` 或 Python 中的 `__str__()` 。但大多数 OCaml 值不是对象，因此需要另一种方法来转换为字符串。对于三种原始类型，有内置函数： string_of_int ， `string_of_float` ， `string_of_bool` 。奇怪的是，没有 `string_of_char` ，但可以使用库函数 `String.make` 来实现相同的目标。

```
string_of_int 42
```

```
- : string = "42"
```

```
String.make 1 'z'
```

```
- : string = "z"
```

同样，对于相同的三种基本类型，如果可能的话，有内置函数可以从字符串转换： `int_of_string` ， `float_of_string` 和 `bool_of_string` 。

```
int_of_string "123"
```

```
- : int = 123
```

```
int_of_string "not an int"
```

```
Exception: Failure "int_of_string".
Raised by primitive operation at unknown location
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

在字符串中没有 `char_of_string` ，但可以通过基于 `0` 的索引访问字符串的各个字符。索引运算符用点号和方括号表示：

```
"abc".[0]
```

```
- : char = 'a'
```


```
"abc".[1]
```

```
- : char = 'b'
```

```
"abc".[3]
```

```
Exception: Invalid_argument "index out of bounds".
Raised by primitive operation at unknown location
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

## 更多运算符

我们已经讨论了大多数内置运算符，但还有一些您可以在 [OCaml 手册](https://ocaml.org/manual/expr.html#ss%3Aexpr-operators)中看到的。

OCaml 中有两个相等运算符 `=` 和 `==` ，对应的不相等运算符分别是 `<>` 和 `!=` 。运算符 `=` 和 `<>` 检查结构相等性，而 `==` 和 `!=` 检查物理相等性。在我们学习了 OCaml 的命令式特性之前，很难解释它们之间的区别。如果你现在感到好奇，可以查看 `Stdlib.(==)` 的[文档](https://ocaml.org/manual/5.2/api/Stdlib.html)。

> 立即开始训练自己使用 `=` ，而不使用 `==` 。如果您之前使用的是像 Java 这样的语言，其中 `==` 是通常的相等运算符，那么这可能会有些困难。

## 断言

表达式 `assert e` 评估 `e` 。如果结果是 `true` ，则不会发生任何其他情况，并且整个表达式评估为一个称为单位的特殊值。单位值写为 `()` ，其类型为 `unit` 。但如果结果是 `false` ，则会引发异常。

测试函数 `f` 的一种方法是编写一系列类似以下的断言：

```
let () = assert (f input1 = output1)
let () = assert (f input2 = output2)
let () = assert (f input3 = output3)
```

那些声称 `f input1` 应该是 `output1` 等等的人。这些中的 `let () =` ... 部分用于处理每个断言返回的单位值。

## If表达式

<iframe width="791" height="445" src="https://www.youtube.com/embed/XJ6QPtlPD7s" title="If Expressions | OCaml Programming | Chapter 2 Video 3" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

表达式 `if e1 then e2 else e3` 的值为 `e2` ，如果 `e1` 的值为 `true` ，否则为 `e3` 。我们称 `e1` 为 `if` 表达式的保护条件。

```
if 3 + 5 > 2 then "yay!" else "boo!"
```

```
- : string = "yay!"
```

与您可能在命令式语言中使用的 `if-then-else` 语句不同，OCaml 中的 `if-then-else` 表达式就像任何其他表达式一样；它们可以放在任何表达式可以放置的地方。这使它们类似于您可能在其他语言中使用的三元运算符 `? :` 。


```
4 + (if 'a' = 'b' then 1 else 2)
```

```
- : int = 6
```

`If` 表达式可以以一种愉快的方式嵌套:

```
if e1 then e2
else if e3 then e4
else if e5 then e6
...
else en
```

您应该将最终的 `else `视为强制性，无论您是在编写单个 `if` 表达式还是高度嵌套的 `if` 表达式。如果您省略它，您很可能会收到一个目前难以理解的错误消息：

```
if 2 > 3 then 5
```

```
File "[20]", line 1, characters 14-15:
1 | if 2 > 3 then 5
                  ^
Error: This expression has type int but an expression was expected of type
         unit
       because it is in the result of a conditional with no else branch
```

**语法**。一个 `if` 表达式的语法：

```
if e1 then e2 else e3
```

在这里，字母 `e` 用于代表任何其他 OCaml 表达式；它是一个句法变量，也称为元变量的示例，实际上并不是 OCaml 语言本身的变量，而是对某种句法结构的名称。字母 `e` 后面的数字用于区分它的三个不同出现。

**动态语义学**。一个 `if` 表达式的动态语义：

- 如果 `e1` 评估为 `true` ，并且如果 `e2` 评估为值 `v` ，那么 `if e1 then e2 else e3` 评估为 `v` 。
- 如果 `e1` 评估为 `false` ，并且如果 `e3` 评估为值 `v` ，那么 `if e1 then e2 else e3` 评估为 `v` 。

我们称这些为评估规则：它们定义了如何评估表达式。请注意，描述 `if` 表达式的评估需要两条规则，一条用于当条件为真时，另一条用于当条件为假时。这里使用字母 `v` 来代表任何 OCaml 值；这是另一个元变量的例子。稍后我们将会开发一种更数学化的动态语义表达方式，但现在我们将继续使用这种更不正式的解释风格。

**静态语义**。一个 `if` 表达式的静态语义：

- 如果 `e1` 具有类型 `bool` ， `e2` 具有类型 `t` ， e3 具有类型 `t` ，那么 `if e1 then e2 else e3` 具有类型 `t` 。

我们称之为打字规则：它描述了如何对表达式进行类型检查。请注意，只需要一条规则来描述 `if` 表达式的类型检查。在编译时，当进行类型检查时，无论守卫是真还是假都没有影响；事实上，编译器无法知道守卫在运行时会有什么值。这里的字母 `t` 用于表示任何 OCaml 类型；OCaml 手册还定义了[所有类型](https://ocaml.org/manual/types.html)（有趣的是没有命名语言的基本类型，如 `int` 和 `bool` ）。

我们将经常写“具有类型”，因此让我们为其引入更紧凑的表示法。每当我们写“ `e` 具有类型 `t` ”时，让我们改为写 `e : t` 。冒号的发音是“具有类型”。这种冒号的用法与顶层在评估您输入的表达式后的响应一致：

```
let x = 42
```

```
val x : int = 42
```

在上面的例子中，变量 `x` 的类型为 `int` ，这就是冒号所表示的。

## Let 表达式

<iframe width="791" height="445" src="https://www.youtube.com/embed/ug3L97FXC6A" title="Let Expressions | OCaml Programming | Chapter 2 Video 5" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

迄今为止，我们在顶层和 `.ml` 文件中对 `let` 一词进行了定义。例如，

```
let x = 42;;
```

```
val x : int = 42
```

将 `x` 定义为 42，之后我们可以在顶层的未来定义中使用 `x` 。我们将这种使用 `let` 的方式称为 `let` 定义。

`let` 的另一个用途是作为一个表达式：

```
let x = 42 in x + 1
```

```
- : int = 43
```

这里我们将一个值绑定到名称 `x` ，然后在另一个表达式 `x+1` 中使用该绑定。我们将这种 `let` 的用法称为 `let` 表达式。由于它是一个表达式，它会被求值为一个值。这与定义不同，定义本身不会被求值为任何值。如果您尝试将 `let` 定义放在期望表达式的位置，您会发现这一点：

```
(let x = 42) + 1
```

```
File "[24]", line 1, characters 11-12:
1 | (let x = 42) + 1
               ^
Error: Syntax error
```


从句法上讲， `let` 定义不允许出现在 `+` 运算符的左侧，因为那里需要一个值，而定义不会求值为值。另一方面， `let` 表达式可以正常工作：

```
(let x = 42 in x) + 1
```

```
- : int = 43
```

另一种理解顶层的 `let` 定义的方式是将它们视为 `let` 表达式，只是我们尚未提供主体表达式。隐含地，该主体表达式就是我们将来键入的任何其他内容。例如，

```
# let a = "big";;
# let b = "red";;
# let c = a ^ b;;
# ...
```

OCaml 以相同的方式理解

```
let a = "big" in
let b = "red" in
let c = a ^ b in
...
```

后一系列 `let` 绑定在习语上是指几个变量如何在给定的代码块内绑定。

**句法**

```
let x = e1 in e2
```

通常， `x` 是一个标识符。这些标识符必须以小写字母开头，而不是大写字母，并且按照惯例用 `snake_case` 而不是 `camelCase` 来书写。我们称 `e1` 为绑定表达式，因为它是绑定到 `x` 的内容；我们称 `e2` 为主体表达式，因为那是绑定将在其中范围内的代码主体。

**动态语义**

评估 `let x = e1 in e2`

- 评估 `e1` 的值为 `v1` 。
- 将 `x` 替换为 `v1` 在 `e2` 中，得到一个新表达式 `e2'` 。
- 评估 `e2'` 的值为 `v2` 。
- 评估 `let` 表达式的结果是 `v2` 。

这里有一个例子：

```
let x = 1 + 4 in x * 3
-->   (evaluate e1 to a value v1)
let x = 5 in x * 3
-->   (substitute v1 for x in e2, yielding e2')
5 * 3
-->   (evaluate e2' to v2)
15
  (result of evaluation is v2)
```

**静态语义**

- 如果 `e1 : t1` ，并且在假设 `x : t1` 的情况下成立` e2 : t2` ，那么 (`let x = e1 in e2) : t2` 。

我们仅仅是为了清晰起见而使用上述括号。通常情况下，编译器的类型推断器会确定变量的类型，或者程序员可以使用以下语法明确注释它：

```
let x : t = e1 in e2
```

## 范围

<iframe width="791" height="445" src="https://www.youtube.com/embed/_TpTC6eo34M" title="Variable Expressions and Scope | OCaml Programming | Chapter 2 Video 6" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


`Let` 绑定仅在其出现的代码块中生效。这正是您从几乎任何现代编程语言中熟悉的。例如：

```
let x = 42 in
  (* y is not meaningful here *)
  x + (let y = "3110" in
         (* y is meaningful here *)
         int_of_string y)
```

变量的作用域是其名称有意义的范围。变量 `y` 仅在绑定其的 `let` 表达式内部有效。

可以有相同名称的重叠绑定。例如：

```
let x = 5 in
  ((let x = 6 in x) + x)
```

但这太令人困惑了，因此，这种风格是强烈不推荐的——就像自然语言中不推荐使用模糊代词一样。尽管如此，让我们考虑一下那段代码的含义。

代码将评估为什么值？答案取决于每次出现时 `x` 被一个值替换。以下是一些替换的可能性：

```
(* possibility 1 *)
let x = 5 in
  ((let x = 6 in 6) + 5)

(* possibility 2 *)
let x = 5 in
  ((let x = 6 in 5) + 5)

(* possibility 3 *)
let x = 5 in
  ((let x = 6 in 6) + 6)
```

第一个是几乎任何合理的语言都会做的事情。很可能这就是你猜到的，但是，为什么呢？

答案是我们将称之为名称无关原则：变量的名称本质上不应该重要。你在数学中已经习惯了这一点。例如，以下两个函数是相同的

\\[ f(x) = x^2 \\]
\\[ f(y) = y^2 \\]

无论我们将函数的参数称为 `x` 还是 `y`
，本质上都没有关系；无论如何，它仍然是平方函数。因此，在程序中，这两个函数应该是相同的：

```
let f x = x * x
let f y = y * y
```

这个原则更常被称为α等价性：这两个函数在变量重命名上是等价的，这也被称为α转换，出于历史原因，这里并不重要。

根据名称无关原则，这两个表达式应该是相同的：


```
let x = 6 in x
let y = 6 in y
```

因此，以下两个表达式中嵌入了上述表达式，也应该是相同的：

```
let x = 5 in (let x = 6 in x) + x
let x = 5 in (let y = 6 in y) + x
```

但是要使它们相同，我们必须选择上述三种可能性中的第一种。这是唯一一个使变量名称无关紧要的可能性。

有一个常用术语来描述这种现象：对变量的新绑定会掩盖任何旧的变量名称绑定。比喻地说，就好像新的绑定暂时将旧的绑定遮蔽起来。但最终，随着阴影消退，旧的绑定可能会重新出现。

<iframe width="791" height="445" src="https://www.youtube.com/embed/4SqMkUwakEA" title="Scope and the Toplevel | OCaml Programming | Chapter 2 Video 7" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

阴影不是可变赋值。例如，以下两个表达式都会计算为 11：

```
let x = 5 in ((let x = 6 in x) + x)
let x = 5 in (x + (let x = 6 in x))
```

同样，以下的utop transcript 并非可变赋值，尽管乍看起来可能是这样的：

```
# let x = 42;;
val x : int = 42
# let x = 22;;
val x : int = 22
```

请记住，顶层中的每个 `let` 定义实际上都是一个嵌套的 `let` 表达式。因此，上述实际上等同于以下内容：

```
let x = 42 in
  let x = 22 in
    ... (* whatever else is typed in the toplevel *)
```

正确的思考方式是，第二个 let 绑定了一个全新的变量，恰好与第一个 let 同名。

这是另一个值得研究的utop transcript:

```
# let x = 42;;
val x : int = 42
# let f y = x + y;;
val f : int -> int = <fun>
# f 0;;
: int = 42
# let x = 22;;
val x : int = 22
# f 0;;
- : int = 42  (* x did not mutate! *)
```

总结一下，每个 `let` 定义都绑定了一个全新的变量。如果这个新变量恰好与旧变量同名，那么新变量会暂时遮蔽旧变量。但是旧变量仍然存在，并且其值是不可变的：它永远不会改变。因此，即使 `let` 表达式在表面上看起来像命令式语言中的赋值语句，它们实际上是非常不同的。

## 类型注解

OCaml 自动推断每个表达式的类型，程序员无需手动编写。然而，有时手动指定表达式的期望类型可能会有用。类型注释可以实现这一点：

```
(5 : int)
```

```
- : int = 5
```

一个不正确的注释会产生编译时错误：

```
(5 : float)
```

```
File "[27]", line 1, characters 1-2:
1 | (5 : float)
     ^
Error: This expression has type int but an expression was expected of type
         float
  Hint: Did you mean `5.'?
```

这个例子说明了为什么在调试过程中可能会使用手动类型注释。也许你忘记了 `5` 不能被视为 `float` ，然后尝试编写：

```
5 +. 1.1
```

您可以尝试手动指定 `5` 应该是一个 `float` ：

```
(5 : float) +. 1.1
```

```
File "[28]", line 1, characters 1-2:
1 | (5 : float) +. 1.1
     ^
Error: This expression has type int but an expression was expected of type
         float
  Hint: Did you mean `5.'?
```

很明显，类型注释失败了。尽管对于这个小程序来说可能看起来有点傻，但随着程序变得更大，你可能会发现这种技术是有效的。

> 类型注释不是类型转换，例如在 C 或 Java 中可能找到的类型转换。它们不表示从一种类型转换为另一种类型。相反，它们表示检查表达式确实具有给定的类型。

**语法**。类型注释的语法：

```
(e : t)
```

请注意，括号是必需的。

**动态语义**。类型注释没有运行时含义。它在编译过程中消失，因为它表示编译时检查。没有运行时转换。因此，如果 `(e : t)` 成功编译，那么在运行时它就是简单的 `e` ，并且它的评估结果与 `e` 相同。


**静态语义**。如果 `e` 具有类型 `t` ，那么 `(e : t)` 具有类型 `t` 。
