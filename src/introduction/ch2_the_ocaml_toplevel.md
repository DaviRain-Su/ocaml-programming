# The OCaml Toplevel

toplevel类似于 OCaml 的计算器或命令行界面。它类似于 Java 的 JShell，或者交互式 Python 解释器。topleve 对于尝试小段代码而不必启动 OCaml 编译器很方便。但不要过分依赖它，因为创建、编译和测试大型程序需要更强大的工具。一些其他语言可能会将toplevel称为 REPL，即读取-求值-打印-循环：它读取程序员输入，求值它，打印结果，然后重复。

在终端窗口中，输入 `utop` 以启动 toplevel。按下 Control-D 退出 toplevel。您也可以输入 `#quit;;` 然后按回车。请注意，您必须在那里输入 `#` ：这是除了您已经看到的 `#` 提示之外的内容。

## 类型和值

您可以在 OCaml toplevel输入表达式。用双分号 `;;` 结束一个表达式，然后按回车键。OCaml 将求值表达式，告诉您结果值及其类型。例如：

```ocaml
# 42;;
- : int = 42
```

让我们解析 utop 的回应，从右到左阅读：

- `42` 是该值。
- `int` 是该值的类型。
- 该值没有被赋予名称，因此使用符号 `-` 。

那个 utop interaction 是作为这本书的一部分“硬编码”进去的。我们不得不输入所有的字符： `#` ， `-` 等等。但用来撰写这本书的基础设施实际上使我们能够编写代码，在将书翻译成 HTML 或 PDF 时由 OCaml 求值。从现在开始，这通常是我们要做的。它看起来像这样：

```
42
```

```
- : int = 42
```

第一个代码块中带有 `42` 的是我们要求 OCaml 运行的代码。如果您想将其输入到 utop 中，可以复制粘贴。在块的右上角有一个图标可以轻松完成这个操作。只需记得在末尾添加双分号。第二个代码块，稍微缩进，是 OCaml 在翻译书籍时的输出。

> 如果您在网络浏览器中查看此内容，请查找右上角的下载图标。选择 `.md` 选项，您将看到本书此页面的原始 [MyST Markdown](https://myst-parser.readthedocs.io/en/latest/) 源代码。您会发现，上面第二个示例的输出实际上并不存在于源代码中。这是好事！这意味着输出与我们用来构建本书的 OCaml 编译器的当前版本保持一致。这也意味着任何编译错误都可以作为构建本书的一部分来检测，而不是潜伏在那里等待您，亲爱的读者，去发现它们。

<iframe width="791" height="445" src="https://www.youtube.com/embed/eRnG4gwOTlI" title="Let Definitions | OCaml Programming | Chapter 2 Video 4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

您可以通过 `let` 定义将值绑定到名称，如下所示：

```
let x = 42
```

```
val x : int = 42
```

再次，让我们解析这个回答，这次从左到右阅读：

- 一个值被绑定到一个名称，因此使用 `val` 关键字。
- `x` 是绑定的值的名称。
- `int` 是该值的类型。
- `42` 是该值。

您可以将整个输出发音为“ `x` 的类型为 `int` ，等于 `42` 。”

## Functions

可以使用以下类似的语法在 toplevel 定义一个函数：

```
let increment x = x + 1
```

```
val increment : int -> int = <fun>
```

让我们剖析一下这个回应：

- `increment` 是与值绑定的标识符。
- `int -> int` 是值的类型。这是接受 `int` 作为输入并产生 `int` 作为输出的函数类型。将箭头 -> 视为一种将一个值转换为另一个值的视觉隐喻，这正是函数所做的。
- 值是一个函数，utop选择不打印它（因为它现在已经被编译，并且在内存中有一个不容易适合漂亮打印的表示）。相反，toplevel打印 `<fun>` ，这只是一个占位符。

> `<fun>` 本身不是一个值。它只是表示一个不可打印的函数值。

您可以使用以下语法“调用”函数：

```
increment 0
```

```
- : int = 1
```

```
increment(21)
```

```
- : int = 22
```

```
increment (increment 5)
```

```
- : int = 7
```

但在 OCaml 中，通常的术语是我们“应用”函数，而不是“调用”它。

注意 OCaml 在写括号或不写括号，以及写空格或不写空格方面是灵活的。学习 OCaml 时的一个挑战之一是弄清楚何时实际上需要括号。因此，如果发现自己遇到语法错误问题，一种策略是尝试添加一些括号。然而，首选风格通常是在不需要时省略括号。因此， `increment 21` 比 `increment(21)` 更好。

## 在toplevel加载代码

除了允许您定义函数外，toplevel还将接受指令，这些指令不是 OCaml 代码，而是告诉toplevel自身要执行某些操作。所有指令都以 `#` 字符开头。也许最常见的指令是 `#use` ，它会将文件中的所有代码加载到toplevel，就好像您在toplevel中键入了该文件中的代码一样。

例如，假设您创建了一个名为 `mycode.ml` 的文件。在该文件中放入以下代码：

```
let inc x = x + 1
```

启动toplevel。尝试输入以下表达式，并观察错误：

```
inc 3
```

```
File "[7]", line 1, characters 0-3:
1 | inc 3
    ^^^
Error: Unbound value inc
Hint: Did you mean incr?
```

错误发生是因为toplevel尚不知道名为 `inc` 的函数。现在向toplevel发出以下指令：

```
# #use "mycode.ml";;
```

请注意，上面的第 `#` 个字符表示给您的toplevel提示。第二个 `#` 字符是您键入的字符，用于告诉toplevel您正在发布指令。如果没有该字符，toplevel会认为您正在尝试应用名为 `use` 的函数。

现在再试一次：

```
inc 3
```

```
- : int = 4
```


## toplevel的工作流程

使用存储在文件中的代码时，最佳工作流程是：

- 编辑文件中的代码。
- 在toplevel加载代码 `#use` 。
- 交互式地测试代码。
- 退出toplevel。警告：不要跳过这一步。

> 假设您想要修复代码中的一个错误。诱人的做法是不退出toplevel，编辑文件，并将 `#use` 指令重新发出到同一toplevel会话中。抵制这种诱惑。在同一会话中从早期 `#use` 指令加载的“陈旧代码”可能会导致一些令人惊讶的事情发生——尤其是在您初学该语言时。因此，在重新使用文件之前，始终退出toplevel。
