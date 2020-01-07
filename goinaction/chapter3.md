# 打包和工具链

## 包

同一个目录下的go文件必须声明同一个包名

### main包

main包：Go语言的编译程序会试图将main包编译成二进制可执行文件。

## 导入

导入方法：

```go
import (
    "fmt"
    "string"
)
```

寻找路径：`GOPATH\src`

如果导入的为一个链接时，会远程导入：

```go
import "github.com/spf13/viper"
```

**bulid**命令使用`go get`命令获取，保存在GOPATH匹配的目录下。

命名导入：当存在重复的包名时可以将导入的包命名为其他名字：

```go
import (
    "fmt"
    myfmt "mylib/fmt"
)
```

## 函数init

每个包可以包含任意多个init函数，这些函数将在函数执行开始前被调用。

常用于设置包、初始化变量或其他要在程序运行前优先完成的引导工作。

## Go的工具

常用的命令：

* go bulid：编译包
* go clean：删除可执行文件
* go vet：检查代码的常见错误
* go fmt：格式化代码

## 依赖管理

golang 提供了 `go mod`命令来管理包。

使用方法：

1. 在GOPATH目录外新建目录，并使用`go mod init 目录名`初始化`go.mod`文件
2. 添加依赖后执行`go run xxx.go`会自动查找依赖进行下载

