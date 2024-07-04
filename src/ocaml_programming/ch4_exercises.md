# Exercises

大多数练习的解决方案都可以[获得](https://github.com/cs3110/textbook-solutions)。2022 年秋季是这些解决方案的首次公开发布。尽管康奈尔大学的学生已经可以获得这些解决方案几年了，但更广泛的传播将揭示可以进行改进的地方是不可避免的。我们很乐意添加或更正解决方案。请通过 GitHub 进行贡献。


## Exercise: twice, no arguments [★]

考虑以下定义：

```
let double x = 2 * x
let square x = x * x
let twice f x = f (f x)
let quad = twice double
let fourth = twice square
```

使用 toplevel 确定 quad 和 fourth 的类型。解释为什么 quad 不是以语法形式编写为接受参数的函数，但其类型显示它实际上是一个函数。

## Exercise: mystery operator 1 [★★]

以下运算符是做什么用的？

```
let ( $ ) f x = f x
```

提示：调查 square $ 2 + 2 与 square 2 + 2 之间的差异。

## Exercise: mystery operator 2 [★★]

以下运算符是做什么用的？

```
let ( @@ ) f g x = x |> g |> f
```

提示：研究 String.length @@ string_of_int 应用于 1 ， 10 ， 100 等。

## Exercise: repeat [★★]

将 twice 概括为一个函数 repeat ，使得 repeat f n x 将 f 应用于 x 共 n 次。也就是说，

- repeat f 0 x yields x
- repeat f 1 x yields f x
- repeat f 2 x yields f (f x) (which is the same as twice f x)
- repeat f 3 x yields f (f (f x))
- and so on.

## Exercise: product [★]

使用 fold_left 编写一个函数 product_left ，用于计算一组浮点数的乘积。空列表的乘积是 1.0 。提示：回想一下我们在讲座中如何用一行代码实现 sum 。

使用 fold_right 编写一个函数 product_right ，用于计算一个浮点数列表的乘积。相同的提示适用。

## Exercise: terse product [★★]

你能将对产品练习的解决方案简化到多少？提示：每个解决方案只需要一行代码，且不需要使用 fun 关键字。对于 fold_left ，你的函数定义甚至不需要显式地接受一个列表参数。如果你使用 ListLabels ，对于 fold_right 也是同样的情况。

## Exercise: sum_cube_odd [★★]

编写一个函数 sum_cube_odd n ，计算介于 0 和 n 之间（包括 0 和 n ）所有奇数的立方和。不要编写任何新的递归函数。而是使用函数式编程中的 map、fold 和 filter，以及管道化讨论中定义的 ( -- ) 运算符。

## Exercise: sum_cube_odd pipeline [★★]

重写函数 sum_cube_odd 以使用管道运算符 |> 。

## Exercise: exists [★★]

考虑编写一个函数 exists: ('a -> bool) -> 'a list -> bool ，使得 exists p [a1; ...; an] 返回列表中至少有一个元素满足谓词 p 的情况。也就是说，它的评估结果与 (p a1) || (p a2) || ... || (p an) 相同。当应用于空列表时，它的评估结果为 false 。

写出三个解决这个问题的方法，就像我们之前所做的那样：

  - exists_rec 必须是一个递归函数，不能使用 List 模块
  - exists_fold ，使用 List.fold_left 或 List.fold_right ，但不使用任何其他 List 模块函数，也不使用 rec 关键字
  - exists_lib ，使用除 fold_left 或 fold_right 之外的任何 List 模块函数的组合，并且不使用 rec 关键字。


## Exercise: account balance [★★★]

编写一个函数，给定一个代表借方的数字列表，从账户余额中扣除它们，最后返回余额中剩余的金额。编写三个版本： fold_left ， fold_right ，以及一个直接的递归实现。

## Exercise: library uncurried [★★]

这是 List.nth 的非柯里化版本：

```
let uncurried_nth (lst, n) = List.nth lst n
```

以类似的方式，编写这些库函数的非柯里化版本：

- List.append
- Char.compare
- Stdlib.max

## Exercise: map composition [★★★]

展示如何用一个调用 List.map 仅一次的等效表达式替换任何形式为 List.map f (List.map g lst) 的表达式。

## Exercise: more list fun [★★★]

编写执行以下计算的函数。 您编写的每个函数都应该使用 List.fold ， List.map 或 List.filter 中的一个。 要选择使用哪个，请考虑计算正在做什么：组合、转换或过滤元素。

- 找出字符串列表中长度严格大于 3 的元素。
- 将 1.0 添加到浮点数列表的每个元素。
- 给定一个字符串列表 strs 和另一个字符串 sep ，生成包含 strs 的每个元素以 sep 分隔的字符串。例如，给定输入 ["hi";"bye"] 和 "," ，生成 "hi,bye" ，确保不会在结果字符串的开头或结尾产生额外的逗号。

## Exercise: association list keys [★★★]

回想一下，关联列表是一种字典的实现，它是一对列表，我们将每对中的第一个组件视为键，第二个组件视为值。

编写一个函数 keys: ('a * 'b) list -> 'a list ，该函数返回一个关联列表中唯一键的列表。由于它们必须是唯一的，在输出列表中不应出现多次相同的值。输出值的顺序不重要。您能使解决方案多么紧凑和高效？您能在一行中以线性对数空间和时间完成吗？提示： List.sort_uniq 。

## Exercise: valid matrix [★★★]

数学矩阵可以用列表表示。在行主表示中，这个矩阵

[ [1; 1; 1]; [9; 8; 7]]

将其表示为列表 [[1; 1; 1]; [9; 8; 7]] 。让我们将行向量表示为 int list 。例如， [9; 8; 7] 是一个行向量。

一个有效的矩阵是一个至少有一行、至少有一列，并且每列具有相同行数的 int list list 。有许多 int list list 类型的无效值，例如，

- []
- [[1; 2]; [3]]
