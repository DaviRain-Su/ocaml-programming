# Algebraic Data Types

到目前为止，我们仅将变体视为列举一组常量值，例如：

```ocaml
type day = Sun | Mon | Tue | Wed | Thu | Fri | Sat

type ptype = TNormal | TFire | TWater

type peff = ENormal | ENotVery | Esuper
```

但变体远比这更强大。

## 携带数据的变体

<iframe width="791" height="445" src="https://www.youtube.com/embed/u6P5XdRta04" title="Variants | OCaml Programming | Chapter 3 Video 13" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

作为一个运行示例，这里有一个变体类型 `shape` ，它不仅仅是枚举值：

```ocaml
type point = float * float
type shape =
  | Point of point
  | Circle of point * float (* center and radius *)
  | Rect of point * point (* lower-left and upper-right corners *)
```

```ocaml
type point = float * float
```

```ocaml
type shape = Point of point | Circle of point * float | Rect of point * point
```

这种类型， `shape` ，代表着一个点、一个圆或一个矩形的形状。一个点由一个构造函数 `Point` 表示，它携带一些额外的数据，即类型为 `point` 的值。一个圆由一个构造函数 `Circle` 表示，它携带两个数据片段：一个是类型为 `point` 的，另一个是类型为 `float` 的。这些数据代表圆的中心和半径。一个矩形由一个构造函数 `Rect` 表示，它携带另外两个点。

<iframe width="791" height="445" src="https://www.youtube.com/embed/K_eA-8LhlVY" title="Pattern Matching with Variants Part 1 | OCaml Programming | Chapter 3 Video 14" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="791" height="445" src="https://www.youtube.com/embed/SpuQfO_597E" title="Pattern Matching with Variants Part 2 | OCaml Programming | Chapter 3 Video 15" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

这里有几个使用 `shape` 类型的函数：

```ocaml
let area = function
  | Point _ -> 0.0
  | Circle (_, r) -> Float.pi *. (r ** 2.0)
  | Rect ((x1, y1), (x2, y2)) ->
      let w = x2 -. x1 in
      let h = y2 -. y1 in
      w *. h

let center = function
  | Point p -> p
  | Circle (p, _) -> p
  | Rect ((x1, y1), (x2, y2)) -> ((x2 +. x1) /. 2.0, (y2 +. y1) /. 2.0)
```

```ocaml
val area : shape -> float = <fun>
```

```ocaml
val center : shape -> point = <fun>
```


`shape` 变体类型与我们之前见过的类型相同，因为它是根据一组构造函数定义的。与以往不同的是，这些构造函数携带额外的数据。类型 `shape` 的每个值都是从这些构造函数中的一个精确形成的。有时我们称构造函数为标签，因为它标记携带的数据来自特定的构造函数。

变体类型有时被称为标记联合。该类型的每个值都来自构造函数携带的基础类型的所有值的并集。例如，对于 `shape` 类型，每个值都标记为 `Point` 或 `Circle` 或 `Rect` ，并携带一个值。

- 所有 `point` 值的集合，与并集
- 所有 `point * float` 值的集合，与并集
- 所有 `point * point` 值的集合。


这些变体类型的另一个名称是代数数据类型。这里的“代数”指的是变体类型包含前一讲中定义的和类型和积类型。和类型源自于一个变体的值是由其中一个构造器形成的事实。积类型源自于构造器可以携带元组或记录的事实，这些元组或记录的值分别来自于它们各自的组件类型。

使用变体，我们可以以一种类型安全的方式表示代表多个其他类型的联合类型。例如，这里是一个代表 `string` 或 `int` 的类型：

```ocaml
type string_or_int =
  | String of string
  | Int of int
```

```ocaml
type string_or_int = String of string | Int of int
```

如果我们愿意的话，我们可以使用这种类型来编写包含字符串或整数的列表（例如）。

```ocaml
type string_or_int_list = string_or_int list

let rec sum : string_or_int list -> int = function
  | [] -> 0
  | String s :: t -> int_of_string s + sum t
  | Int i :: t -> i + sum t

let lst_sum = sum [String "1"; Int 2]
```

```ocaml
type string_or_int_list = string_or_int list
```

```ocaml
val sum : string_or_int list -> int = <fun>
```

```ocaml
val lst_sum : int = 3
```

变体因此提供了一种类型安全的方式来做一些以前似乎不可能的事情。

变体还可以区分值是用哪个标签构造的，即使多个构造函数具有相同的类型。例如：

