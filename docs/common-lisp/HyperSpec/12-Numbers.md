# <span id="12">12</span> 数值

[TOC]

## <span id="12.1">12.1</span> 数值的概念

### <span id="12.1.1">12.1.1</span> 数值操作

Common Lisp提供了大量与数值相关的操作. 这一节提供了这些操作的概览, 将它们分组以体现它们之间的关系.

<span id="Figure12-1">图 12-1. 与算术相关的操作符.</span>

``` lisp
*
+
-
/
1+
1-
conjugate
decf
gcd
incf
lcm
```

<span id="Figure-">图 12-2. 与指数、对数和三角学相关的已定义名称.</span>

``` lisp
abs
acos
acosh
asin
asinh
atan
atanh
cis
cos
cosh
exp
expt
isqrt
log
phase
pi
signum
sin
sinh
sqrt
tan
tanh
```

<span id="Figure12-3">图 12-3. 数值比较和谓词操作符.</span>

``` lisp
/=
<
<=
=
>
>=
evenp
max
min
minusp
oddp
plusp
zerop
```

<span id="Figure12-4">图 12-4. 数值类型操作和转换相关的已定义名称.</span>

``` lisp
ceiling
complex
decode-float
denominator
fceiling
ffloor
float
float-digits
float-precision
float-radix
float-sign
floor
fround
ftruncate
imagpart
integer-decode-float
mod
numerator
rational
rationalize
realpart
rem
round
scale-float
truncate
```

#### <span id="12.1.1.1">12.1.1.1</span> 数值操作中的可结合性和可交换性

