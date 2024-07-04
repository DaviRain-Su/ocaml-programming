# Example: Natural Number

我们可以定义一个递归变体，它的行为类似于数字，从而证明我们实际上并不一定需要将数字内置到 OCaml 中！（不过出于效率考虑，将数字内置进去是件好事。）

自然数要么是零，要么是另一个自然数的后继。这是你在数理逻辑课程中可能会定义自然数的方式，它自然地导致以下 OCaml 类型 `nat` ：

```ocaml
type nat = Zero | Succ of nat
```

```ocaml
type nat = Zero | Succ of nat
```

我们已经定义了一个新类型 `nat` ， `Zero` 和 `Succ` 是这种类型值的构造函数。这使我们能够构建具有任意数量嵌套 `Succ` 构造函数的表达式。这样的值就像自然数：

```ocaml
let zero = Zero
let one = Succ zero
let two = Succ one
let three = Succ two
let four = Succ three
```

```ocaml
val zero : nat = Zero
```

```ocaml
val one : nat = Succ Zero
```

```ocaml
val two : nat = Succ (Succ Zero)
```

```ocaml
val three : nat = Succ (Succ (Succ Zero))
```

```ocaml
val four : nat = Succ (Succ (Succ (Succ Zero)))
```

现在我们可以编写函数来操作这种类型的值。在下面的代码中，我们将写很多类型注释，以帮助读者跟踪哪些值是 `nat` 而不是 `int` ；当然，编译器不需要我们的帮助。

```ocaml
let iszero = function
  | Zero -> true
  | Succ _ -> false

let pred = function
  | Zero -> failwith "pred Zero is undefined"
  | Succ m -> m
```

```ocaml
val iszero : nat -> bool = <fun>
```

```ocaml
val pred : nat -> nat = <fun>
```

同样地，我们可以定义一个函数来相加两个数字：

```ocaml
let rec add n1 n2 =
  match n1 with
  | Zero -> n2
  | Succ pred_n -> add pred_n (Succ n2)
```

```ocaml
val add : nat -> nat -> nat = <fun>
```

我们可以将 `nat` 值转换为类型 `int` ，反之亦然：

```ocaml
let rec int_of_nat = function
  | Zero -> 0
  | Succ m -> 1 + int_of_nat m

let rec nat_of_int = function
  | i when i = 0 -> Zero
  | i when i > 0 -> Succ (nat_of_int (i - 1))
  | _ -> failwith "nat_of_int is undefined on negative ints"
```

```ocaml
val int_of_nat : nat -> int = <fun>
```

```ocaml
val nat_of_int : int -> nat = <fun>
```

确定一个自然数是偶数还是奇数，我们可以编写一对相互递归的函数：

```ocaml
let rec even = function Zero -> true | Succ m -> odd m
and odd = function Zero -> false | Succ m -> even m
```

```ocaml
val even : nat -> bool = <fun>
val odd : nat -> bool = <fun>
```
