# 为什么我可以调用`nil`指针上的方法？

实际上，在 Go 中，`Expression.Name()`语法调用的函数完全由`Expression`类型决定，而不是由该表达式的特定运行时值（包括`nil`）决定。考虑：

```go
func (p *Sometype) Somemethod (firstArg int) {}
```

具有字面意义:

```go
func SometypeSomemethod(p *Sometype, firstArg int) {}
```

从这个角度来看，`SometypeSomemethod()`函数的第一个参数`p *Sometype`当然可以是`nil`或者非`nil`。但要注意的是，`nil`值调用方法必须拥有预期类型的上下文。在 Go 中，直接使用`nil.Somemethod`会引起`panic`，因为无类型`nil`值没有隐式的`Sometype`来将`Somemethod()`调用扩展为`SometypeSomemethod()`。

因此，执行以下代码：

```go
package main

import "fmt"

type T struct {
    V int
}

func (t *T) hello() string {
    return "world"
}

func main() {
    var t *T
    fmt.Println(t, t.hello())
}
```

正常输出：

```go
<nil> world
```

参考资料：

* [Why can I call methods on nil pointers?](https://groups.google.com/g/golang-nuts/c/wcrZ3P1zeAk/m/WI88iQgFMvwJ)
* [Calling a method on a nil struct pointer doesn't panic. Why not?](https://stackoverflow.com/questions/42238624/calling-a-method-on-a-nil-struct-pointer-doesnt-panic-why-not)
