# Example: Tree

<iframe width="791" height="445" src="https://www.youtube.com/embed/WV9DGpRTAE0" title="Binary Trees | OCaml Programming | Chapter 3 Video 23" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

树是一种非常有用的数据结构。二叉树，正如你在 CS 2110 中所记得的那样，是一个包含值和两个子树的节点。二叉树也可以是一棵空树，我们也用它来表示没有子节点的情况。

## 用元组表示

这里是二叉树数据类型的定义：

```ocaml
type 'a tree =
| Leaf
| Node of 'a * 'a tree * 'a tree
```

```ocaml
type 'a tree = Leaf | Node of 'a * 'a tree * 'a tree
```

一个节点携带类型为 `'a` 的数据项，并具有左子树和右子树。叶子为空。将此定义与列表的定义进行比较，注意它们结构是多么相似：

```ocaml
type 'a tree =                        type 'a mylist =
  | Leaf                                | Nil
  | Node of 'a * 'a tree * 'a tree      | Cons of 'a * 'a mylist
```

唯一的基本区别在于 `Cons` 包含一个子列表，而 `Node` 包含两个子树。

这里是构建一个小树的代码：

```ocaml
(* the code below constructs this tree:
         4
       /   \
      2     5
     / \   / \
    1   3 6   7
*)
let t =
  Node(4,
    Node(2,
      Node(1, Leaf, Leaf),
      Node(3, Leaf, Leaf)
    ),
    Node(5,
      Node(6, Leaf, Leaf),
      Node(7, Leaf, Leaf)
    )
  )
```

```ocaml
val t : int tree =
  Node (4, Node (2, Node (1, Leaf, Leaf), Node (3, Leaf, Leaf)),
   Node (5, Node (6, Leaf, Leaf), Node (7, Leaf, Leaf)))
```

树的大小是指其中节点的数量（即 `Node` 个，而不是 Leaf 个）。例如，上面树 `t` 的大小为 7。这里是一个函数 `size : 'a tree -> int` ，用于返回树中节点的数量：

```ocaml
let rec size = function
  | Leaf -> 0
  | Node (_, l, r) -> 1 + size l + size r
```

## 记录表示

接下来，让我们修改我们的树类型，使用记录类型来表示树节点。在 OCaml 中，我们必须定义两个相互递归的类型，一个用于表示树节点，另一个用于表示（可能为空的）树：

```ocaml
type 'a tree =
  | Leaf
  | Node of 'a node

and 'a node = {
  value: 'a;
  left: 'a tree;
  right: 'a tree
}
```

```ocaml
type 'a tree = Leaf | Node of 'a node
and 'a node = { value : 'a; left : 'a tree; right : 'a tree; }
```

这是一个示例树：

```ocaml
(* represents
      2
     / \
    1   3  *)
let t =
  Node {
    value = 2;
    left = Node {value = 1; left = Leaf; right = Leaf};
    right = Node {value = 3; left = Leaf; right = Leaf}
  }
```

```ocaml
val t : int tree =
  Node
   {value = 2; left = Node {value = 1; left = Leaf; right = Leaf};
    right = Node {value = 3; left = Leaf; right = Leaf}}
```

我们可以使用模式匹配来编写通常用于递归遍历树的算法。例如，这里是对树进行递归搜索的一个示例：

```ocaml
(** [mem x t] is whether [x] is a value at some node in tree [t]. *)
let rec mem x = function
  | Leaf -> false
  | Node {value; left; right} -> value = x || mem x left || mem x right
```

```ocaml
val mem : 'a -> 'a tree -> bool = <fun>
```

函数名称 `mem` 是“成员”的缩写；标准库通常使用这个名称的函数来实现对集合数据结构的搜索，以确定某个元素是否是该集合的成员。

这是一个计算树的先序遍历的函数，其中每个节点在其任何子节点之前被访问，通过构建一个列表，其中值按照它们被访问的顺序出现：

```ocaml
let rec preorder = function
  | Leaf -> []
  | Node {value; left; right} -> [value] @ preorder left @ preorder right
```

```ocaml
val preorder : 'a tree -> 'a list = <fun>
```

```ocaml
preorder t
```

```ocaml
- : int list = [2; 1; 3]
```

尽管上面的代码中算法非常清晰，但由于 `@` 运算符，在不平衡树上它需要二次时间。这个问题可以通过引入一个额外的参数 `acc` 来解决，在每个节点累积值，尽管这会使代码变得不太清晰。

```ocaml
let preorder_lin t =
  let rec pre_acc acc = function
    | Leaf -> acc
    | Node {value; left; right} -> value :: (pre_acc (pre_acc acc right) left)
  in pre_acc [] t
```

```ocaml
val preorder_lin : 'a tree -> 'a list = <fun>
```

上述版本在树中每个 `Node` 使用了恰好一个 `::` 操作，使其具有线性时间。
