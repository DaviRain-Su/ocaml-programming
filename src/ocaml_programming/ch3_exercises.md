# Exercises

大多数练习的[解决方案](https://github.com/cs3110/textbook-solutions)都可以获得。2022 年秋季是这些解决方案的首次公开发布。尽管康奈尔大学的学生已经可以获得这些解决方案几年了，但更广泛的传播将揭示可以进行改进的地方是不可避免的。我们很乐意添加或更正解决方案。请通过 GitHub 进行贡献。

## Exercise: list expressions [★]

- 构建一个包含整数 1 到 5 的列表。使用方括号表示法表示列表。
- 构建相同的列表，但不要使用方括号表示法。而是使用 :: 和 [] 。
- 再次构建相同的列表。这次，您的答案中必须包含以下表达式: [2; 3; 4] 。使用 @ 运算符，不要使用 :: 。

## Exercise: product [★★]

编写一个函数 product ，它返回列表中所有元素的乘积。空列表的所有元素的乘积是 1 。

## Exercise: concat [★★]

编写一个函数，将列表中的所有字符串连接起来。空列表中所有字符串的连接是空字符串 "" 。

## Exercise: product test [★★]

单元测试您在上面练习中编写的函数 product 。

## Exercise: patterns [★★★]

使用模式匹配，编写三个函数，分别针对以下属性。如果输入列表具有该属性，则您的函数应返回 true ，否则返回 false 。

- 列表的第一个元素是 "bigred"
- 列表恰好有两个或四个元素；不要使用 length 函数
- 列表的前两个元素相等

## Exercise: library [★★★]

请参考 [List 标准库](https://ocaml.org/manual/5.2/api/List.html)来解决这些练习：

- 编写一个函数，该函数接受一个 int list 并返回该列表的第五个元素（如果存在）。如果列表少于五个元素，则返回 0 。提示： List.length 和 List.nth 。
- 编写一个函数，该函数接受一个 int list 并返回按降序排序的列表。提示：使用 List.sort 作为其第一个参数，以及 Stdlib.compare 。

## Exercise: library test [★★★]

在前面的练习中，为您编写的每个函数编写一对 OUnit 单元测试。

## Exercise: library puzzle [★★★]

- 编写一个函数，该函数返回列表的最后一个元素。您的函数可以假定列表非空。提示：使用两个库函数，不要编写任何自己的模式匹配代码。
- 编写一个函数 any_zeros : int list -> bool ，当输入列表至少包含一个 0 时返回 true 。提示：使用一个库函数，不要编写任何自己的模式匹配代码。

您的解决方案将只有一两行代码。

## Exercise: take drop [★★★]

- 编写一个函数 take : int -> 'a list -> 'a list ，使得 take n lst 返回 lst 的前 n 个元素。如果 lst 的元素少于 n 个，则返回所有元素。
- 编写一个函数 drop : int -> 'a list -> 'a list ，使得 drop n lst 返回 lst 的除了前 n 个元素之外的所有元素。如果 lst 的元素少于 n 个，则返回空列表。

## Exercise: take drop tail [★★★★]

修改您的解决方案 take 和 drop ，使其成为尾递归，如果它们还不是的话。在具有大值 n 的长列表上对它们进行测试，看看它们是否会耗尽堆栈空间。要构建长列表，请使用列表部分的 -- 运算符。

## Exercise: unimodal [★★★]

编写一个函数 is_unimodal : int list -> bool ，该函数接受一个整数列表并返回该列表是否是单峰的。 单峰列表是一个列表，它单调增加到某个最大值，然后在该值之后单调减少。 两个段（增加或减少）中的一个或两个段可能为空。 常数列表是单峰的，空列表也是单峰的。

## Exercise: powerset [★★★]

编写一个函数 powerset : int list -> int list list ，该函数接受一个以列表表示的集合 S，并返回 S 的所有子集。幂集中子集的顺序和子集中元素的顺序都不重要。

提示：考虑这个问题的递归结构。假设你已经有 p ，使得 p = powerset s 。你如何使用 p 来计算 powerset (x :: s) ？

## Exercise: print int list rec [★★]

编写一个函数 print_int_list : int list -> unit ，它打印其输入列表，每行一个数字。例如， print_int_list [1; 2; 3] 应该产生以下输出：

```ocaml
1
2
3
```

这里是一些代码，让您可以开始：

```ocaml
let rec print_int_list = function
| [] -> ()
| h :: t -> (* fill in here *); print_int_list t
```

## Exercise: print int list iter [★★]

编写一个函数 print_int_list' : int list -> unit ，其规范与 print_int_list 相同。在您的解决方案中不要使用关键字 rec ，而是使用 [List 模块](https://ocaml.org/manual/5.2/api/List.html)函数 List.iter 。以下是一些代码，可帮助您开始：

```ocaml
let print_int_list' lst =
  List.iter (fun x -> (* fill in here *)) lst
```

## Exercise: student [★★]

假设以下类型定义：

```ocaml
type student = {first_name : string; last_name : string; gpa : float}
```

给出具有以下类型的 OCaml 表达式：

- student
- student -> string * string （提取学生姓名的函数）
- string -> string -> float -> student （创建学生记录的函数）

## Exercise: pokerecord [★★]

这里是代表几种宝可梦类型的一个变种：

```ocaml
type poketype = Normal | Fire | Water
```

- 定义类型 pokemon 为一个记录，具有字段 name （字符串）， hp （整数）和 ptype （ poketype ）。
- 创建一个名为 charizard 的记录，类型为 pokemon ，代表一只生命值为 78 的火属性宝可梦。
- 创建一个名为 squirtle 的记录，类型为 pokemon ，代表一只生命值为 44 的水属性宝可梦。

## Exercise: safe hd and tl [★★]

编写一个函数 safe_hd : 'a list -> 'a option ，如果输入列表的头部是 x ，则返回 Some x ，如果输入列表为空，则返回 None 。

另外编写一个函数 safe_tl : 'a list -> 'a list option ，该函数返回列表的尾部，如果列表为空则返回 None 。

## Exercise: pokefun [★★★]

编写一个函数 max_hp : pokemon list -> pokemon option ，给定一个 pokemon 列表，找到生命值最高的宝可梦。

## Exercise: date before [★★]

定义类似日期的三元组为类型为 int * int * int 的值。类似日期的三元组的示例包括 (2013, 2, 1) 和 (0, 0, 1000) 。日期是一个类似日期的三元组，其第一部分是正年份（即公元年份），第二部分是 1 到 12 之间的月份，第三部分是 1 到 31 之间的日期（根据月份和年份可能是 30、29 或 28）。 (2013, 2, 1) 是一个日期； (0, 0, 1000) 不是。

编写一个函数 is_before ，它接受两个日期作为输入，并计算为 true 或 false 。如果第一个参数是早于第二个参数的日期，则计算结果为 true 。（如果两个日期相同，则结果为 false 。）

您的函数只需要在日期上正确工作，而不是在任意类似日期的三元组上工作。然而，如果您考虑使其适用于任意类似日期的三元组，您可能会发现编写解决方案更容易。例如，如果您忘记输入是否真正是日期，而是简单地编写一个函数声称（例如）2013 年 1 月 100 日在 2013 年 2 月 34 日之前—因为任何一月的日期都在任何二月的日期之前，但一个函数声称 2013 年 1 月 100 日在 2013 年 2 月 34 日之后也是有效的。您可以忽略闰年。

## Exercise: earliest date [★★★]

编写一个函数 earliest : (int*int*int) list -> (int * int * int) option 。如果输入列表为空，则计算结果为 None ，如果日期 d 是列表中最早的日期，则计算结果为 Some d 。提示：使用 is_before 。

与之前的练习一样，您的函数只需要针对日期正确工作，而不是任意类似日期的三元组。

## Exercise: assoc list [★]

使用[关联列表](./ch3_association_lists.md)部分的函数 insert 和 lookup 构建一个将整数 1 映射到字符串“one”，2 映射到“two”，3 映射到“three”的关联列表。查找键 2。查找键 4。


## Exercise: cards [★★]

- 定义一个变体类型 suit ，代表标准 [52 张牌](https://en.wikipedia.org/wiki/Standard_52-card_deck)中的四种花色，♣ ♦ ♥ ♠。您类型的所有构造函数应为常量。
- 定义一个类型 rank ，表示一张卡牌可能的等级：2、3、…、10、J、Q、K 或 A。有许多可能的解决方案；您可以自由选择适合您的任何方法。其中一种方法是使 rank 成为 int 的同义词，并假定 J=11，Q=12，K=13，A=1 或 14。另一种方法是使用变体。
- 定义一个类型 card ，表示一张牌的花色和等级。将其设为一个具有两个字段的记录。
- 定义几个 card 类型的值：梅花 A、红心 Q、方块 2、黑桃 7。

## Exercise: matching [★]

对于下面列表中的每个模式，请给出一个类型为 int option list 的值，该值不匹配该模式且不是空列表，或者解释为什么这是不可能的。

- Some x :: tl
- [Some 3110; None]
- [Some x; _]
- h1 :: h2 :: tl
- h :: tl

## Exercise: quadrant [★★]

完成下面的 quadrant 函数，根据右侧的图表（摘自[维基百科](https://en.wikipedia.org/wiki/File:Cartesian_coordinates_2D.svg)），返回给定 x, y 点所在的象限。位于坐标轴上的点不属于任何一个象限。提示：（a）为整数的符号定义一个辅助函数，（b）与一对进行匹配。

```ocaml
type quad = I | II | III | IV
type sign = Neg | Zero | Pos

let sign (x:int) : sign =
  ...

let quadrant : int*int -> quad option = fun (x,y) ->
  match ... with
    | ... -> Some I
    | ... -> Some II
    | ... -> Some III
    | ... -> Some IV
    | ... -> None
```

## Exercise: quadrant when [★★]

重写象限函数以使用 when 语法。您不需要之前的辅助函数。

```ocaml
let quadrant_when : int*int -> quad option = function
    | ... when ... -> Some I
    | ... when ... -> Some II
    | ... when ... -> Some III
    | ... when ... -> Some IV
    | ... -> None
```

## Exercise: depth [★★]

编写一个函数 depth : 'a tree -> int ，该函数返回从根节点到叶子节点的任意最长路径中节点的数量。例如，空树的深度（简单地 Leaf ）为 0 ，上面的树 t 的深度为 3 。提示：有一个库函数 max : 'a -> 'a -> 'a ，它返回相同类型的任意两个值中的最大值。

## Exercise: shape [★★★]

编写一个函数 same_shape : 'a tree -> 'b tree -> bool ，用于确定两棵树是否具有相同的形状，而不考虑它们在每个节点携带的值是否相同。提示：使用具有三个分支的模式匹配，其中被匹配的表达式是一对树。

## Exercise: list max exn [★★]

编写一个函数 list_max : int list -> int ，它返回列表中的最大整数，如果列表为空则引发 Failure "list_max" 。

## Exercise: list max exn string [★★]

编写一个函数 list_max_string : int list -> string ，该函数返回一个包含列表中最大整数的字符串，或者如果列表为空，则返回字符串 "empty" （注意，不是异常 Failure "empty" ，而只是字符串 "empty" ）。提示：标准库中的 string_of_int 将执行其名称所暗示的操作。

## Exercise: list max exn ounit [★]

编写两个 OUnit 测试来确定您对上面的列表最大异常解决方案在其输入为空列表时是否正确引发异常，以及当该列表非空时是否正确返回输入列表的最大值。

## Exercise: is_bst [★★★★]

编写一个函数 is_bst : ('a*'b) tree -> bool ，仅当给定树满足二叉搜索树不变式时返回 true 。编写一个有效的版本，该函数最多访问每个节点一次，有些棘手。提示：编写一个递归辅助函数，该函数接受一个树，要么给出树中的最小值和最大值，要么告诉您树为空，要么告诉您树不满足不变式。您的 is_bst 函数不会是递归的，但会调用您的辅助函数并对结果进行模式匹配。您需要为辅助函数的返回类型定义一个新的变体类型。

## Exercise: quadrant poly [★★]

修改您对象限的定义，使用多态变体。您的函数的类型应该变成这样：

```ocaml
val sign : int -> [> `Neg | `Pos | `Zero ]
val quadrant : int * int -> [> `I | `II | `III | `IV ] option
```