```ocaml
type t = Left of int | Right of int
let x = Left 1
let double_right = function
  | Left i -> i
  | Right i -> 2 * i
```

```ocaml
type t = Left of int | Right of int
```

```ocaml
val x : t = Left 1
```

```
val double_right : t -> int = <fun>
```

## 语法和语义

<iframe width="791" height="445" src="https://www.youtube.com/embed/3A_PNz5njt0" title="Variant Syntax and Semantics | OCaml Programming | Chapter 3 Video 16" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

**句法**

定义变体类型：

```ocaml
type t = C1 [of t1] | ... | Cn [of tn]
```

上面的方括号表示 `of ti` 是可选的。每个构造函数可以单独不携带数据或携带数据。我们称不携带数据的构造函数为常量；携带数据的构造函数称为非常量。

编写一个变体表达式：

```ocaml
C e
```

或者:

```ocaml
C
```

取决于构造函数名称 `C` 是非常量还是常量。

**动态语义学**

- 如果 `e ==> v` ，那么 `C e ==> C v` ，假设 `C` 是非常数的。
- `C` 已经是一个数值，假设 `C` 是常数。

**静态语义学**

- 如果 t = ... | C | ... ，那么 C : t 。
- 如果 t = ... | C of t' | ... ，并且如果 e : t' ，那么 C e : t 。

**模式匹配**

我们将以下新的模式形式添加到合法模式列表中：

- `C p`

我们将模式与值匹配并生成绑定的定义扩展如下：

- 如果 `p` 匹配 `v` 并产生绑定 `b` ，那么 `C p` 匹配 `C v` 并产生绑定 `b`。

## 全部情况

对抗变体进行模式匹配时要注意的一点是现实世界 OCaml 所称的“全捕获情况”。以下是一个可能出错的简单示例。假设您编写了以下变体和函数：

```ocaml
type color = Blue | Red

(* a thousand lines of code in between *)

let string_of_color = function
  | Blue -> "blue"
  | _ -> "red"
```

```ocaml
type color = Blue | Red
```

```ocaml
val string_of_color : color -> string = <fun>
```

看起来没问题，对吧？但有一天你意识到世界上有更多的颜色。你需要表示绿色。所以你回去，将绿色添加到你的变体中：

```ocaml
type color = Blue | Red | Green

(* a thousand lines of code in between *)

let string_of_color = function
  | Blue -> "blue"
  | _ -> "red"
```

```ocaml
type color = Blue | Red | Green
```

```ocaml
val string_of_color : color -> string = <fun>
```

但是由于中间有上千行代码，你忘记了 `string_of_color` 需要更新。现在，突然间，你变成了红绿色盲：

```ocaml
string_of_color Green
```

```ocaml
- : string = "red"
```

问题在于模式匹配中的通配情况 `string_of_color` ：使用通配符模式匹配任何内容的最终情况。这样的代码对于将来对变体类型的更改不够健壮。

如果您最初将函数编码为以下内容，生活会更美好：

```ocaml
let string_of_color = function
  | Blue -> "blue"
  | Red  -> "red"
```

```ocaml
File "[9]", lines 1-3, characters 22-17:
1 | ......................function
2 |   | Blue -> "blue"
3 |   | Red  -> "red"
Warning 8 [partial-match]: this pattern-matching is not exhaustive.
Here is an example of a case that is not matched:
Green
val string_of_color : color -> string = <fun>
```

OCaml 类型检查器现在提醒您，您尚未更新 `string_of_color` 以考虑新构造函数。

故事的道德是：捕捉所有情况会导致错误的代码。避免使用它们。

## 递归变体

<iframe width="791" height="445" src="https://www.youtube.com/embed/gDh217oAfnY" title="Recursive Parameterized Variants | OCaml Programming | Chapter 3 Video 19" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

变体类型可能在其自身的内部提及自己的名称。例如，这里是一个变体类型，可以用来表示类似于 `int list` 的东西：

```ocaml
type intlist = Nil | Cons of int * intlist

let lst3 = Cons (3, Nil)  (* similar to 3 :: [] or [3] *)
let lst123 = Cons(1, Cons(2, lst3)) (* similar to [1; 2; 3] *)

let rec sum (l : intlist) : int =
  match l with
  | Nil -> 0
  | Cons (h, t) -> h + sum t

let rec length : intlist -> int = function
  | Nil -> 0
  | Cons (_, t) -> 1 + length t

let empty : intlist -> bool = function
  | Nil -> true
  | Cons _ -> false
```

