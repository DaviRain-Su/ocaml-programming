# 使用 OUnit 进行单元测试

> NOTE:
>
> 这一部分有点偏离我们对数据类型的研究，但这是一个很好的地方来进行这个偏离：我们现在已经知道足够多，以理解在 OCaml 中如何进行单元测试，没有任何理由再等待更长时间来了解它。

使用 toplevel 来测试函数只适用于非常小的程序。更大的程序需要包含许多单元测试并且可以在每次更新代码库时重新运行的测试套件。单元测试是对程序中一个小功能的测试，比如一个单独的函数。

我们现在已经学习了足够多的 OCaml 特性，以便了解如何使用一个名为 OUnit 的库进行单元测试。它是类似于 Java 中的 JUnit、Haskell 中的 HUnit 等的单元测试框架。使用 OUnit 的基本工作流程如下：

- 在文件 f.ml 中编写一个函数。该文件中可能还有许多其他函数。
- 在单独的文件 test.ml 中为该函数编写单元测试。实际上，确切的名称并不是必要的。
- 构建并运行 test 以执行单元测试。

[OUnit](https://gildor478.github.io/ounit/ounit2/index.html) 文档可在 GitHub 上找到。

## OUnit 的示例

以下示例向您展示了如何创建一个 OUnit 测试套件。示例中有一些东西起初可能看起来很神秘；它们将在下一节中讨论。

创建一个新目录。在该目录中，创建一个名为 sum.ml 的文件，并将以下代码放入其中

```ocaml
let rec sum = function
  | [] -> 0
  | x :: xs -> x + sum xs
```

现在创建一个名为 test.ml 的第二个文件，并将以下代码放入其中：

```ocaml
open OUnit2
open Sum

let tests = "test suite for sum" >::: [
  "empty" >:: (fun _ -> assert_equal 0 (sum []));
  "singleton" >:: (fun _ -> assert_equal 1 (sum [1]));
  "two_elements" >:: (fun _ -> assert_equal 3 (sum [1; 2]));
]

let _ = run_test_tt_main tests
```

根据您的编辑器及其配置，您可能会看到关于 OUnit2 和 Sum 的一些“未绑定模块”错误。不用担心；代码实际上是正确的。我们只需要设置好 dune 并告诉它链接 OUnit。创建一个 dune 文件并将以下内容放入其中：

```ocaml
(executable
 (name test)
 (libraries ounit2))
```

创建一个 dune-project 文件，像往常一样：

```ocaml
(lang dune 3.4)
```

现在构建测试套件：

```shell
$ dune build test.exe
```

返回编辑器，做任何会导致其重新访问 test.ml 的操作。您可以关闭并重新打开窗口，或者在文件中进行微小更改（例如，添加然后删除一个空格）。现在错误应该全部消失。

最后，您可以运行测试套件：

```shell
$ dune exec ./test.exe
```

您将会收到类似以下内容的回复：

```ocaml
...
Ran: 3 tests in: 0.12 seconds.
OK
```

现在假设我们通过将其中的代码更改为以下内容来修改 sum.ml 以引入一个错误：

```ocaml
let rec sum = function
  | [] -> 1 (* bug *)
  | x :: xs -> x + sum xs
```

如果重建并重新执行测试套件，现在所有测试用例都会失败。输出会告诉我们失败用例的名称。以下是输出的开头，我们已经用 ... 替换了一些依赖于您本地计算机的字符串：

```ocaml
FFF
==============================================================================
Error: test suite for sum:2:two_elements.

File ".../_build/oUnit-test suite for sum-...#01.log", line 9, characters 1-1:
Error: test suite for sum:2:two_elements (in the log).

Raised at OUnitAssert.assert_failure in file "src/lib/ounit2/advanced/oUnitAssert.ml", line 45, characters 2-27
Called from OUnitRunner.run_one_test.(fun) in file "src/lib/ounit2/advanced/oUnitRunner.ml", line 83, characters 13-26

not equal
------------------------------------------------------------------------------
```

输出的第一行

```ocaml
FFF
```

告诉我们，OUnit 运行了三个测试用例，全部三个都失败了。

下一个有趣的一行

```ocaml
Error: test suite for sum:2:two_elements.
```

告诉我们，在名为 test suite for sum 的测试套件中，索引为 2 的测试用例名为 two_elements 失败了。该测试用例的其余输出并不特别有趣；现在让我们暂时忽略它。

## OUnit 示例的解释

让我们仔细研究一下我们在上一节中所做的事情。在测试文件中， open OUnit2 将 OUnit2 中的许多定义引入范围，这是 OUnit 框架的第二版。而 open Sum 则将 sum.ml 中的定义引入范围。我们将在后面的章节中更多地了解作用域和 open 关键字。

然后我们创建了一个测试用例列表：

```ocaml
[
  "empty"  >:: (fun _ -> assert_equal 0 (sum []));
  "one"    >:: (fun _ -> assert_equal 1 (sum [1]));
  "onetwo" >:: (fun _ -> assert_equal 3 (sum [1; 2]));
]
```

每一行代码都是一个独立的测试用例。一个测试用例有一个字符串作为描述性名称，以及一个作为测试用例运行的函数。在名称和函数之间，我们写入 >:: ，这是由 OUnit 框架定义的自定义运算符。让我们看一下上面的第一个函数：

```ocaml
fun _ -> assert_equal 0 (sum [])
```

每个测试用例函数接收一个参数，OUnit 将其称为测试上下文。在这里（以及我们编写的许多测试用例中），我们实际上不需要担心上下文，因此我们使用下划线表示函数忽略其输入。然后函数调用 assert_equal ，这是由 OUnit 提供的函数，用于检查其两个参数是否相等。如果相等，则测试用例成功。如果不相等，则测试用例失败。

然后我们创建了一个测试套件：

```ocaml
let tests = "test suite for sum" >::: [
  "empty" >:: (fun _ -> assert_equal 0 (sum []));
  "singleton" >:: (fun _ -> assert_equal 1 (sum [1]));
  "two_elements" >:: (fun _ -> assert_equal 3 (sum [1; 2]));
]
```

>::: 运算符是另一个自定义的 OUnit 运算符。它位于测试套件的名称和该套件中测试用例列表之间。

最后，我们运行测试套件：

```ocaml
let _ = run_test_tt_main tests
```

函数 run_test_tt_main 由 OUnit 提供。它运行一个测试套件，并将测试用例的通过与失败的结果打印到标准输出。这里使用 let _ = 表示我们不关心函数返回什么值；它只是被丢弃。

## 改进 OUnit 输出

在我们的示例中，使用有错误的 sum 实现，我们得到了以下输出：

```ocaml
==============================================================================
Error: test suite for sum:2:two_elements.
...
not equal
------------------------------------------------------------------------------
```

OUnit 输出中的 not equal 表示 assert_equal 发现在该测试用例中传递给它的两个值不相等。这并不是很具体：我们想知道它们为什么不相等。特别是，我们想知道 sum 为该测试用例产生的实际输出是什么。为了找出，我们需要向 assert_equal 传递一个额外的参数。该参数的标签是 printer ，应该是一个能够将输出转换为字符串的函数。在这种情况下，输出是整数，因此 Stdlib 模块中的 string_of_int 就足够了。我们修改测试套件如下：

```ocaml
let tests = "test suite for sum" >::: [
  "empty" >:: (fun _ -> assert_equal 0 (sum []) ~printer:string_of_int);
  "singleton" >:: (fun _ -> assert_equal 1 (sum [1]) ~printer:string_of_int);
  "two_elements" >:: (fun _ -> assert_equal 3 (sum [1; 2]) ~printer:string_of_int);
]
```

现在我们获得了更多信息性的输出：

```ocaml
==============================================================================
Error: test suite for sum:2:two_elements.
...
expected: 3 but got: 4
------------------------------------------------------------------------------
```

该输出意味着名为 two_elements 的测试断言 3 和 4 的相等性。预期输出为 3 ，因为那是 assert_equal 的第一个输入，并且该函数的规范规定在 assert_equal x y 中，您（作为测试人员）期望获得的输出应该是 x ，而被测试函数实际产生的输出应该是 y 。

请注意我们的测试套件积累了大量冗余代码。特别是，我们不得不在几行中添加 printer 参数。让我们通过提取一个构建测试用例的函数来改进该代码：

```ocaml
let make_sum_test name expected_output input =
  name >:: (fun _ -> assert_equal expected_output (sum input) ~printer:string_of_int)

let tests = "test suite for sum" >::: [
  make_sum_test "empty" 0 [];
  make_sum_test "singleton" 1 [1];
  make_sum_test "two_elements" 3 [1; 2];
]
```

对于比整数更复杂的输出类型，您最终需要编写自己的函数传递给 printer 。这类似于在 Java 中编写 toString() 方法：对于您自己发明的复杂类型，语言不知道如何将它们呈现为字符串。您必须提供执行此操作的代码。

## 异常测试

我们还需要学习一点 OCaml 知识，然后我们就可以看到如何测试异常情况了。如果你想现在知道，可以提前查看关于[异常](./ch3_exceptions.md)的部分。


##  测试驱动开发

测试不一定要严格在编写代码之后进行。在测试驱动开发（TDD）中，测试是第一位的！它强调代码的逐步开发：总是有东西可以被测试。测试不是在实现之后发生的事情；相反，连续测试被用来尽早捕捉错误。因此，在编写代码时立即开发单元测试非常重要。自动化测试套件至关重要，以便连续测试基本上不需要任何努力。

这里是 TDD 的一个示例。我们故意选择一个非常简单的函数来实现，以便过程清晰。假设我们正在处理一种表示天数的数据类型：

```ocaml
type day = Sunday | Monday | Tuesday | Wednesday | Thursday | Friday | Saturday
```

我们想要编写一个函数 next_weekday : day -> day ，它返回给定日期后的下一个工作日。我们首先编写这个函数的最基本、有缺陷的版本：

```ocaml
let next_weekday d = failwith "Unimplemented"
```

> NOTE:
>
> 内置函数 failwith 会引发一个异常，同时传递给函数的错误消息。

然后我们编写我们能想象到的最简单的单元测试。例如，我们知道周一之后的下一个工作日是周二。因此，我们添加一个测试

```ocaml
let tests = "test suite for next_weekday" >::: [
  "tue_after_mon"  >:: (fun _ -> assert_equal Tuesday (next_weekday Monday));
]
```

然后我们运行 OUnit 测试套件。正如预期的那样，它失败了。这是好事！现在我们有一个具体的目标，那就是让该单元测试通过。我们修改 next_weekday 以实现这一目标：

```ocaml
let next_weekday d =
  match d with
  | Monday -> Tuesday
  | _ -> failwith "Unimplemented"
```

我们编译并运行测试；测试通过。是时候添加更多的测试了。剩下的最简单可能性是涉及工作日而不是周末的测试。所以让我们为工作日添加测试。

```ocaml
let tests = "test suite for next_weekday" >::: [
  "tue_after_mon"  >:: (fun _ -> assert_equal Tuesday (next_weekday Monday));
  "wed_after_tue"  >:: (fun _ -> assert_equal Wednesday (next_weekday Tuesday));
  "thu_after_wed"  >:: (fun _ -> assert_equal Thursday(next_weekday Wednesday));
  "fri_after_thu"  >:: (fun _ -> assert_equal Friday (next_weekday Thursday));
]
```

我们编译并运行测试；许多测试失败。这是好事！我们添加新功能：

```ocaml
let next_weekday d =
  match d with
  | Monday -> Tuesday
  | Tuesday -> Wednesday
  | Wednesday -> Thursday
  | Thursday -> Friday
  | _ -> failwith "Unimplemented"
```

我们编译并运行测试；测试通过。此时我们可以继续处理周末，但我们应该首先注意到我们编写的测试中有一些重复的代码。事实上，我们可能是通过复制粘贴第一个测试来编写它们的，然后为接下来的三个进行修改。这表明我们应该重构代码。（就像我们之前测试的 sum 函数一样。）

让我们抽象出一个函数，用于创建 next_weekday 的测试用例：

```ocaml
let make_next_weekday_test name expected_output input =
  name >:: (fun _ -> assert_equal expected_output (next_weekday input))

let tests = "test suite for next_weekday" >::: [
  make_next_weekday_test "tue_after_mon" Tuesday Monday;
  make_next_weekday_test "wed_after_tue" Wednesday Tuesday;
  make_next_weekday_test "thu_after_wed" Thursday Wednesday;
  make_next_weekday_test "fri_after_thu" Friday Thursday;
]
```

现在我们通过处理周末来完成测试和实施。首先，我们添加一些测试用例：

```ocaml
...
make_next_weekday_test "mon_after_fri" Monday Friday;
make_next_weekday_test "mon_after_sat" Monday Saturday;
make_next_weekday_test "mon_after_sun" Monday Sunday;
...
```

然后我们完成函数：

```ocaml
let next_weekday d =
  match d with
  | Monday -> Tuesday
  | Tuesday -> Wednesday
  | Wednesday -> Thursday
  | Thursday -> Friday
  | Friday -> Monday
  | Saturday -> Monday
  | Sunday -> Monday
```

当然，大多数人即使不使用 TDD 也能编写该函数而不出错。但我们很少实现如此简单的函数。

过程。让我们回顾一下 TDD 的过程：

- 编写一个失败的单元测试用例。运行测试套件以证明测试用例失败。
- 实现足够的功能使测试用例通过。运行测试套件以证明测试用例通过。
- 根据需要改进代码。在上面的示例中，我们重构了测试套件，但通常我们需要重构正在实现的功能。
- 重复直到您满意为止，测试套件提供了证据表明您的实现是正确的。
