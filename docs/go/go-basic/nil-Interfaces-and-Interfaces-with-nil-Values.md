# nil 接口与底层值为 nil 的接口

接口既包含其所代表的底层具体结构的类型，也包含其值。我们使用下面的插图来形象地说明这种关系。

![interface](https://trstringer.com/images/go-interface1-type-value.png)

假设我们存在如下接口及其实现；

```go
type messagePrinter interface {
    printMessage()
}

type thing struct {
    message string
}

func (t thing) printMessage() {
    fmt.Printf("Message: %s\n", t.message)
}
```

现在我们可以使用辅助函数`analyzeInterface`接收任何实现了`messagePrinter`接口的实参，并打印接口本身的一些信息：

```go
func analyzeInterface(mp messagePrinter) {
    fmt.Printf("Interface type: %T\n", mp)
    fmt.Printf("Interface value: %v\n", mp)
    fmt.Printf("Interface is nil: %t\n", mp == nil)
}
```

示例一：

```go
t1 := &thing{message: "hello"}
analyzeInterface(t1)
```

查看`analyzeInterface`的输出：

```go
Interface type: *main.thing
Interface value: &{hello}
Interface is nil: false
```

在这段代码中，`messagePrinter`接口的类型被设置为 `*thing`，而且它具有一个值，因此，接口和底层数据结构都不是nil。该接口可以形象表示为：

![示例一](https://trstringer.com/images/go-interface2-non-nil-all.png)

示例二：

```go
var t2 *thing
analyzeInterface(t2)
```

执行`analyzeInterface`函数，输出为：

```go
Interface type: *main.thing
Interface value: <nil>
Interface is nil: false
```

与示例一不同，尽管底层数据结构是一个具有 nil 值的指针，但接口本身并不是 nil。因此，接口可表示为：

![示例二](https://trstringer.com/images/go-interface3-nil-underlying-type.png)

示例三：

```go
analyzeInterface(nil)
```

`analyzeInterface`的输出为：

```go
Interface type: <nil>
Interface value: <nil>
Interface is nil: true
```

这种情况下，接口底层数据结构为 nil，同时接口本身也为 nil。接口可以表示为：

![示例三](https://trstringer.com/images/go-interface4-nil-interface.png)

参考资料：

* [Understanding nil Interfaces and Interfaces with nil Values in Go](https://trstringer.com/go-nil-interface-and-interface-with-nil-concrete-value/)
