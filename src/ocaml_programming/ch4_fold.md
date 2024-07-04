# Fold

map 函数为我们提供了一种逐个转换列表中每个元素的方式。filter 函数为我们提供了一种逐个决定是保留还是丢弃列表中每个元素的方式。但这两个函数实际上只是逐个查看单个元素。如果我们想要以某种方式结合列表的所有元素呢？这就是 fold 函数的作用。事实证明，它有两个版本，我们将在本节中学习。但首先，让我们看一个相关的函数——实际上不在标准库中——我们称之为 combine。

## 合并

<iframe width="791" height="445" src="https://www.youtube.com/embed/uYJVwW2BFPg" title="Combine | OCaml Programming | Chapter 4 Video 4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

再写两个函数：

```ocaml
(** [sum lst] is the sum of all the elements of [lst]. *)
let rec sum = function
  | [] -> 0
  | h :: t -> h + sum t

let s = sum [1; 2; 3]
```

```
val sum : int list -> int = <fun>
```

```
val s : int = 6
```

```
(** [concat lst] is the concatenation of all the elements of [lst]. *)
let rec concat = function
  | [] -> ""
  | h :: t -> h ^ concat t

let c = concat ["a"; "b"; "c"]
```

```
val concat : string list -> string = <fun>
```

```
val c : string = "abc"
```

与我们在使用map和filter进行类似练习时一样，这些函数共享许多共同的结构。这里的区别是：

- 空列表的情况返回不同的初始值， 0 vs ""
- 非空列表的情况使用不同的运算符将头元素与递归调用的结果结合起来， + vs ^ 。

那么我们可以再次应用抽象原则吗？当然可以！但这次我们需要分解出两个参数：一个用于这两个差异中的每一个。

首先，让我们只提取出初始值：

```
let rec sum' init = function
  | [] -> init
  | h :: t -> h + sum' init t

let sum = sum' 0

let rec concat' init = function
  | [] -> init
  | h :: t -> h ^ concat' init t

let concat = concat' ""
```

```
val sum' : int -> int list -> int = <fun>
```

```
val sum : int list -> int = <fun>
```

```
val concat' : string -> string list -> string = <fun>
```

```
val concat : string list -> string = <fun>
```

现在 sum' 和 concat' 之间唯一真正的区别是用于将头部与尾部的递归调用组合的运算符。该运算符也可以成为我们称之为 combine 的统一函数的参数：

```
let rec combine op init = function
  | [] -> init
  | h :: t -> op h (combine op init t)

let sum = combine ( + ) 0
let concat = combine ( ^ ) ""
```

```
val combine : ('a -> 'b -> 'b) -> 'b -> 'a list -> 'b = <fun>
```

```
val sum : int list -> int = <fun>
```

```
val concat : string list -> string = <fun>
```

一种思考 combine 的方式是：

- 列表中的 [] 值被 init 替换，而
- 每个 :: 构造函数都被 op 替换。

例如， [a; b; c] 只是 a :: (b :: (c :: [])) 的语法糖。因此，如果我们用 0 替换 [] ，用 (+) 替换 :: ，我们得到 a + (b + (c + 0)) 。这将是列表的总和。


再次，抽象原则让我们得到了一个非常简单而简洁的计算表达。

## Fold Right

<iframe width="791" height="445" src="https://www.youtube.com/embed/WKKkIGncRn8" title="Fold | OCaml Programming | Chapter 4 Video 5" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

combine 函数是一个实际 OCaml 库函数的基本思想。为了实现这一点，我们需要对我们目前的实现进行一些更改。

首先，让我们重新命名一些参数：我们将把 op 更改为 f ，以强调我们实际上可以传入任何函数，而不仅仅是像 + 这样的内置运算符。我们将把 init 更改为 acc ，通常代表“累加器”。这样就得到了：



```
let rec combine f acc = function
  | [] -> acc
  | h :: t -> f h (combine f acc t)
```

```
val combine : ('a -> 'b -> 'b) -> 'b -> 'a list -> 'b = <fun>
```

其次，让我们做一个明显动机较弱的改变。我们将隐式列表参数与 init 参数进行交换：

```
let rec combine' f lst acc = match lst with
  | [] -> acc
  | h :: t -> f h (combine' f t acc)

let sum lst = combine' ( + ) lst 0
let concat lst = combine' ( ^ ) lst ""
```

