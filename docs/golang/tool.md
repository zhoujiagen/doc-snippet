# Golang Tools

## Command Line

- [Command Documentation](https://golang.org/doc/cmd)
- [godoc](https://godoc.org/golang.org/x/tools/cmd/godoc): 静态分析特性[Static analysis features of godoc](https://golang.org/lib/godoc/analysis/help.html)
- [go list](https://golang.org/cmd/go/#hdr-List_packages_or_modules): List packages or modules
- [go test](https://golang.org/cmd/go/#hdr-Test_packages): Test packages, see also [Testing flags](https://golang.org/cmd/go/#hdr-Testing_flags), [Testing functions](https://golang.org/cmd/go/#hdr-Testing_functions)

### 启动本地文档

``` Shell
godoc -http=:6060
```

### go get超时

依赖: `golang.org/x/time/rate`

``` Shell
$ mkdir -p $GOPATH/src/golang.org/x/
$ cd $GOPATH/src/golang.org/x/
$ git clone https://github.com/golang/time.git time
$ go install time
```

## Doc

- [A Tour of Go](https://tour.golang.org/welcome/1)
- [How to Write Go Code](https://golang.org/doc/code.html)
- [GoDoc](https://godoc.org/)
- [Packages](https://golang.org/pkg/)
- [The Go Memory Model](https://golang.org/ref/mem): Go内存模型
- [Release History](https://golang.org/doc/devel/release.html): 发布历史

### [The Go Programming Language Specification](https://golang.org/ref/spec)

- [Constant declarations](https://golang.org/ref/spec#Constant_declarations)
- [Variable declarations](https://golang.org/ref/spec#Variable_declarations)
- [Short variable declarations](https://golang.org/ref/spec#Short_variable_declarations)
- [IncDec statements](https://golang.org/ref/spec#Variable_declarations)
- [For statements](https://golang.org/ref/spec#For_statements)
- [Package initialization](https://golang.org/ref/spec#Package_initialization): 包中初始化
- [Comparison operators](https://golang.org/ref/spec#Comparison_operators): 可比较的
- [Composite literals](https://golang.org/ref/spec#Composite_literals): 聚合字面量, 构造结构、数组、切片、映射的值
- [Built-in functions](https://golang.org/ref/spec#Built-in_functions): 内建函数

### Blog

- [The Go Blog](https://blog.golang.org/)
- [Godoc: documenting Go code](https://blog.golang.org/godoc-documenting-go-code)
- [Using Go Modules](https://blog.golang.org/using-go-modules)


### Packages

- [builtin](https://golang.org/pkg/builtin/): Go的预定义的标识符.
- [fmt](https://godoc.org/fmt): 格式化输出
- [log](https://godoc.org/log): 日志
- [flag](https://godoc.org/flag): 命令行标志
- [math/cmplx](https://godoc.org/math/cmplx): 复数
- [unicode/utf8](https://godoc.org/unicode/utf8): Unicode
- [regexp](https://godoc.org/regexp): 实现正则表达式搜索
- [sort](https://godoc.org/sort): 排序
- [encoding/json](https://godoc.org/encoding/json): 参考博客文章[JSON and Go](https://blog.golang.org/json-and-go)
- [text/template](https://godoc.org/text/template): 文本模板DSL
- [text/tabwriter](https://godoc.org/text/tabwriter): 输出表
- [net/http](https://godoc.org/net/http): 实现HTTP客户端和服务端
- [golang.org/x/net/html](https://godoc.org/golang.org/x/net/html): 实现了HTML5兼容的词法器和解析器
- [bytes](https://godoc.org/bytes): 实现了操作字节切片的函数
- [io/ioutil](https://godoc.org/io/ioutil): 实现了一些IO工具函数
- [os](https://godoc.org/os): OS功能平台独立的接口
- [syscall](https://godoc.org/syscall): 底层OS原语接口
- [sync](https://godoc.org/sync): 基本同步原语
- [reflect](https://godoc.org/reflect): 运行时反射
- [go-yaml](https://github.com/go-yaml/yaml): YAML support for the Go language.
- [go-sql-driver/mysql](https://github.com/go-sql-driver/mysql): Go MySQL Driver is a MySQL driver for Go's (golang) database/sql package. (`database/sql`教程: [Go database/sql tutorial](http://go-database-sql.org/index.html))
- [grpc-go](https://github.com/grpc/grpc-go): The Go language implementation of gRPC. HTTP/2 based RPC.([gRPC Go Quick Start](https://grpc.io/docs/quickstart/go/))
- [cobra](https://github.com/spf13/cobra): A Commander for modern Go CLI interactions.
- [viper](https://github.com/spf13/viper): Go configuration with fangs.
- [go-homedir](https://github.com/mitchellh/go-homedir): Go library for detecting and expanding the user's home directory without cgo.

## IDE

- [LiteIDE](https://github.com/visualfc/liteide), [How to link GOPATH to LiteIDE?](https://stackoverflow.com/questions/18165881/how-to-link-gopath-to-liteide)


- 在线编辑: [The Go Playground](https://play.golang.org/)
- [Delve](https://github.com/go-delve/delve): Delve is a debugger for the Go programming language.

## Tools

- [go-kit/kit](https://github.com/go-kit/kit): A standard library for microservices. 
