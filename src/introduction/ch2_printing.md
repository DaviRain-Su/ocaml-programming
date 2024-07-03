# 打印

OCaml 为一些内置基本类型提供了内置打印函数：`print_char`、`print_string`、`print_int` 和 `print_float`。还有一个 `print_endline` 函数，它与 `print_string` 类似，但还会输出换行符。

```Ocaml
print_endline "Camels are bae"
```

```
Camels are bae
```

```
- : unit = ()
```

## Uint

让我们看一下其中几个函数的类型：

```Ocaml
print_endline
```

```Ocaml
- : string -> unit = <fun>
```

```Ocaml
print_string
```

```Ocaml
- : string -> unit = <fun>
```

它们都以字符串作为输入并返回 unit 类型的值，这是我们之前从未见过的。这种类型的值只有一个，写为 ()，发音为“unit”。因此 unit 类似于 bool，只是 unit 类型的值比 bool 类型的值少一个。

当您需要接受参数或返回值，但没有要传递或返回的有趣值时，使用 Unit。它相当于 Java 中的 void，类似于 Python 中的 None。Unit 通常在编写或使用具有副作用的代码时使用。打印就是副作用的一个例子：它改变了世界，并且无法撤消。

## Semicolon

如果要逐个打印内容，可以使用嵌套 let 表达式对一些打印函数进行排序：

```Ocaml
let _ = print_endline "Camels" in
let _ = print_endline "are" in
print_endline "bae"
```

```Ocaml
Camels
```

```Ocaml
are
```

```Ocaml
bae
```

```Ocaml
- : unit = ()
```

上面的 `let _ = e` 语法是一种计算 `e` 但不将其值绑定到任何名称的方法。事实上，我们知道每个 print_endline 函数将返回的值：它始终是 `()`，即 unit。因此没有充分的理由将其绑定到变量名。我们也可以写 `let () = e` 来表示我们知道它只是一个我们不关心的 unit：

```Ocaml
let () = print_endline "Camels" in
let () = print_endline "are" in
print_endline "bae"
```

```Ocaml
Camels
```

```Ocaml
are
```

```Ocaml
bae
```

```Ocaml
- : unit = ()
```

但无论哪种方式，编写所有 `let..in` 的样板代码都很烦人！因此，有一种特殊的语法可用于将多个返回 unit 的函数链接在一起。表达式 `e1; e2` 首先计算 `e1`，它应该计算为 `()`，然后丢弃该值，并计算 `e2`。因此，我们可以将上述代码重写为：

```Ocaml
print_endline "Camels";
print_endline "are";
print_endline "bae"
```

```Ocaml
Camels
```

```Ocaml
are
```

```Ocaml
bae
```

```Ocaml
- : unit = ()
```

这是更加惯用的 OCaml 代码，并且对于命令式程序员来说看起来也更加自然。

> ⚠️警告
> 在该示例中，最后一个 `print_endline` 后面没有分号。一个常见的错误是在每个 print 语句后面都加一个分号。相反，分号严格位于语句之间。也就是说，分号是语句分隔符，而不是语句终止符。如果您在末尾添加分号，则可能会出现语法错误，具体取决于周围的代码。

## Ignore

如果 `e1` 不是 `unit` 类型，则 `e1; e2` 将发出警告，因为您正在丢弃一个可能有用的值。如果这确实是您的意图，您可以调用内置函数 `ignore : 'a -> unit` 将任何值转换为 `()`：

```Ocaml
(ignore 3); 5
```

```
- : int = 5
```

实际上 `ignore` 很容易自己实现：

```Ocaml
let ignore x = ()
```

```Ocaml
val ignore : 'a -> unit = <fun>
```

或者你甚至可以写下划线来表示函数接受一个值但不将该值绑定到名称。这意味着函数永远不能在其主体中使用该值。但没关系：我们想忽略它。

```Ocaml
let ignore _ = ()
```

```Ocaml
val ignore : 'a -> unit = <fun>
```

## Printf

对于复杂的文本输出，使用内置函数进行原始类型打印很快就会变得乏味。例如，假设您想编写一个函数来打印统计数据：

