# 文档

OCaml 提供了一个名为 OCamldoc 的工具，其工作原理与 Java 的 Javadoc 工具非常相似：它从源代码中提取特殊格式的注释并将其呈现为 HTML，使程序员可以轻松阅读文档。

## 如何生成文档

以下是 OCamldoc 注释的一个示例：

```Ocaml
(** [sum lst] is the sum of the elements of [lst]. *)
let rec sum lst = ...
```

+ 双星号使注释被识别为 OCamldoc 注释。
+ 注释部分周围的方括号表示这些部分应以`打印字体`而不是常规字体呈现在 HTML 中。

此外，与 Javadoc 一样，OCamldoc 支持文档标签，例如 `@author`、`@deprecated`、`@param`、`@return` 等。例如，在大多数编程作业的第一行，我们要求您完成这样的注释：

```Ocaml
(** @author Your Name (your netid) *)
```

有关 OCamldoc 注释中可能使用的所有标记，请参阅 [OCamldoc 手册](https://ocaml.org/manual/5.2/ocamldoc.html)。但我们在此处介绍的内容足以满足您需要编写的大多数文档的需求。

## 文档应该包含什么

本书中我们喜欢的文档风格与 OCaml 标准库的风格类似：简洁且声明性强。作为示例，让我们重新回顾一下 sum 的文档：

```Ocaml
(** [sum lst] is the sum of the elements of [lst]. *)
let rec sum lst = ...
```

该注释以 `sum lst` 开头，这是函数应用于参数的示例。注释以单词“is”继续，从而以声明方式描述应用程序的结果。（可以使用单词“returns”，但“is”强调函数的数学性质。）该描述使用参数的名称 `lst` 来解释结果。

请注意，无需添加标签来重复描述参数或返回值，而 Javadoc 中经常这样做。需要说的一切都已经说完了。我们强烈反对以下文档：

```Ocaml
(** Sum a list.
    @param lst The list to be summed.
    @return The sum of the list. *)
let rec sum lst = ...
```

这份糟糕的文档用三行不必要的、难以阅读的文字来表达与清晰的一行版本相同的内容。

有一种方法可以改进我们目前的文档，即明确说明空列表会发生什么情况：

```Ocaml
(** [sum lst] is the sum of the elements of [lst].
    The sum of an empty list is 0. *)
let rec sum lst = ...
```

## 先决条件和后置条件

以下是一些以我们喜欢的风格撰写的注释示例。

```Ocaml
(** [lowercase_ascii c] is the lowercase ASCII equivalent of
    character [c]. *)

(** [index s c] is the index of the first occurrence of
    character [c] in string [s].  Raises: [Not_found]
    if [c] does not occur in [s]. *)

(** [random_int bound] is a random integer between 0 (inclusive)
    and [bound] (exclusive).  Requires: [bound] is greater than 0
    and less than 2^30. *)
```

`index` 的文档指定该函数会引发异常，以及该异常是什么以及引发该异常的条件。（我们将在下一章中更详细地介绍异常。）`random_int` 的文档指定该函数的参数必须满足条件。

在之前的课程中，您已经了解了先决条件和后置条件的概念。先决条件是某些代码段之前必须为真的条件；后置条件是之后必须为真的条件。

`random_int` 文档中的上述“Requires”子句是一种先决条件。它表示 `random_int` 函数的客户端负责保证 `bound` 的值。同样，同一文档的第一句话是一种后置条件。它保证函数返回的值。

`index` 文档中的“Raises”子句是另一种后置条件。它保证该函数会引发异常。请注意，该子句不是先决条件，即使它以输入的形式陈述了条件。

请注意，这些示例中均没有“Requires”子句来说明输入的类型。如果您来自动态类型语言（如 Python），这可能会让您感到惊讶。Python 程序员经常会记录有关函数输入类型的先决条件。然而，OCaml 程序员却不会这样做。这是因为编译器本身会进行类型检查，以确保您永远不会将错误类型的值传递给函数。再次考虑 `lowercase_ascii`：尽管英文注释有助于向读者识别 c 的类型，但注释并未像这样声明“Requires”子句：

```Ocaml
(** [lowercase_ascii c] is the lowercase ASCII equivalent of [c].
    Requires: [c] is a character. *)
```

对于 OCaml 程序员来说，这样的注释非常不符合语言习惯，他们会读到这个注释并感到困惑，可能会想：“当然，c 是一个字符；编译器会保证这一点。写这个注释的人到底是什么意思？他们或我是否遗漏了什么？”
