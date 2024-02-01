# 接口

在 Go 中，接口是一组方法签名。当一个类型为接口中的所有方法提供定义时，就说它实现了该接口。

Python、Ruby 和 JavaScript 等动态类型语言使用“鸭子类型”代替接口。Java、C++ 等语言则需要显式声明实现了接口。Go 中的接口介于两者之间，通过不要求类型显式声明实现接口，只需要实现接口的方法，编译器就可以检测到，是一种 “非侵入式”的实现。

```go
package main

import ("fmt")

type IGreeting interface {
    sayHello()
}

func sayHello(i IGreeting) {
    i.sayHello()
}

type Go struct {}
func (g Go) sayHello() {
    fmt.Println("hi, I'm Go")
}

type PHP struct{}
func (p PHP) sayHello() {
    fmt.Println("hi, I'm PHP")
}

func main() {
    golang := Go{}
    php := PHP{}
    sayHello(golang)
    sayHello(php)
}
```

## nil 接口

接口既包含其所代表的底层具体结构的类型，也包含其值。因此，当且仅当它的底层类型和值都是 nil 时，接口才为 nil。

## 空接口

空接口是特殊形式的接口类型，空接口不包含任何方法声明，并且可以接受任何类型的值。因此，我们可以说所有类型都实现空接口。

## 接口实现

方法能给用户自定义的类型添加新的行为。它和函数的区别在于方法有一个接收者，给一个函数添加一个接收者，那么它就变成了方法。接收者可以是值接收者，也可以是指针接收者。

类型通过实现一个接口的所有方法来实现该接口。当调用一个接口的方法时，方法的接收者类型必须与接口具体类型相同，或者接收者类型可以从接口具体类型隐式获得：

* 指针方法可以通过指针调用
* 值方法可以通过值调用
* 接收者是值的方法可以通过指针调用，因为指针会首先被解引用（本质是*T的方法集包含T的方法集）
* 接收者是指针的方法不可以通过值调用，因为存储在接口中的值没有地址（本质是T的方法集不包含*T的方法集）

让我们来看一个例子：

```go
type Stringer interface {
    String() string
}

type MyType struct {
    value string
}

func (m *MyType) String() string { return m.value }
```

`Stringer`接口类型只有一个方法：`String()`。任何存储在`Stringer`接口值中的值都必须有这个方法。我们还创建了一个`MyType`，并创建了一个带有指针接收器的方法`MyType.String()`。这意味着`String()`方法在 `*MyType` 类型的方法集中，但不在`MyType`的方法集中。也就是说，`MyType` 并没有实现接口，是`*MyType`实现了接口。所以，可以看到：

```go
m := MyType{value: "something"}
var s Stringer
s = m // error
s = &m // right
```

注意：一般来说，只有可寻址的值才能使用地址。但 Go 中有一种语法糖，允许我们在复合字面量上取址。语法糖是语法中的一个例外，目的是方便编程。

## 类型断言（Type assertion）

 类型断言是一种在 Go 语言中将接口类型转换为具体类型的操作。通过类型断言，我们可以在运行时判断接口变量的底层类型，并将其转换为指定的类型。类型断言的存在使得我们可以在需要时以正确的类型使用接口变量。

在 Go 语言中，可以使用以下两种语法进行类型断言：

1. `x.(T)`：将接口类型变量`x`转换为类型`T`。
2. `x, ok := y.(T)`：将接口类型`y`转换为类型`T`，并检查转换是否成功。

如果类型断言失败，将会触发运行时错误。为了避免这种情况，我们可以使用第二种语法并检查转换是否成功。

## 类型选择（Type Switch）

类型选择是 Go 语言中一种特殊的 switch 语句，它比较的是类型而不是具体的值。它判断某个接口变量的类型，然后根据具体类型再做相应处理。注意，在类型选择结构的 case 子句中不能使用`fallthrough`。

```go
switch x.(type) {
case Type1:
    doSomeThingWithType1()
case Type2:
    doSomeThingWithType2()
default:
    doSomeDefaultThing()
}
```

其中，x必须是一个接口类型的变量，而所有的 case 语句后面跟的类型必须实现了 x 的接口类型。如果我们不仅想要判断某个接口变量的类型，还想要获得其类型转换后的值的话，可以声明一个变量来获取这个值。

```go
whatAmI := func(i interface{}) {
    switch t := i.(type) {
    case bool:
        fmt.Println("I'm a bool")
    case int:
        fmt.Println("I'm an int")
    default:
        fmt.Printf("Don't know type %T\n", t)
    }
}
```

## 接口内嵌（Embedding Interface）

如同结构体类型可以内嵌结构体类型，接口类型也可以内嵌另一个接口类型。 这相当于直接将这些内嵌接口的方法列举在外层接口中一样。

```go
type Reader interface {
    Read(p []byte) (n int, err errot)
}

type Closer interface {
    Closer() error
}
type ReadCloser interface {
    Reader
    Closer
}
```

参考资料：

* [Go语言的interface](https://blog.chongsheng.art/post/golang/interface/#%E6%8E%A5%E5%8F%A3%E5%80%BC%E5%92%8Cnil%E6%AF%94%E8%BE%83)
* [Go 语言的 Type Switch 语句解析](https://www.bwangel.me/2018/02/03/golang-type-switch/)
* [iface 和 eface 的区别是什么](https://golang.design/go-questions/interface/iface-eface/)
* [X does not implement Y (... method has a pointer receiver)](https://stackoverflow.com/questions/40823315/x-does-not-implement-y-method-has-a-pointer-receiver)
* [Implementing interface in golang gives method has pointer receiver](https://stackoverflow.com/questions/46306888/implementing-interface-in-golang-gives-method-has-pointer-receiver)
* [Structs in Go](https://go101.org/article/struct.html)
