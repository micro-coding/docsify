# 泛型

## 介绍

Go 1.18 版本增加了对泛型（Generics）的支持，在本文中，我们将介绍新的语言特性。我们不会尝试覆盖所有细节，但会涉及所有重要的点。

泛型是一种编程范式，这种范式与特定的类型无关，泛型允许在函数和类型的实现中使用某个类型集合中的任何一种类型。

泛型为 Go 语言添加了三个重要的新功能：

1. 函数和类型的类型形参。
2. 将接口类型定义为一组类型的集合，包括没有方法的类型。
3. 类型推导，允许在多数情况下调用函数时省略类型实参。


## 类型形参（Type Parameters）

Go 函数和类型现在可以具有类型形参（type Parameters）。类型形参列表与普通的函数形参列表相似，只是用方括号代替了括号。

这是一个求最小值的普通函数：

```go
func Min(x, y float64) float64{
    if x < y {
        return x
    }
    return y
}
```

我们可以通过添加一个类型形参列表来使这个函数泛化，使它能够适用于不同的类型。在本例中，我们添加了一个类型形参列表，其中包含一个类型形参（type parameter） T，约束（constraint on type）constraints.Ordered，并将 float64 替换为 T。

```go
import "golang.org/x/exp/constraints"

func GMin[T constraints.Ordered](x, y T) T {
    if x < y {
        return x
    }
    return y
}
```

现在可以像下面代码那样，使用类型实参（type argument）来调用该函数：

```go
x := GMin[int](2, 3)
```

向 GMin 提供类型实参（本例中为 int）称为实例化。实例化分两步进行。首先，编译器会将泛型函数或类型中所有类型形参替换为对应的类型实参。其次，编译器会验证每个类型实参是否满足对应的类型约束（constraint on type）。实例化成功后，我们可以得到一个非泛型函数，可以像使用普通函数一样调用它：

```go
fmin := Gmin[float64]
m := fmin(2.71, 3.14)
```

可以看到，GMin[float64] 的实例化生成的 fmin 函数等价于最初的 Min 函数。

类型也可以具有类型形参。

```go
type Tree[T interface{}] struct{
    left, right *Tree[T]
    value T
}

func (t *Tree[T]) Lookup(x T) *Tree[T] {
    ...
}

var stringTree Tree[string]

```

在这里，泛型类型 Tree 存储了类型参数 T 的值。泛型类型可以有方法，就像本例中的 Lookup。为了使用泛型，必须将其实例化：Tree[string] 就是使用类型实参 string 实例化泛型类型 Tree 的例子。

## 类型集合（Type sets）

接下来，我们深入了解一下用来实例化类型形参的类型实参。

对于普通函数，每个值形参（value parameter，相对于类型形参 type parameter）都有一个类型，该类型定义了一组值。例如，上面的非泛型函数 Min 有一个float64 类型的形参，那么函数 Min 允许的实参值集合就是 float64 类型可以表示的浮点数集合。

同样，类型形参列表中的每个类型形参都有一个类型。因为类型形参本身就是一种类型，所以类型形参的类型定义了类型集。这种元类型称为类型约束（type constraint）。

在泛型函数 GMin 中，类型约束 `constraints.Ordered` 是从包 `constraints` 中导入的。`constraints.Ordered` 描述了所有值可排序的类型的集合。该约束确保了只有具有可排序值的类型才能传递给 GMin。这意味着，在 GMin 函数体中，该类型形参的值可使用比较运算符进行比较。

在 Go 中，类型约束必须是接口。也就是说，接口类型可以用作值类型，也可以用作元类型。接口定义了方法，因此，显然可以作为要求某些方法存在的类型约束。但是，`constraints.Ordered` 也是一种接口类型，但使用比较操作符并不是一种方法。

为了实现这一点，我们将以一种新的方式来看待接口。

通常观点认为，接口定义了一个方法集，任何实现方法集中所有方法的类型都实现该接口。

