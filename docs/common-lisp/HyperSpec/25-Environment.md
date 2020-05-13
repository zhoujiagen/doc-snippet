# 25. 环境

[TOC]

## <span id="25.1">25.1</span> 外部环境

### <span id="25.1.1">25.1.1</span> 顶层循环

顶层循环是用于与Common Lisp系统交互的机制. 因为它由无终止的读取表达式、求值该表达式并打印结果的循环构成, 这个循环有时被称为Lisp读-求值-打印循环(REPL).

顶层循环没有被完整描述, 因此它的用户接口是由实现定义的. 顶层循环打印求值形式的结果中的所有值. 下图列出了Lisp REPL操作的变量.

<span id="Figure25-1">图 25-1. REPL操作的变量.</span>

``` lisp
*
**
***
+
++
+++
/
//
///
-
```

### <span id="25.1.2">25.1.2</span> 调试工具

下图列出了与调试相关的已定义名称:

<span id="Figure25-2">图 25-2. 与调试相关的已定义名称.</span>

``` lisp
*debugger-hook*
apropos
apropos-list
break
describe
documentation
dribble
ed
inspect
invoke-debugger
step
time
trace
untrace
```

### <span id="25.1.3">25.1.3</span> 探究环境

探究环境的已定义名称提供了关于Common Lisp程序执行所在的硬件和软件的配置信息.

下图列出了与探究环境相关的已定义名称:

<span id="Figure25-3">图 25-3. 与探究环境相关的已定义名称.</span>

``` lisp
*features*
lisp-implementation-type
lisp-implementation-version
long-site-name
machine-instance
machine-type
machine-version
room
short-site-name
software-type
software-version
```

### <span id="25.1.4">25.1.4</span> 时间

Common Lisp中时间按四种方式表示: 解码的时间、世界时间、内部时间和秒. 解码的时间和世界时间主要用于表示日历时间, 精确到秒. 内部时间用于表示计算机时间(例如运行时间)的度量, 其精确性是依赖于实现的, 是由`internal-time-units-per-second`指定的内部时间单元. 内部时间可用于绝对或相对时间度量. 世界时间和解码的时间智能用于绝对时间度量. 在函数`sleep`中, 时间间隔由非负实数的秒数表示.

下图列出了与时间相关的已定义名称:

<span id="Figure25-4">图 25-4. 包含时间的已定义名称.</span>

``` lisp
decode-universal-time
encode-universal-time
get-decoded-time
get-internal-real-time
get-internal-run-time
get-universal-time
internal-time-units-per-second
sleep
```

#### <span id="25.1.4.1">25.1.4.1</span> 解码的时间

解码的时间是由9个值组成的系列, 表示日历时间中一个时刻点(不考虑闰秒).

* 秒<br>
0到59的整数, 包含两端值.
* 分钟<br>
0到59的整数, 包含两端值.
* 小时<br>
0到23的整数, 包含两端值.
* 日<br>
1到31的整数, 包含两端值(当然上界依赖于年和月).
* 月<br>
1到12的整数, 包含两端值; 1表示1月, 2表示2月等等.
* 年<br>
表示公元年的证书. 然而, 如果整数在0到99之间, 使用显式年; 即实际年模100后在当前年五十年范围内(前包含后不包含).因此, 在1978年, 年28表示1928, 但年27表示2027. (按这种格式返回时间的函数总是返回完整的年数字)
* 一周中的一天<br>
0到6的整数, 包含两端值. 0表示周一, 1表示周二等等.
* 夏令时标志<br>
一个广义布尔值, 为true时表示使用夏令时.
* 时区<br>
一个时区.

下图列出了与解码的时间相关的已定义名称:

<span id="Figure25-5">图 25-5. 包含解码的时间中时间的已定义名称.</span>

``` lisp
decode-universal-time
get-decoded-time
```

#### <span id="25.1.4.2">25.1.4.2</span> 世界时间

> GMT: 格林尼治时间.

世界时间是表示为一个非负整数的绝对时间, 从1900 GMT 1月1日午夜历经的秒数(不考虑闰秒). 因此时间1表示1999 GMT 1月一日 00:00:01(即12:00:01 a.m.). 类似的, 时间2398291201表示 1976 GMT 1月1日 00:00:01.
1900年不是闰年, 在Common Lisp的一年是闰年, 当且仅当, 它对应的数值被4整除, 例外情况: 被100整除的不是闰年, 被400整除的是润年.
因为, 2000年是闰年.
因为世界时间必须是一个非负整数, Common Lisp不能处理基时间之前的时间.

<span id="Figure25-6">图 25-6. 包含时间时间中时间的已定义名称.</span>

``` lisp
decode-universal-time
encode-universal-time
get-universal-time
```

#### <span id="25.1.4.3">25.1.4.3</span> 内部时间

内部时间依据称为内部时间单元的依赖于实现的单元, 将时间表示为单个整数. 相对时间按这些单元计算. 绝对时间是相对于任意时间基的.

下图列出了与内部时间相关的已定义名称.

<span id="Figure25-7">图 25-7. 包含内部时间中时间的已定义名称.</span>

``` lisp
get-internal-real-time
get-internal-run-time
internal-time-units-per-second
```

#### <span id="25.1.4.4">25.1.4.4</span> 秒

函数`sleep`的传递参数是非负的实数秒数. 可以认为这是一个相对世界时间, 但存在一个重要的差别: 世界时间总是非负的整数, `sleep`的传递参数可以是任意非负的实数, 允许表示分数秒.

<span id="Figure25-8">图 25-8. 包含秒中时间的已定义名称.</span>

``` lisp
sleep
```

## <span id="25.2">25.2</span> 环境的字典

见[环境的字典](../Dictionary#25.2).
