# Beyond Lists

<iframe width="791" height="445" src="https://www.youtube.com/embed/5Yyk-l-cUNI" title="Trees with Map and Fold | OCaml Programming | Chapter 4 Video 7" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

像 map 和 fold 这样的功能并不局限于列表。它们对几乎任何类型的数据收集都是有意义的。例如，回想一下这棵树的表示：

```
type 'a tree =
  | Leaf
  | Node of 'a * 'a tree * 'a tree
```

```
type 'a tree = Leaf | Node of 'a * 'a tree * 'a tree
```

## Map on Trees

这很容易。我们只需要在每个节点上将函数 f 应用于值 v 。

```
let rec map_tree f = function
  | Leaf -> Leaf
  | Node (v, l, r) -> Node (f v, map_tree f l, map_tree f r)
```

```
val map_tree : ('a -> 'b) -> 'a tree -> 'b tree = <fun>
```

## Fold on Trees

这个稍微难一点。让我们为 'a tree 开发一个类似于我们在 'a list 上的 fold_right 的折叠功能。一种思考 List.fold_right 的方式是列表中的 [] 值被 acc 参数替换，每个 :: 构造函数被 f 参数的应用所替换。例如， [a; b; c] 是 a :: (b :: (c :: [])) 的语法糖。因此，如果我们用 0 替换 [] ，用 ( + ) 替换 :: ，我们得到 a + (b + (c + 0)) 。沿着这些思路，这是我们可以重写 fold_right 的一种方式，这将帮助我们更清晰地思考一下：

```
type 'a mylist =
  | Nil
  | Cons of 'a * 'a mylist

let rec fold_mylist f acc = function
  | Nil -> acc
  | Cons (h, t) -> f h (fold_mylist f acc t)
```

```
type 'a mylist = Nil | Cons of 'a * 'a mylist
```

```
val fold_mylist : ('a -> 'b -> 'b) -> 'b -> 'a mylist -> 'b = <fun>
```

算法是相同的。我们所做的只是改变了列表的定义，使用用字母字符编写的构造函数，而不是标点符号，并改变了 fold 函数的参数顺序。

对于树，我们希望将 acc 的初始值替换每个 Leaf 构造函数，就像它在列表中替换 [] 一样。我们希望每个 Node 构造函数被操作符替换。但现在操作符将需要是三元的而不是二元的 - 也就是说，它需要接受三个参数而不是两个 - 因为树节点有一个值，一个左子节点和一个右子节点，而列表的 cons 只有一个头部和一个尾部。

受到这些观察的启发，这里是树的折叠函数：

```
let rec fold_tree f acc = function
  | Leaf -> acc
  | Node (v, l, r) -> f v (fold_tree f acc l) (fold_tree f acc r)
```

```
val fold_tree : ('a -> 'b -> 'b -> 'b) -> 'b -> 'a tree -> 'b = <fun>
```

如果将该函数与 fold_mylist 进行比较，您会注意到它们几乎是相同的。第二个模式匹配分支中只多了一个递归调用，对应于在该类型定义中 'a tree 的一个额外出现。

我们可以使用 fold_tree 来实现我们之前看到的一些树函数：

```
let size t = fold_tree (fun _ l r -> 1 + l + r) 0 t
let depth t = fold_tree (fun _ l r -> 1 + max l r) 0 t
let preorder t = fold_tree (fun x l r -> [x] @ l @ r) [] t
```

```
val size : 'a tree -> int = <fun>
```

```
val depth : 'a tree -> int = <fun>
```

```
val preorder : 'a tree -> 'a list = <fun>
```

为什么我们选择 fold_right 而不是 fold_left 进行这个开发？因为 fold_left 是尾递归的，这是我们永远无法在二叉树上实现的。假设我们首先处理左分支；然后我们仍然必须在返回之前处理右分支。因此，在对一个分支进行递归调用后，总会有剩余的工作要做。因此，在树上， fold_right 的等价物是我们所能期望的最好的。

我们用来推导 fold_tree 的技术适用于任何 OCaml 变体类型 t ：

- 编写一个递归 fold 函数，该函数接受 t 的每个构造函数的一个参数。
- 该 fold 函数与构造函数匹配，在遇到任何类型为 t 的值时会递归调用自身。
- 使用 fold 的适当参数来组合所有递归调用的结果，以及在每个构造函数中不属于 t 类型的所有数据。

这种技术构建了一种称为 catamorphism 的东西，又称为广义折叠操作。要了解更多关于 catamorphisms 的信息，请参加范畴论课程。

## Filter on Trees

这可能是最难设计的一个。问题是：如果我们决定过滤一个节点，那么我们应该怎么处理它的子节点？

- 我们可以对子节点进行递归。如果在筛选它们后只剩下一个子节点，我们可以将其提升为其父节点的位置。但如果两个子节点都保留下来，或者一个都没有呢？那么我们就必须以某种方式重塑树形结构。如果不了解树应该如何使用——也就是说，它代表什么样的数据，我们就会陷入困境。
- 相反，我们可以完全消除子节点。因此，过滤节点的决定意味着修剪以该节点为根的整个子树。

后者易于实施：

```
let rec filter_tree p = function
  | Leaf -> Leaf
  | Node (v, l, r) ->
    if p v then Node (v, filter_tree p l, filter_tree p r) else Leaf
```

```
val filter_tree : ('a -> bool) -> 'a tree -> 'a tree = <fun>
```
