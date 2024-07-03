# 高级模式匹配

以下是一些有用的额外模式形式：

- p1 | ... | pn ：一个“或”模式；如果与任何一个单独模式 pi 匹配成功，则匹配成功，这些模式按从左到右的顺序尝试。所有模式必须绑定相同的变量。
- (p : t) ：带有显式类型注释的模式。
- c ：在这里， c 表示任何常量，如整数字面值、字符串字面值和布尔值。
- 'ch1'..'ch2' ：在这里， ch 表示一个字符字面量。例如， 'A'..'Z' 匹配任何大写字母。
- p when e ：仅当 e 评估为 true 时，才匹配 p 。

您可以在[手册](https://ocaml.org/manual/5.2/patterns.html)中阅读有关所有图案形式的信息。

## 使用 Let 进行模式匹配

到目前为止，我们一直在使用的 let 表达式的语法实际上是 OCaml 允许的完整语法的一个特例。该语法是：

```ocaml
let p = e1 in e2
```

也就是说，绑定的左侧实际上可能是一个模式，而不仅仅是一个标识符。当然，变量标识符在我们的有效模式列表中，这就是为什么我们迄今为止学习的语法只是一个特例。

鉴于这种语法，我们重新审视 let 表达式的语义。

**动态语义学**

评估 let p = e1 in e2
- 评估 e1 的值为 v1
- 匹配 v1 与模式 p 。如果不匹配，则引发异常 Match_failure 。否则，如果匹配，则产生一组绑定
 。
- 将 e2 中的绑定 b 替换为新表达式 e2' 。
- 评估 e2' 的值为 v2
- 评估 let 表达式的结果是 v2 。

**静态语义**

- 如果所有以下条件成立，则 (let p = e1 in e2) : t2 ：
  - e1 : t1
  - p 中的模式变量为 x1..xn
  - 在假设对于 1..n 中的所有 i 都成立 xi : ti 的情况下， e2 : t2

**让我们来看一下定义**

与以往一样，let 定义可以被理解为一个 let 表达式，其主体尚未给出。因此，它们的语法可以泛化为

```ocaml
let p = e
```

它们的语义与之前的 let 表达式的语义一致。

## 使用函数进行模式匹配

到目前为止，我们一直在使用的函数语法也是 OCaml 允许的完整语法的一个特例。这种语法是：

```ocaml
let f p1 ... pn = e1 in e2   (* function as part of let expression *)
let f p1 ... pn = e          (* function definition at toplevel *)
fun p1 ... pn -> e           (* anonymous function *)
```

我们需要关心的真正原始的句法形式是 fun p -> e 。让我们重新审视匿名函数的语义及其在该形式下的应用；其他形式的变化源自以下内容：

**静态语义**

- 设 x1..xn 为出现在 p 中的模式变量。如果假设 x1 : t1 和 x2 : t2 和…和 xn : tn ，我们可以得出 p : t 和 e :u 的结论，则 fun p -> e : t -> u 。
- 应用程序的类型检查规则保持不变。

**动态语义**
- 匿名函数的评估规则保持不变。
- 评估 e0 e1
  - 将 e0 评估为匿名函数 fun p -> e ，并将 e1 评估为值 v1
  - 匹配 v1 与模式 p 。如果不匹配，则引发异常 Match_failure 。否则，如果匹配，则产生一组绑定  b。
  - 将 e 中的绑定 b 替换为新表达式 e' 。
  - 评估 e' 的值为 v ，这是评估 e0 e1 的结果。

## 模式匹配示例

<iframe width="791" height="445" src="https://www.youtube.com/embed/3ExRHHqfWm4" title="An ADT for Pokemon | OCaml Programming | Chapter 3 Video 18" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

以下是获取宝可梦生命值的几种方法：

```ocaml
(* Pokemon types *)
type ptype = TNormal | TFire | TWater

(* A record to represent Pokemon *)
type mon = { name : string; hp : int; ptype : ptype }

(* OK *)
let get_hp m = match m with { name = n; hp = h; ptype = t } -> h

(* better *)
let get_hp m = match m with { name = _; hp = h; ptype = _ } -> h

(* better *)
let get_hp m = match m with { name; hp; ptype } -> hp

(* better *)
let get_hp m = match m with { hp } -> hp

(* best *)
let get_hp m = m.hp
```

```ocaml
type ptype = TNormal | TFire | TWater
```

```ocaml
type mon = { name : string; hp : int; ptype : ptype; }
```

```ocaml
val get_hp : mon -> int = <fun>
```

```ocaml
val get_hp : mon -> int = <fun>
```

```ocaml
val get_hp : mon -> int = <fun>
```

```ocaml
val get_hp : mon -> int = <fun>
```

```ocaml
val get_hp : mon -> int = <fun>
```

这是如何获取一对中的第一个和第二个组件：

```ocaml
let fst (x, _) = x

let snd (_, y) = y
```

```ocaml
val fst : 'a * 'b -> 'a = <fun>
```

```ocaml
val snd : 'a * 'b -> 'b = <fun>
```

fst 和 snd 实际上已经在标准库中为您定义好了。

最后，以下是获取三元组的第三个组件的几种方法：

```ocaml
(* OK *)
let thrd t = match t with x, y, z -> z

(* good *)
let thrd t =
  let x, y, z = t in
  z

(* better *)
let thrd t =
  let _, _, z = t in
  z

(* best *)
let thrd (_, _, z) = z
```

```ocaml
val thrd : 'a * 'b * 'c -> 'c = <fun>
```

```ocaml
val thrd : 'a * 'b * 'c -> 'c = <fun>
```

```ocaml
val thrd : 'a * 'b * 'c -> 'c = <fun>
```

```ocaml
val thrd : 'a * 'b * 'c -> 'c = <fun>
```

标准库没有为三元组、四元组等定义任何函数。