对数学意义上可结合(可交换)的函数, 符合标准的实现可以按与可结合(可交换)重排一致的方式处理传递参数.
这并不影响传递参数求值的顺序; 关于求值顺序的讨论见[3.1.2.1.2.3 函数形式(Function Forms)](../03-Evaluation-and-Compilation#3.1.2.1.2.3).
只是传递参数的值如何被处理的顺序未描述.
这意味着实现在自动转型上存在差异, 见[12.1.1.2 数值操作中的传播](#12.1.1.2).

符合标准的实现可以显式的将操作分隔为不同的(可能内嵌的)函数形式, 或者显式调用执行转型的函数, 来控制处理顺序.

##### <span id="12.1.1.1.1">12.1.1.1.1</span> 示例: 数值操作中的可结合性和可交换性

考虑下面的表达式, 假设`1.0`和`1.0e-15`表示单精度浮点数:

``` lisp
(+ 1/3 2/3 1.0d0 1.0 1.0e-15)
```

一个符合标准的实现可以按从左至右的顺序处理传递参数, 首先执行`1/3`与`2/3`相加获得`1`, 将其转换为双精度浮点数以与`1.0d0`相加, 后续转换并加上`1.0`和`1.0e-15`.

另一个符合标准的实现可以按从右至左的顺序处理床底参数, 首先执行`1.0`与`1.0e-15`单精度浮点数假发(可能在处理中丢失精度), 将其和转换为双精度浮点数以加上`1.0d0`, 然后将`2/3`转换为双精度浮点数并相加, 再将`1/3`转换为双精度浮点数并相加.

第三个符合标准的实现可以首先扫描所有传递参数, 首先处理所有有理数以精确计算这一部分, 然后找到有最大浮点数格式中传递参数并相加, 然后加上所有其他传递参数, 依次转换(可能是为保持计算尽可能精确的错误尝试).

在任何情况下, 这三个策略都是合法的.

符号标准的程序可以通过(使用括号)控制顺序, 例如:

``` lisp
(+ (+ 1/3 2/3) (+ 1.0d0 1.0e-15) 1.0)
```

#### <span id="12.1.1.2">12.1.1.2</span> 数值操作中的传播

数值操作中隐式的传递参数转型的传播规则, 见[12.1.4.4 浮点数精度传播规则](#12.1.4.4)、[12.1.4.1 浮点数和有理数传播规则](#12.1.4.1)、[12.1.5.2 复数传播规则](#12.1.5.2).

#### <span id="12.1.1.3">12.1.1.3</span> 将整数视为位和字节

##### <span id="12.1.1.3.1">12.1.1.3.1</span> 整数上的逻辑操作

逻辑操作要求整数作为传递参数, 如果提供的一个传递参数不是整数时, 应该发出类型为`type-error`的错误信号.
逻辑操作的整数传递参数被视为使用补码表示.

下图列出了与数值上逻辑操作相关的已定义名称.

<span id="Figure12-5">图 12-5. 与数值上逻辑操作相关的已定义名称.</span>

``` lisp
ash
boole
boole-1
boole-2
boole-and
boole-andc1
boole-andc2
boole-c1
boole-c2
boole-clr
boole-eqv
boole-ior
boole-nand
boole-nor
boole-orc1
boole-orc2
boole-set
boole-xor
integer-length
logand
logandc1
logandc2
logbitp
logcount
logeqv
logior
lognand
lognor
lognot
logorc1
logorc2
logtest
logxor
```

##### <span id="12.1.1.3.2">12.1.1.3.2</span> 整数上的字节操作

字节操作函数使用称为字节描述符的对象指定整数中特定字节的大小和位置.
字节描述符的表示是依赖于实现的, 可以是也可以不是一个数值.
函数`byte`构造一个字节描述符, 可被其他字节操作函数接受.

下图列出了与操作数值中字节相关的已定义名称.

<span id="Figure12-6">图 12-6. 与操作数值中字节相关的已定义名称.</span>

``` lisp
byte
byte-position
byte-size
deposit-field
dpb
ldb
ldb-test
mask-field
```

### <span id="12.1.2">12.1.2</span> 依赖于实现的数值常量

下图列出了与关于数值的依赖于实现的细节相关的已定义名称.

<span id="Figure12-7">图 12-7. 与关于数值的依赖于实现的细节相关的已定义名称.</span>

``` lisp
double-float-epsilon
double-float-negative-epsilon
least-negative-double-float
least-negative-long-float
least-negative-short-float
least-negative-single-float
least-positive-double-float
least-positive-long-float
least-positive-short-float
least-positive-single-float
long-float-epsilon
long-float-negative-epsilon
most-negative-double-float
most-negative-fixnum
most-negative-long-float
most-negative-short-float
most-negative-single-float
most-positive-double-float
most-positive-fixnum
most-positive-long-float
most-positive-short-float
most-positive-single-float
short-float-epsilon
short-float-negative-epsilon
single-float-epsilon
single-float-negative-epsilon
```

### <span id="12.1.3">12.1.3</span> 有理数计算

这一节的规则应用于有理数计算.

#### <span id="12.1.3.1">12.1.3.1</span> 未绑定的有理数精度规则

因为整数和比值可以是任意量级, 通常有理数计算不能上溢(尽管可能没有足够的表示结果的存储空间).

#### <span id="12.1.3.2">12.1.3.2</span> 有理数的标准表示规则

如果计算产生的结果是两个整数的数学比值, 分子能够整除分母, 则结果会被转换为等价的整数.

如果分子不能整除分母, 有理数的标准表示是比值, 该比值的分子和分母的最大公约数是1, 分子为大于1的正数.

`-0`作为输入(默认语法下), 总是表示整数`0`.
符合标准的实现中, 整数负零与整数零不能有不同的表示.
然而对浮点数这种不同是可以存在的, 见类型`float`.

#### <span id="12.1.3.3">12.1.3.3</span> 浮点数可代换性规则

当无理数数学函数的传递参数都是有理数, 且其数学结果也是有理数, 则除了特别说明, 允许实现返回精确的有理数结果或者单精度浮点数近似结果.
如果所有传递参数都是有理数, 但结果不能表示为有理数数值, 则总是返回单精度浮点数近似结果.

如果无理数数学函数的传递参数都是类型`(or rational (complex rational))`, 并且其数学结果是复数, 有有理数实部和虚部, 则除非显式说明, 允许实现或者返回类型为`(or rational (complex rational))`的精确结果, 或者返回单精度浮点数(只在数学结果的虚部为零时), 后者返回`(complex single-float)`.
如果所有传递参数的类型都是`(or rational (complex rational))`, 但结果不能表示为有理数或复数有理数, 则返回值的类型是`single-float`或者`(complex single-float)`.

浮点数的可代换性, 不能应用于有理数函数`+`、`-`、`*`和`/`, 也不能用于相关的操作符`1+`、`1-`、`incf`、`decf`和`conjugate`.
对于有理数函数, 如果所有传递参数都是有理数, 则结果是有理数; 如果所有传递参数类型都是`(or rational (complex rational))`, 则结果的类型是`(or rational (complex rational))`.

<span id="Figure12-8">图 12-8. 受浮点数可代换性规则影响的函数.</span>

``` lisp
函数       示例结果
abs       (abs #c(3 4)) =>  5 or 5.0
acos      (acos 1) =>  0 or 0.0
acosh     (acosh 1) =>  0 or 0.0
asin      (asin 0) =>  0 or 0.0
asinh     (asinh 0) =>  0 or 0.0
atan      (atan 0) =>  0 or 0.0
atanh     (atanh 0) =>  0 or 0.0
cis       (cis 0) =>  1 or #c(1.0 0.0)
cos       (cos 0) =>  1 or 1.0
cosh      (cosh 0) =>  1 or 1.0
exp       (exp 0) =>  1 or 1.0
expt      (expt 8 1/3) =>  2 or 2.0
log       (log 1) =>  0 or 0.0
          (log 8 2) =>  3 or 3.0
phase     (phase 7) =>  0 or 0.0
signum    (signum #c(3 4)) =>  #c(3/5 4/5) or #c(0.6 0.8)
sin       (sin 0) =>  0 or 0.0
sinh      (sinh 0) =>  0 or 0.0
sqrt      (sqrt 4) =>  2 or 2.0
          (sqrt 9/16) =>  3/4 or 0.75
tan       (tan 0) =>  0 or 0.0
tanh      (tanh 0) =>  0 or 0.0
```

### <span id="12.1.4">12.1.4</span> 浮点数计算

下面的规则应用于浮点数计算.

#### <span id="12.1.4.1">12.1.4.1</span> 浮点数和有理数传播规则

当有理数和浮点数用于数值函数时, 有理数被转换为与浮点数有相同格式的浮点数.
对于像函数`+`这种可以有多于两个传递参数的函数, 允许操作的部分使用有理数执行, 剩余部分使用浮点数算术执行.

当有理数和浮点数使用数值函数比较时, 调用函数`rational`将浮点数转换为有理数, 然后执行比较.
对于复数, 其实部和虚部被分别处理.

##### <span id="12.1.4.1.1">12.1.4.1.1</span> 示例: 浮点数和有理数传播规则

``` lisp
;;;; 组合使用有理数和浮点数
;;; This example assumes an implementation in which
;;; (float-radix 0.5) is 2 (as in IEEE) or 16 (as in IBM/360),
;;; or else some other implementation in which 1/2 has an exact
;;;  representation in floating point.
(+ 1/2 0.5) =>  1.0
(- 1/2 0.5d0) =>  0.0d0
(+ 0.5 -0.5 1/2) =>  0.5

;;;; 比较有理数和浮点数
;;; This example assumes an implementation in which the default float
;;; format is IEEE single-float, IEEE double-float, or some other format
;;; in which 5/7 is rounded upwards by FLOAT.
(< 5/7 (float 5/7)) =>  true
(< 5/7 (rational (float 5/7))) =>  true
(< (float 5/7) (float 5/7)) =>  false
```

#### <span id="12.1.4.2">12.1.4.2</span> 浮点数近似规则

浮点数计算只是近似计算, 尽管描述时结果是数学上正确的.
因为浮点数近似内在的误差, 数学上恒等的两个表达式可能计算结果不同.
浮点数的精度不一定与数值的精确性相关.
例如, `3.142857142857142857`是比`3.14159`更精密的对 $\pi$ 的近似, 但后者更精确.
精度的含义是表示中的位的数量.
当操作使用了短精度和长精度, 结果将是长精度.
Common Lisp中函数假设它们的传递参数的精确性不超过他们的精度.
因此, 当使用两个小浮点数时, 结果是小浮点数.
Common Lisp中函数从不自动将大精度转换为小精度.

#### <span id="12.1.4.3">12.1.4.3</span> 浮点数下溢和上溢规则

如果浮点数计算导致指数上溢或下溢, 则发出类型为`floating-point-overflow`或`floating-point-underflow`的错误信号.

#### <span id="12.1.4.4">12.1.4.4</span> 浮点数精度传播规则

数值函数的结果是与在所有浮点数传递参数中最大格式相同的浮点数.

### <span id="12.1.5">12.1.5</span> 复数计算

下面的规则应用于复数计算.

#### <span id="12.1.5.1">12.1.5.1</span> 复数可代换性规则

除了在执行无理数函数和超越函数时, 没有数值函数生成复数结果, 除非它的一个或多个传递参数是复数.

#### <span id="12.1.5.2">12.1.5.2</span> 复数传播规则

当计算中有实数和复数时, 实数被转换为虚部为`0`的复数.

#### <span id="12.1.5.3">12.1.5.3</span> 复数有理数标准表示规则

如果计算的结果是一个复数, 其实部的类型是`rational`, 虚部是零, 则结果被转换成实部部分的有理数.这个规则不适用于实数的各部分是浮点数的情况.
例如, `#C(5 0)`与`5`在Common Lisp中不是相异的两个对象(在`eql`语义下相同); `#C(5.0 0.0)`与`5.0`在Common Lisp中总是两个不同的对象, 在`eql`语义下不相同, 尽管在`equalp`和`=`语义下是相同的.

##### <span id="12.1.5.3.1">12.1.5.3.1</span> 示例: 复数有理数标准表示规则

``` lisp
#c(1.0 1.0) =>  #C(1.0 1.0)
#c(0.0 0.0) =>  #C(0.0 0.0)
#c(1.0 1) =>  #C(1.0 1.0)
#c(0.0 0) =>  #C(0.0 0.0)
#c(1 1) =>  #C(1 1)
#c(0 0) =>  0
(typep #c(1 1) '(complex (eql 1))) =>  true
(typep #c(0 0) '(complex (eql 0))) =>  false
```

#### <span id="12.1.5.4">12.1.5.4</span> 主值[^1]和分支切割[^2]

[^1]: https://en.wikipedia.org/wiki/Principal_value
[^2]: https://zh.wikipedia.org/wiki/复平面

有大量无理数函数和超越函数在复数域中是乘法定义的; 例如对数函数有无限数量的复数值.
在这种情况下, 必须选择一个主值作为函数结果返回.
通常, 这些值不能在连续的区间中选择; 不许在域中定义称为分支切割的线, 它定义了区间的不连续性.
Common Lisp依据 **Principal Values and Branch Cuts in Complex APL.** 定义了复数函数的分支切割、主值和边界条件. 应用于每个函数的分支切割规则位于各函数的描述中.

下图列数了复数域中的恒等式:

<span id="Figure12-9">图 12-9. 复数域中的三角恒等式.</span>


``` lisp
sin i z = i sinh z  sinh i z = i sin z        arctan i z = i arctanh z
cos i z = cosh z    cosh i z = cos z          arcsinh i z = i arcsin z
tan i z = i tanh z  arcsin i z = i arcsinh z  arctanh i z = i arctan z
```

下图展示了讨论中分支切割的四象限数:

<span id="Figure12-10">图 12-10. 分支切割的四象限数.</span>

![图 12-10. 分支切割的四象限数.](./images/Figure12-10.Quadrant_Numbering_for_Branch_Cuts.gif)

### <span id="12.1.6">12.1.6</span> 区间设计器

数值类型描述符的复合类型描述符形式, 允许用于指定实轴上的区间, 它描述了类型的子类型, 它应该使用相应的原子类型描述符描述.
类型T的子类型用称为类型T的区间设计器的一对有序对象描述.

类型T的两个区间设计器的第一个可以是:

- 类型为T的数值N: 表示N的包含下界, 即T的子类型的元素大于等于N.
- 元素为类型是T的数值M的单例列表: 表示M的不包含下界, 即T的子类型的元素大于M.
- 符号`*`: 表示区间没有下界

类型T的两个区间设计器的第二个可以是:

- 类型为T的数值N: 表示N的包含上界, 即T的子类型的元素小于等于N.
- 元素为类型是T的数值M的单例列表: 表示M的不包含上界, 即T的子类型的元素小于M.
- 符号`*`: 表示区间没有上界

### <span id="12.1.7">12.1.7</span> 随机状态操作

下图列出了一些可用于随机状态的已定义名称.

<span id="Figure12-11">图 12-11. 随机状态的已定义名称.</span>

``` lisp
*random-state*
make-random-state
random
random-state-p
```

## <span id="12.2">12.2</span> 数值的字典

见[数值的字典](../Dictionary#12.2).
