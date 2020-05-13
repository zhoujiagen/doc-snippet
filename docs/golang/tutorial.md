# The Go Programming Language

| 时间| 内容|
|:-------|:---------------------------------------|
|20190717| kick off.|
|20191021| re-kick off. add notes of variable, package.|
|20191022| add notes of basic and composite types.|


```
@Book{Donovan2016,
  title     = {The Go Programming Language},
  publisher = {Addison-Wesley},
  year      = {2016},
  author    = {Alan A. A. Donovan, Brian W. Kernighan},
  comment   = {版本: 1.5(P.xvi)},
  file      = {:The Go Programming Language.pdf:PDF;:The Go Programming Language-CN.pdf:PDF},
  groups    = {Golang},
}
```


## 程序结构
### 名称

名称是大小写敏感的. 如果名称以大写字母开始, 它是导出的. 包名总是小写的.

25个关键字:

``` go
break 	 default 	func 	interface 	select
case 	 defer 		go 	map 		struct
chan 	 else 		goto 	package 	switch
const 	 fallthrough 	if 	range 		type
continue for 		import 	return 		var
```


内建常量:

``` go
true false iota nil
```

内建类型:

``` go
int int8 int16 int32 int64
uint uint8 uint16 uint32 uint64 uintptr
float32 float64 complex128 complex64
bool byte rune string error

```

内建函数:

``` go
make len cap new append copy close delete
complex real imag
panic recover
```

### 声明

四种主要的声明: `var`、`const`、`type`和`func`.

Go程序存储在`.go`后缀的文件中. 每个文件以`package`声明开始, 后接任意数量的`import`声明, 然后是一组类型、变量、常量和函数的包级声明.

#### var: 变量

``` go
var name type = expression

// 例子
var s string
var s = ""
var s string = ""
```

`type`或`= expression`部分可省略, 但不能同时省略.
如果省略了`type`, 变量的类型由初始化表达式确定; 如果省略的`= expression`, 其初始值为类型的零值.

在单个声明中声明和初始化多个变量:

``` go
var i, j, k int // int, int, int
var b, f, s = true, 2.3, "four" // bool, float64, string
```

包级的变量在`main`开始之前初始化, 局部变量在函数执行时遇到其声明时初始化.

##### 零值

给变量和值指定默认值:

