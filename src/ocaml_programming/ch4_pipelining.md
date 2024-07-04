# Pipelining

假设我们想要计算从 0 到 n 的数字的平方和。我们该如何做呢？当然（数学是最佳的优化形式），最有效的方式是使用一个封闭形式的公式： n(n+1)(2n+1) / 6

但是让我们想象一下，你忘记了那个公式。在一种命令式语言中，你可以使用一个 for 循环：

```
# Python
def sum_sq(n):
	sum = 0
	for i in range(0, n+1):
		sum += i * i
	return sum
```

在 OCaml 中，等价的（尾）递归代码将是：

```
let sum_sq n =
  let rec loop i sum =
    if i > n then sum
    else loop (i + 1) (sum + i * i)
  in loop 0 0
```

```
val sum_sq : int -> int = <fun>
```

在 OCaml 中产生相同结果的另一种更清晰的方法是使用高阶函数和管道操作符：

```
let rec ( -- ) i j = if i > j then [] else i :: i + 1 -- j
let square x = x * x
let sum = List.fold_left ( + ) 0

let sum_sq n =
  0 -- n              (* [0;1;2;...;n]   *)
  |> List.map square  (* [0;1;4;...;n*n] *)
  |> sum              (*  0+1+4+...+n*n  *)
```

```
val ( -- ) : int -> int -> int list = <fun>
```

```
val square : int -> int = <fun>
```

```
val sum : int list -> int = <fun>
```

```
val sum_sq : int -> int = <fun>
```

函数 sum_sq 首先构造一个包含所有数字 0..n 的列表。然后它使用管道运算符 |> 将该列表传递给 List.map square ，该操作对每个元素进行平方。然后将结果列表通过 sum 进行管道传递，该操作将所有元素相加。

您可能考虑的其他选择有些更加丑陋：

```
(* Maybe worse: a lot of extra [let..in] syntax and unnecessary names to
   for intermediate values we don't care about. *)
let sum_sq n =
  let l = 0 -- n in
  let sq_l = List.map square l in
  sum sq_l

(* Maybe worse: have to read the function applications from right to left
   rather than top to bottom, and extra parentheses. *)
let sum_sq n =
  sum (List.map square (0--n))
```

```
val sum_sq : int -> int = <fun>
```

```
val sum_sq : int -> int = <fun>
```

与原始的尾递归版本相比，所有这些方法的缺点是它们浪费空间——是线性的而不是常数的——并且需要更多的时间。因此，就像在编程中经常发生的情况一样，代码的清晰度和效率之间存在权衡。

请注意，低效并不是由于管道操作符本身，而是由于需要构建所有那些不必要的中间列表。因此不要认为流水线处理本质上是不好的。事实上，它可以非常有用。当我们到达关于模块的章节时，我们将经常在那里学习的一些数据结构中使用它。