```ocaml
type intlist = Nil | Cons of int * intlist
val lst3 : intlist = Cons (3, Nil)
val lst123 : intlist = Cons (1, Cons (2, Cons (3, Nil)))
val sum : intlist -> int = <fun>
val length : intlist -> int = <fun>
val empty : intlist -> bool = <fun>
```

请注意，在 `intlist` 的定义中，我们将 `Cons` 构造函数定义为携带包含 `intlist` 的值。这使得类型 `intlist` 成为递归的：它是根据自身定义的。

如果使用 `and` 关键字，类型可能会相互递归

```ocaml
type node = {value : int; next : mylist}
and mylist = Nil | Node of node
```

```ocaml
type node = { value : int; next : mylist; }
and mylist = Nil | Node of node
```

任何这种相互递归必须涉及至少一个变体或记录类型，该递归“经过”。例如，以下情况是不允许的：

```ocaml
type t = u and u = t
```

```ocaml
File "[12]", line 1, characters 0-10:
1 | type t = u and u = t
    ^^^^^^^^^^
Error: The definition of t contains a cycle:
       u
```

但这是:

```ocaml
type t = U of u and u = T of t
```

```ocaml
type t = U of u and u = T of t
```

```ocaml
type t = U of u
and u = T of t
```

记录类型也可以是递归的：

```ocaml
type node = {value : int; next : node}
```

```ocaml
type node = { value : int; next : node; }
```

但是普通的类型同义词可能不是：

```ocaml
type t = t * t
```

```ocaml
File "[15]", line 1, characters 0-14:
1 | type t = t * t
    ^^^^^^^^^^^^^^
Error: The type abbreviation t is cyclic
```

尽管 `node` 是一种合法的类型定义，但由于涉及循环性，无法构造该类型的值：要构造出存在的第一个 `node` 值，您已经需要一个类型为 `node` 的值存在。稍后，当我们涵盖命令式特性时，我们将看到类似的想法用于可变链表（但成功地）。

## 参数化变体

变体类型可以基于其他类型进行参数化。例如，上面的 `intlist` 类型可以泛化为提供列表（我们自己编码）的任何类型：

```ocaml
type 'a mylist = Nil | Cons of 'a * 'a mylist

let lst3 = Cons (3, Nil)  (* similar to [3] *)
let lst_hi = Cons ("hi", Nil)  (* similar to ["hi"] *)
```

```ocaml
type 'a mylist = Nil | Cons of 'a * 'a mylist
```

```ocaml
val lst3 : int mylist = Cons (3, Nil)
```

```ocaml
val lst_hi : string mylist = Cons ("hi", Nil)
```

在这里， `mylist` 是一种类型构造器而不是一种类型：没有办法编写类型为 `mylist` 的值。但是我们可以编写类型为 `int mylist` 的值（例如， `lst3` ）和 `string mylist` 的值（例如， `lst_hi` ）。将类型构造器视为类似于函数，但是它将类型映射到类型，而不是将值映射到值。

这里是一些关于 `'a mylist` 的函数：

```ocaml
let rec length : 'a mylist -> int = function
  | Nil -> 0
  | Cons (_, t) -> 1 + length t

let empty : 'a mylist -> bool = function
  | Nil -> true
  | Cons _ -> false
```

```ocaml
val length : 'a mylist -> int = <fun>
```

```ocaml
val empty : 'a mylist -> bool = <fun>
```

请注意，每个函数的主体与其之前的定义 `intlist` 没有变化。我们改变的只是类型注释。甚至可以安全地省略这一点。

```ocaml
let rec length = function
  | Nil -> 0
  | Cons (_, t) -> 1 + length t

let empty = function
  | Nil -> true
  | Cons _ -> false
```

```ocaml
val length : 'a mylist -> int = <fun>
```

```ocaml
val empty : 'a mylist -> bool = <fun>
```

我们刚刚编写的函数是一种称为参数多态性的语言特性的示例。这些函数不关心 `'a` 在 `'a mylist` 中是什么，因此它们可以很好地在 `int mylist` 或 `string mylist` 或任何其他 (whatever) mylist 上工作。单词“多态性”基于希腊词根“`poly`”（许多）和“`morph`”（形式）。类型为 `'a mylist` 的值可能有许多形式，取决于实际类型 `'a` 。

一旦您对类型 `'a` 可能是什么加以限制，您就放弃了一些多态性。例如，

