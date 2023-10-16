# Go 项目目录规范

Go 项目可以包括 package、command-cli 项目或两者的组合。本指南按项目类型编排。

## Basic package

basic package 项目的所有代码都在仓库的根目录下。项目只含有一个 module，module 只包含一个 package。包名与模块名称的最后一段路径一致。对于由单个文件组成包的 package 项目，目录结构如下：

```file
project-root-directory/
    go.mod
    modname.go
    modname_test.go
```

假设该项目已上传至 Github 仓库`github.com/someuser/modname`，则 go.mod 文件中的 module line 应为 `module github.com/someuser/modname`。

modname.go 文件包名应为：

```go
package modname

//...package code here
```

用户可以使用以下命令导入该包到自己的代码中：

```go
import "github.com/someuser/modname"
```

一个 Go 软件包可以分割成多个文件，所有文件都位于同一目录下，例如:

```file
project-root-directory/
  go.mod
  modname.go
  modname_test.go
  auth.go
  auth_test.go
  hash.go
  hash_test.go
```

目录中的所有文件都必须声明包名为 modname。

## Basic Command

最简单的 Command-line 项目只包含一个 go 文件，文件中定义了 main 函数。较大一点的项目可以将其代码拆分为多个文件，在所有文件声明包名 `package main`：

```file
project-root-directory/
  go.mod
  auth.go
  auth_test.go
  client.go
  main.go
```

此例中，main.go 文件包含 main 函数，但这只是一种习惯。main 函数所在的文件并不强制要求名称为 main.go。

假设该项目已上传至名为 `github.com/someuser/modname` 的 GitHub 仓库，go.mod 文件中的 module line 应为：

```go
module github.com/someuser/modname
```

用户应该能够使用以下命令在自己的机器上安装：

```go
go install github.com/someuser/modname@latest
```

## Package or command with supporting packages

大型 package 或 command 项目可能会受益于将某些功能拆分为 support package。推荐将此类软件包放在名为 internal 的目录中，这样可以防止其他模块依赖我们不希望公共的包。软件包的项目结构如下：

```file
project-root-directory/
  internal/
    auth/
      auth.go
      auth_test.go
    hash/
      hash.go
      hash_test.go
  go.mod
  modname.go
  modname_test.go
```

文件 modname.go 声明 `package modname`、auth.go 文件声明 `package auth`等。modname.go 可以按如下方式导入 `auth` 包:

```go
import "github.com/someuser/modname/internal/auth"
```

带有 support package 的 command 项目目录结构与 internal 目录下非常相似，除了根目录中的文件声明了 `package main`。

## Multiple packages

一个 module 可以包含多个重要的 package，每个 package 都有自己的目录，并可以按层次结构排列。下面是一个项目结构示例：

```file
project-root-directory/
  go.mod
  modname.go
  modname_test.go
  auth/
    auth.go
    auth_test.go
    token/
      token.go
      token_test.go
  hash/
    hash.go
  internal/
    trace/
      trace.go
```

我们假定 go.mod 中的 module line 为：

```go
module github.com/someuser/modname
```

modname 软件包位于根目录中，声明了 modname package，用户可以通过以下命令导入该软件包：

```go
import "github.com/someuser/modname"
```

子包可以被用户使用以下命令导入:

```go
import "github.com/someuser/modname/auth"
import "github.com/someuser/modname/auth/token"
import "github.com/someuser/modname/hash"
```

internal/trace 中的 package 无法被该模块之外项目导入，因此推荐将 package 放在该目录下。

## Multiple commands

同一代码仓中的多个项目通常具有单独的目录：

```file
project-root-directory/
  go.mod
  internal/
    ... shared internal packages
  prog1/
    main.go
  prog2/
    main.go
```

在每个目录中，程序的 go 文件都会声明 `package main`。顶层的 internal 目录可以包含仓库中所有 command 项目使用的共享 package。

用户可以使用以下命令安装项目：

```go
go install github.com/someuser/modname/prog1@latest
go install github.com/someuser/modname/prog2@latest
```

一个常见的惯例是将代码库中的所有 command 项目放在 `cmd` 目录中。这在仅包含 command 项目的代码库中并非必要，但在同时包含 command 和 package 的混合代码仓中却非常有用，我们将在下文中讨论这一点。

## Packages and commands in the same repository

有时，代码仓中会同时包含 package 和 command 项目。示例如下：

```file
project-root-directory/
  go.mod
  modname.go
  modname_test.go
  auth/
    auth.go
    auth_test.go
  internal/
    ... internal packages
  cmd/
    prog1/
      main.go
    prog2/
      main.go
```

假设该模块名为 `github.com/someuser/modname`，用户可以导入该包：

```go
import "github.com/someuser/modname"
import "github.com/someuser/modname/auth"
```

也可以安装该项目:

```go
go install github.com/someuser/modname/cmd/prog1@latest
go install github.com/someuser/modname/cmd/prog2@latest

```

## Server project

Go 是开发服务端应用的常用语言。由于服务端开发涉及许多方面：协议（REST？gRPC？）、部署、前端文件、容器化、脚本等，因此此类项目的结构差异很大。我们只聚焦于项目中的 Go 语言部分。

Server 项目通常不会有用于导出的 package，因为服务器通常是一个独立的二进制文件（或一组二进制文件）。因此，建议将实现服务器逻辑的 Go 包保留在 internal 目录中。此外，由于项目中可能有许多包含非 go 文件的其他目录，因此最好将所有 Go comman 程序放在 cmd 目录中：

```file
project-root-directory/
  go.mod
  internal/
    auth/
      ...
    metrics/
      ...
    model/
      ...
  cmd/
    api-server/
      main.go
    metrics-analyzer/
      main.go
    ...
  ... the project's other directories with non-Go code
```

如果 Server project 仓库中的 package 可以与其他项目共享，最好将它拆分为单独的模块。

参考资料：

* [Organizing a Go module](https://go.dev/doc/modules/layout)
