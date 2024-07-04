# 记录和元组

<iframe width="791" height="445" src="https://www.youtube.com/embed/E3d1IFMU558" title="Records and Tuples | OCaml Programming | Chapter 3 Video 3" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


单链表是一种很好的数据结构，但如果您想要固定数量的元素，而不是无限数量呢？或者如果您希望元素具有不同的类型呢？或者如果您希望通过名称而不是数字访问元素呢？列表不会使这些可能性变得容易。相反，OCaml 程序员使用记录和元组。

## 记录

记录是其他类型数据的组合，每个数据都有名称。OCaml 记录类似于 C 语言中的结构体。这里是一个关于宝可梦的记录类型定义的示例，重用了[变体](./ch3_variants.md)部分的定义。

```ocaml
type ptype = TNormal | TFire | TWater
type mon = {name : string; hp : int; ptype : ptype}
```

```ocaml
type ptype = TNormal | TFire | TWater
```

```ocaml
type mon = { name : string; hp : int; ptype : ptype; }
```

这种类型定义了一个记录，其中包含三个字段，分别命名为 `name` ， `hp` （生命值）和 `ptype` 。每个字段的类型也已给出。请注意， `ptype` 可以同时用作类型名称和字段名称；在 OCaml 中，它们的命名空间是不同的。

要构建记录类型的值，我们编写一个记录表达式，看起来像这样:

```ocaml
{name = "Charmander"; hp = 39; ptype = TFire}
```

```ocaml
- : mon = {name = "Charmander"; hp = 39; ptype = TFire}
```

因此，在类型定义中，我们在字段的名称和类型之间使用冒号，但在表达式中我们使用等号。

要访问记录并从中获取字段，我们使用点符号表示法，这是您在许多其他语言中所期望的。例如：

```ocaml
let c = {name = "Charmander"; hp = 39; ptype = TFire};;
c.hp
```

```ocaml
val c : mon = {name = "Charmander"; hp = 39; ptype = TFire}
```

```ocaml
- : int = 39
```

还可以使用模式匹配来访问记录字段：

```ocaml
match c with {name = n; hp = h; ptype = t} -> h
```

```ocaml
- : int = 39
```

这里的 `n` ， `h` 和 `t` 是模式变量。如果您想要为字段和模式变量使用相同的名称，可以提供一种语法糖：

```ocaml
match c with {name; hp; ptype} -> hp
```

```ocaml
- : int = 39
```

这里，模式 `{name; hp; ptype}` 是 `{name = name; hp = hp; ptype = ptype}` 的糖。在这些子表达式中，出现在等号左侧的标识符是字段名，出现在右侧的标识符是模式变量。


<iframe width="791" height="445" src="https://www.youtube.com/embed/gXlWHvEoIvg" title="Record Syntax and Semantics | OCaml Programming | Chapter 3 Video 5" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

**句法**。

记录表达式被写为：

```ocaml
{f1 = e1; ...; fn = en}
```

记录表达式中 `fi=ei` 的顺序是无关紧要的。例如， `{f = e1; g = e2}` 完全等同于 `{g = e2; f = e1}` 。

一个字段访问被写为：

```ocaml
e.f
```

其中 `f` 必须是字段名称的标识符，而不是表达式。这个限制与其他具有类似特性的任何语言相同——例如，Java 字段名称。如果您真的想计算要访问的标识符，那么实际上您需要一个不同的数据结构：一个映射（也被许多其他名称称为：字典或关联列表或哈希表等，尽管每个术语都隐含着微妙的差异。）

**动态语义学**

- 如果对于 `1..n` 中的所有 `i` ，都成立 `ei ==> vi` ，那么 `{f1 = e1; ...; fn = en} ==> {f1 = v1; ...; fn = vn}` 。
- 如果 `e ==> {...; f = v; ...}` ，那么 `e.f ==> v` 。

**静态语义学**

记录类型是这样写的：

```ocaml
{f1 : t1; ...; fn : tn}
```

记录类型中 `fi:ti` 的顺序是无关紧要的。例如， `{f : t1; g : t2}` 完全等同于 `{g:t2;f:t1}` 。

请注意，记录类型必须在使用之前定义。这使得 OCaml 能够进行比如果记录类型可以在没有定义的情况下使用时更好的类型推断。

类型检查规则如下：

- 如果对于 `1..n` 中的所有 `i` ，都满足 `ei : ti` ，并且如果 `t` 被定义为 `{f1 : t1; ...; fn : tn}` ，那么 `{f1 = e1; ...; fn = en} : t` 。请注意，记录表达式中提供的字段集必须是作为记录类型的一部分定义的完整字段集（但请参见下文有关记录复制的内容）。

- 如果 `e : t1` ，并且如果 `t1` 被定义为 `{...; f : t2; ...}` ，那么 `e.f : t2`。

**记录复制**

另外还提供了另一种语法来从旧记录构造新记录：

```ocaml
{e with f1 = e1; ...; fn = en}
```

这不会改变旧记录。相反，它会用新值构建一个新记录。在 `with` 之后提供的字段集合不必是作为记录类型的一部分定义的完整字段集合。在新复制的记录中，任何未作为 `with` 的一部分提供的字段都会从旧记录中复制过来。