- 给变量分配存储时: 不管是通过声明, 或调用[`new`](https://golang.org/pkg/builtin/#new)
- 创建新的值时: 不管是通过聚合字面量, 或调用[`make`](https://golang.org/pkg/builtin/#make), 没有提供显式的初始化

类型的零值[^1]:
[^1]: The zero value: https://golang.org/ref/spec#The_zero_value

- `bool`: `false`
- 数值类型: `0`
- `string`: `""`
- 指针、函数、接口、切片、通道、映射: `nil`
- 结构: 由每个字段的零值构成
- 其它: 这种初始化是递归的, 例如结构的数组中没有元素的字段在没有指定值时是零值


##### 简短变量声明

声明和初始化局部变量:

``` go
name := expression
```

`name`的类型根据`expression`来自动推导.

`:=`与`=`的区别:

- `:=`是声明
- `=`是赋值

简短变量声明不一定要声明其左侧的所有变量: 如果其中一些变量在同一个词法块中已经声明过了, 则简短变量声明对这些变量表现的像赋值. 一个简短变量声明必须至少声明一个新的变量.


##### 指针

指针的值是变量的地址. 不是所有值都有地址, 但变量都有地址;
变量有时被称为可寻址的值, 表示变量的表达式是唯一可以使用取地址符`&`的表达式.
可寻址的详细信息见[Address operators](https://golang.org/ref/spec#Address_operators).

``` go
x := 1
p := &x		// 指针对应的数据类型是*int
fmt.Println(*p)	// 1: *p表达式对应p指针指向的变量的值
*p = 2		// *p是x的别名
fmt.Println(x)	// 2
```

任何类型的指针的零值都是`nil`. 如果指针`p`指向一个变量, 则`p != nil`为真.

指针是可比较的: 两个指针指向同一个变量或都为nil, 则这两个指针相等.

``` go
var x, y int
fmt.Println(&x)		// 0xc0000a4010
fmt.Println(&x == &x, &x == &y, &y == nil)	// true, false, false
```

在函数中返回局部变量的地址是安全的.

因为指针包含了一个变量的地址, 因此如果将指针作为参数调用函数, 那将可以在函数中通过该指针来更新变量的值.

每次我们对一个变量取地址, 或者复制指针, 我们都是为原变量创建了新的 ==别名==.
不仅指针可以创建别名, 拷贝其它引用类型(slice、map、channel)、甚至包含这些类型的struct、array、interface时也创建别名.

聚合类型每个成员都是对应一个变量, 因此可以被取地址.


##### new函数

表达式`new(T)`: 创建一个`T`类型的未命名变量, 初始化为`T`类型的零值, 返回变量地址, 这个指针类型为`*T`.

`new`是一个预声明的函数, 不是关键字, 可以重新定义这个名称.

##### 变量的生命周期

- 包级变量: 它们的生命周期和整个程序的运行周期是一致的
- 局部变量: 动态的, 每次执行声明语句时创建一个新实例, 直到该变量不再被引用为止, 然后变量的存储空间可能被回收; 函数的参数变量和返回值变量都是局部变量.

##### 赋值

变量的值由赋值语句更新: `variable = expression`.

算术和位二元操作符有相应的赋值操作符, 例如`count[X] *= scale`.

数值变量可以用`++`或`--`语句递增或递减.

##### 元组赋值

一次赋值多个变量: 所有`=`右端的表达式在更新变量之前求值.

``` go
x, y = y, x
_, ok = x.(T) // 类型断言, 使用空标识符
```

##### 可赋值性

赋值语句是显式的赋值, 一些隐式的赋值:

- 函数调用隐式的将传递参数值赋值给相应的参数变量
- `return`语句隐式的将`return`操作数赋值为相应的返回变量
- 聚合类型的字面量表达式隐式的给每个元素赋值, 例如: `medals := []string{"gold", "silver", "bronze"}`

合法的赋值: 只有右边的值对于左边的变量是可赋值的, 赋值才是合法的.

`nil`可以赋值给任何指针或引用类型的变量.

两个值是否可以用`==` 或`!=` 进行相等比较与可赋值性有关系: 在任何比较中, 第一个操作数必须可以对第一个操作数的类型是可赋值的, 反之亦然.


#### const: 常量

常量是编译器已知其值的表达式, 它在编译时求值.
常量的底层类型是: 布尔、字符串、数值.

常量序列:

``` go
const (
	a = 1
	b
	c = 2
	d
)
fmt.Println(a,b,c,d) // 1 1 2 2
```

常量生成器`iota`:

``` go
const (
	Sunday Weekday = iota
	Monday
	Tuesday
	Wednesday
	Thursday
	Friday
	Saturaty
)

fmt.Println(Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturaty) // 0 1 2 3 4 5 6
```

无类型的常量(untyped constants): 无类型布尔型、无类型整数、无类型rune、无类型浮点数、无类型复数、无类型字符串.


#### type: 类型

类型声明定义了一个新的命名的类型, 与既有类型由相同的底层类型. 类型声明通常在包级出现.

``` go
type name underlying-type
```

对于每一个类型`T`, 都有一个显式的类型转换操作`T(x)`: 将值`x`转为`T`类型.

允许的类型转换:

- 两个类型的底层类型相同
- 两个类型是未命名的指针类型, 指向有相同底层类型的变量

上面的转换改变了值的类型, 但不改变值的表示.

数值类型之间、字符串和一些slice类型之间也允许转换, 但这些转换可能会修改值的表示.

命名的类型的底层类型决定了它的内部结构和表达方式, 也决定它可以像直接使用底层类型一样支持底层类型上的操作.

比较运算符`==` 和`<` 可以用来比较同一个命名的类型的两个值、一个命名的类型的值和一个其底层类型的值. 但不能直接进行比较两个属于不同的命名的类型的值.

==Go的类型== 有四类:

- 基本类型: 数值、字符串、布尔型
- 聚合类型: 数组、结构
- 引用类型: 指针、切片、映射、函数和通道
- 接口类型.

==类型断言(type assertion)==: `x.(T)`, `x`是接口类型的表达式, `T`是一个类型, 称为被断言的类型.
类型断言检查操作数的动态类型是否与被断言的类型匹配.

- `T`是具体类型

类型断言检查`x`的动态类型是否与`T`一致:<br>
(1) 一致: 类型断言的结果是`x`的动态值, 类型为`T`; 即提取了操作数的具体值.<br>
(2) 不一致: panic.

- `T`是接口类型

类型断言检查`x`的动态类型是否满足`T`:<br>
(1) 满足: 不提取动态值, 结果仍是接口值(保持了动态类型和动态值), 但结果有接口类型`T`; 即改变了表达式的类型, 使得可访问一组不同的方法, 但保持了接口值中的动态类型和动态值.<br>
(2) 不满足: panic.

注意: 如果操作数是空接口值, 类型断言失败.


下面的实例中类型、方法、变量的签名:

``` go
// io.Writer: 接口类型
type Writer interface {
    Write(p []byte) (n int, err error)
}
type Reader interface {
    Read(p []byte) (n int, err error)
}
type ReadWriter interface {
    Reader
    Writer
}

// os.Stdout: 具体值
var (
    Stdin  = NewFile(uintptr(syscall.Stdin), "/dev/stdin")
    Stdout = NewFile(uintptr(syscall.Stdout), "/dev/stdout")
    Stderr = NewFile(uintptr(syscall.Stderr), "/dev/stderr")
)
func NewFile(fd uintptr, name string) *File
type File struct {
}
func (f *File) Read(b []byte) (n int, err error)
func (f *File) Write(b []byte) (n int, err error)

// bytes.Buffer: 具体类型
type Buffer struct {
}
func (b *Buffer) Write(p []byte) (n int, err error)

// 第7章中
type ByteCounter int
func (c *ByteCounter) Write(p []byte) (int, error)
```

``` go
var w io.Writer
w = os.Stdout
f := w.(*os.File) // success
c := w.(*bytes.Buffer) // panic
```

``` go
var w io.Writer
w = os.Stdout
rw := w.(io.ReaderWriter) // success
```

``` go
var w io.Writer
w = new(ByteCounter)
rw := w.(io.ReadWriter) // panic
```

测试是否是某个特定类型: 返回两个值, 如果操作失败, 第二个值为false, 第一个值为被断言的类型的零值.

``` go
var w io.Writer = os.Stdout
f, ok := w.(*os.File) // success: ok, f == os.Stdout
b, ok := w.(*bytes.Buffer) // failure: !ok, b == nil
```


##### 基础数据类型

###### 整数

``` go
int8, int16, int32, int64
uint8, uint16, uint32, uint64
int, uint // 平台相关的
rune // int32, 表示值是Unicode码点
byte // uint8, 强调值是原始数据
uintptr // 宽度不定但可持有指针值的所有位, 用于底层编程
```

|类别| 操作符| 名称|可应用类型|说明/示例|
|:-------|:-------|:-------|:-------|:-------|
|算术|`+`|sum|integers, floats, complex values, strings|整数`x`, `+x`表示`0+x`;<br> 浮点数和复数`y`, `+y`表示其自身, `-y`表示;<br> 字符串拼接|
|算术|`-`|difference|integers, floats, complex values|整数`x`, `-x`表示`0-x`|
|算术|`*`|product|integers, floats, complex values||
|算术|`/`|quotient|integers, floats, complex values||
|算术|`%`|remainder|integers||
|算术|`&`|bitwise AND|integers||
|算术|`|`|bitwise OR |integers||
|算术|`^`|bitwise XOR|integers|整数`x`, `^x`表示bitwise complement, <br>m ^ x  with m = "all bits set to 1" for unsigned x <br>and  m = -1 for signed x|
|算术|`&^`|bit clear (AND NOT)|integers| in expression `z= x &^ y`, each bit of `z` is 0 if corresponding bit of `y` is 1;<br> otherwise it equals the corresponding bit of `x`|
|算术|`<<`|left shift|integer << unsigned integer||
|算术|`>>`|right shift|integer >> unsigned integer||
|比较|`==`|equal|comparable||
|比较|`!=`|not equal|comparable||
|比较|`<`|less|ordered||
|比较|`<=`|less or equal|ordered||
|比较|`>`|greater|ordered||
|比较|`>=`|greater or equal|ordered||
|逻辑|`&&`|conditional AND|bool|p && q  is  "if p then q else false"|
|逻辑|`||`|conditional OR|bool|p `||` q  is  "if p then true else q"|
|逻辑|`!`|NOT|bool|!p is "not p"|
|地址|`&`||addressable|For an operand `x` of type `T`, the address operation `&x` generates a pointer of type `*T` to `x`.<br> 操作数需是:<br>变量、指针间接、切片索引操作、可寻址的结构操作数的字段选择器、可寻址的数组的数组索引操作.<br> 可寻址性要求的一个例外是: `x`也可以是一个聚合字面量(Composite literals).|
|地址|`*`|pointer indirection|pointer|For an operand `x` of pointer type `*T`, the pointer indirection `*x` denotes the variable of type `T` pointed to by `x`.|
|接收|`<-`|receive| channel|For an operand `ch` of channel type, the value of the receive operation `<-ch` is the value received from the channel `ch`.|


算术、逻辑和比较的二元操作符的优先级如下, 其中同层的操作符左结合:

```
优先级   操作符
5       * / % << >> & &^
4       + - | ^
3       == != < <= > >=
2       &&
1       ||
```

头两行的操作符有相应的赋值操作符, 例如`+=`.

###### 浮点数

标准: IEEE 754.

``` go
float32 float64

math.MaxFloat32 math.MaxFloat64
```

``` go
var z float64
fmt.Println(z, -z, 1/z, -1/z, z/z) // 0 -0 +Inf -Inf NaN

fmt.Println(math.IsNaN(z/z))
nan := math.NaN()
fmt.Println(nan == nan, nan < nan, nan > nan) // 不可比较: false false false
```

###### 复数

``` go
complex64 complex128

complex real imag // 函数
3.141592i 2i // 虚部字面量
math/cmplx包
```

###### 布尔类型

``` go
bool

true false // 值
```

###### 字符串

字符串是不可变的字节序列. 文本字符串通常被解释为Unicode码点(rune)的UTF-8编码的序列.

``` go
s := "hello, world" // 字面量
fmt.Println(len(s)) // len: 12
fmt.Println(s[0], s[7]) // 索引: 104 119
fmt.Println(s[0:5]) // 子字符串操作: hello
fmt.Println(s[:5]) // hello
fmt.Println(s[7:]) // world
fmt.Println(s[:]) // hello, world
fmt.Println("goodbye" + s[5:]) // 拼接: goodbye, world
```

字符串是不可变的:

``` go
s = "left foot"
t := s
s += ", right foot"
fmt.Println(s) // left foot, right foot
fmt.Println(t) // left foot
```

原始字符串字面量:

``` go
s := `hello,
world
`
fmt.Println(s)
// hello,
//	world
```

字符串与`rune`(Unicode码点):

``` go
import (
	"fmt"
	"unicode/utf8"
)

s := "hello, 世界"
fmt.Println(len(s)) // 13
fmt.Println(utf8.RuneCountInString(s)) // 9

for i := 0; i < len(s); {
  r, size := utf8.DecodeRuneInString(s[i:])
  fmt.Printf("%d\t%c\n", i, r)
  i += size
}

// 0	h
// 1	e
// 2	l
// 3	l
// 4	o
// 5	,
// 6
// 7	世
// 10	界

for i, r := range s { // range会自动处理rune
  fmt.Printf("%d\t%q\t%d\n", i, r, r)
}

// 0	'h'	104
// 1	'e'	101
// 2	'l'	108
// 3	'l'	108
// 4	'o'	111
// 5	','	44
// 6	' '	32
// 7	'世'	19990
// 10	'界'	30028
```

``` go
s := "hello, 世界"
r := []rune(s)

fmt.Println(r) // [104 101 108 108 111 44 32 19990 30028]
fmt.Println(string(r)) // hello, 世界
fmt.Println(string(0x4eac)) // 京
```

一些可用的包:

- bytes
- strings
- strconv: Itoa, Atoi, FormatInt, ParseInt
- fmt: Sprintf, Scanf
- unicode
- path, path/filepath

##### 复合数据类型

###### 数组

数组是特定类型的零个或多个元素的定长序列. 长度作为数组类型的一部分, 例如`[3]int`与`[4]int`不是同一类型.

可比较性: 如果元素类型是可比较的, 则该数组类型是可比较的.

字面量:

``` go
arr := [3]int{1,2,3}
arr[2]=22
fmt.Printf("%T, %v\n", arr, arr) // [3]int, [1 2 22]
arr = [...]int{11,22,33}
fmt.Printf("%T, %v\n", arr, arr) // [3]int, [11 22 33]
arr = [...]int{0: 11, 2: 33}
fmt.Printf("%T, %v\n", arr, arr) // [3]int, [11 0 33]
```

作为参数:

``` go
func arrayAsParamater(a [3]int) {
	a[2] = -1
	fmt.Printf("%T, %v\n", a, a)
}

func arrayPtrAsParamater(aptr *[3]int) {
	aptr[2] = -1
	fmt.Printf("%T, %v\n", *aptr, *aptr)
}

arr = [3]int{1,2,3}
arrayAsParamater(arr) // [3]int, [1 2 -1]
fmt.Printf("%T, %v\n", arr, arr) // [3]int, [1 2 3]

arr = [3]int{1,2,3}
arrayPtrAsParamater(&arr) // [3]int, [1 2 -1]
fmt.Printf("%T, %v\n", arr, arr) // [3]int, [1 2 -1]
```

###### 切片

切片表示同一个类型的元素的变长序列, 切片的类型记为`[]T`, `T`为元素的类型.

切片是提供访问数组的子序列轻量级数据结构, 这个数组称为切片的底层数组. 切片有三个组件:

- 指针: 指向数组中可被切片访问的第一个元素的指针.
- 长度(length): 切片元素的数量, 不能超过容量; BIF `len`.
- 容量(capacity): 在切片开始到数组结束中的元素的数量; BIF `cap`.

将切片作为传递参数, 允许函数修改底层的数组元素; 即创建了底层数组的别名.

创建切片的三种方式:

- 切片操作符`s[i:j]`: 序列`s`可以是数组变量、数组的指针、其它切片
- 切片字面量: `a := []int{0,1,2,3,4,5}`
- BIF `make`: `make([]T, length)`, `make([]T, length, capacity)`

切片的零值是`nil`.

可比较性: 切片不可比较. `bytes.Equal`函数可用于比较字节切片(`[]byte`).

操作切片:

- BIF `append`
- BIF `copy`

###### 映射

映射是一个哈希表, 类型为`map[K]V`, `K`和`V`分别是键值的类型; 类型`K`必须可以通过`==`比较.


创建:

``` go
a := make(map[string]int)
fmt.Printf("%T, %v\n", a, a) // map[string]int, map[]

ages := map[string]int{
  "alice": 31,
  "bob": 32,
}
fmt.Printf("%T, %v\n", ages, ages) // map[string]int, map[alice:31 bob:32]
```

访问, 删除:

``` go
ages["alice"] = 32
fmt.Println(ages["alice"]) // 32

delete(ages, "alice")

fmt.Println(ages["alice"]) // 0
value, ok := ages["alice"]
fmt.Println(value, ok) // 0 false
```

映射的零值是`nil`, 必须先分配后再存储.

可比较性: 映射不可比较.

###### 结构

结构是一个聚合数据类型, 将零个或多个任意类型的命名的值组合在一起; 每个值称为字段(field).
空结构类型: `struct{}`.

字段的顺序对结构类型的标识是重要的.

结构的零值由各字段的零值构成.

可比较性: 如果每个字段都是可比较的, 该结构是可比较的.

结构内嵌机制: 匿名字段有隐式的名称, 点表达式中是可选的.


##### 接口类型

#### func: 函数

函数声明:

``` go
func name(parameter-list) (result-list) {
  body
}
```

调用者指定传递参数(argument)调用函数.
有`result-list`的函数必须以`return`语句结束, 除非执行明显不能达到函数的末尾, 可能是因为以调用[`panic`](https://golang.org/pkg/builtin/#panic)结束或没有`break`的无限`for`循环.
没有`body`的函数声明, 表示这个函数是用其它语言实现的.

函数的类型称为函数的签名(signature):

``` go
func add(x,y int) int {
	return x + y
}

fmt.Printf("%T\n", add) // func(int, int) int
```


##### 方法


### 包和文件

一个包的源代码在一个或多个`.go`文件中, 通常在以导入路径结尾的目录下, 例如包`xxx/helloworld`的文件在目录`$GOPATH/src/xxx/helloworld`中.

包提供了它的声明的命名空间, 也提供了控制命名是否外部可见的机制: 以大写字母开头的标识符是导出的.

#### 导入

每个包由称为导入路径的唯一的字符串标识, 语言规范没有定义这些字符串的来源和含义, 它们是由工具解释的.

每个包有一个包名称, 是出现在它的`package`声明中的短名称(不需要是唯一的).
通常包的名称与导入路径的最后一个片段相同, 例如包`xxx/tempconv`的名称是`tempconv`.

示例:

``` go
// 导入声明, Sin的本地名称
import   "lib/math"         math.Sin
import m "lib/math"         m.Sin
import . "lib/math"         Sin

// 只为使用包的副作用(作用)
import _ "lib/math"
```

#### 包初始化

包的初始化以初始化包级变量开始, 按解析依赖后的声明的顺序.

如果包中有多个`.go`文件, 按`go`工具调用编译器之前排列的顺序初始化这些文件.

每个文件可以包含任意数量的`init`函数, 其声明为`func init() {/* ... */}`; 在程序执行前按声明的顺序执行.

按程序中导入的顺序一次初始化一个包, 被依赖的包优先, 最后初始化包`main`. 例如包`p`中导入了包`q`, 则`q`在开始初始化`p`之前已经完全初始化了.

### 作用域

- 词法作用域: 名称遮盖问题
- 声明分类: 包级、文件级、局部
- `:=`是声明, `=`是赋值

## 并发工具
### Goroutines, Channels

### 共享变量的并发

## 包和工具

## 测试

在包目录中, 以`_test.go`结尾的文件不是`go build`构建的包的一部分, 但是是`go test`构建的包的一部分.

在`*_test.go`结尾的文件中, 有三种函数:

- 测试: 函数名以`Test`开始
- 基准: 函数名以`Benchmark`开始
- 示例: 函数名以`Eample`开始

## 反射
