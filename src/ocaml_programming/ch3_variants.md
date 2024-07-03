# Variants

变体是表示值为多种可能性之一的数据类型。在最简单的情况下，变体类似于 C 或 Java 中的枚举：

```ocaml
type day = Sun | Mon | Tue | Wed | Thu | Fri | Sat
let d = Tue
```

```ocaml
type day = Sun | Mon | Tue | Wed | Thu | Fri | Sat
```

OCaml 中变体值的个体名称被称为构造函数。在上面的示例中，构造函数是 Sun ， Mon 等。这与 C++或 Java 中构造函数一词的用法略有不同。

对于 OCaml 中的每种数据类型，我们已经讨论了如何构建和访问它。对于变体，构建很容易：只需编写构造函数的名称。对于访问，我们使用模式匹配。例如：

```ocaml
let int_of_day d =
  match d with
  | Sun -> 1
  | Mon -> 2
  | Tue -> 3
  | Wed -> 4
  | Thu -> 5
  | Fri -> 6
  | Sat -> 7
```

```ocaml
val int_of_day : day -> int = <fun>
```

目前还没有一种自动将构造函数名称映射到 int 的方法，就像你可能期望从具有枚举的语言中看到的那样。

**句法**

定义变体类型：

```ocaml
type t = C1 | ... | Cn
```

构造函数的名称必须以大写字母开头。OCaml 使用这一点来区分构造函数和变量标识符。

编写构造函数值的语法只是它的名称，例如， C 。

**动态语义学。**

- 构造函数已经是一个值。没有需要执行的计算。

**静态语义。**

- 如果 t 被定义为 type t = ... | C | ... 类型，则 C : t 。

## 范围

假设有两种类型被定义为具有重叠的构造函数名称，例如，

```ocaml
type t1 = C | D
type t2 = D | E
let x = D
```

```ocaml
type t1 = C | D
```

```ocaml
type t2 = D | E
```

```ocaml
val x : t2 = D
```

当这些定义之后出现 D 时，它指的是哪种类型？也就是说，上面的 x 是什么类型？答案是后面定义的类型胜出。所以 x : t2 。这对程序员来说可能会有些意外，因此在任何给定的范围内（例如，一个文件或一个模块，尽管我们还没有涉及模块），每当可能出现重叠的构造函数名称时，习惯上会在它们前面加上一些区分字符。例如，假设我们正在定义用于表示宝可梦的类型：

```ocaml
type ptype =
  TNormal | TFire | TWater

type peff =
  ENormal | ENotVery | ESuper
```

```ocaml
type ptype = TNormal | TFire | TWater
```

```ocaml
type peff = ENormal | ENotVery | ESuper
```


由于“Normal”自然会成为宝可梦的类型和宝可梦攻击的有效性的构造函数名称，我们在每个构造函数名称前面添加一个额外的字符，以指示它是类型还是有效性。


## 模式匹配

每次我们引入一种新的数据类型，我们需要引入与之相关的新模式。对于变体来说，这很容易。我们将以下新模式形式添加到合法模式列表中：

- 构造函数名称 C

我们将模式与值匹配并生成绑定的定义扩展如下：

- 模式 C 匹配值 C 并且不生成任何绑定。

> NOTE:
>
> 变体比我们在这里看到的要强大得多。我们很快会再次回到它们。
