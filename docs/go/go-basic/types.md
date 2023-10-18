# 类型系统

## 类型

类型决定了一组值，以及针对这些值的操作和方法。

## 预声明类型(Pre-declared Types)

Go 提供了一组内置的预声明（命名）基元类型，分别是：数值型、布尔型和字符串类型。

## 具名类型和匿名类型(named type and unamed type)

类型可以用标识符（称为类型名称）或先前声明的类型的组合（称为类型字面量）来表示。在 Golang 中，这两种形式分别称为具名类型和匿名类型。

具名类型可以有自己的方法集。

预声明类型属于具名类型。

## 底层类型(Underlying Type)

所有类型都有底层类型。预声明类型和类型字面量都将自己称为底层类型。声明新类型时，必须基于现有类型。新类型将与现有类型具有相同的底层类型。

另外需要注意的是，新声明的类型不会从底层类型继承任何方法，因此，如果你定义了一个新类型，你也需要为它定义新的方法集。

但是，接口类型、复合类型（a composite type）的元素则会继承方法集。

```go
type interface2 interface1   //interface2 会继承interface1的methods set

type NewType struct {
    InnerType                //NewType 会继承InnerType的methods set
}

```

## 可赋值性(Assignability)

```go
type Mystring string
var str string = "abc"
var my_str MyString = str //gives a compile error
```

在上述情况下不能将`str`赋值给`my_str`，因为`str`和`my_str`属于不同类型。基本上，要给变量赋值，值的类型必须与变量的类型相同。如果两个变量的基本类型相同，并且其中一个是匿名类型，那么也可以为变量赋值。

## 类型内嵌(Type Embedding)

当你声明一个新类型时，它不会继承现有类型的方法集。不过，有一种方法可以在新类型中嵌入现有类型的方法集。这可以通过在结构类型中使用匿名字段的属性来实现。在结构体中定义匿名字段时，其所有字段和方法都将晋升为所定义的结构体类型。

```go
package main

type User struct {
    Id   int
    Name string
}

type Employee struct {
    User       //annonymous field
    Title      string
    Department string
}

func (u *User) SetName(name string) {
    u.Name = name
}

func main(){
    employee := new(Employee)
    employee.SetName("Jack")
}
```

在这里，`User`类型的字段和方法被升级为`Employee`，这样我们就可以在`Employe`类型的实例上调用`SetName`方法。

## 类型转换(Type Conversions)

具名类型和底层类型之间可以进行转换。

```go
type Mystring string

var my_str Mystring = Mystring("awesome")
var str string = string(my_str)
```

参考资料：

* [Learning Go - Types](https://www.laktek.com/2012/01/27/learning-go-types)
* [go 类型系统](https://studygolang.com/articles/6979)
* [Go类型系统概述](https://gfw.go101.org/article/type-system-overview.html)
* [Named and Unnamed Types](https://stackoverflow.com/questions/32983546/named-and-unnamed-types)
* [理解 Go 类型系统](https://sanyuesha.com/2017/07/27/go-type/)
* [Types](https://go.dev/ref/spec#Types)
