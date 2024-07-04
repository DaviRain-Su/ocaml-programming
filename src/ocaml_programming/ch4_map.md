# Map

<iframe width="791" height="445" src="https://www.youtube.com/embed/qz7kn2pIl3M" title="Map | OCaml Programming | Chapter 4 Video 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

这里有两个我们可能想要编写的函数：

```
(** [add1 lst] adds 1 to each element of [lst]. *)
let rec add1 = function
  | [] -> []
  | h :: t -> (h + 1) :: add1 t

let lst1 = add1 [1; 2; 3]
```

```
val add1 : int list -> int list = <fun>
```

```
val lst1 : int list = [2; 3; 4]
```

```
(** [concat_bang lst] concatenates "!" to each element of [lst]. *)
let rec concat_bang = function
  | [] -> []
  | h :: t -> (h ^ "!") :: concat_bang t

let lst2 = concat_bang ["sweet"; "salty"]
```

```
val concat_bang : string list -> string list = <fun>
```

```
val lst2 : string list = ["sweet!"; "salty!"]
```

这两个函数之间有很多相似之处：

- 它们都与一个列表进行模式匹配。
- 它们在空列表的基本情况下返回相同的值。
- 它们在非空列表的情况下都对尾部进行递归。

事实上，它们之间唯一的区别（除了它们的名称）就是它们对于头元素的操作：添加与连接。让我们重写这两个函数，使这种区别更加明显：

```
(** [add1 lst] adds 1 to each element of [lst]. *)
let rec add1 = function
  | [] -> []
  | h :: t ->
    let f = fun x -> x + 1 in
    f h :: add1 t

(** [concat_bang lst] concatenates "!" to each element of [lst]. *)
let rec concat_bang = function
  | [] -> []
  | h :: t ->
    let f = fun x -> x ^ "!" in
    f h :: concat_bang t
```

```
val add1 : int list -> int list = <fun>
```

```
val concat_bang : string list -> string list = <fun>
```

现在这两个函数之间唯一的区别（除了它们的名称之外）就是辅助函数 f 的主体。在这两个函数之间存在如此微小的差异时，为什么要重复所有那些代码呢？我们不妨将一个辅助函数从每个主函数中抽象出来，并将其作为一个参数。

```
let rec add1' f = function
  | [] -> []
  | h :: t -> f h :: add1' f t

(** [add1 lst] adds 1 to each element of [lst]. *)
let add1 = add1' (fun x -> x + 1)

let rec concat_bang' f = function
  | [] -> []
  | h :: t -> f h :: concat_bang' f t

(** [concat_bang lst] concatenates "!" to each element of [lst]. *)
let concat_bang = concat_bang' (fun x -> x ^ "!")
```

```
val add1' : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val add1 : int list -> int list = <fun>
```

```
val concat_bang' : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val concat_bang : string list -> string list = <fun>
```

但现在 add1' 和 concat_bang' 之间实际上没有任何区别，除了它们的名称。它们是完全重复的代码。甚至它们的类型现在也是相同的，因为关于它们的任何内容都没有提到整数或字符串。我们可能会考虑只保留其中一个，并为其想一个好的新名称。一个可能的选择是 transform ，因为它们通过将函数应用于列表的每个元素来转换列表：

```
let rec transform f = function
  | [] -> []
  | h :: t -> f h :: transform f t

(** [add1 lst] adds 1 to each element of [lst]. *)
let add1 = transform (fun x -> x + 1)

(** [concat_bang lst] concatenates "!" to each element of [lst]. *)
let concat_bang = transform (fun x -> x ^ "!")
```

```
val transform : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val add1 : int list -> int list = <fun>
```

```
val concat_bang : string list -> string list = <fun>
```

> NOTE:
>
> 相反
> `let add1 lst = transform (fun x -> x + 1) lst`
> 以上我们写道
> `let add1 = transform (fun x -> x + 1)`
>
> 这是另一种高阶的方式，但这是我们在部分应用的伪装下已经了解过的。后一种编写函数的方式部分应用 transform 到其两个参数中的一个，从而返回一个函数。该函数绑定到名称 add1 。

事实上，C++库确实调用等效函数 transform 。但是 OCaml 和许多其他语言（包括 Java 和 Python）使用更短的单词“map”，在数学上的意义是函数如何将输入映射到输出。因此，让我们对该名称进行最后一次更改：