```Ocaml
(** [print_stat name num] prints [name: num]. *)
let print_stat name num =
  print_string name;
  print_string ": ";
  print_float num;
  print_newline ()
```

```Ocaml
val print_stat : string -> float -> unit = <fun>
```

```Ocaml
print_stat "mean" 84.39
```

```Ocaml
mean: 84.39
```

```Ocaml
- : unit = ()
```

我们如何缩短 `print_stat`？在 Java 中，您可以使用重载 `+` 运算符将所有对象转换为字符串：

```Ocaml
void print_stat(String name, double num) {
   System.out.println(name + ": " + num);
}
```

但是 OCaml 值不是对象，它们没有从某个根 `Object` 类继承的 `toString()` 方法。OCaml 也不允许运算符重载。

不过很久以前，FORTRAN 发明了一种不同的解决方案，其他语言（如 C 和 Java 甚至 Python）也支持该解决方案。这个想法是使用格式说明符（顾名思义）来指定如何格式化输出。这个想法最著名的名字可能是“printf”，它指的是实现它的 C 库函数的名称。许多其他语言和库仍然使用该名称，包括 OCaml 的 `Printf` 模块。

以下是我们使用 `printf` 重新实现 `print_stat` 的方法：

```Ocaml
let print_stat name num =
  Printf.printf "%s: %F\n%!" name num
```

```Ocaml
val print_stat : string -> float -> unit = <fun>
```

```Ocaml
print_stat "mean" 84.39
```

```Ocaml
mean: 84.39
```

```Ocaml
- : unit = ()
```

`Printf.printf` 函数的第一个参数是格式说明符。它看起来像一个字符串，但实际上它的作用不止于此。OCaml 编译器实际上对它的理解相当深刻。格式说明符内部有：

+ 纯字符，以及
+ 转换说明符，以 `%` 开头。

大约有二十几个转换说明符可用，您可以在 [Printf 的文档](https://cs3110.github.io/textbook/chapters/basics/printing.html#:~:text=documentation%20of%20Printf)中阅读。让我们以上面的格式说明符为例。

+ 它以 `%s` 开头，这是字符串的转换说明符。这意味着 `printf` 的下一个参数必须是 `string`，并且将输出该字符串的内容。
+ 接下来 `: `，这只是纯字符。这些将插入到输出中。
+ 然后是另一个转换说明符 `%F`。这意味着 `printf` 的下一个参数必须是 `float` 类型，并且将以 OCaml 用于打印浮点的相同格式输出。
+ 之后的换行符 `\n` 是另一个纯字符序列。
+ 最后，转换说明符 `%!` 表示刷新输出缓冲区。正如您可能在之前的编程课程中学到的那样，输出通常是缓冲的，这意味着它不会一次或立即发生。刷新缓冲区可确保缓冲区中仍存在的任何内容立即得到输出。这个说明符的特殊之处在于它实际上不需要给 `printf` 传递另一个参数。

如果参数的类型与转换说明符不符，OCaml 会检测到。让我们添加类型注释以强制 `num` 为 `int`，并查看浮点转换说明符 `%F` 会发生什么：

```Ocaml
let print_stat name (num : int) =
  Printf.printf "%s: %F\n%!" name num
```

```Ocaml
File "[14]", line 2, characters 34-37:
2 |   Printf.printf "%s: %F\n%!" name num
                                      ^^^
Error: This expression has type int but an expression was expected of type
         float
```

为了解决这个问题，我们可以将 `int` 的转换说明符改为 `%i`：

```Ocaml
let print_stat name num =
  Printf.printf "%s: %i\n%!" name num
```

```Ocaml
val print_stat : string -> int -> unit = <fun>
```

`printf` 的另一个非常有用的变体是 `sprintf`，它以字符串形式收集输出而不是打印它：

```Ocaml
let string_of_stat name num =
  Printf.sprintf "%s: %F" name num
```

```Ocaml
val string_of_stat : string -> float -> string = <fun>
```

```Ocaml
string_of_stat "mean" 84.39
```

```Ocaml
- : string = "mean: 84.39"
```