```
val combine' : ('a -> 'b -> 'b) -> 'a list -> 'b -> 'b = <fun>
```

```
val sum : int list -> int = <fun>
```

```
val concat : string list -> string = <fun>
```

以这种方式编写函数会稍微不太方便，因为我们不再能够利用 function 关键字，也不能在定义 sum 和 concat 时利用部分应用。但算法上没有任何改变。

我们现在拥有的是标准库函数 List.fold_right 的实际实现。我们剩下要做的就是更改函数名称：

```
let rec fold_right f lst acc = match lst with
  | [] -> acc
  | h :: t -> f h (fold_right f t acc)
```

```
val fold_right : ('a -> 'b -> 'b) -> 'a list -> 'b -> 'b = <fun>
```

为什么这个函数被称为“fold right”？直觉是它的工作方式是从右到左“折叠”列表的元素，使用运算符结合每个新元素。例如， fold_right ( + ) [a; b; c] 0 导致表达式 a + (b + (c + 0)) 的评估。括号从最右边的子表达式向左关联。

## 尾递归和组合

既不是 fold_right 也不是 combine 是尾递归的：在递归调用返回后，仍然需要在应用函数参数 f 或 op 时进行工作。让我们回到 combine 并将其重写为尾递归。所需的只是更改 cons 分支：

```
let rec combine_tr f acc = function
  | [] -> acc
  | h :: t -> combine_tr f (f acc h) t  (* only real change *)
```

```
val combine_tr : ('a -> 'b -> 'a) -> 'a -> 'b list -> 'a = <fun>
```

细心的读者会注意到 combine_tr 的类型与 combine 的类型不同。我们很快会解决这个问题。

现在函数 f 应用于递归调用之前的头元素 h 和累加器 acc ，从而确保在调用返回后没有剩余的工作需要完成。如果这看起来有点神秘，这里是两个函数的重写，可能会有所帮助：

```
let rec combine f acc = function
  | [] -> acc
  | h :: t ->
    let acc' = combine f acc t in
    f h acc'

let rec combine_tr f acc = function
  | [] -> acc
  | h :: t ->
    let acc' = f acc h in
    combine_tr f acc' t
```

```
val combine : ('a -> 'b -> 'b) -> 'b -> 'a list -> 'b = <fun>
```

```
val combine_tr : ('a -> 'b -> 'a) -> 'a -> 'b list -> 'a = <fun>
```

请密切关注每个版本中 acc' ，即新的累加器的定义：

- 在原始版本中，我们使用头元素 h 来拖延。首先，我们将所有剩余的尾元素组合在一起得到 acc' 。然后我们才使用 f 将头部合并进去。因此，作为 acc 的初始值传递的值最终对于 combine 的每次递归调用都是相同的：它一直传递到需要的地方，即列表的最右边元素，然后在那里被使用一次。

- 但在尾递归版本中，我们通过立即将 h 与旧累加器 acc 折叠在一起来“预先处理”。然后我们将其与所有尾元素折叠在一起。因此，在每次递归调用时，作为参数传递的值 acc 可能是不同的。

结合的尾递归版本对求和（和连接，我们省略了）非常有效

```
let sum = combine_tr ( + ) 0
let s = sum [1; 2; 3]
```

```
val sum : int list -> int = <fun>
```

```
val s : int = 6
```

但是减法可能会发生一些令人惊讶的事情：

```
let sub = combine ( - ) 0
let s = sub [3; 2; 1]

let sub_tr = combine_tr ( - ) 0
let s' = sub_tr [3; 2; 1]
```

```
val sub : int list -> int = <fun>
```

```
val s : int = 2
```

```
val sub_tr : int list -> int = <fun>
```

```
val s' : int = -6
```

这两个结果是不同的！

- 使用 combine 我们计算 3 - (2 - (1 - 0)) 。首先我们将 1 合并，然后 2 ，最后 3 。我们从右到左处理列表，将初始累加器放在最右边。
- 但是使用 combine_tr 我们计算 (((0 - 3) - 2) - 1) 。我们从左到右处理列表，将初始累加器放在最左边。


在加法中，我们处理列表的顺序并不重要，因为加法是可交换和结合的。但是减法不是，所以两个方向会得出不同的答案。

实际上，如果我们回想一下我们将 map 设为尾递归时，这并不应该太令人惊讶。然后，我们发现尾递归会导致我们以与同一函数的非尾递归版本相反的顺序处理列表。这就是这里发生的事情。


