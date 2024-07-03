# Type Synonyms

类型同义词是对已经存在的类型的新名称。例如，以下是一些在表示线性代数中的一些类型时可能有用的类型同义词：

```ocaml
type point = float * float
type vector = float list
type matrix = float list list
```

```ocaml
type point = float * float
```

```ocaml
type vector = float list
```

```ocaml
type matrix = float list list
```

无论何处需要 float * float ，您都可以使用 point ，反之亦然。这两者可以完全互换。在下面的代码中， get_x 不在乎您传递的是被注释为其中一个还是另一个的值：

```ocaml
let get_x = fun (x, _) -> x

let p1 : point = (1., 2.)
let p2 : float * float = (1., 3.)

let a = get_x p1
let b = get_x p2
```

```ocaml
val get_x : 'a * 'b -> 'a = <fun>
```

```ocaml
val p1 : point = (1., 2.)
```

```ocaml
val p2 : float * float = (1., 3.)
```

```ocaml
val a : float = 1.
```

```ocaml
val b : float = 1.
```

类型同义词很有用，因为它们让我们为复杂类型赋予描述性名称。它们是使代码更具自我说明性的一种方式。
