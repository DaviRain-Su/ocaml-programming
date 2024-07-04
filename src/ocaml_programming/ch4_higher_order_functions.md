# 高阶函数

考虑这些整数上的函数 double 和 square ：

```ocaml
let double x = 2 * x
let square x = x * x
```

```
val double : int -> int = <fun>
```

```
val square : int -> int = <fun>
```

让我们使用这些函数来编写其他函数，使数字变为四倍并将数字提升到四次方：

```ocaml
let quad x = double (double x)
let fourth x = square (square x)
```

```
val quad : int -> int = <fun>
```

```
val fourth : int -> int = <fun>
```

这两个函数之间存在明显的相似性：它们的作用是将给定的函数两次应用于一个值。通过将函数作为参数传递给另一个函数 twice ，我们可以抽象出这种功能：

```ocaml
let twice f x = f (f x)
```

```
val twice : ('a -> 'a) -> 'a -> 'a = <fun>
```

函数 twice 是高阶的：它的输入 f 是一个函数。而且——回想一下，所有 OCaml 函数实际上只接受一个参数——它的输出在技术上是 fun x -> f (f x) ，所以 twice 返回一个函数，因此在这方面也是高阶的。

使用 twice ，我们可以以统一的方式实现 quad 和 fourth ：

```ocaml
let quad x = twice double x
let fourth x = twice square x
```

```
val quad : int -> int = <fun>
```

```
val fourth : int -> int = <fun>
```

## 抽象原则

以上，我们利用了 quad 和 fourth 之间的结构相似性来节省工作量。诚然，在这个简单的例子中，看起来可能并不需要太多工作。但想象一下，如果 twice 实际上是一个更复杂的函数。那么，如果有人提出了一个更高效的版本，那么所有以它为基础编写的函数（如 quad 和 fourth ）都可以从这种效率提升中受益，而无需重新编码。

成为一名优秀的程序员的一部分是识别这种相似之处，并通过创建函数（或其他代码单元）来将它们抽象化。Bruce MacLennan 在他的教材《函数式编程：理论与实践》（1990 年）中将此称为抽象原则。抽象原则建议避免要求某事重复陈述；而是将重复模式提取出来。高阶函数使得这种重构成为可能，因为它们允许我们提取函数并将函数参数化为其他函数。

除了 twice 之外，这里还有一些相对简单的例子，也受到麦克莱南的启发：

应用。我们可以编写一个函数，将其第一个输入应用于其第二个输入：

```ocaml
let apply f x = f x
```

```
val apply : ('a -> 'b) -> 'a -> 'b = <fun>
```

当然，写 apply f 比写 f 要多得多。

管道。我们之前见过的管道运算符是一个高阶函数

```ocaml
let pipeline x f = f x
let (|>) = pipeline
let x = 5 |> double
```

```
val pipeline : 'a -> ('a -> 'b) -> 'b = <fun>
```

```
val ( |> ) : 'a -> ('a -> 'b) -> 'b = <fun>
```

```
val x : int = 10
```

组合。我们可以编写一个函数来组合另外两个函数：

```
let compose f g x = f (g x)
```

```
val compose : ('a -> 'b) -> ('c -> 'a) -> 'c -> 'b = <fun>
```

这个函数可以让我们创建一个新的函数，可以多次应用，例如以下：

```
let square_then_double = compose double square
let x = square_then_double 1
let y = square_then_double 2
```

```
val square_then_double : int -> int = <fun>
```

```
val x : int = 2
```

```
val y : int = 8
```

两者。我们可以编写一个函数，将两个函数应用于相同的参数，并返回结果的一对：

```ocaml
let both f g x = (f x, g x)
let ds = both double square
let p = ds 3
```

```
val both : ('a -> 'b) -> ('a -> 'c) -> 'a -> 'b * 'c = <fun>
```

```
val ds : int -> int * int = <fun>
```

```
val p : int * int = (6, 9)
```

条件。我们可以编写一个函数，根据谓词有选择地选择应用哪个函数：

```
let cond p f g x =
  if p x then f x else g x
```

```
val cond : ('a -> bool) -> ('a -> 'b) -> ('a -> 'b) -> 'a -> 'b = <fun>
```

## “Higher Order” 的含义

术语“高阶”在逻辑学和计算机科学中被广泛使用，尽管在所有情况下并不一定具有精确或一致的含义。

在逻辑学中，一阶量化主要涉及全称量词和存在量词。这些让你对某个感兴趣的领域进行量化，比如自然数。但对于任何给定的量化，比如说，被量化的变量代表该领域中的一个个体元素，比如自然数 42。

二阶量化使您能够做一些严格更强大的事情，即对域的属性进行量化。属性是关于个体元素的断言，例如，自然数是偶数，或者它是质数。在某些逻辑中，我们可以将属性与个体的集合等同起来，例如所有偶数自然数的集合。因此，二阶量化通常被认为是对集合的量化。您还可以将属性视为接受一个元素并返回一个布尔值指示元素是否满足属性的函数；这被称为属性的特征函数。

第三阶逻辑将允许对属性的属性进行量化，第四阶逻辑将允许对属性的属性的属性进行量化，依此类推。高阶逻辑指的是所有比一阶逻辑更强大的逻辑；尽管在这个领域的一个有趣结果是所有高阶逻辑都可以用二阶逻辑来表达。

在编程语言中，一阶函数类似地指的是操作单个数据元素（例如字符串、整数、记录、变体等）的函数。而高阶函数可以操作函数，就像高阶逻辑可以对属性进行量化（这些属性类似于函数）。

## 著名的高阶函数

在接下来的几节中，我们将深入探讨三个最著名的高阶函数：map、filter 和 fold。这些函数可以针对许多数据结构进行定义，包括列表和树。每个函数的基本思想是：

- map transforms elements
- filter eliminates elements, and
- fold combines elements.