```
let rec map f = function
  | [] -> []
  | h :: t -> f h :: map f t

(** [add1 lst] adds 1 to each element of [lst]. *)
let add1 = map (fun x -> x + 1)

(** [concat_bang lst] concatenates "!" to each element of [lst]. *)
let concat_bang = map (fun x -> x ^ "!")
```

```
val map : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val add1 : int list -> int list = <fun>
```

```
val concat_bang : string list -> string list = <fun>
```

我们现在已成功应用了抽象原则：通用结构已被分解出来。剩下的清晰地表达了计算，至少对于熟悉 map 的读者来说，这种表达方式不像原始版本那样迅速显而易见。

<iframe width="791" height="445" src="https://www.youtube.com/embed/hynjCGMpcFk" title="Implementing Map | OCaml Programming | Chapter 4 Video 3" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 副作用

源文本：在 OCaml 的标准库中， map 函数已经存在，名称为 List.map ，但与我们上面发现的实现有一个小差异。首先，让我们看看我们自己实现可能存在的问题，然后再看标准库的实现。

在我们讨论异常时，我们已经看到 OCaml 语言规范通常不指定子表达式的评估顺序，并且当前的语言实现通常从右到左进行评估。因此，以下（相当牵强的）代码实际上导致列表元素以看似相反的顺序打印出来：

```
let p x = print_int x; print_newline(); x + 1

let lst = map p [1; 2]
```

```
val p : int -> int = <fun>
```

```
2
```

```
1
```

```
val lst : int list = [2; 3]
```

这就是为什么：
- 表达式 map p [1; 2] 的值为 p 1 :: map p [2] 。
- 该表达式的右侧然后被评估为 p 1 :: (p 2 :: map p []) 。 p 应用于 1 尚未发生。
- :: 的右侧再次进行评估，得到 p 1 :: (p 2 :: []) 。
- 然后 p 被应用到 2 ，最后应用到 1 。

这对于那些倾向于认为评估会从左到右进行的人可能会感到惊讶。解决方案是使用 let 表达式，以使函数应用的评估在递归调用之前发生：

```
let rec map f = function
  | [] -> []
  | h :: t -> let h' = f h in h' :: map f t

let lst2 = map p [1; 2]
```

```
val map : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
1
```

```
2
```

```
val lst2 : int list = [2; 3]
```

这就是为什么这样做有效的原因：

- 表达式 map p [1; 2] 的值为 let h' = p 1 in h' :: map p [2] 。
- 绑定表达式 p 1 被评估，导致 1 被打印并且 h' 被绑定到 2 。
- 身体表达 h' :: map p [2] 然后被评估，导致接下来打印 2 。

这就是标准库如何定义 List.map 的方式。我们应该从现在开始使用它，而不是重新定义这个函数。但很好的是，我们已经发现了这个函数的“从零开始”的方式，如果需要的话，我们可以快速地重新编码它。

这次讨论中更重要的教训是，当评估顺序很重要时，我们需要使用 let 来确保它。什么时候它很重要？只有在存在副作用时。打印和异常是我们目前看到的两种情况。稍后我们将添加可变性。

## 映射和尾递归

敏锐的读者会注意到 map 的实现不是尾递归的。在某种程度上，这是不可避免的。以下是一种诱人但糟糕的方法来创建它的尾递归版本：

```
let rec map_tr_aux f acc = function
  | [] -> acc
  | h :: t -> map_tr_aux f (acc @ [f h]) t

let map_tr f = map_tr_aux f []

let lst = map_tr (fun x -> x + 1) [1; 2; 3]
```

```
val map_tr_aux : ('a -> 'b) -> 'b list -> 'a list -> 'b list = <fun>
```

```
val map_tr : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val lst : int list = [2; 3; 4]
```

在某种程度上这是有效的：输出是正确的， map_tr_aux 是尾递归的。微妙的缺陷在于子表达式 acc @ [f h] 。回想一下，追加操作在单链表上是线性时间的。也就是说，如果有
 个列表元素，那么追加操作需要时间
 。因此，在每次递归调用时我们执行一个
 操作。并且会有
 次递归调用，每个元素都会有一次。这总共是
 的工作量，这是
 的。因此，我们实现了尾递归，但代价很高：原本应该是线性时间操作变成了二次时间。

为了解决这个问题，我们可以使用常数时间的 cons 操作，而不是线性时间的 append 操作：

