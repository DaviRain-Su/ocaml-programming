# 关联列表

地图是一种将键映射到值的数据结构。地图也被称为字典。地图的一种简单实现是关联列表，它是一对列表。这里，例如，是一个将一些形状名称映射到它们拥有的边数的关联列表：

```ocaml
let d = [("rectangle", 4); ("nonagon", 9); ("icosagon", 20)]
```

```ocaml
val d : (string * int) list =
  [("rectangle", 4); ("nonagon", 9); ("icosagon", 20)]
```

请注意，关联列表在 OCaml 中并不是一种内置数据类型，而是两种其他类型的组合：列表和对。

这里有两个函数，用于在关联列表中实现插入和查找操作：

```ocaml
(** [insert k v lst] is an association list that binds key [k] to value [v]
    and otherwise is the same as [lst] *)
let insert k v lst = (k, v) :: lst

(** [lookup k lst] is [Some v] if association list [lst] binds key [k] to
    value [v]; and is [None] if [lst] does not bind [k]. *)
let rec lookup k = function
| [] -> None
| (k', v) :: t -> if k = k' then Some v else lookup k t
```

```ocaml
val insert : 'a -> 'b -> ('a * 'b) list -> ('a * 'b) list = <fun>
```

```ocaml
val lookup : 'a -> ('a * 'b) list -> 'b option = <fun>
```

insert 函数只是简单地在列表的前面添加一个从键到值的新映射。它不会去检查键是否已经在列表中。 lookup 函数从左到右遍历列表。因此，如果列表中恰好有多个给定键的映射，只会返回最近插入的那个。

插入在关联列表中是常数时间，查找是线性时间。虽然字典有更高效的实现方式，我们将在课程后面学习一些，但关联列表是一个非常简单和有用的实现方式，适用于不需要高性能的小型字典。OCaml 标准库在 List 模块中有关联列表的函数；在文档中查找 List.assoc 和其下面的函数。我们刚刚写的 lookup 实际上已经被定义为 List.assoc_opt 。标准库中没有预定义的 insert 函数，因为在上面简单地将一对元素连接起来是如此微不足道。
