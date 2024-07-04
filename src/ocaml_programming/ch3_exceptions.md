# Exceptions


<iframe width="791" height="445" src="https://www.youtube.com/embed/0zZNEJvcZqg" title="Exceptions | OCaml Programming | Chapter 3 Video 21" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

OCaml 具有类似于许多其他编程语言的异常机制。使用以下语法定义 OCaml 异常的新类型：

```ocaml
exception E of t
```

其中 `E` 是构造函数名称， `t` 是类型。 `of t` 是可选的。注意这与定义变体类型的构造函数类似。例如：

```ocaml
exception A
exception B
exception Code of int
exception Details of string
```

```ocaml
exception A
exception B
exception Code of int
exception Details of string
```

要创建一个异常值，使用创建变体值时相同的语法。例如，这里是一个异常值，其构造函数是 `Failure` ，携带了一个 `string` ：

```ocaml
Failure "something went wrong"
```

```ocaml
- : exn = Failure "something went wrong"
```

这个构造函数是在标准库中[预定义](https://ocaml.org/manual/5.2/core.html#ss:predef-exn)的，是 OCaml 程序员经常使用的更常见的异常之一。

要引发异常值 `e` ，只需简单地编写

```ocaml
raise e
```

标准库中有一个方便的函数 `failwith : string -> 'a` ，它引发 `Failure` 。也就是说， `failwith s` 等同于 `raise (Failure s)` 。

<iframe width="791" height="445" src="https://www.youtube.com/embed/XTdT1zdF2IY" title="Handling Exceptions | OCaml Programming | Chapter 3 Video 22" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

要捕获异常，请使用以下语法：

```ocaml
try e with
| p1 -> e1
| ...
| pn -> en
```

表达式 `e` 可能会引发异常。如果没有引发异常，整个 `try` 表达式将评估为 `e` 的值。如果 `e` 引发异常值 `v` ，该值 `v` 将与提供的模式进行匹配，就像 `match` 表达式一样。

## 异常情况是可扩展的变体

所有异常值都具有类型 `exn` ，这是核心中定义的一种变体。不过，它是一种不寻常的变体，称为可扩展变体，允许在定义变体类型本身之后定义新的变体构造函数。如果您感兴趣，可以查看 OCaml 手册了解更多关于可扩展变体的信息。

## 异常语义

由于它们只是变体，异常的语法和语义已经被变体的语法和语义所覆盖——只有一个例外（故意的），即异常被引发和处理的动态语义。

动态语义。正如我们最初所说，每个 OCaml 表达式都是

- 评估为一个值
-  引发异常
- 或者无法终止（即“无限循环”）。

到目前为止，我们只介绍了处理这三种情况中的第一种情况的动态语义部分。当我们添加异常时会发生什么？现在，表达式的评估要么产生一个值，要么产生一个异常数据包。数据包不是正常的 OCaml 值；只有语言中的 `raise` 和 `try` 才能识别它们。由（例如） `Failure "oops"` 产生的异常值是由 `raise` (Failure "oops") 产生的异常数据包的一部分，但数据包不仅包含异常值；例如，还可以有一个堆栈跟踪。

对于除 `try` 之外的任何表达式 `e` ，如果 `e` 的子表达式的评估产生异常数据包 `P` ，那么 `e` 的评估会产生数据包 `P` 。

但现在我们第一次遇到一个问题：子表达式的求值顺序是什么？有时，这个问题的答案可以通过我们已经开发的语义来提供。例如，对于 `let` 表达式，我们知道绑定表达式必须在主体表达式之前求值。因此，以下代码会引发 `A` ：

```ocaml
let _ = raise A in raise B;;
```

```ocaml
Exception: A.
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

在 OCaml 中，对于函数，它并没有正式规定函数和参数的求值顺序，但当前的实现是在函数之前对参数进行求值。因此，以下代码也会引发 `A` ，除了产生一些编译器警告，即第一个表达式实际上永远不会被应用为函数的参数：

```ocaml
(raise B) (raise A)
```

```ocaml
File "[4]", line 1, characters 10-19:
1 | (raise B) (raise A)
              ^^^^^^^^^
Warning 10: this expression should have type unit.
File "[4]", line 1, characters 10-19:
1 | (raise B) (raise A)
              ^^^^^^^^^
Warning 20 [ignored-extra-argument]: this argument will not be used by the function.
```

```ocaml
Exception: A.
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

这两段代码引发相同异常是合理的，因为我们知道 `let x = e1 in e2` 是 `(fun x -> e2) e1` 的语法糖。

但是以下代码会引发什么异常？

```ocaml
(raise A, raise B)
```

```ocaml
Exception: B.
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

答案是微妙的。语言规范并未规定对成对组件的评估顺序。我们的语义也没有确切确定顺序。（尽管如果你认为是从左到右也是可以原谅的。）因此，程序员实际上不能依赖于该顺序。事实证明，OCaml 的当前实现是从右向左评估的。因此，上面的代码实际上会引发 `B` 。如果你真的想要强制评估顺序，你需要使用 `let` 表达式：

```ocaml
let a = raise A in
let b = raise B in
(a, b)
```

```ocaml
Exception: A.
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

该代码保证会引发 `A` 而不是 `B` 。

一个有趣的边界情况是当一个 `raise` 表达式本身具有一个引发异常的子表达式时会发生什么：

```ocaml
exception C of string;;
exception D of string;;
raise (C (raise (D "oops")))
```

```ocaml
exception C of string
```

```ocaml
exception D of string
```

```ocaml
Exception: D "oops".
Called from Stdlib__Fun.protect in file "fun.ml", line 33, characters 8-15
Re-raised at Stdlib__Fun.protect in file "fun.ml", line 38, characters 6-52
Called from Topeval.load_lambda in file "toplevel/byte/topeval.ml", line 89, characters 4-150
```

该代码最终会引发 `D` ，因为首先必须评估 `C (raise (D "oops"))` 的值。这需要评估 `raise (D "oops")` 的值。这会导致生成一个包含 `D "oops"` 的数据包，然后该数据包传播并成为评估 `C (raise (D "oops"))` 的结果，因此也是评估 `raise (C (raise (D "oops")))` 的结果。

一旦表达式的评估产生异常数据包 `P` ，该数据包会传播直至到达一个 `try` 表达式：

```ocaml
try e with
| p1 -> e1
| ...
| pn -> en
```

在 `P` 内部的异常值与提供的模式进行匹配，使用通常的模式匹配评估规则——有一个例外（再次，双关语）。如果没有任何模式匹配成功，那么不会在新的异常数据包内生成 `Match_failure` ，而是原始异常数据包 `P` 会继续传播，直到达到下一个 `try` 表达式。

## 模式匹配

有一个用于异常情况的模式形式。以下是其用法示例：

```ocaml
match List.hd [] with
  | [] -> "empty"
  | _ :: _ -> "non-empty"
  | exception (Failure s) -> s
```

请注意，上面的代码只是一个标准的 `match` 表达式，而不是一个 `try` 表达式。它将 `List.hd []` 的值与提供的三个模式进行匹配。正如我们所知， `List.hd []` 将引发一个包含值 `Failure "hd"` 的异常。异常模式 `exception (Failure s)` 与该值匹配。因此，上述代码将求值为 `"hd"` 。

异常模式是一种语法糖。例如，考虑以下代码：

```ocaml
match e with
  | p1 -> e1
  | exception p2 -> e2
  | p3 -> e3
  | exception p4 -> e4
```

我们可以重写代码以消除异常模式：

```ocaml
try
  match e with
    | p1 -> e1
    | p3 -> e3
with
  | p2 -> e2
  | p4 -> e4
```

通常情况下，如果存在异常模式和非异常模式，则评估过程如下：尝试评估 `e` 。如果它产生异常数据包，则使用原始匹配表达式中的异常模式来处理该数据包。如果它不产生异常数据包，而是产生非异常值，则使用原始匹配表达式中的非异常模式来匹配该值。

## 异常和 OUnit

如果函数的规范要求它引发异常，您可能希望编写 `OUnit  测试来检查函数是否正确执行此操作。以下是如何做到这一点：

```ocaml
open OUnit2

let tests = "suite" >::: [
    "empty" >:: (fun _ -> assert_raises (Failure "hd") (fun () -> List.hd []));
  ]

let _ = run_test_tt_main tests
```

表达式 `assert_raises exn (fun () -> e)` 用于检查表达式 `e` 是否引发异常 `exn` 。如果是，则 `OUnit` 测试用例成功，否则失败。

请注意， `assert_raises` 的第二个参数是类型为 `unit -> 'a` 的函数，有时被称为“`thun`k”。写一个这种类型的函数可能看起来很奇怪——唯一可能的输入是 `()` ——但这是函数式语言中常见的模式，用于暂停或延迟程序的评估。在这种情况下，我们希望 `assert_raises` 在准备好时评估 `List.hd []` 。如果我们立即评估 `List.hd []` ， `assert_raises` 将无法检查是否引发了正确的异常。我们将在后面的章节中更多地了解 `thunks`。


> WARNING:
>
> 常见的错误是忘记在 `e` 周围加上 (`fun () -> ...`) 。如果您犯了这个错误，程序可能仍然可以进行类型检查，但` OUnit` 测试用例将失败：没有额外的匿名函数，异常会在 `assert_raises` 有机会处理它之前被引发。
