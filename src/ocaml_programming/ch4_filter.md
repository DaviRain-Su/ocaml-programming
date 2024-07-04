# Filter

<iframe width="791" height="445" src="https://www.youtube.com/embed/FaWtD-LRdpU" title="Filter | OCaml Programming | Chapter 4 Video 6" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

假设我们想要从一个列表中筛选出偶数或奇数。以下是一些函数来实现这一目的：

```ocaml
(** [even n] is whether [n] is even. *)
let even n =
  n mod 2 = 0

(** [evens lst] is the sublist of [lst] containing only even numbers. *)
let rec evens = function
  | [] -> []
  | h :: t -> if even h then h :: evens t else evens t

let lst1 = evens [1; 2; 3; 4]
```

```
val even : int -> bool = <fun>
```

```
val evens : int list -> int list = <fun>
```

```
val lst1 : int list = [2; 4]
```

```ocaml
(** [odd n] is whether [n] is odd. *)
let odd n =
  n mod 2 <> 0

(** [odds lst] is the sublist of [lst] containing only odd numbers. *)
let rec odds = function
  | [] -> []
  | h :: t -> if odd h then h :: odds t else odds t

let lst2 = odds [1; 2; 3; 4]
```

```
val odd : int -> bool = <fun>
```

```
val odds : int list -> int list = <fun>
```

```
val lst2 : int list = [1; 3]
```


函数 evens 和 odds 几乎是相同的代码：唯一的关键区别是它们应用于头元素的测试。因此，就像我们在上一节中对 map 所做的那样，让我们将该测试提取为一个函数。让我们将该函数命名为 p ，缩写为“predicate”，这是一种测试某事是真还是假的花哨方式：

```
let rec filter p = function
  | [] -> []
  | h :: t -> if p h then h :: filter p t else filter p t
```

```
val filter : ('a -> bool) -> 'a list -> 'a list = <fun>
```

现在我们可以重新实现我们最初的两个函数：

```
let evens = filter even
let odds = filter odd
```

```
val evens : int list -> int list = <fun>
```

```
val odds : int list -> int list = <fun>
```

这些是多么简单！多么清晰！（至少对熟悉 filter 的读者来说。）


## 过滤器和尾递归

与 map 一样，我们可以创建 filter 的尾递归版本：

```ocaml
let rec filter_aux p acc = function
  | [] -> acc
  | h :: t -> if p h then filter_aux p (h :: acc) t else filter_aux p acc t

let filter p = filter_aux p []

let lst = filter even [1; 2; 3; 4]
```

```
val filter_aux : ('a -> bool) -> 'a list -> 'a list -> 'a list = <fun>
```

```
val filter : ('a -> bool) -> 'a list -> 'a list = <fun>
```

```
val lst : int list = [4; 2]
```

再次发现输出是反向的。在这里，标准库做出了与 map 不同的选择。它内置了反转到 List.filter ，实现方式如下：

```
let rec filter_aux p acc = function
  | [] -> List.rev acc (* note the built-in reversal *)
  | h :: t -> if p h then filter_aux p (h :: acc) t else filter_aux p acc t

let filter p = filter_aux p []
```

```
val filter_aux : ('a -> bool) -> 'a list -> 'a list -> 'a list = <fun>
```

```
val filter : ('a -> bool) -> 'a list -> 'a list = <fun>
```

为什么标准库在这一点上对 map 和 filter 有所不同？很好的问题。也许只是从来没有对一个 filter 函数有过时间效率是一个常数倍更好的需求。或者这只是历史意外。

## 其他语言中的过滤器

再次，过滤器的概念存在于许多编程语言中。在 Python 中是这样的：

```python
>>> print(list(filter(lambda x: x % 2 == 0, [1, 2, 3, 4])))
[2, 4]
```

在 Java 中：

```java
jshell> Stream.of(1, 2, 3, 4).filter(x -> x % 2 == 0).collect(Collectors.toList())
$1 ==> [2, 4]
```
