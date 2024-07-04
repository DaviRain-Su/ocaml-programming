# Currying

我们已经看到，一个接受类型为 t1 和 t2 的两个参数并返回类型为 t3 的值的 OCaml 函数具有类型 t1 -> t2 -> t3 。在 let 表达式中函数名后面我们使用两个变量：

```
let add x y = x + y
```

```
val add : int -> int -> int = <fun>
```

定义一个接受两个参数的函数的另一种方法是编写一个接受元组的函数：

```
let add' t = fst t + snd t
```

```
val add' : int * int -> int = <fun>
```

不使用 fst 和 snd ，我们可以在函数定义中使用元组模式，从而导致第三种实现：

```
let add'' (x, y) = x + y
```

```
val add'' : int * int -> int = <fun>
```

使用第一种风格编写的函数（带有类型 t1 -> t2 -> t3 ）称为柯里化函数，而使用第二种风格（带有类型 t1 * t2 -> t3 ）的函数称为非柯里化函数。比喻地说，柯里化函数更“辛辣”，因为您可以部分应用它们（这是您无法对非柯里化函数做到的：您无法传入一对的一半）。实际上，“柯里”一词并不是指香料，而是指逻辑学家 [Haskell Curry](https://en.wikipedia.org/wiki/Haskell_Curry)（他是极少数以名字和姓氏命名编程语言的人之一）。


有时候你会遇到一些库提供了一个非柯里化版本的函数，但你想要一个柯里化版本来在自己的代码中使用；或者反过来。因此，了解如何在这两种函数之间进行转换是很有用的，就像我们在上面使用 add 时所做的那样。

您甚至可以编写一对高阶函数来为您进行转换：

```
let curry f x y = f (x, y)
let uncurry f (x, y) = f x y
```

```
val curry : ('a * 'b -> 'c) -> 'a -> 'b -> 'c = <fun>
```

```
val uncurry : ('a -> 'b -> 'c) -> 'a * 'b -> 'c = <fun>
```

```
let uncurried_add = uncurry add
let curried_add = curry add''
```

```
val uncurried_add : int * int -> int = <fun>
```

```
val curried_add : int -> int -> int = <fun>
```
