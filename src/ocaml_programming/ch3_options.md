# Options

<iframe width="791" height="445" src="https://www.youtube.com/embed/lByoIw5wpao" title="Options | OCaml Programming | Chapter 3 Video 20" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

假设您想编写一个通常返回类型为 `t` 的值的函数，但有时不返回任何内容。例如，您可能想定义一个函数 `list_max` ，它返回列表中的最大值，但在空列表上没有明智的返回值：

```ocaml
let rec list_max = function
  | [] -> ???
  | h :: t -> max h (list_max t)
```

有几种可能性需要考虑：

- 返回 `min_int` ? 但这样 `list_max` 只能用于整数— 而不是浮点数或其他类型。
- 引发异常？但是函数的用户必须记得捕获异常。
- 返回 `null` ? 这在 Java 中有效，但是按设计，OCaml 没有 `null` 值。这实际上是件好事：空指针错误不好调试。


> NOTE:
>
> 何瑞爵士称他发明的 null 为“[价值十亿美元的错误](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/)”。

除了这些可能性之外，OCaml 还提供了一种更好的东西，称为Option。（Haskellers 将把选项识别为 Maybe 单子。）

你可以把期权想象成一个封闭的盒子。也许盒子里面有东西，或者盒子是空的。在打开盒子之前，我们不知道里面是什么。如果当我们打开盒子时发现里面有东西，我们可以把那个东西拿出来并使用。因此，期权提供一种“或许类型”，最终是一种一种的类型：盒子处于两种状态之一，满或空。

在上面的 `list_max` 中，我们想要用隐喻的方式返回一个空箱子，如果列表为空，或者如果列表非空，则返回一个包含列表中最大元素的箱子。

这是我们如何创建一个选项，就像一个里面有 `42` 的盒子：

```ocaml
Some 42
```

```ocaml
- : int option = Some 42
```

这是我们如何创建一个类似空盒子的选项：

```ocaml
None
```

```ocaml
- : 'a option = None
```

`Some` 表示盒子里面有东西，而且是 `42` 。 `None` 表示盒子里面没有东西。

像 `list` 一样，我们称 `option` 为类型构造器：给定一个类型，它会产生一个新类型；但是，它本身不是一个类型。因此对于任何类型 `t` ，我们可以将 `t option` 写成一个类型。但是 `option` 本身不能被用作一个类型。类型 `t option` 的值可能包含类型 `t` 的值，或者它们可能什么也不包含。 `None` 的类型是 `'a option` ，因为里面的东西的类型是不受限制的 — 因为里面什么也没有。

您可以使用模式匹配访问选项值 `e` 的内容。以下是一个从选项中提取 `int` （如果存在）并将其转换为字符串的函数：

```ocaml
let extract o =
  match o with
  | Some i -> string_of_int i
  | None -> "";;
```

```ocaml
val extract : int option -> string = <fun>
```

以下是该函数的几个示例用法：

```ocaml
extract (Some 42);;
extract None;;
```

```ocaml
- : string = "42"
```

```ocaml
- : string = ""
```

这是我们如何使用选项写 `list_max` 的方法：

```ocaml
let rec list_max = function
  | [] -> None
  | h :: t -> begin
      match list_max t with
        | None -> Some h
        | Some m -> Some (max h m)
      end
```

```ocaml
val list_max : 'a list -> 'a option = <fun>
```

> NOTE:
>
> 上面嵌套模式匹配的 `begin .. end` 包装在这里并不是严格必需的，但也不是一个坏习惯，因为它将预防更复杂代码中的潜在语法错误。关键字 `begin` 和 `end` 等同于 ( 和 ) 。

在 Java 中，每个对象引用都隐式地是一个选项。要么引用中有一个对象，要么引用中什么都没有。那个“什么都没有”由值 `null` 表示。Java 不强制程序员显式检查空值情况，这会导致空指针异常。OCaml 选项强制程序员在模式匹配中包含一个分支 `None` ，从而确保程序员在没有内容时考虑正确的操作。因此，我们可以将选项视为一种有原则的方法，从语言中消除 `null` 。使用选项通常被认为是比引发异常更好的编码实践，因为它强制调用者在 `None` 情况下执行一些明智的操作。

**选项的语法和语义。**

- `t option` 是每种类型 `t` 的一种类型。
- `None` 是一种 `'a option` 类型的值。
- `Some e` 是 `t option` 类型的表达式，如果 `e : t` 。如果 `e ==> v` ，那么 `Some e ==> Some v` 。
