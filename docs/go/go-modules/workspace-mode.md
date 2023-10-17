# Go 工作区模式

Go 项目依赖管理中，有两个典型问题难以处理。

1. 依赖一个本地修改过的模块
2. 依赖一个本地未发布的模块

## Replace module

在 repalce module 方案中，我们使用 `replace` 指令使用本地模块替换依赖。go.mod 文件示例：

```mod
replace golang.org/x/net => /Users/eddycjy/go/awesomeProject
```

这种方案会带来以下问题：

* 本地路径：`replace` 指定的是本地地址，这意味着多个开发者可能具有不同的路径。
* 仓库依赖：文件更改如果被不小心上传到 Git 仓库，会影响到其他开发者。因此必须在每次上传撤销修改。

## Publish module

此方案下，用户必须及时上传依赖的模块，再重新拉取。该方案需要开发者频繁提交代码，严重拖慢开发效率。

## Workspace mode

Go 1.18 发布了 Multi-Module Workspaces 模式。开发者可以在当前项目的 go.work 文件中设置一系列依赖 module 的本地路径，然后路径下的所有 module 合并为 workspace，即 N 个 Go 模块组成 1 个 Go Work，且 workspace 具有最高优先级。

可以使用 `go work init` 来创建一个 workspace，参数是 Go Module 所在的本地目录。如果有多个Go Module，就用空格分开。如果go work init后面没有参数，会创建一个空的workspace。

```go
go work init ./mod ./tools
```

项目结构如下：

```file
awesomeProject
├── mod
│   ├── go.mod      // 子模块
│   └── main.go
├── go.work         // 工作区
└── tools
    ├── fish.go
    └── go.mod      // 子模块
```

生成的 go.work 文件的内容如下：

```go
go 1.18

use (
    ./mod 
    ./tools
)
```

使用 `replace` 替换依赖：

```go
go 1.18

use (...)

replace golang.org/x/net => example.com/fork/net v1.4.5

```

go.work 的语法和 go.mod类似，支持3个指令：

* go:声明go版本号，主要用于后续新语义的版本控制。
* use：声明应用程序所依赖的模块的具体文件路径。该路径可以是绝对路径或相对路径，并且可以位于应用程序的目标目录之外。
* replace：声明替换模块依赖的导入路径，优先级高于go.mod中的replace指令

通常情况下，不要提交go.work到git上，因为它主要用于本地代码开发，因此可以将它添加到 .gitignore 文件中以忽略提交。

参考资料：

* [New in Go 1.18: Multi-Module workspace mode](https://www.sobyte.net/post/2022-01/go-multi-module/)