## Fold Left

我们的 combine_tr 函数也在标准库中以 List.fold_left 的名称存在：

```
let rec fold_left f acc = function
  | [] -> acc
  | h :: t -> fold_left f (f acc h) t

let sum = fold_left ( + ) 0
let concat = fold_left ( ^ ) ""
```

```
val fold_left : ('a -> 'b -> 'a) -> 'a -> 'b list -> 'a = <fun>
```

```
val sum : int list -> int = <fun>
```

```
val concat : string list -> string = <fun>
```

我们再次成功应用了抽象原则。

## Fold Left vs. Fold Right

让我们回顾一下 fold_right 和 fold_left 之间的区别：

- 它们根据它们的名称以相反的顺序组合列表元素。函数 fold_right 从右到左组合，而 fold_left 从左到右进行。
- 函数 fold_left 是尾递归的，而 fold_right 不是。
- 函数的类型是不同的。

关于最后一点，很难记住这些类型是什么！幸运的是，我们总是可以询问顶层：

```
List.fold_left;;
List.fold_right;;
```

```
- : ('a -> 'b -> 'a) -> 'a -> 'b list -> 'a = <fun>
```

```
- : ('a -> 'b -> 'b) -> 'a list -> 'b -> 'b = <fun>
```

要理解这些类型，请查找它们各自的列表参数。这会告诉你列表中值的类型。然后查找返回值的类型；这会告诉你累加器的类型。从那里，你可以推断出其他一切。

- 在 fold_left 中，列表参数的类型为 'b list ，因此列表包含类型为 'b 的值。返回类型为 'a ，因此累加器的类型为 'a 。知道这一点，我们可以推断第二个参数是累加器的初始值（因为它的类型为 'a ）。我们可以推断第一个参数，即组合运算符，将累加器值作为其自己的第一个参数（因为它的类型为 'a ），将列表元素作为其自己的第二个参数（因为它的类型为 'b ），并返回一个新的累加器值。

- 在 fold_right 中，列表参数的类型为 'a list ，因此列表包含类型为 'a 的值。返回类型为 'b ，因此累加器的类型为 'b 。知道这一点，我们可以推断第三个参数是累加器的初始值（因为它的类型为 'b ）。我们可以推断第一个参数，即组合运算符，将累加器值作为自己的第二个参数（因为它的类型为 'b ），将列表元素作为自己的第一个参数（因为它的类型为 'a ），并返回一个新的累加器值。

> TIP:
>
> 你可能会想为什么这两个 fold 函数的参数顺序不同。很好的问题。事实上，其他库确实使用不同的参数顺序。在 OCaml 中记住这一点的一种方法是，在 fold_X 中，累加器参数放在列表参数的 X 位置。

