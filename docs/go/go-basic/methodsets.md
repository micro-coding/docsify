# 方法集

## 介绍

特定类型或值的方法集在 Go 中尤为重要，因为方法集决定了一个值实现了哪些接口。

## 规范

[Go 语言规范](https://go.dev/ref/spec)中有两个关于方法集的重要条款。它们如下：

方法集：一个类型可以有一个与之关联的方法集。接口类型的方法集就是它的接口。任何其他命名类型(named type) `T` 的方法集由接收者类型为 `T` 的所有方法组成。相应的指针类型 `*T`的方法集是接收者为 `*T`或`T` 的所有方法的集合（也就是说，它也包含 `T` 的方法集）。任何其他类型的方法集都是空的。在方法集中，每个方法必须有唯一的名称。

调用：如果`x`的方法集包含 `m`，并且参数列表可以赋值给`m`的参数列表，则方法调用`x.m()`是有效的。如果`x`是可寻址的，且`&x`的方法集包含`m`，则`x.m()`是`(&x).m()`的简写形式。

## 使用

### 变量

对于变量类型，指针方法和值接收者方法都可以在指针和值对象上调用。

```go
type List []int

func (l List) Len() int        { return len(l) }
func (l *List) Append(val int) { *l = append(*l, val) }

func main() {
    // A bare value
    var lst List
    lst.Append(1)
    fmt.Printf("%v (len: %d)\n", lst, lst.Len())

    // A pointer value
    plst := new(List)
    plst.Append(2)
    fmt.Printf("%v (len: %d)\n", plst, plst.Len())
}
```

通过上面的说明，我们可以获取类型`T`和`*T`的方法集:

```go
List
- Len() int

*List
- Len() int
- Append(int) 
```

可以看到，`List`的方法集实际上并不包含`Append(int)`，然而方法`lst.Append(1)`依然可以顺利调用，这是上面第二条规范造成的结果。它将下面的第一行隐式地转换成了第二行，因此，调用是合法的。

```go
lst.Append(1)
(&lst).Append(1)
```

综述，对指针或可寻址对象，调用指针接收者方法合法。对值或可解引用对象，调用值接收者方法合法。

### 切片元素

切片元素与变量几乎相同。因为它们是可寻址的，因此指针和值接收者方法都可以在指针和值元素上调用。

### map 元素

map 元素不可寻址。因此，以下调用会失败;

```go
lists := map[string]List{}
lists["primes"].Append(7) // cannot be rewritten as (&lists["primes"]).Append(7)
```

但是以下调用时合法的:

```go
lists := map[string]*List{}
lists["primes"] = new(List)
lists["primes"].Append(7)
count := lists["primes"].Len() // can be rewritten as (*lists["primes"]).Len()
```

因此，指针和值接收者方法都可以在 map 的指针元素上调用，但只有值接收者方法可以在 map 的值元素上调用。这就是带有结构体元素的映射几乎总是由指针元素构成的原因。

### 接口

与 map 元素相似，接口的具体值不可寻址。这是因为接口是一种高度抽象的类型，它隐藏了具体值的底层细节。当调用一个接口的方法时，方法的接收者类型必须与接口具体类型相同，或者接收者类型可以从接口具体类型隐式获得。指针接收者和值接收者方法可以分别用指针和值来调用，值接收者方法可以被指针调用，因为他们会首先被解引用。指针接收者方法不能被值类型的接口调用，因为接口的具体值不可寻址。将值分配给接口时，编译器确保所有可能的接口方法实际上都可以在该值上调用，因此试图进行不恰当的分配，编译过程将失败。

```go
type List []int

func (l List) Len() int        { return len(l) }
func (l *List) Append(val int) { *l = append(*l, val) }

type Appender interface {
    Append(int)
}

func CountInto(a Appender, start, end int) {
    for i := start; i <= end; i++ {
        a.Append(i)
    }
}

type Lener interface {
    Len() int
}

func LongEnough(l Lener) bool {
    return l.Len()*10 > 42
}

func main() {
    // A bare value
    var lst List
    CountInto(lst, 1, 10) // INVALID: Append has a pointer receiver
    if LongEnough(lst) {  // VALID: Identical receiver type
        fmt.Printf(" - lst is long enough")
    }

    // A pointer value
    plst := new(List)
    CountInto(plst, 1, 10) // VALID: Identical receiver type
    if LongEnough(plst) {  // VALID: a *List can be dereferenced for the receiver
        fmt.Printf(" - plst is long enough")
    }
}
```

参考资料：

* [MethodSets](https://github.com/golang/go/wiki/MethodSets)
