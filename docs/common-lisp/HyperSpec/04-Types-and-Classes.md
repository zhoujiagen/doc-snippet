# 4. 类型和类

[TOC]

## <span id="4.1">4.1</span> 介绍

==类型== 是一组(可能无限数量的)对象. 一个对象可以属于多个类型.
Common Lisp从不将对象显式表示为对象, 而是通过使用 **类型描述符** 间接引用, 类型描述符是表示类型的对象.

可以通过`deftype`、`defstruct`、`defclass`和`define-condition`定义新的类型.

函数`typep`, 是集合成员测试, 用于确定给定对象是否属于给定类型.
函数`subtypep`, 是子集测试, 用于确定给定类型是否是另一个给定类型的子类型.
函数`type-of`返回给定对象所属的一个特定类型, 尽管对象可以属于多个类型.(例如, 每个对象术语类型`t`, 但`type-of`总是返回比`t`特殊的类型描述符.)

对象, 不是变量, 是有类型的. 通常任意对象的值可以是任意对象.
可以通过显式类型声明, 声明一个变量的值只能是属于给定类型的对象.
除了等价出现外, 类型组织成有向无环图.

可以使用`declare`、`proclaim`、`declaim`或`the`做类型声明.
有关声明的更多信息见[3.3 声明](../03-Evaluation-and-Compilation#3.3).

对象系统的基础对象中有类. ==类== 确定了一组其他对象的结构和行为, 这些对象称作它的 ==实例==.
每个对象是一个类的直接实例.
对象所属的类确定了可以在该对象上执行的操作集合. 更多信息见[4.3 类(Classes)](#4.3).

可以编写对作为其传递参数的对象的类上有特化行为的函数. 更多信息见[7.6 广义函数和方法](07-Objects#7.6).

对象的类的类称为它的 ==元类(metaclass)==. 关于元类的更多信息见[7.4 元对象](07-Objects#7.4).

## <span id="4.2">4.2</span> 类型(Types)

### <span id="4.2.1">4.2.1</span> 数据类型定义

类型的使用信息在下图中给出.
[图 4-7. 对象系统类.](#Figure4-7)列举了一些与对象系统相关的类.
[图 9-1. 标准状况类型.](../09-Conditions#Figure9-1)列出了已定义的状况类型.

<span id="Figure4-1">图 4-1. 数据类型信息的引用.</span>

|章节                     | 数据类型|
|:-----------------------|:--------|
|[4.3 类(Classes)](../04-Types-and-Classes#4.3) | 对象系统类型|
|[7.5 槽(Slots)](../07-Objects#7.5) | 对象系统类型|
|[7 对象](../07-Objects) | 对象系统类型|
|[7.6 广义函数和方法](../07-Objects#7.6) | 对象系统类型|
|[9.1 状况系统概念](../09-Conditions#9.1) | 状况系统类型|
|[4 类型和类](../04-Types-and-Classes) | 多种类型|
|[2 语法](../02-Syntax) | 所有类型: 读和打印语法|
|[22.1 Lisp打印器](../22-Printer#22.1) | 所有类型: 打印语法|
|[3.2 编译](../03-Evaluation-and-Compilation#3.2) | 所有类型: 编译问题|

### <span id="4.2.2">4.2.2</span> 类型关系

- 类型`cons`、`symbol`、`array`、`number`、`character`、`hash-table`、`function`、`readtable`、`package`、`pathname`、`stream`、`random-state`、`condition`、`restart`和通过`defstruct`、`define-condition`或`defclass`定义的单个类型, 相互之间是隔离的, 除非在`declass`或`define-condition`中或者`defstruct`的`:include`选项中指定超类, 显式的建立了类型关系.
- 使用`defstruct`创建的两个类型是隔离的, 除非使用了`:include`选项将其中一个类型设置为另一个类型的超类型.
- 使用`defclass`或`define-condition`创建的两个不同类是隔离的, 除非它们有共同的超类, 或者一个类是另一个类的子类.
- 实现可以扩展添加类型之间的子类型关系, 只要它们没有违背这里描述的类型关系和不相交要求. 实现可以定义任意类型的超类型或子类型, 只要每个有超类型`t`和子类型`nil`, 且没有违背不相交要求.

在实现中, `standard-object`或`structure-object`可以在没有描述`standard-object`或`structure-object`的系统类的类优先级列表中出现.
如果出现了, 必须在类`t`之前和其他标准类之后出现.

### <span id="4.2.3">4.2.3</span> 类型描述符

==类型描述符== 可以是符号、类或列表.
[图 4-2. 标准原子类型描述符.](#Figure4-2)列出了是标准原子类型描述符的符号,
[图 4-3. 标准复合类型描述符名称.](#Figure4-3)列出了标准复合类型描述符的名称.
类型描述符的语法参见字典一节.
可以使用`defclass`、`define-condition`、`defstruct`或`deftype`定义新的类型描述符.

<span id="Figure4-2">图 4-2. 标准原子类型描述符.</span>

``` lisp
arithmetic-error
array
atom
base-char
base-string
bignum
bit
bit-vector
broadcast-stream
built-in-class
cell-error
character
class
compiled-function
complex
concatenated-stream
condition
cons
control-error
division-by-zero
double-float
echo-stream
end-of-file
error
extended-char
file-error
file-stream
fixnum
float
floating-point-inexact
floating-point-invalid-operation
floating-point-overflow
floating-point-underflow
function
generic-function
hash-table
integer
keyword
list
logical-pathname
long-float
method
method-combination
nil
null
number
package
package-error
parse-error
pathname
print-not-readable
program-error
random-state
ratio
rational
reader-error
readtable
real
restart
sequence
serious-condition
short-float
signed-byte
simple-array
simple-base-string
simple-bit-vector
simple-condition
simple-error
simple-string
simple-type-error
simple-vector
simple-warning
single-float
standard-char
standard-class
standard-generic-function
standard-method
standard-object
storage-condition
stream
stream-error
string
string-stream
structure-class
structure-object
style-warning
symbol
synonym-stream
t
two-way-stream
type-error
unbound-slot
unbound-variable
undefined-function
unsigned-byte
vector
warning
```

如果类型描述符是一个列表, 该列表的car是一个符号, 列表的剩余部分是附属类型信息.
这样的类型描述符被称为 ==复合类型描述符==.
除非特别支持, 可以不指定附属项. 未指定的附属项通过`*`标识. 例如, 为完整的描述一个向量, 必须指定元素的类型和向量的长度.

``` lisp
(vector double-float 100)
```

下面没有指定长度:

``` lisp
(vector double-float *)
```

下面没有指定元素类型:

``` lisp
(vector * 100)
```

假设两个类型描述符除了第一个有个`*`而第二个有显式的描述之外均相同. 则第二个表示第一个表示的类型的子类型.

如果列表尾有未指定项, 这些项被移出. 如果移除所有`*`的结果为单个元素的列表, 则括号页可以被移除(这个列表可以被其car的符号替代).
例如, `(vector double-float *)`可以简写为`(vector double-float)`, `(vector * *)`可以简写为`(vector)`或`vector`.

<span id="Figure4-3">图 4-3. 标准复合类型描述符名称.</span>

``` lisp
and
array
base-string
bit-vector
complex
cons
double-float
eql
float
function
integer
long-float
member
mod
not
or
rational
real
satisfies
short-float
signed-byte
simple-array
simple-base-string
simple-bit-vector
simple-string
simple-vector
single-float
string
unsigned-byte
values
vector
```

下图展示了可用作复合类型描述符名称、但不可以用作原子类型描述符的已定义名称。

<span id="Figure4-4">图 4-4. 标准的只用于复合类型描述符的名称.</span>

``` lisp
and
eql
member
mod
not
or
satisfies
values
```

可以使用两种方式定义类型描述符:

- 使用不带`:type`描述符的`defstruct`定义结构, 或者使用`defclass`或`define-conditon`定义类, 自动将结构或类的名称转变为新的类型描述符的符号.
- 使用`deftype`定义导出的类型描述符, 它是其他类型描述符的简写.

类对象可被用作类型描述符, 它表示这个类的所有成员的集合.

下图展示了与类型和类型相关的一些已定义名称.

<span id="Figure4-5">图 4-5. 与类型和声明相关的已定义名称.</span>

``` lisp
coerce
declaim
declare
defclass
define-condition
defstruct
deftype
ftype
locally
proclaim
subtypep
the
type
type-of
typep
```

下图展示了所有是类型描述符的已定义名称, 或者是原子类型描述符或者是复合类型描述符, 这个列表是[图 4-2. 标准原子类型描述符.](../04-Types-and-Classes#Figure4-2)和[图 4-3. 标准复合类型描述符名称.](../04-Types-and-Classes#Figure4-3)的并.

<span id="Figure4-6">图 4-6. 标准类型描述符名称.</span>

``` lisp
and
arithmetic-error
array
atom
base-char
base-string
bignum
bit
bit-vector
broadcast-stream
built-in-class
cell-error
character
class
compiled-function
complex
concatenated-stream
condition
cons
control-error
division-by-zero
double-float
echo-stream
end-of-file
eql
error
extended-char
file-error
file-stream
fixnum
float
floating-point-inexact
floating-point-invalid-operation
floating-point-overflow
floating-point-underflow
function
generic-function
hash-table
integer
keyword
list
logical-pathname
long-float
member
method
method-combination
mod
nil
not
null
number
or
package
package-error
parse-error
pathname
print-not-readable
program-error
random-state
ratio
rational
reader-error
readtable
real
restart
satisfies
sequence
serious-condition
short-float
signed-byte
simple-array
simple-base-string
simple-bit-vector
simple-condition
simple-error
simple-string
simple-type-error
simple-vector
simple-warning
single-float
standard-char
standard-class
standard-generic-function
standard-method
standard-object
storage-condition
stream
stream-error
string
string-stream
structure-class
structure-object
style-warning
symbol
synonym-stream
t
two-way-stream
type-error
unbound-slot
unbound-variable
undefined-function
unsigned-byte
values
vector
warning
```

## <span id="4.3">4.3</span> 类(Classes)

对象系统通常可以描述所有标准类(例如包括`number`、`hash-table`和`symbol`), 下图中有一组与理解对象系统相关的类.

<span id="Figure4-7">图 4-7. 对象系统类.</span>

``` lisp
built-in-class
class
generic-function
method
method-combination
standard-class
standard-generic-function
standard-method
standard-object
structure-class
structure-object
```

### <span id="4.3.1">4.3.1</span> 类的介绍

==类== 是一个确定一组其他独享的结构和行为的对象, 这一组对象称为它的实例.

类可以继承其它类的结构和行为. 一个类的定义中出于继承目的而引用其它类, 称这个类是其它类的子类.
设计用于被继承目的的类, 称为是继承类的超类.

类有一个名称. 函数`class-name`以类对象为传递参数, 返回它的名称.
匿名类的名称是`nil`.
符号可以命名类. 函数`find-class`已符号为传递参数, 返回该符号名称的类.
如果类的名称是一个符号, 类的名称命名了该类, 则称 ==类有合适的名称==. 即, 类`C`有合适的名称`S`, 如果`S=(class-name C)`且`C=(find-class S)`.
注意可能有`(find-class S1) = (find-class S2)`但`S1 /= S2`的情况.
如果`C=(find-class S)`, 称`C`是命名为`S`的类.

类`C1`是类`C2`的直接超类, 如果`C2`显式的在定义中指定`C1`作为超类. 在这种情况下, `C2`是`C1`的直接子类.
类`Cn`是类`C1`的超类, 如果存在一组类`C2,...,Cn-1`, 其中对`1 <= i < n`, `Ci+1`是`Ci`的直接超类. 在这种情况下, `C1`是`Cn`的子类.
类不是自身的超类或子类. 即, 如果`C1`是`C2`的超类, 则`C1 /= C2`.
给定类`C`和它的所有超类的集合, 称为`C`和它的超类.

每个类有一个类优先级列表, 它是在给定类和它的超类上的全序.
全序被表述为按从最特殊到最不特殊排列的列表. 类优先级列表被多种方式使用.
通常, 较特殊的类可以遮盖从较不特殊的类继承的特性.
方法选择和组合处理, 使用类优先级列表将方法按最特殊到最不特殊排序.

当定义类时, 在定义形式中的直接超类的顺序很重要. 每个类有一个 ==本地优先级顺序==, 是一个该类和按在其定义形式中直接超类顺序排列的直接超类.

类的优先级列表总是与列表中每个类的本地优先级顺序一致. 每个本地优先级顺序中类在类优先级列表中按相同的顺序出现.
如果本地优先级顺序与其他的不一致, 构造不出类优先级列表, 发出错误信号.
类优先级列表和它的计算在[4.3.5 确定类优先级列表](#4.3.5)中讨论.

类被组织成一个有向无环图. 有两个特别的类: `t`和`standard-object`.
名称为`t`的类没有超类, 它是除自身之外的所有类的超类.
名称为`standard-object`的类是类`standard-class`的一个实例, 是出自身外其它是类`standard-class`的实例的所有类的超类.

存在从对象系统类空间到类型空间的映射. 该文档中描述的许多标准类型有响应的同名的类. 一些类型没有相应的类.
集成类和类系统, 在[4.3.7 集成类型和类](#4.1.7)中讨论.

类被表示成自身是类的实例的对象. 对象的类的类是该对象的元类.
不存在歧义时, 术语元类用于引用一个自身实例也是类的类.
元类确定了被类使用的继承形式, 这些类是其实例和属于这些类的示例的表示.
对象系统提供了一个默认元类: `standard-class`, 对大多数程序适用.

除非特别说明, 该标准中提到的所有类是类`standard-class`的实例, 所有广义函数是类`standard-generic-function`的实例,
所有方法是类`standard-method`的实例.

### <span id="4.3.1.1">4.3.1.1</span> 标准元类

对象系统提供了一组预定义的元类. 包括: 类`standard-class`、`built-in-class`和`structure-class`:

- 类`standard-class`是用`defclass`定义的类的类.
- 类`built-in-class`是其实例是有受限能力的特殊实现的类的类. 与标准类型对应的类可以是`built-in-class`的实例. [图 4-8. 与预定义的类型描述对应的类.](#Figure4-8)中列出了要求有对应的类的预定义的类型描述符. 是否将这些类实现为内置类是依赖于实现的.
- 用`defstruct`定义的类是类`structure-class`的实例.

### <span id="4.3.2">4.3.2</span> 定义类

用宏`defclass`定义新命名的类.

类的定义包括:

- 新类的名称. 对新定义的类, 这个名称是合适的名称.
- 新类的直接超类的列表.
- 一组槽描述符. 每个槽描述符包括槽的名称和槽选项. 槽选项只属于一个槽. 如果一个类定义中有两个相同的名称的槽描述符, 发出错误信号.
- 一组类选项. 每个类选项属于类.

`defclass`中槽选项和类选项提供了机制:

- 为给定槽提供默认初始值形式
- 要求自动生成读写槽的广义函数的方法
- 控制给定槽是否被类的所有实例共享, 还是每个实例有自己的槽
- 为实例创建提供一组初始化传递参数和初始化传递参数默认值
- 指示元类不是默认的. `:metaclass`选项被保留给未来使用, 实现可以扩展利用`:metaclass`选项
- 指示在槽中存储预期类型的值
- 指示槽的文档字符串

### <span id="4.3.3">4.3.3</span> 创建类的实例

广义函数`make-instance`创建和返回类的新实例.
对象系统提供了描述如何初始化新实例的机制. 例如, 可以通过给`make-instance`提供传递参数或提供默认初始值, 指定新建对象的槽的初始值.
进一步的初始化活动可以用作为初始化协议一部分的广义函数的方法来执行.
完整的初始化协议在[7.1 对象创建和初始化](../07-Objects#7.1)中描述.

### <span id="4.3.4">4.3.4</span> 继承(Inheritance)

类可以从超类中继承方法、槽和一些`defclass`选项.
其他章节描述方法继承、槽和槽选项继承、类选项继承.

#### <span id="4.3.4.1">4.3.4.1</span> 示例: 继承

``` lisp
(defclass C1 ()
    ((S1 :initform 5.4 :type number)
     (S2 :allocation :class)))

(defclass C2 (C1)
    ((S1 :initform 5 :type integer)
     (S2 :allocation :instance)
     (S3 :accessor C2-S3)))
```

类`C1`的实例有一个本地槽`S1`, 它的默认初始值是5.4, 它的值总是一个数值.
类`C1`也有一个共享槽`S2`.

类`C2`的实例有一个本地槽`S1`, 它的默认初始值是5, 它的值的类型是`(and integer number)`.
类`C2`的实例也有本地槽`S2`和`S3`, 类`C2`有一个方法`C2-S3`读取槽`S3`的值, 有一个`(setf C2-S3)`方法写槽`S3`的值.

#### <span id="4.3.4.2">4.3.4.2</span> 类的选项继承

类选项`:default-initargs`被继承.
类的默认初始化传递参数是该类和其超类中`:default-initargs`中提供的初始化传递参数的并集.
当对给定的初始化传递参数有多个默认初始值形式时, 使用根据类优先级列表中最特殊的类提供的默认初始值形式.

如果一个给定的`:default-initargs`选项就同一名称指定了多个初始化传递参数, 发出类型为`program-error`的错误信号.

### <span id="4.3.5">4.3.5</span> 确定类优先级列表

> 参考 https://www.cs.cmu.edu/Groups/AI/html/cltl/clm/node274.html

类的`defclass`形式提供了该类和其直接超类的全序, 这个顺序被称为 ==本地优先级顺序==. 它是类和其直接超类的有序列表.
类 $C$ 的类优先级列表是 $C$ 和它的超类的全序, 由 $C$ 和它的超类的本地优先级顺序构成.

(在优先级列表中)类出现在它的直接超类之前, 一个直接超类出现在`defclass`形式的超类列表中后面的超类之前.

对每个类 $C$ 定义:

$$
R_{C} = \{ (C, C_{1}), (C_{1}, C_{2}), ..., (C_{n-1}, C_{n}) \}
$$

其中, $C_{1}, ... C_{n}$ 在 $C$ 的`defclass`形式中出现, 且保持(本地优先级顺序中的)顺序. 这些有序对生成了类 $C$ 和它的直接超类的全序.

记 $S_{C}$ 为类 $C$ 和它的超类构成的集合, 定义 $R$ 为:

$$
R = \bigcup_{c \in S_{C}}^{} R_{c}
$$

$R$ 可能会或不会生成一个偏序, 依赖于 $R_{c}, c \in S_{C}$ 是否是一致的; 当 $R$ 生成偏序时, 认为它们是一致的.
如果 $R_{c}$ 不是一致的, 称 $R$不是一致的.

计算类 $C$ 的类优先级列表时, 将 $S_{C}$ 中元素按 $R$ 生成的顺序拓扑排序.
当拓扑排序必须从一组类中选择一个类时, 这组类在 $R$ 意义下没有顺序, 则按后面描述的方式选择.

如果 $R$ 不是一致的, 则发出错误信号.

#### <span id="4.3.5.1">4.3.5.1</span> 拓扑排序

拓扑排序在 $S_{C}$ 中找一个类, 在 $R$ 中没有其它类在该类之前.
类 $C$ 作为结果列表中第一个元素, 将 $C$ 从 $S_{C}$ 中移除, 同时从 $R$中移除所有 $(C, D), D \in S_{C}$.
重复上述动作, 将没有前继的类添加到结果列表中. 在没有无前继元素时停止.

如果 $S_{C}$ 不空, 但处理过程已结束, 则 $R$ 不是一致的.
如果在一个有限数量的类的集合中, 每个类在其它类之前出现, 则 $R$ 包含一个 **环**. 即, 有一个类的链 $C_{1}, ..., C_{n}$, 对 $1<= i < n$, $C_{i}$ 在 $C_{i+1}$ 之前出现, 且 $C_{n}$ 在 $C_{1}$ 之前出现.

有时 $S_{C}$ 中有些类没有前继. 这种情况下, 从有直接子类在当前计算出的类优先级列表中的类集合中, 选择有最右边的直接子类对应的类.

如果没有这种候选类, $R$ 不能生成一个偏序, $R_{c}, c \in S_{C}$ 是不一致的.

如果有多个候选类, $\{N_{1}, ..., N_{m}\}, m >= 2$ 是 $S_{C}$ 中没有前继的类集合, $(C_{1}, ..., C_{n}), n >= 1$ 是当前构造出的类优先级列表, $C_{1}$ 是最特殊的类, $C_{n}$ 是最不特殊的类.
对 $1 <= j <= n$, 有 $N_{i}, 1 <= i <= m$ 是 $C_{j}$ 的直接超类, 取对应于最大的 $j$ 的 $N_{i}$ 作为结果列表的下一个元素.

这个从一组没有前继的类中选择的规则的效果是, 简单超类链中的类在类优先级列表中是邻近的, 在每个相对隔离的子图中的类在类优先级列表中是邻近的.
例如, $T_{1}$ 和 $T_{2}$ 是只共有类 $J$ 的两个子图,
假设 $J$ 的超类不出现在 $T_{1}$ 或 $T_{2}$ 中,
$C_{1}$ 是 $T_{1}$ 的底, $C_{2}$ 是 $T_{2}$ 的底,
类$C$ 的直接超类是 $C_{1}, C_{2}$, 则 $C$ 的类优先级列表以 $C$ 开始, 后接 $T_{1}$ 中除了 $J$ 的所有类, 再后接 $T_{2}$ 中所有类,
类 $J$ 和它的超类最后出现.

#### <span id="4.3.5.2">4.3.5.2</span> 示例: 确定类优先级列表

下面的示例确定类pie的优先级列表, 类定义如下:

``` lisp
(defclass pie (apple cinnamon) ())
(defclass apple (fruit) ())
(defclass cinnamon (spice) ())
(defclass fruit (food) ())
(defclass spice (food) ())
(defclass food () ())
```

$S_{pie}$ = {pie, apple, cinnamon, fruit, spice, food, standard-object, t},

$R$ = {(pie, apple), (apple, cinnamon), (apple, fruit), (cinnamon, spice), (fruit, food), (spice, food), (food, standard-object), (standard-object, t)}.

类pie没有前继, 作为结果列表的第一个元素, 当前结果列表为(pie).
将pie从 $S$ 移除, 将pie作为对中第一个元素出现的对从 $R$ 中移除, 则
$S$ = {apple, cinnamon, fruit, spice, food, standard-object, t},
$R$ = {(apple, cinnamon), (apple, fruit), (cinnamon, spice), (fruit, food), (spice, food), (food, standard-object), (standard-object, t)}.

类apple没有前继, 作为结果列表中下一个元素, 当前结果列表为(pie apple),
移除apple及其相关对后,
$S$ = {cinnamon, fruit, spice, food, standard-object, t},
$R$ = {(cinnamon, spice), (fruit, food), (spice, food), (food, standard-object), (standard-object, t)}.

类cinnamon和fruit没有前继, 选择有直接子类在当前计算出的类优先级列表中, 最右的直接子类对应的类.
类apple是fruit的直接子类, pie是cinnamon的直接子类, 但在当前优先级列表中apple出现在pie右边, 所以选择fruit作为结果列表下一元素, 当前结果列表为(pie apple fruit),
$S$ = {cinnamon, spice, food, standard-object, t},
$R$ = {(cinnamon, spice), (spice, food), (food, standard-object), (standard-object, t)}.

类cinnamon是下一个元素, 当前结果列表为(pie apple fruit cinnamon),
$S$ = {spice, food, standard-object, t},
$R$ = {(spice, food), (food, standard-object), (standard-object, t)}.

按顺序添加spice、food、standard-object和t, 类优先级列表为(pie apple fruit cinnamon spic food standard-object t).

也可能写出一组不能排序的类, 例如:

``` lisp
(defclass new-class (fruit apple) ())
(defclass apple (fruit) ())
```

类fruit必须在apple之前出现, 因为必须保留本地超类的顺序.
类apple必须在fruit之前出现, 因为类总是再它的超类前出现.
当发生这种情况时, 发出错误信号, 当系统尝试计算出new-class的类优先级列表时.

下面是可能表现出冲突的定义:

``` lisp
(defclass pie (apple cinnamon) ())
(defclass pastry (cinnamon apple) ())
(defclass apple () ())
(defclass cinnamon () ())
```

类pie的优先级列表是(pie apple cinnamon standard-object t),
类pastry的优先级列表是(pastry cinnamon apple standard-object t),

在类pie的优先级列表中, apple出现在cinnamon之前是没有问题的,
但在类pastry的优先级列表中, apple出现在cinnamon之前是是有问题的.
然而, 无法创建一个有超类pie和pastry的新类.

### <span id="4.3.6">4.3.6</span> 重定义类

`standard-class`的直接实例的类, 可被重定义, 如果新的类也是`standard-class`的直接实例.
重定义一个类会修改现有的类对象, 以反映新的类定义; 并不为该类创建一个新的类对象.
所有通过在旧的`defclass`形式中指定`:reader`、`:writer`、`:accessor`选项创建方法对象, 被从相应的广义函数中移除.
用新的`defclass`形式指定的方法被添加.

当类`C`被重定义, 修改会传播到它的实例和它的子类的实例中.
这种对实例的修改发生在依赖于实现的时刻, 但不晚于下一次该实例的槽被读取或写入.
这种对实例的修改并不改变它的标识(函数`eq`含义下).
这个修改过程可能修改特定实例的槽, 但不创建新的实例.
修改实例是否消耗存储空间是依赖于是实现的.

注意重定义类可能导致添加槽或移除槽.
如果以修改实例的本地槽可访问性的方式重定义类, 这些实例被修改.
如果重定义类没有修改实例的本地槽可访问性, 则这些实例是否被修改是依赖于实现的.

被指定为再旧类和新类中共享的槽值被保留. 如果这种共享的槽在旧类中是未绑定的, 则在新类中也是未绑定的.
在旧类中是本地的、在新类中是共享的槽, 被初始化. 新加的共享槽被初始化.

如果重定义类修改了类的实例中本地槽可访问性, 使用两个步骤的过程修改类的实例.
这个过程可以显式的调用广义函数`make-instances-obsolete`开始.
这个两个步骤的过程可以在一些实现中在其它场景下发生. 例如, 在一些实现中, 如果存储空间中槽的顺序被修改, 会触发这个两个步骤的过程.

过程中第一个步骤, 通过添加新的本地槽, 移除未在新类中定义的本次槽, 来修改实例的解构.
第二个步骤, 初始化新加的本地槽, 执行其它用户定义的动作. 详情见[4.3.6.1 修改实例的结构](#4.3.6.1)和[4.3.6.2 初始化新加的本地槽](#4.3.6.2).

#### <span id="4.3.6.1">4.3.6.1</span> 修改实例的结构

第一个步骤修改被重定义类的实例的结构, 以遵循新的类定义.
被新类定义的、未在旧类中指定为本地或共享的本地槽, 被添加;
在旧类中指定为本地的、未被新类定义为本地或共享的槽被移除.
这些被添加和移除的槽的名称, 作为`update-instance-for-redefined-class`的传递参数.

同时被新类和旧类指定的本地槽值被保留. 如果这样的本地槽是未绑定的, 则保持为未绑定.

在旧类中指定为共享、在新类中指定为本地的槽值被保留. 如果这样的共享槽是未绑定的, 则对应的本地槽未绑定.

#### <span id="4.3.6.2">4.3.6.2</span> 初始化新加的本地槽

第二个步骤初始化新加的本地槽, 指定其它用户定义的动作. 这个步骤用广义函数`update-instance-for-redefined-class`实现, 在修改实例结构的第一个步骤完成之后被调用.

广义函数`update-instance-for-redefined-class`有4个必备参数: 已经过第一个步骤处理的需修改实例、被添加的本地槽名称的列表、被移除的本地槽名称列表、包含被移除的槽的名称和值的关联列表.
被移出的槽中包含在旧类中指定为本地的、在新类中指定为共享的槽.

广义函数`update-instance-for-redefined-class`也接受一些初始化传递参数.
当被系统调用更新被重定义的类的实例时, 不会提供初始化传递参数.

广义函数`update-instance-for-redefined-class`有一个系统提供的主方法, 实例传递参数的参数特化符是类`standard-object`.
这个方法首先检查初始化传递参数的有效性, 无效时发出错误信号(更多信息见[7.1.2 声明初始化传递参数的有效性](../07-Objects#7.1.2)).
然后, 调用广义函数`shared-initialize`, 传递参数: 该实例、新加槽的名称的列表、接收到的初始化传递参数.

#### <span id="4.3.6.3">4.3.6.3</span> 定制类重定义

广义函数`update-instance-for-redefined-class`的方法定义中可以指定实例被更新时执行的动作.
如果只定义了after方法, 它们会在系统提供的用于初始化的主方法之后运行, 因此不会影响`update-instance-for-redefined-class`的默认行为.
因为`update-instance-for-redefined-class`被系统调用时不会传递初始化参数, 被`update-instance-for-redefined-class`的before方法填充的槽的初始化形式不会被`shared-initialize`求值.

`shared-initialize`的方法可以定制类重定义. 更多信息见[7.1.5 共享的可初始化](../07-Objects#7.1.5).

### <span id="4.3.7">4.3.7</span> 集成类型和类

对象系统将类空间映射到类型的空间, 每个有合适名称的类有对应的同名的类型.

每个类的合适名称是有效的类型描述符. 此外, 每个类对象是有效的类型描述符.
所以表达式`(typep object class)`在`object`的类是`class`或`class`的子类时求值为true.
表达式`(subtypep class1 class2)`, 在`class1`是`class2`的子类或同一个类时, 求值为ture, true; 否则求值为false, true.
如果`I`是命名为`S`的类`C`的实例, `C`是`standard-class`的实例, 表达式`(type-of I)`, 在`S`是`C`的合适名称时返回`S`; 否则返回`C`.

因为类的名称和类对象的名称是类型描述符, 它们可被用于特殊形式`the`和类型声明中.

大量但不是所有的预定义的类型描述符有类型相应的有相同合适名称的类, 这些类型描述符见[图 4-8. 与预定义的类型描述对应的类.](#Figure4-8).
例如, 类型`array`有相应命名为`array`的类.
是列表的类型描述符, 例如`(vector double-float 100)`, 没有相应的类.
操作符`deftype`不会创建类.

与预定义类型描述符对应的类, 实现可用三种方式实现. 它们可以是标准类、结构类或系统类.

内建类是其通用实例有受限能力或特殊表示的类.
尝试使用`defclass`定义内建类的子类, 会发出错误信号.
调用`make-instance`创建内建类的通用实例, 会发出错误信号.
在内建类的通用实例上调用`slot-value`, 会发出错误信号.
重定义内建类或使用`change-class`修改内建类实例的类或将其它类的实例的类修改为内建类, 会发出错误信号.
然而, 内建类可用作方法中参数特化符.

可以通过检查元类, 确定一个类是否是内建类. 标准类是类`standard-class`的实例, 内建类是类`built-in-class`的实例,
结构类是类`structure-class`的实例.

通过`defstruct`不带`:type`选项创建的结构类型有相应的类. 这个类是类`structure-class`的通用实例.
`defstruct`的`:include`选项创建与被包含的结构类型对应的类的子类.

除了该规范中显示定义槽, 槽是否被包含到该规范定义的类的实例上函数的操作中, 是依赖于实现的.

如果在特定实现中, 该规范定义的类有该规范未定义的槽, 这些槽的名称必须不是该规范中定义的包的外部符号, 在`CL-USER`包中不可访问.

指定标准类型描述符有相应的类的意图是, 方便用户编写不区分这些类型的方法. 方法选择要求对每个类确定类优先级列表.

类型描述符的层次关系, 反应在与这些类型对应的类的层次关系中.

[图 4-8. 与预定义的类型描述对应的类.](#Figure4-8)列出了与预定义类型描述符对应的类.

<span id="Figure4-8">图 4-8. 与预定义的类型描述对应的类.</span>

``` lisp
arithmetic-error
array
bit-vector
broadcast-stream
built-in-class
cell-error
character
class
complex
concatenated-stream
condition
cons
control-error
division-by-zero
echo-stream
end-of-file
error
file-error
file-stream
float
floating-point-inexact
floating-point-invalid-operation
floating-point-overflow
floating-point-underflow
function
generic-function
hash-table
integer
list
logical-pathname
method
method-combination
null
number
package
package-error
parse-error
pathname
print-not-readable
program-error
random-state
ratio
rational
reader-error
readtable
real
restart
sequence
serious-condition
simple-condition
simple-error
simple-type-error
simple-warning
standard-class
standard-generic-function
standard-method
standard-object
storage-condition
stream
stream-error
string
string-stream
structure-class
structure-object
style-warning
symbol
synonym-stream
t
two-way-stream
type-error
unbound-slot
unbound-variable
undefined-function
vector
warning
```

这些类定义中描述的类优先级列表信息, 是对象系统需要的.

实现可以扩展定义其它有对应类的类型描述符.
实现可以扩展添加其他子类关系, 在类优先级列表中添加其它元素, 只要没有违背该规范中类型关系和隔离性要求.
保证未指定直接超类的标准类与上图中所有类是不相交的, 除了类`t`.

## <span id="4.4">4.4</span> 类型和类的字典

见[类型和类的字典](../Dictionary#4.4).