如果您发现很难跟踪所有这些参数顺序，标准库中的 [ListLabels](https://ocaml.org/manual/5.2/api/ListLabels.html) 模块可以提供帮助。它使用带标签的参数为组合运算符（称为 f ）和初始累加器值（称为 init ）命名。在内部，实现实际上与 List 模块完全相同。

```
ListLabels.fold_left;;
ListLabels.fold_left ~f:(fun x y -> x - y) ~init:0 [1;2;3];;
```

```
- : f:('a -> 'b -> 'a) -> init:'a -> 'b list -> 'a = <fun>
```

```
- : int = -6
```

```
ListLabels.fold_right;;
ListLabels.fold_right ~f:(fun y x -> x - y) ~init:0 [1;2;3];;
```

```
- : f:('a -> 'b -> 'b) -> 'a list -> init:'b -> 'b = <fun>
```

```
- : int = -6
```

请注意，在上述两个 fold 的应用中，由于它们的标签，我们能够以统一的顺序编写参数。然而，我们仍然需要注意将列表元素与累加器值作为组合运算符的哪个参数。

## A Digression on Labeled Arguments and Fold

我们可以编写自己版本的 fold 函数，该函数将为组合运算符的参数加上标签，因此我们甚至不必记住它们的顺序：

```
let rec fold_left ~op:(f: acc:'a -> elt:'b -> 'a) ~init:acc lst =
  match lst with
  | [] -> acc
  | h :: t -> fold_left ~op:f ~init:(f ~acc:acc ~elt:h) t

let rec fold_right ~op:(f: elt:'a -> acc:'b -> 'b) lst ~init:acc =
  match lst with
  | [] -> acc
  | h :: t -> f ~elt:h ~acc:(fold_right ~op:f t ~init:acc)
```

```
val fold_left : op:(acc:'a -> elt:'b -> 'a) -> init:'a -> 'b list -> 'a =
  <fun>
```


```
val fold_right : op:(elt:'a -> acc:'b -> 'b) -> 'a list -> init:'b -> 'b =
  <fun>

```

但这些功能并不像它们看起来那么有用：

```
let s = fold_left ~op:( + ) ~init:0 [1;2;3]
```


```
File "[17]", line 1, characters 22-27:
1 | let s = fold_left ~op:( + ) ~init:0 [1;2;3]
                          ^^^^^
Error: This expression has type int -> int -> int
       but an expression was expected of type acc:'a -> elt:'b -> 'a
```

问题在于内置的 + 运算符没有标记参数，因此我们无法将其作为组合运算符传递给我们的带标签函数。我们必须定义自己的带标签版本

```
let add ~acc ~elt = acc + elt
let s = fold_left ~op:add ~init:0 [1; 2; 3]
```

但现在我们必须记住， ~acc 参数将成为 add 的左手参数。这并没有比我们一开始必须记住的要好多少。

## 使用 Fold 实现其他函数

折叠是如此强大，以至于我们可以用 fold_left 或 fold_right 来编写许多其他列表函数。例如，

```
let length lst =
  List.fold_left (fun acc _ -> acc + 1) 0 lst

let rev lst =
  List.fold_left (fun acc x -> x :: acc) [] lst

let map f lst =
  List.fold_right (fun x acc -> f x :: acc) lst []

let filter f lst =
  List.fold_right (fun x acc -> if f x then x :: acc else acc) lst []
```

```
val length : 'a list -> int = <fun>
```

```
val rev : 'a list -> 'a list = <fun>
```

```
val map : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val filter : ('a -> bool) -> 'a list -> 'a list = <fun>
```

在这一点上，开始有争议，是更好地使用折叠来表达上面的计算，还是使用我们已经看到的方法。即使对于经验丰富的函数式程序员来说，理解折叠的作用可能比阅读天真的递归实现更耗时。如果您查阅标[准库的源代码](https://github.com/ocaml/ocaml/blob/trunk/stdlib/list.ml)，您会发现 List 模块内部没有一个是使用折叠来实现的，这或许是对折叠可读性的一种评价。另一方面，使用折叠确保程序员不会意外地错误地编写递归遍历。对于比列表更复杂的数据结构，这种健壮性可能是一种胜利。

## Fold vs. Recursive vs. Library

我们现在已经看到了三种不同的编写操作列表的函数的方法：

- 直接作为一个递归函数，该函数模式匹配空列表和 cons
- 使用 fold 函数
- 使用其他库函数。


让我们尝试使用这些方法来解决问题，这样我们就能更好地欣赏它们。

考虑编写一个函数 lst_and: bool list -> bool ，使得 lst_and [a1; ...; an] 返回列表的所有元素是否都是 true 。也就是说，它的评估结果与 a1 && a2 && ... && an 相同。当应用于空列表时，它的评估结果为 true 。

以下是编写此类函数的三种可能方式。为了清晰起见，我们为每种方式赋予略有不同的函数名称。

```
let rec lst_and_rec = function
  | [] -> true
  | h :: t -> h && lst_and_rec t

let lst_and_fold =
	List.fold_left (fun acc elt -> acc && elt) true

let lst_and_lib =
	List.for_all (fun x -> x)
```

```
val lst_and_rec : bool list -> bool = <fun>
```

```
val lst_and_fold : bool list -> bool = <fun>
```

```
val lst_and_lib : bool list -> bool = <fun>
```

所有三个函数的最坏运行时间与列表的长度成线性关系。但是：

- 第一个函数， lst_and_rec 的优点在于它不需要处理整个列表。一旦在列表中发现一个 false 元素，它将立即返回 false 。
- 第二个函数， lst_and_fold ，将始终处理列表的每个元素。
- 至于第三个功能 lst_and_lib ，根据 List.for_all 的文档，它返回 (p a1) && (p a2) && ... && (p an) 。因此，与 lst_and_rec 一样，它不需要处理每个元素。