![method-set](https://go.dev/blog/intro-generics/method-sets.png)

但在另一种观点中，接口定义的是一组类型，即实现这些方法的类型。从这个角度看，接口类型集的任何类型都实现了该接口。

![type-sets](https://go.dev/blog/intro-generics/type-sets.png)

这两种观点的结果是一样的：对于每一组方法，我们都可以想象出实现这些方法的相应类型集，这就是接口定义的类型集。

不过，就我们的目的而言，类型集（type set）观点比方法集（method set）观点更有优势：我们可以明确地将类型添加到集合中，从而以新的方式控制类型集。

为了实现这一点，我们扩展了接口类型的语法。例如，`interface{ int|string|bool }` 定义了包含 int、string 和 bool 类型的类型集。

![type-sets-2](https://go.dev/blog/intro-generics/type-sets-2.png)

换句话说，只有 int、string 或 bool 类型才能满足该接口的要求。

现在我们来看看 constraints.Ordered 的实际定义：

```go
type Ordered interface {
    Integer|Float|~string
}
```

该定义表明 `Ordered` 接口是所有整数、浮点和字符串类型的集合。竖线`|`表示类型的联合（或在本例中表示类型集）。Integer 和 Float 是接口类型，在约束包中有类似的定义。请注意，Ordered 接口没有定义方法。

对于类型约束，我们通常并不关心某一种特定的类型，例如 `string`。我们感兴趣的是所有的字符串类型。这就是 `~` 标记的作用。表达式 `~string` 表示底层类型为`string`的所有类型的集合。这包括`string`类型本身，以及所有使用类似`type Mystring string`定义的类型。

当然，仍然可以在接口中指定方法。因此，在 Go 1.18 中，接口可以像以前一样包含方法和嵌入接口，但也可以嵌入非接口类型、联合（`|`）和底层类型集(`~type`)。

在将接口用作类型约束时，接口定义的类型集明确指定了可以作为类型形参的类型实参的类型。在泛型函数体中，如果操作数的类型是带有约束 C 的类型形参 P，对操作数进行的操作仅能是约束 C 定义的类型集中所有类型都允许的操作。（目前这里有一些实现限制，但普通代码不太可能遇到这些限制）。

作为约束使用的接口可以给定名称（如 Ordered），也可以是内联类型参数列表的字面量接口。例如：

```go
[S interface{~[]E}, E interface{}]
```

这里的 S 必须是一个切片类型，切片元素可以是任何类型。

因为这是一种常见的场景，因此，对于处于约束位置的接口，可以省略外层的 interface{}，写为：

```go
[S ~[]E, E interface{}]
```

此外，由于空接口在类型形参列表和普通 Go 代码中都很常见，Go 1.18 引入了一个新的预定义标识符 any 作为空接口类型的别名。这样，我们就得到了这段惯用代码：

```go
[S ~[]E, E any]
```

接口作为类型集是一种强大的新机制，也是在 Go 中实现类型约束的关键。目前，使用新语法形式的接口只能用作约束。

## 类型推导（Type inference）

最后一个新的主要语言特性是类型推导。在某种程度上，这是语言最复杂的变化，但它却非常重要，因为它让人们在编写调用泛型函数的代码时使用一种自然的风格。

### 函数实参类型推导（Function argument type inference）

有了类型形参，就需要传递类型实参，这会导致代码冗长。以 GMin 函数为例：

```go
func Gmin[T constraints.Ordered] (x, y t) T {
    ...
}
```

类型形参 T 用于指定普通非类型实参（non-type arguments） x 和 y 的类型。如前所示，可以使用显式类型实参来调用它。

```go
var a, b, m float64

m = GMin[float64](a, b) // explicit type argument

```

多数情况下，编译器可以从普通实参中推导出 T 的类型实参。这是的代码更短，同时保持清晰。

```go
var a, b, m float64

m = GMin(a, b) // no type argument
```

其工作原理是将函数实参 a 和 b 的类型与形参 x 和 y 的类型相匹配。

这种根据函数实参（function arguments，或者叫 value arguments，或者叫 non-type arguments）的类型推导类型实参的方法称为函数实参类型推理。

函数实参类型推导只适用于在函数形参（function parameters）中使用的类型形参（type parameters），不适用于仅在函数结果中或仅在函数体中使用的类型形参。例如，它不适用于 `MakeT[T any]() T` 这样只使用 T 作为结果的函数。

### 约束类型推导（Constraint type inference）

Go 语言还支持另一种类型推导，即约束类型推导。为了说明这一点，让我们从缩放整数切片的例子开始：

```go
// Scale returns a copy of s with each element multiplied by c.
// This implementation has a problem, as we will see.
func Scale[E constraints.Integer](s []E, c E) []E {
    r := make([]E, len(s))
    for i, v := range s {
        r[i] = v * c
    }
    return r
}
```

这是一个泛型函数，适用于任何整数类型的切片。

现在，假设我们有一个多维 Point 类型，其中每个 Point 都是一个简单的整数列表，给出了该点的坐标。这种类型自然会有一些方法。

```go
type Point []int32

func (p Point) String() string {
    // Details not important.
}
```

现在，我们想缩放一个 Point。由于 Point 是一个整数切片，因此我们可以使用之前编写的 Scale 函数：

```go
// ScaleAndPrint doubles a Point and prints it.
func ScaleAndPrint(p Point) {
    r := Scale(p, 2)
    fmt.Println(r.String()) // DOES NOT COMPILE
}
```

不幸的是，代码无法通过编译，问题在于 Scale 函数返回一个 `[]E` 类型的值，其中 E 是实参切片的元素类型。当我们使用 Point 类型(底层类型为 `[]int32`)的值调用 Scale 时，返回的是 `[]int32` 类型的值，而不是 Point 类型的值。这是泛型代码的编写方式导致的，但并不是我们想要的结果。

为了解决这个问题，我们必须修改 Scale 函数，使用一个类型形参代替切片类型。

```go
// Scale returns a copy of s with each element multiplied by c.
func Scale[S ~[]E, E constraints.Integer](s S, c E) S {
    r := make(S, len(s))
    for i, v := range s {
        r[i] = v * c
    }
    return r
}
```

我们引入了一个新的类型形参 S，即切片实参的类型。非类型形参的底层类型现在是 S 而非 []E， 返回值类型也是 S。由于 E 被限制为整数，因此效果和之前相同：第一个实参必须是某个整数类型的切片。函数体唯一的变化是调用 make 时传递的是 S 而不是 []E 。

如果我们使用普通切片调用新函数，它的行为与以前相同，但如果我们使用类型 Point 调用它，现在会得到一个 Point 类型的值。这正是我们想要的。使用这个版本的 Scale 函数，之前的 ScaleAndPrint 函数将会编译并按我们的预期进行。

但是，为什么调用 Scale 可以不显示传递类型参数？也就是说，为什么我们可以写成 `Scale(p, 2)`， 而不必写成`Scale[Point, int32](p, 2)`？我们的新函数有两个类型形参：S 和 E。在不传递类型实参调用 Scale 函数时，函数实参类型推导可以让编译器推导出 S 的类型实参是 Point。但是函数还有一个类型形参 E，它是乘数 c 的类型。相应的函数实参是 2，因为2是一个无类型常量，所以函数实参类型推导无法推导出正确的 E 类型（最多只能推导出2的默认类型是 int，这是不正确的）。相反，编译器推导出类型形参 E 是切片的元素类型的过程称为约束类型推导。

约束类型推导从类型形参约束中推导出类型实参。当一个类型形参的约束是使用另一个类型形参写出的形式时，就会用到这种推导方法。当类型形参其中之一对应的类型实参已知时，就可以利用约束关系推导其它类型形参的类型实参。

这种情况通常发生在约束使用形式`~type`对某种类型进行约束，而该类型时使用另一种类型形参编写的情况。以 Scale 函数为例，S 的约束是 `~[]E`， 即 `~` 后面跟一个另一个类型参数 E 写成的 `[]E`形式。如果知道 S 的类型实参，我们就可以推导出 E 的类型实参。S 是切片类型，E 是该切片元素的类型。

这些只是约束类型推导的简单介绍，有关详细信息，请参阅[提案文件](https://go.googlesource.com/proposal/+/HEAD/design/43651-type-parameters.md)或[语言规范](https://go.dev/ref/spec)。

参考资料：

* [An Introduction To Generics](https://go.dev/blog/intro-generics)
* [Go泛型介绍[译]](https://tonybai.com/2022/03/25/intro-generics/)
* [PARAMETER和ARGUMENT的区别](https://wduo.github.io/2017/09/11/Differences-between-parameters-and-argument/)