记录副本是一种语法糖。它相当于编写

```ocaml
{ f1 = e1;   ...; fn = en;
  g1 = e.g1; ...; gn = e.gn }
```

其中 `gi` 的集合是记录类型的所有字段的集合减去 `fi` 的集合。

** 模式匹配。**

我们将以下新的模式形式添加到合法模式列表中：

- `{f1 = p1; ...; fn = pn}`

我们将模式与值匹配并生成绑定的定义扩展如下：

- 如果对于 `1..n` 中的所有 `i` ，满足 `pi` 匹配 `vi` 并生成绑定 `bi`，那么记录模式 `{f1 = p1; ...; fn = pn}` 匹配记录值 `{f1 = v1; ...; fn = vn; ...}` 并生成绑定集 `Uibi`。请注意，记录值可能比记录模式具有更多的字段。

作为一种语法糖，提供了另一种记录模式的形式： `{f1; ...; fn}` 。它被展开为 `{f1 = f1; ...; fn = fn}` 。

## 元组

与记录类似，元组是其他类型数据的组合。但是，元组不是通过名称来标识组件，而是通过位置来标识。以下是一些元组的示例：

```ocaml
(1, 2, 10)
(true, "Hello")
([1; 2; 3], (0.5, 'X'))
```

具有两个组件的元组称为对。具有三个组件的元组称为三元组。除此之外，通常我们只使用“元组”这个词，而不是继续基于数字的命名方案。

> NOTE:
>
> 超过三个组件后，使用记录而不是元组可能更好，因为程序员很难记住哪个组件应该代表什么信息。

元组的构建很容易：只需像上面那样写出元组。再次访问涉及模式匹配，例如：

```ocaml
match (1, 2, 3) with (x, y, z) -> x + y + z
```

```ocaml
- : int = 6
```

<iframe width="791" height="445" src="https://www.youtube.com/embed/4QNzC2KZ5I4" title="Tuple Syntax and Semantics | OCaml Programming | Chapter 3 Video 6" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

**语法**

元组被写入

```ocaml
(e1, e2, ..., en)
```

括号并非完全必需的 - 通常情况下，您的代码可以成功解析而不使用它们 - 但通常被认为是良好的风格包含它们。

**动态语义学**

- 如果对于 `1..n` 中的所有 `i` 都成立 `ei ==> vi` ，那么 `(e1, ..., en) ==> (v1, ..., vn)` 。

**静态语义学**

元组类型是使用一个新的类型构造函数 `*` 编写的，这与乘法运算符不同。类型 `t1 * ... * tn` 是第一个组件类型为 `t1 ，...`，第 `n` 个组件类型为 `tn` 的元组类型。

- 如果对于 `1..n` 中的所有 `i` 都成立 `ei : ti` ，那么 `(e1, ..., en) : t1 * ... * tn` 。

**模式匹配**

我们将以下新的模式形式添加到合法模式列表中：

- `(p1, ..., pn)`

我们将模式与值匹配并生成绑定的定义扩展如下：

- 如果对于 `1..n` 中的所有 `i` ，都满足 `pi` 与 `vi` 匹配并产生绑定 `bi`，那么元组模式 `(p1, ..., pn)` 与元组值 `(v1, ..., vn)` 匹配并产生绑定集 Uibi。请注意，元组值的组件数量必须与元组模式的相同。

## 变体 vs. 元组和记录

<iframe width="791" height="445" src="https://www.youtube.com/embed/9kyOH1kpmjk" title="Comparison of Data Types | OCaml Programming | Chapter 3 Video 4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="791" height="445" src="https://www.youtube.com/embed/oMOO-cWrHuw" title="Algebraic Data Types | OCaml Programming | Chapter 3 Video 17" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

> NOTE:
>
> 上面的第二个视频使用了更高级的变体示例，这些将在[后面的部分](./ch3_algebraic_data_types.md)中学习。

变体与我们刚学过的类型（记录和元组）之间的重要区别在于变体类型的值是一组可能性中的一个，而元组或记录类型的值提供一组可能性中的每一个。回到我们的例子，类型 `day` 的值是 `Sun` 或 `Mon` 或等等之一。但类型 `mon` 的值提供了 `string` 和 `int` 和 `ptype` 的每一个。请注意，在前两个句子中，单词“或”与变体类型相关联，而单词“和”与元组和记录类型相关联。这是一个很好的线索，如果你曾经试图决定是使用变体，还是元组或记录：如果你需要一个数据或另一个数据，你需要一个变体；如果你需要一个数据和另一个数据，你需要一个元组或记录。

一对类型更常被称为和类型，每对类型被称为[积类型](https://en.wikipedia.org/wiki/Disjoint_union)。这些名称来自集合论。变体类似于不相交并集，因为变体的每个值来自许多基础集合中的一个（因此迄今为止，这些集合中的每一个仅仅是一个构造器，因此基数为一）。不相交并集有时确实用求和运算符来表示。元组/记录类似于[笛卡尔积](https://en.wikipedia.org/wiki/Cartesian_product)，因为元组或记录的每个值包含来自许多基础集合中的一个值。笛卡尔积通常用乘法运算符 `x` 或 `PI`来表示。