```ocaml
let rec sum = function
  | Nil -> 0
  | Cons (h, t) -> h + sum t
```

我们使用 `( + )` 运算符与列表的头部结合的事实限制了头部元素必须是 `int` ，因此所有元素必须是 `int` 。这意味着 `sum` 必须接受 `int mylist` ，而不是其他类型的 `'a mylist` 。


还可以为参数化类型设置多个类型参数，这种情况下需要使用括号：

```ocaml
type ('a, 'b) pair = {first : 'a; second : 'b}
let x = {first = 2; second = "hello"}
```

```ocaml
type ('a, 'b) pair = { first : 'a; second : 'b; }
```

```ocaml
val x : (int, string) pair = {first = 2; second = "hello"}
```

## 多态变体

到目前为止，每当您想要定义一个变体类型时，您必须给它一个名称，比如 `day` ， `shape` 或 `'a mylist` ：

```ocaml
type day = Sun | Mon | Tue | Wed | Thu | Fri | Sat

type shape =
  | Point of point
  | Circle of point * float
  | Rect of point * point

type 'a mylist = Nil | Cons of 'a * 'a mylist
```

```ocaml
type day = Sun | Mon | Tue | Wed | Thu | Fri | Sat
```

```ocaml
type shape = Point of point | Circle of point * float | Rect of point * point
```

```ocaml
type 'a mylist = Nil | Cons of 'a * 'a mylist
```

偶尔，您可能仅需要一种变体类型来表示单个函数的返回值。例如，这里有一个函数 `f`，可以返回 `int` 或 `∞`
；您需要定义一个变体类型来表示该结果：

```ocaml
type fin_or_inf = Finite of int | Infinity

let f = function
  | 0 -> Infinity
  | 1 -> Finite 1
  | n -> Finite (-n)
```

```ocaml
type fin_or_inf = Finite of int | Infinity
```

```ocaml
val f : int -> fin_or_inf = <fun>
```

这个定义的缺点是，即使在程序的大部分部分中都不会使用 `fin_or_inf` ，你还是被迫定义它。

OCaml 中还有另一种支持这种编程的变体：多态变体。多态变体就像变体一样，只是：

- 在使用它们之前，您不必声明它们的类型或构造函数。
- 多态变体类型没有名称。（因此，这一特性的另一个名称可以是“匿名变体”。）
- 多态变体的构造者以反引号字符开头。

使用多态变体，我们可以重写 `f` ：

```ocaml
let f = function
  | 0 -> `Infinity
  | 1 -> `Finite 1
  | n -> `Finite (-n)
```

```ocaml
val f : int -> [> `Finite of int | `Infinity ] = <fun>
```

这种类型表示 `f` 要么对某些 `n : int` 返回 ``Finite n` ，要么 ``Infinity`` 。方括号不表示列表，而是一组可能的构造函数。 `>` 符号意味着针对该类型的值进行模式匹配的任何代码必须至少处理构造函数 ``Finite` 和 ``Infinity` ，可能还有更多。例如，我们可以这样写：

```ocaml
match f 3 with
  | `NegInfinity -> "negative infinity"
  | `Finite n -> "finite"
  | `Infinity -> "infinite"
```

这将导致编译器发出警告，因为我们没有处理 `NegInfinity 构造函数。我们可以通过添加一个匹配来修复这个问题：

```ocaml
- : string = "finite"
```

模式匹配包括除 `Finite 或 `Infinity 之外的构造函数是完全可以的，因为 `f` 保证不会返回除此之外的任何构造函数。

在课程的后期我们会看到多态变体还有其他更具说服力的用途。它们在库中特别有用。目前，我们通常会建议您遏制对多态变体的广泛使用，因为它们的类型可能变得难以管理。

## 内置变体

OCaml 的内置列表数据类型实际上是一个递归的、参数化的变体。它的定义如下：

```ocaml
type 'a list = [] | ( :: ) of 'a * 'a list
```

所以 `list` 实际上只是一个类型构造器，具有值构造器 `[]` （我们发音为“`nil`”）和 `::` （我们发音为“cons”）。

OCaml 的内置选项数据类型实际上也是一个参数化的变体。它的定义如下：

```ocaml
type 'a option = None | Some of 'a
```

所以 `option` 实际上只是一个类型构造器，带有值构造器 `None` 和 `Some` 。

您可以在核心 OCaml 库中看到 `list` 和 `option` 都有[定义](https://ocaml.org/manual/5.2/core.html)。