```
let rec map_tr_aux f acc = function
  | [] -> acc
  | h :: t -> map_tr_aux f (f h :: acc) t

let map_tr f = map_tr_aux f []

let lst = map_tr (fun x -> x + 1) [1; 2; 3]
```

```
val map_tr_aux : ('a -> 'b) -> 'b list -> 'a list -> 'b list = <fun>
```

```
val map_tr : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val lst : int list = [4; 3; 2]
```

在某种程度上，这是有效的：它是尾递归和线性时间的。这一次不那么微妙的缺陷是输出是颠倒的。当我们从输入列表的前面取出每个元素时，我们将其放在输出列表的前面，但这会颠倒它们的顺序。

> NOTE:
>
> 要理解为什么会发生逆转，或许可以将输入和输出列表想象成排队的人们：
>
> -  输入：Alice，Bob。
> -  输出：空。
>
> 然后我们将爱丽丝从输入中移除，并将她添加到输出中：
>
> - 输入：Bob。
> - 输出：爱丽丝。
>
> - 然后我们将 Bob 从输入中移除，并将他添加到输出中：
>
> - 输入：空。
> - 输出：Bob，爱丽丝。
>
> 问题在于，对于单链表，我们只能在列表的头部进行操作，并且仍然保持常数时间。我们无法将 Bob 移动到输出的末尾，而不让他经过 Alice 和其他可能站在输出中的人。

因此，标准库将此函数称为 List.rev_map ，即一个（尾递归）映射函数，以相反顺序返回其输出。

```
let rec rev_map_aux f acc = function
  | [] -> acc
  | h :: t -> rev_map_aux f (f h :: acc) t

let rev_map f = rev_map_aux f []

let lst = rev_map (fun x -> x + 1) [1; 2; 3]
```

```
val rev_map_aux : ('a -> 'b) -> 'b list -> 'a list -> 'b list = <fun>
```

```
val rev_map : ('a -> 'b) -> 'a list -> 'b list = <fun>
```

```
val lst : int list = [4; 3; 2]
```

如果您希望以“正确”的顺序输出，那很简单：只需将 List.rev 应用到它上：

```
let lst = List.rev (List.rev_map (fun x -> x + 1) [1; 2; 3])
```

```
val lst : int list = [2; 3; 4]
```

由于 List.rev 既是线性时间又是尾递归的，这导致了一个完整的解决方案。我们得到了一个线性时间和尾递归的映射计算。代价是需要通过列表进行两次遍历：一次进行转换，另一次进行反转。对于单链表，我们无法做得比这更高效。当然，还有其他实现列表的数据结构，我们最终会涉及到它们。与此同时，请记住，通常我们不必担心尾递归（也就是说，关于堆栈空间）直到列表有 10,000 个或更多元素。

为什么标准库没有提供这个一体化功能？也许如果有足够的理由，将来会提供。但你可能会发现在你自己的编程中，并不怎么需要它。在许多情况下，我们可以不使用尾递归，或者满足于一个反转的列表。

从这次讨论中可以得出的更重要的教训是，递归函数在时间和空间效率之间可能存在权衡。试图使函数更加空间高效（即尾递归）时，我们可能会意外地使其在渐近时间效率上变得不那么高效（即二次而不是线性），或者如果我们足够聪明，可以保持渐近时间效率不变（即线性），但代价是一个常数因子（即处理两次）。

## Map in Other Languages

我们之前提到过，映射的概念存在于许多编程语言中。以下是 Python 的一个示例：

```python
>>> print(list(map(lambda x: x + 1, [1, 2, 3])))
[2, 3, 4]
```

我们必须使用 list 函数将 map 的结果转换回列表，因为为了效率起见，Python 会根据需要生成 map 输出的每个元素。在这里，我们再次看到“何时进行评估？”这一主题再次出现。

在 Java 中，map 是 Java 8 中添加的 Stream 抽象的一部分。由于没有内置的 Java 语法用于列表或流，因此给出一个示例会更加冗长。在这里，我们使用一个工厂方法 Stream.of 来创建一个流：

```
jshell> Stream.of(1, 2, 3).map(x -> x + 1).collect(Collectors.toList())
$1 ==> [2, 3, 4]
```

与 Python 示例中一样，我们必须使用某种方法将流转换回列表。在这种情况下，使用的是 collect 方法。
