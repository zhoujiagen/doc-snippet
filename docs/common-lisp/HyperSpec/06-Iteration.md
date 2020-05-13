# 6. 迭代

[TOC]

## 6.1 循环功能

### 6.1.1 循环功能的概览

宏`loop`执行迭代.

#### 6.1.1.1 简单的循环与扩展的循环

`loop`行为被分为两类: 简单的`loop`形式和扩展的`loop`形式.

##### 6.1.1.1.1 简单的循环

简单的`loop`形式有一个只包含复合形式的体. 体中形式按从左至右的顺序依次求值. 在不结束的环内, 当求值完最后一个形式后, 第一个形式被再次求值, 等等. 简单的`loop`形式建立名称为`nil`的隐式块. 可以通过显式的转移控制到隐式块(使用`return`或`return-from`)或到块外的退出点(使用`throw`、`go`或`return-from`), 终止简单的`loop`的执行.

##### 6.1.1.1.2 扩展的循环

扩展的`loop`形式有包含原子表达式的体. 宏`loop`处理这种形式时, 调用称为循环功能的功能.

循环功能提供了对通过循环方案实现的迭代中使用的机制的标准访问, 这些机制由循环关键字引入.

扩展的`loop`形式的体被拆分为`loop`从句, 每个由循环关键字和形式构成.

#### 6.1.1.2 循环关键字

循环关键字不是真正的关键字, 它们是特殊的符号, 按名称而不是对象标识识别, 只在循环功能中有意义.
循环关键字是一个符号, 不管它在哪个表中被访问, 按名称(不是其标识)识别.

通常, 循环关键字不是COMMON-LISP包的外部符号, 除非在Common Lisp中恰好有与其同名的符号用于其它用户. 例如, COMMON-LISP包中有名称为`UNLESS`的符号, 但没有名称为`UNTIL`的符号.

如果`loop`形式中没有提供循环关键字, 循环功能重复的执行循环体; 见[6.1.1.1.1 简单的循环](#6.1.1.1.1).

#### 6.1.1.3 解析循环从句

扩展的`loop`形式的句法部分称为从句; 解析的规则是由从句的关键字确定的. 下面的示例展示了一个有6个从句的`loop`形式:

``` lisp
(loop for i from 1 to (compute-top-value)       ; first clause
      while (not (unacceptable i))              ; second clause
      collect (square i)                        ; third clause
      do (format t "Working on ~D now" i)       ; fourth clause
      when (evenp i)                            ; fifth clause
        do (format t "~D is a non-odd number" i)
      finally (format t "About to exit!"))      ; sixth clause
```

每个循环关键字引入了一个复合循环从句或一个简单的循环从句(由一个循环关键字后接一个简单形式构成). 从句中形式的数量是由从句开始处的循环关键字和从句中的辅助关键字确定的. 关键字`do`、`doing`、`initially`和`finally`是仅有的可以有任意数量形式的关键字, 这些形式被分组在一个隐式progn中.

循环从句可以包含辅助关键字, 它们有时被称为 ==介词==. 例如, 上面的示例中包含介词`from`和`to`, 标记了步进的起止值.

关于`loop`语法的详细信息见宏`loop`.

#### 6.1.1.4 展开循环形式

宏形式`loop`被展开为一个包含一个或多个 **绑定形式** (建立循环变量的绑定)、一个 **块** 和一个 **标签体** (表达循环控制结构)的形式. `loop`中建立的变量像`let`或`lambda`中那样被绑定.

实现可以交错的用绑定设置初始值. 然而, 初始值的赋值总是按用户指定的顺序计算. 因此, 一个变量可能绑定到对其类型无意义的值上, 然后在循环序言部分使用`setq`设置为正确的初始值.
这种交错设置的一个含义是, 在除了for-as-equals-then之外的for-as子句中的初始值形式(称为`form1`、`form1`、`form3`、`setp-fun`、`vector`、`hash-table`和`package`)中的词法环境, 在形式求值时, 是否只包含在该形式之前的循环变量或者包括更多的或所有循环变量, 是依赖于实现的; for-as-equals-then形式中`form1`和`form2`包含有所有循环变量的词法环境.

在形式被展开后, 它由标签体中三个基本部分构成: 循环序言、循环体和循环后记.

* 循环序言(prologue)<br>
循环序言中包含在迭代开始之前执行的形式, 例如变量从句中描述的自动变量初始化, 和出现在源码中的`initially`从句.
* 循环体<br>
循环体包含迭代执行中执行的形式, 包括应用特定的计算、终止测试和变量步进.
* 循环后记(epilogue)<br>
循环后记包含在迭代终止后执行的形式(如果有的话), 例如`finally`从句, 和累积从句或终止测试从句的隐式返回值.

源码形式中一些从句只为循环序言提供代码, 这些从句必须在`loop`形式主体中从句之前出现. 另外的一些源码形式为循环后记提供代码.
所有其它的源码形式按在`loop`源码形式中的顺序为最终翻译后的形式提供代码.

除非显式提供了`named`, 宏`loop`的展开生成名称为`nil`的隐式块. 因此, `return-from`(有时`return`也可以)被用于从`loop`中返回值或退出`loop`.

#### 6.1.1.5 循环从句汇总

循环从句分为如下6个种类:

##### 6.1.1.5.1 变量初始化和步长从句汇总

`for`和`as`构造提供了建立要被初始化的变量的迭代控制从句. `for`和`as`从句可以与循环关键字`and`一起使用, 以支持并行初始化和步进. 否则初始化和步进是顺序的.

`with`构造与单个`let`子句类似. `with`从句可以与循环关键字`and`一起使用, 以支持并行初始化.

更多信息见[6.1.2 变量初始化和步长从句](#6.1.2).

##### 6.1.1.5.2 值累积从句汇总

`collect`(或`collection`)构造在其从句中接受一个形式, 并将该形式的值添加到一个值列表的末尾. `loop`结束时默认返回这个值列表.

`append`(或`appending`)构造在其从句中接受一个形式, 将该形式的值追加到一个值列表的末尾. `loop`结束时默认返回这个值列表.

`nconc`(或`nconcing`)构造与`append`构造类似, 但列表的值是使用`nconc`连接的. `loop`结束时默认返回这个值列表.

`sum`(或`summming`)构造在其从句中接受一个形式, 这个形式必须求值为一个数值, 累积这些数值的和. `loop`结束时默认返回这个累积和.

`count`(或`counting`)构造在其从句中接受一个形式, 计数该形式求值为true的次数. `loop`结束时默认返回这个计数.

`minimize`(或`minimizing`)构造在其从句中接受一个形式, 确定求值该形式的最小值. `loop`结束时默认返回这个最小值.

`maximize`(或`maximizing`)构造在其从句中接受一个形式, 确定求值该形式的最大值. `loop`结束时默认返回这个最大值.

更多信息见[6.1.3 值累积从句](#6.1.3).

##### 6.1.1.5.3 终止测试字符汇总

`for`和`as`构造提供了由迭代控制从句确定的终止测试.

`repeat`构造导致在指定次数的迭代后终止.(使用内部变量跟踪迭代的次数)

`while`构造接受一个形式和一个测试, 在测试求值为false时终止. `while`从句等价于表达式`(if (not test) (loop-finish))`.

`until`构造是`while`的逆; 在测试求值为非`nil`值时终止. `until`从句等价于表达式`(it test (loop-finish))`.

`always`构造接受一个形式, 在该形式一旦求值为false时终止`loop`; 在这种情况下, `loop`形式返回`nil`. 否则提供了默认返回值`t`.

`never`构造接受一个形式, 在该形式一旦求值为true时终止`loop`; 这这种情况下, `loop`形式返回`nil`. 否则提供了默认返回值`t`.

`thereis`构造接受一个行his, 在该形式一旦求值为非`nil`对象时终止`loop`; 在这种情况下, `loop`形式返回该对象. 否则提供了默认返回值`nil`.

如果指定了多个终止测试从句, 在任意从句被满足时, 终止`loop`形式.

更多信息见[6.1.4 终止测试从句](#6.1.4).

##### 6.1.1.5.4 无条件执行从句汇总

`do`(或`doing`)构造求值其从句中的所有形式.

`return`构造接受一个形式. 这个形式返回的值立即从`loop`形式返回. 等价于从句`do (return-from block-name value)`, 这里`block-name`是由`named`从句指定的名称, 或者没有`named`从句时为`nil`.

更多信息见[6.1.5 无条件执行从句](#6.1.5).

##### 6.1.1.5.5 条件执行从句汇总

`if`和`when`构造接受一个作为测试的形式和一个在该测试返回true时指定的从句. 这个从句可以是值累积从句、无条件执行从句或另一个条件执行从句; 也可以是这些从句与循环关键字`and`的组合.

`unless`构造与`when`构造类似, 除非取相反的测试结果.

`else`构造为`if`、`when`和`unless`从句提供了可选的部分, 在`if`或`when`的测试返回false或者`unless`的测试返回true时, 执行这个部分. 这个部分是在`if`中描述的从句中的一个.

`end`构造提供了标记条件从句结束的可选部分.

更多信息见[6.1.6 条件执行从句](#6.1.6).

##### 6.1.1.5.6 杂项从句汇总

`named`构造给`loop`的块指定了一个名称.

`initially`构造导致它的形式在循环序言中被求值, 在除了`with`、`for`或`as`提供的初始值设置的所有`loop`代码之前执行.

`finally`构造导致它的形式在常规迭代终止后的循环后记中求值.

更多信息见[6.1.7 杂项从句](#6.1.7).

#### 6.1.1.6 执行的顺序

除了下面列出的例外情况之外, 从句在循环体中按它们在源码中出现的顺序指定. 重复执行, 直到一个从句终止循环、或遇到将控制转移到循环外一点的`return`、`go`或`throw`形式. 下面的动作是顺序指定的例外:

* 所有变量被首先初始化, 不管建立变量的从句在源码中出现的位置. 初始化的顺序遵循这些从句的顺序.
* 所有`initially`从句的代码, 按从句在源码中的顺序, 被收集到一个progn中. 在隐式变量初始化之后, 这些收集的代码在循环的序言中执行一次.
* 所有`finally`从句的代码, 按从句在源码中的顺序, 被收集到一个progn中. 在返回累积从句的任何隐式值之前, 这些收集的代码在循环后记中执行一次. 然而, 在源码中显式的返回, 不会执行循环后中代码, 而是退出循环.
* `with`从句引入了一个变量绑定和一个可选的初始值. 初始值按`with`从句出现的顺序计算.
* 迭代控制从句隐式的执行这些工作:<br>
-- 初始化变量;<br>
-- 步进变量, 通常在循环体的两次执行之间;<br>
-- 执行终止测试, 通常在循环体执行之后立即执行.

#### 6.1.1.7 解构

`d-type-spec`传递参数被用于解构. 如果`d-type-spec`传递参数只由类型`fixnum`、`float`、`t`或`nil`构成, 关键字`of-type`是可选的.
`of-type`构造在这些情况中是可选的, 是为保持前向兼容; 因此下面的两个表达式是等价的:

``` lisp
;;; This expression uses the old syntax for type specifiers.
 (loop for i fixnum upfrom 3 ...)

;;; This expression uses the new syntax for type specifiers.
 (loop for i of-type fixnum upfrom 3 ...)
```

``` lisp
;; Declare X and Y to be of type VECTOR and FIXNUM respectively.
 (loop for (x y) of-type (vector fixnum)
       in l do ...)
```

解构模式的类型描述符一个类型描述符树, 形状与变量名称树相同, 有如下例外:

* 对齐树时, 类型描述符树中的原子与变量名称树中的一个cons匹配, 变量名称树中以该cons为根的子树中每个变量的类型被声明为同一个类型.
* 匹配变量名称树中原子的类型描述符树中一个cons, 是复合类型描述符.

解构允许将一组变量绑定到相应的一组值, 每个值可以绑定到一个变量. 在循环展开中, 变量列表中的每个变量与值列表中的值匹配. 如果变量列表中变量数量比值列表中值的数量多, 剩余的变量被赋予值`nil`. 如果值的数量比变量的数量多, 多余的值被忽略.

为从一个列表给变量`a`、`b`和`c`赋值, 可以使用`for`从句绑定变量`numlist`到提供的(列表)形式的car, 然后使用另一个`for`从句顺序的绑定变量`a`、`b`和`c`.

``` lisp
;; Collect values by using FOR constructs.
 (loop for numlist in '((1 2 4.0) (5 6 8.3) (8 9 10.4))
       for a of-type integer = (first numlist)
       and b of-type integer = (second numlist)
       and c of-type float = (third numlist)
       collect (list c b a))
=>  ((4.0 2 1) (8.3 6 5) (10.4 9 8))
```

允许变量在每个循环迭代中绑定的解构更容易处理这种情况. 使用一个`type-spec`传递参数列表声明类型.
如果所有类型相同, 可以使用便捷解构语法, 如下所示:

``` lisp
;; Destructuring simplifies the process.
 (loop for (a b c) of-type (integer integer float) in
       '((1 2 4.0) (5 6 8.3) (8 9 10.4))
       collect (list c b a))
=>  ((4.0 2 1) (8.3 6 5) (10.4 9 8))
```

``` lisp
;; If all the types are the same, this way is even simpler.
 (loop for (a b c) of-type float in
       '((1.0 2.0 4.0) (5.0 6.0 8.3) (8.0 9.0 10.4))
       collect (list c b a))
=>  ((4.0 2.0 1.0) (8.3 6.0 5.0) (10.4 9.0 8.0))
```

如果使用解构声明或初始化多组变量, 可以使用循环关键字`and`进一步简化:

``` lisp
;; Initialize and declare variables in parallel by using the AND construct.
 (loop with (a b) of-type float = '(1.0 2.0)
       and (c d) of-type integer = '(3 4)
       and (e f)
       return (list a b c d e f))
=>  (1.0 2.0 3 4 NIL NIL)
```

如果在解构列表中使用了`nil`, 它的位置上没有变量.

``` lisp
(loop for (a nil b) = '(1 2 3)
      do (return (list a b)))
=>  (1 3)
```

注意点列表可以用于解构:

``` lisp
(loop for (x . y) = '(1 . 2)
      do (return y))
=>  2
(loop for ((a . b) (c . d)) of-type ((float . float) (integer . integer)) in
      '(((1.2 . 2.4) (3 . 4)) ((3.4 . 4.6) (5 . 6)))
      collect (list a b c d))
=>  ((1.2 2.4 3 4) (3.4 4.6 5 6))
```

如果在单个循环表达式的任意变量绑定从句中同一个变量被两次绑定, 则(在宏展开时)发出类型为`program-error`的错误信号. 这些变量包括本地变量、迭代控制变量和解构使用的变量.

#### 6.1.1.8 副作用的限制

见[3.6 遍历规则和副作用](../03-Evaluation-and-Compilation#3.6).

### 6.1.2 变量初始化和步长从句

#### 6.1.2.1 迭代控制

迭代控制从句允许指定循环迭代的方向. 循环关键字`for`和`as`指示出迭代控制从句. 迭代控制子句之间在终止测试的描述和循环变量的初始化和步进方面存在差异. 迭代子句自身不会导致循环功能返回值, 但可以与值累积从句一起使用返回值.

所有变量在循环序言中被初始化. 一个变量绑定有词法作用域, 除非它被声明`special`; 因此默认情况下, 这些变量只可被文本上在循环内部的形式访问. 步进赋值在循环体中在体中其它形式求值之前执行.

迭代控制从句的变量传递参数可以是一个解构列表. 解构列表是其非`nil`原子为变量名称的树. 见[6.1.1.7 解构](#6.1.1.7).

迭代控制从句`for`、`as`和`repeat`必须在其它循环从句之前出现, 除了建立变量绑定的`initially`、`with`和`named`从句. 当循环中使用了迭代控制从句时, 循环体中相应的终止测试在体中其它代码执行之前求值.

如果使用多个迭代从句控制迭代, 变量初始化和步进默认按顺序执行. 当不需要顺序的绑定和步进时, `and`构造可用于连接两个或多个迭代子句. 用`and`连接从句的迭代行为的区别与宏`do`和`do*`之间的区别类似.

`for`和`as`从句通过使用一个或多个本地循环变量进行迭代, 这些变量被初始化为某些值, 在每次迭代之后被修改或步进. 对于这些从句, 在本地变量达到给定的值或其他循环从句终止迭代时, 迭代终止. 在每个迭代中, 可以使用递增、递减或通过求值形式赋予新值的方式步进变量. 在迭代时, 可以使用解构赋值变量.

关键字`for`和`as`是同义的; 可被互换使用. 这些构造有7个语法格式. 在每个语法格式中, `var`类型可以用可选的`type-spec`传递参数指定. 如果`var`是一个解构列表, 由`type-spec`传递参数指定的类型必须匹配列表元素. **通常, `for`引入新的迭代, `as`引入依赖于之前的迭代描述的迭代**.

##### 6.1.2.1.1 for-as-arithmetic子句

在for-as-arithmetic子句中, `for`和`as`构造从`form1`提供的值到`form2`提供的值, 按`form3`提供的值步进迭代. 每个表达式只被求值一次, 必须求值为一个数值. 变量`var`在第一个迭代中绑定到`form1`的值, 在每个后续迭代中按`form3`的值步进, 或者在没有提供`form3`时按1步进. 下面的循环关键字在这个语法中被视为有效的介词. 至少使用一个介词, 在单个子句中最多使用一行中的一个.

``` lisp
from | downfrom | upfrom
to | downto | upto | below | above
by
```

每个子句的介词短语可以按任意顺序出现. 例如, `from x by y`和`by y from x`都是允许的. 然而, 因为需要保留从左向右的求值顺序, 有副作用时效果可能不同. 考虑:

``` lisp
(let ((x 1)) (loop for i from x by (incf x) to 10 collect i))
=>  (1 3 5 7 9)
; 先求值(incf x)
(let ((x 1)) (loop for i by (incf x) from x to 10 collect i))
=>  (2 4 6 8 10)
```

这些介词的描述如下:

* from<br>
循环关键字`from`指定了步进值的开始值, 由`form1`提供. 步进默认为递增. 如果需要递减步进, 必须将`downto`或`above`与`form2`一起使用. 对递增步进, `from`的默认值为0.
* downfrom, upfrom<br>
循环关键字`downfrom`表明变量`var`按`form3`提供的步长减少; 循环关键字`upfrom`表示变量`var`按`form3`提供的步长增加.
* to<br>
循环关键字`to`标记由`form2`提供的步进结束值. 步进默认为递增. 如果需要递减步进, 必须将`downfrom`与`form1`一起使用, 或者用`downto`或`above`替代`to`与`form2`一起使用.
* downto, upto<br>
循环关键字`downto`指定了递减步进; 循环关键字`upto`指定了增量步进. 在这两种情况中, 每步修改的值由`form3`指定, 当变量`var`越过`form2`的值时终止循环. 因为在递减步进中没有`form1`的默认值, 使用`downto`时必须提供`form1`的值(使用`from`或`downfrom`).
* below, above<br>
循环关键字`below`和`above`分别与`upto`和`downto`等价. 这些关键字在变量`var`的值达到`form2`的值时, 结束迭代; 不包含`form2`的结束值. 因为在递减步进中没有`form1`的默认值, 使用`above`时必须提供`form1`值(使用`from`或`downfrom`).
* by<br>
循环关键字标记由`form3`提供的增量或减量. `form3`的值可以是任意正数. 默认值为1.

在一个迭代控制从句中, `for`或`as`构造在达到指定的界限时终止迭代. 即在变量`var`的值步进到由`form2`提供的界限值(包含或不包含)之前, 迭代继续执行. 如果使用使用`form3`增加或减少变量`var`的值到`form2`的值, 但没有达到该值, 这个范围是不包含的; 循环关键字`below`和`above`提供了不包含的界限. 包含的界限允许变量`var`达到`form2`的值; 循环关键字`to`、`downto`和`upto`提供了包含的界限.

###### 6.1.2.1.1.1 示例: for-as-arithmetic子句

``` lisp
;; Print some numbers.
 (loop for i from 1 to 3
       do (print i))
>>  1
>>  2
>>  3
=>  NIL

;; Print every third number.
 (loop for i from 10 downto 1 by 3
       do (print i))
>>  10
>>  7
>>  4
>>  1
=>  NIL

;; Step incrementally from the default starting value.
 (loop for i below 3
       do (print i))
>>  0
>>  1
>>  2
=>  NIL
```

##### 6.1.2.1.2 for-as-in-list子句

在for-as-in-list子句中, `for`或`as`构造迭代一个列表中的内容. 会检查是否到达列表尾, 就像使用`endp`那样. 变量`var`在每次迭代之前绑定到列表中的后继元素. 在每次迭代结束时, 在这个列表上使用函数`step-fun`; `step-fun`的默认值是`cdr`. 循环关键字`in`和`by`在这个语法中被视为有效的介词. `for`或`as`构造导致当达到列表尾部时终止循环.

###### 6.1.2.1.2.1 示例: for-as-in-list子句

``` lisp
;; Print every item in a list.
 (loop for item in '(1 2 3) do (print item))
>>  1
>>  2
>>  3
=>  NIL

;; Print every other item in a list.
 (loop for item in '(1 2 3 4 5) by #'cddr
       do (print item))
>>  1
>>  3
>>  5
=>  NIL

;; Destructure a list, and sum the x values using fixnum arithmetic.
 (loop for (item . x) of-type (t . fixnum) in '((A . 1) (B . 2) (C . 3))
       unless (eq item 'B) sum x)
=>  4
```

##### 6.1.2.1.3 for-as-on-list子句

在for-as-on-list子句中, `for`或`as`构造在一个列表上迭代. 检查是否到达列表尾, 就像使用`atom`那样. 变量`var`绑定到`form1`提供的列表的后继尾. 在每次迭代结束时, 在这个列表上应用函数`step-fun`; `step-fun`的默认值是`cdr`. 循环关键字`on`和`by`在这个语法中被视为有效介词. `for`或`as`构造导致在达到列表尾时结束循环.

###### 6.1.2.1.3.1 示例: for-as-on-list子句

``` lisp
;; Collect successive tails of a list.
 (loop for sublist on '(a b c d)
       collect sublist)
=>  ((A B C D) (B C D) (C D) (D))

;; Print a list by using destructuring with the loop keyword ON.
 (loop for (item) on '(1 2 3)
       do (print item))
>>  1
>>  2
>>  3
=>  NIL
```

##### 6.1.2.1.4 for-as-equals-then子句

在for-as-equals-then子句中, `for`或`as`构造在第一个迭代中初始化变量`var`, 将它设置为求值`form1`的结果值, 然后在第二个和后续的迭代中, 将其设置为求值`form2`的结果值. 如果省略了`form2`, 这个构造在第二个和后续的迭代中使用`form1`. 循环关键字`=`和`then`在这个语法中是有效的介词. 这个构造没有提供任何终止测试.

###### 6.1.2.1.4.1 示例: for-as-equals-then子句

``` lisp
;; Collect some numbers.
 (loop for item = 1 then (+ item 10)
       for iteration from 1 to 5
       collect item)
=>  (1 11 21 31 41)
```

##### 6.1.2.1.5 for-as-across子句

在for-as-across子句中, `for`或`as`构造(分别)绑定变量`var`到数组`vector`中的每个元素. 循环关键字`across`标记数组`vector`; 在这个语法中`across`被用作介词. 在提供的数组中没有更多元素可被引用时, 迭代停止. 一些实现可以识别`vector`形式中的`the`特殊形式, 以生成更高效的代码.

###### 6.1.2.1.5.1 示例: for-as-across子句

``` lisp
(loop for char across (the simple-string (find-message channel))
      do (write-char char stream))
```

##### 6.1.2.1.6 for-as-hash子句

在for-as-hash子句中, `for`或`as`构造在一个哈希表的元素、键和值上迭代. 在这个语法中, 使用复合介词设计对哈希表的访问. 变量`var`取提供的哈希表中的每个哈希键或者哈希值. 下面的循环关键字是这个语法中有效的介词:

* being<br>
循环关键字引入循环方案`hash-key`或`hash-value`
* each, the<br>
当使用`hash-key`或`hash-value`时, 循环关键字`each`在`being`之后出现. 循环关键字`the`与`hash-keys`和`hash-values`一起使用, 只是为了便于读取. 这个约定不是必要的.
* hash-key, hash-keys<br>
这些循环关键字访问哈希表中的每个键. 如果在`using`构造中使用了`hash-value`, 迭代可以可选的访问键的值. 值被访问的顺序是未定义的; 哈希表中的空槽被忽略.
* hash-value, hash-values<br>
这些循环关键字访问哈希表中的每个值. 如果在`using`构造中使用了`hash-key`, 迭代可以可选的访问与值对应的键. 键被访问的顺序是未定义的; 哈希表中的空槽被忽略.
* using<br>
循环关键字`using`引入可选的被访问的键或键的值. 如果迭代是在哈希值上的允许访问哈希键, 如果迭代是在哈希键上的允许访问哈希值.
* in, of<br>
这些循环介词引入哈希表.

实际上

``` EBNF
being { each | the } { hash-value | hash-values | hash-key | hash-keys } { in | of }
```

是一个复合介词.

当提供的哈希表中没有更多哈希键或哈希值时, 迭代停止.

##### 6.1.2.1.7 for-as-package子句

在for-as-package子句中, `for`或`as`构造在包中符号上迭代. 在这个语法中, 使用复合介词设计对包的访问. 变量`var`取提供的包中每个符号的值. 下面的循环关键字在这个语法中是有效的介词:

* being<br>
循环关键字`being`引入循环方案`symbol`、`present-symbol`或`external-symbol`.
* each, the<br>
当使用`symbol`、`present-symbol`或`external-symbol`时, 循环关键字`each`在`being`之后出现. 循环关键字`the`与`symbol`、`present-symbol`和`external-symbol`一起使用, 只是为了便于读取. 这个约定不是必要的.
* present-symbol, present-symbols<br>
这些循环方案在包中出现的符号上迭代. 按`find-package`中包传递参数一样的方法给迭代提供包. 如果没有给迭代提供包, 则使用当前包. 如果提供的包不存在, 则发出类型为`package-error`的错误信号.
* symbol, symbols<br>
这些循环方案在包中可访问的符号上迭代. 按`find-package`中包传递参数一样的方法给迭代提供包. 如果没有给迭代提供包, 则使用当前包. 如果提供的包不存在, 则发出类型为`package-error`的错误信号.
* external-symbol, external-symbols<br>
这些循环方案在包中外部符号上迭代. 按`find-package`中包传递参数一样的方法给迭代提供包. 如果没有给迭代提供包, 则使用当前包. 如果提供的包不存在, 则发出类型为`package-error`的错误信号.
* in, of<br>
这些循环介词引入包.

实际上

``` EBNF
being { each | the } { symbol | symbols | present-symbol | present-symbols | external-symbol | external-symbols } { in | of }
```

是一个复合介词.

当在提供的包中没有更多可被引用的符号时, 迭代停止.


###### 6.1.2.1.7.1 示例: for-as-package子句

``` lisp
(let ((*package* (make-package "TEST-PACKAGE-1")))
  ;; For effect, intern some symbols
  (read-from-string "(THIS IS A TEST)")
  (export (intern "THIS"))
  (loop for x being each present-symbol of *package*
         do (print x)))
>>  A
>>  TEST
>>  THIS
>>  IS
=>  NIL
```

#### 6.1.2.2 本地变量初始化

执行`loop`形式时, 本地变量被绑定并初始化. 这些本地变量一直存在直到迭代终止, 这时它们不再存在.
迭代控制从句和累积从句的`into`介词也会建立隐式变量.

`with`构造初始化循环中本地变量. 这些变量只被初始化一次. 如果为变量`var`提供了可选的`type-spec`传递参数, 但没有相关的需被求值的表达式, `var`被初始化为其类型的恰当的默认值. 例如, 对类型`t`、`number`和`float`, 默认是分别是`nil`、`0`和`0.0`. 如果指定了`type-spec`传递参数, 但提供的表达式求值结果值不是该类型, 其后果是未定义的.
`with`构造默认顺序的初始化变量; 即赋值一个变量在求值下一个表达式之前发生. 然而, 使用循环关键字`and`连接多个`with`从句时, 初始化可以并行执行; 即求值所有提供的形式, 求值结果同时赋予相应的变量.

当一些变量的初始化需要依赖与之前绑定的变量时, 使用顺序绑定. 例如, 变量`a`、`b`和`c`按顺序绑定:

``` lisp
(loop with a = 1
      with b = (+ a 2)
      with c = (+ b 3)
      return (list a b c))
=>  (1 3 6)
```

上面的循环的执行效果等价与执行下面的代码:

``` lisp
(block nil
  (let* ((a 1)
         (b (+ a 2))
         (c (+ b 3)))
    (tagbody
        (next-loop (return (list a b c))
                   (go next-loop)
                   end-loop))))
```

如果之前绑定的变量值不需要用于其它本地变量, 可以使用`and`从句指定并行绑定:

``` lisp
(loop with a = 1
      and b = 2
      and c = 3
      return (list a b c))
=>  (1 2 3)
```

上面的循环的执行效果等价与执行下面的代码:

``` lisp
(block nil
  (let ((a 1)
        (b 2)
        (c 3))
    (tagbody
        (next-loop (return (list a b c))
                   (go next-loop)
                   end-loop))))
```

##### 6.1.2.2.1 示例: WITH从句

``` lisp
;; 顺序的绑定
 (loop with a = 1
       with b = (+ a 2)
       with c = (+ b 3)
       return (list a b c))
=>  (1 3 6)

;; 并行的绑定
 (setq a 5 b 10)
=>  10
 (loop with a = 1
       and b = (+ a 2)
       and c = (+ b 3)
       return (list a b c))
=>  (1 7 13)

;; 声明多个类型本地变量的便捷方式
 (loop with (a b c) of-type (float integer float)
       return (format nil "~A ~A ~A" a b c))
=>  "0.0 0 0.0"

;; 声明多个同类型本地变量的便捷方式
 (loop with (a b c) of-type float
       return (format nil "~A ~A ~A" a b c))
=>  "0.0 0.0 0.0"
```

### 6.1.3 值累积从句

构造`collect`、`collecting`、`append`、`appending`、`nconc`、`nconcing`、`count`、`counting`、`maximize`、`maximizing`、`minimize`、`minimizing`、`sum`、`summing`, 允许在循环中累积值.

构造`collect`、`collecting`、`append`、`appending`、`nconc`、`nconcing`, 指定在列表中累积值并返回该列表的从句.
构造`count`、`counting`、`maximize`、`maximizing`、`minimize`、`minimizing`、`sum`、`summing`, 指定累积并返回数值值的从句.

在每个迭代中, 构造`collect`和`collecting`收集提供的形式的值到列表中. 迭代终止时返回该列表. 传递参数`var`被设置为收集的值的列表; 如果提供了`var`, 循环不会自动返回最终的列表. 如果没有提供`var`, 等价于为`var`指定了一个内部名称并在`finally`从句中返回它的值. 就像使用`with`构造一样绑定传递参数`var`. 没有声明`var`的类型的机制, `var`的类型必须是类型`list`.

构造`append`、`appending`、`nconc`、`nconcing`与`collect`类似, 除了提供的形式的值必须是列表.

* 关键字`append`导致它的列表值串接到单个列表中, 就像使用函数`apppend`.
* 关键字`nconc`导致它的列表值串接到单个列表中, 就像使用函数`nconc`.

传递参数`var`被设置为串接值后的列表; 如果提供了`var`, 循环不会自动返回最终的列表. 就像使用`with`构造一样绑定传递参数`var`. 不能指定`var`的类型, `var`的类型必须是类型`list`. 构造`nconc`破坏性的修改它的传递参数.

构造`count`计数提供的形式返回true的次数. 传递参数累积出现的次数; 如果提供了`var`, 循环不会自动返回最终的计数值. 就像使用`with`构造绑定到恰当类型的零值一样, 绑定传递参数`var`. 后续的值(包括必要的转换)用函数`1+`计算. 如果使用了`into var`, 可以用传递参数`type-spec`指定`var`的类型; 提供了一个非数值类型的后果是未定义的. 如果没有`into`变量, 可选的传递参数`type-spec`应用在内部计数的变量上. 默认类型是依赖于实现的, 但必须是类型`fixnum`的超类型.

构造`maximize`和`minimize`比较提供的形式在第一次迭代和后续迭代中的值. 遇到的最大值(使用函数`max`)或最小值(使用函数`min`)被确定并返回. 如果`maximize`和`minimize`从句未被执行过, 累积值是未描述的. 传递参数`var`累积最大或最小值; 如果提供了`var`, 循环不会自动返回最大值或最小值. 就像使用`with`构造一样绑定传递参数`var`. 如果使用了`into var`, 可以用传递参数`type-spec`指定`var`的类型; 提供了一个非数值类型的后果是未定义的. 如果没有`into`变量, 可选的传递参数`type-spec`应用在内部记录最大值或最小值的变量上. 默认类型是依赖于实现的, 但必须是类型`real`的超类型.

构造`sum`生成提供的形式在每次迭代中求值的主值的累加和. 传递参数`var`用于累加和; 如果提供了`var`, 循环不会自动返回最终的和. 就像使用`with`构造绑定到恰当类型的零值一样, 绑定传递参数`var`. 后续的值(包括必要的转换)用函数`1`计算. 如果使用了`into var`, 可以用传递参数`type-spec`指定`var`的类型; 提供了一个非数值类型的后果是未定义的. 如果没有`into`变量, 可选的传递参数`type-spec`应用在内部记录和的变量上. 默认类型是依赖于实现的, 但必须是类型`number`的超类型.

如果使用了`into`, 构造不会提供一个默认返回值; 然而这个变量在任何`finally`从句中可用.

如果特定种类的累积从句的目标(循环的结果或者`into var`)是一致的, 这些从句可以在循环中组合, 引入它们可视为累积概念上兼容的量. 通常下面的组中元素可以在循环形式的同样的目标中与同组中其它元素混用:

* `collect`、`append`、`nconc`
* `sum`、`count`
* `maximize`、`minimize`

``` lisp
;; Collect every name and the kids in one list by using COLLECT and APPEND.
 (loop for name in '(fred sue alice joe june)
       for kids in '((bob ken) () () (kris sunshine) ())
       collect name
       append kids)
=>  (FRED BOB KEN SUE ALICE JOE KRIS SUNSHINE JUNE)
```

任何两个累积结果对象类型不同的从句可以在一个循环中同时出现, 仅当每个从句累积到不同的变量.

#### 6.1.3.1 示例: COLLECT从句

``` lisp
;; 收集列表中所有符号
 (loop for i in '(bird 3 4 turtle (1 . 4) horse cat)
       when (symbolp i) collect i)
=>  (BIRD TURTLE HORSE CAT)

;; 收集所有奇数
 (loop for i from 1 to 10
       if (oddp i) collect i)
=>  (1 3 5 7 9)

;; 收集项到本地变量, 但不返回
 (loop for i in '(a b c d) by #'cddr
       collect i into my-list
       finally (print my-list))
>>  (A C)
=>  NIL
```

#### 6.1.3.2 示例: APPEND和NCONC从句

``` lisp
;; APPEND串接子列表
  (loop for x in '((a) (b) ((c)))
        append x)
=>  (A B (C))

;; NCONC字列表. 只有用LIST构造的列表被修改.
  (loop for i upfrom 0
        as x in '(a b (c))
        nconc (if (evenp i) (list x) nil))
=>  (A (C))
```

#### 6.1.3.3 示例: COUNT从句

``` lisp
(loop for i in '(a b nil c nil d e)
      count i)
=>  5
```

#### 6.1.3.4 示例: MAXIMIZE和MINIMIZE从句

``` lisp
(loop for i in '(2 1 5 3 4)
      maximize i)
=>  5
(loop for i in '(2 1 5 3 4)
      minimize i)
=>  1

;; FIXNUM应用在持有最大值的内部变量上
(setq series '(1.2 4.3 5.7))
=>  (1.2 4.3 5.7)
(loop for v in series
      maximize (round v) of-type fixnum)
=>  6

;; FIXNUM应用在变量RESULT上.
(loop for v of-type float in series
      minimize (round v) into result of-type fixnum
      finally (return result))
=>  1
```

#### 6.1.3.5 示例: SUM从句

``` lisp
(loop for i of-type fixnum in '(1 2 3 4 5)
      sum i)
=>  15

(setq series '(1.2 4.3 5.7))
=>  (1.2 4.3 5.7)
(loop for v in series
      sum (* 2.0 v))
=>  22.4
```

### 6.1.4 终止测试从句

构造`repeat`导致迭代在指定次数后终止. 循环体执行n次, n是表达式`form`的值. 传递参数`form`在循环序言中求值一次. 如果这个逼到大师求值结果为0或一个负数, 循环体不被求值.

构造`always`、`never`、`thereis`、`while`、`until`和宏`loop-finish`允许在循环中迭代的条件终止.

构造`always`、`never`和`thereis`提供了循环终止时返回的特定值. 在循环中与不是`into`从句的值累积从句一起使用`always`、`never`或`thereis`, 发出类型为`program-error`的错误信号(在宏展开时). 因为`always`、`never`和`thereis`使用特殊操作符`return-from`终止迭代, 当这些迭代导致退出时, 提供的`finally`从句不被求值. 在其它方面, 这些构造与构造`while`和`until`相同.

构造`always`接受一个形式, 在形式一旦求值为`nil`是终止循环; 在这种情况下, 循环形式返回`nil`. 否则返回`t`. 如果提供的形式的值从不是`nil`, 其它构造可以终止迭代.

构造`never`在提供的形式首次求值为非`nil`时终止迭代; 循环形式返回`nil`. 如果提供的形式的值总是`nil`, 其它构造可以终止迭代. 除非其它从句贡献了返回值, 默认返回`t`.

构造`thereis`在提供的形式首次求值为非`nil`时终止迭代; 循环形式返回提供的形式的值. 如果提供的形式的值总是`nil`, 其它构造可以终止迭代. 除非其它从句贡献了返回值, 默认返回`nil`.

构造`thereis`与`until`之间有两个差异:

* 构造`unti`不会基于提供的形式的值返回一个值或`nil`.
* 构造`until`执行任意`finally`从句. 因为`thereis`使用特殊操作符`return-from`终止节点, 由于`thereis`导致退出时, 提供的任意`finally`从句不被求值.

构造`while`允许迭代一直执行直到提供的`form`求值为false. 提供的形式在`while`从句的位置被重复求值.

构造`until`等价于`while (not form)...`. 如果提供的形式的值是非`nil`, 迭代终止.

终止测试控制解构可以在循环体中任何位置出现. 按它们出现的顺序依次使用. 如果`until`或`while`导致终止, 源码中在它之前出现的任意从句仍被求值. 如果`until`和`while`导致终止, 控制被转移到循环后记, 任意`finally`从句被执行.

构造`never`与`until`质检有两个差异:

* 构造`unti`不会基于提供的形式的值返回`t`或`nil`.
* 构造`until`不会跳过任何`finally`从句. 因为`never`使用特殊操作符`return-from`终止迭代, 由于`never`导致退出时, 提供的任意`finally`从句不被求值.

因为其它循环控制从句可以结束循环, 大多数情况下不需要使用`loop-finish`. 宏`loop-finish`用于从循环中的内嵌条件中常规退出. 因为`loop-finish`将控制转移到循环后记, 在`finally`表达式中使用`loop-finish`会导致无限循环.

#### 6.1.4.1 示例: REPEAT从句

``` lisp
(loop repeat 3
      do (format t "~&What I say three times is true.~%"))
>>  What I say three times is true.
>>  What I say three times is true.
>>  What I say three times is true.
=>  NIL

(loop repeat -15
  do (format t "What you see is what you expect~%"))
=>  NIL
```

#### 6.1.4.2 示例: ALWAYS、NEVER和THEREIS从句

``` lisp
;; 确保I总是小于11(两种方式).
;; FOR构造终止循环.
 (loop for i from 0 to 10
       always (< i 11))
=>  T
 (loop for i from 0 to 10
       never (> i 11))
=>  T

;; If I exceeds 10 return I; otherwise, return NIL.
;; THEREIS构造终止循环
 (loop for i from 0
       thereis (when (> i 10) i) )
=>  11

;;; 在这些示例中, FINALLY从句不被求值.
 (loop for i from 0 to 10
       always (< i 9)
       finally (print "you won't see this"))
=>  NIL
 (loop never t
       finally (print "you won't see this"))
=>  NIL
 (loop thereis "Here is my value"
       finally (print "you won't see this"))
=>  "Here is my value"

;; FOR构造终止循环, 因此FINALLY从句被求值.
 (loop for i from 1 to 10
       thereis (> i 11)
       finally (prin1 'got-here))
>>  GOT-HERE
=>  NIL

;; If this code could be used to find a counterexample to Fermat's
;; last theorem, it would still not return the value of the
;; counterexample because all of the THEREIS clauses in this example
;; only return T.  But if Fermat is right, that won't matter
;; because this won't terminate.

 (loop for z upfrom 2
       thereis
         (loop for n upfrom 3 below (log z 2)
               thereis
                 (loop for x below z
                       thereis
                         (loop for y below z
                               thereis (= (+ (expt x n) (expt y n))
                                          (expt z n)))))) (loop while (hungry-p) do (eat))

;; UNTIL NOT is equivalent to WHILE.
 (loop until (not (hungry-p)) do (eat))

;; Collect the length and the items of STACK.
 (let ((stack '(a b c d e f)))
   (loop for item = (length stack) then (pop stack)
         collect item
         while stack))
=>  (6 A B C D E F)

;; Use WHILE to terminate a loop that otherwise wouldn't terminate.
;; Note that WHILE occurs after the WHEN.
 (loop for i fixnum from 3
       when (oddp i) collect i
       while (< i 5))
=>  (3 5)
```

#### 6.1.4.3 示例: WHILE和UNTIL从句

``` lisp
(loop while (hungry-p) do (eat))

;; UNTIL NOT is equivalent to WHILE.
(loop until (not (hungry-p)) do (eat))

;; Collect the length and the items of STACK.
(let ((stack '(a b c d e f)))
  (loop for item = (length stack) then (pop stack)
        collect item
        while stack))
=>  (6 A B C D E F)

;; Use WHILE to terminate a loop that otherwise wouldn't terminate.
;; Note that WHILE occurs after the WHEN.
(loop for i fixnum from 3
      when (oddp i) collect i
      while (< i 5))
=>  (3 5)
```

### 6.1.5 无条件执行从句

构造`do`和`doing`对循环的展开形式中提供的形式执行求值. 传递参数`form`可以是任何复合形式. 在每个迭代中求值每个形式. 因为每个循环从句必须以循环关键字开始, 当不需要除了执行之外的控制动作时, 使用关键字`do`.

构造`return`接受一个形式. 该形式返回的值立即被循环形式返回. 它等价于从句`do (return-from block-name value)`, 这里的`block-name`是在`named`从句中指定的名称或者`nil`(没有指定`named`从句时).

#### 6.1.5.1 示例: 无条件执行

``` lisp
;; 打印数和数平方
;; DO构造用于多个形式
 (loop for i from 1 to 3
       do (print i)
          (print (* i i)))
>>  1
>>  1
>>  2
>>  4
>>  3
>>  9
=>  NIL
```

### 6.1.6 条件执行从句

构造`if`、`when`和`unless`建立循环中的条件控制. 如果测试通过, 执行后续的循环从句. 如果测试不通过, 跳过后续的循环从句, 程序控制转移到循环关键字`else`之后的从句.
如果测试不通过且没有提供`else`从句, 控制转移到整个条件从句之后的从句或构造.

如果条件从句是嵌套的, 每个`else`与其前面最近的没有关联`else`或`end`的条件从句适配.

在`if`和`when`从句中, 它们是等价的, 如果提供的形式的值为true, 测试通过.

在`unless`从句中, 如果提供的形式的值为false, 测试通过.

可以使用循环关键字, 将在测试表达式之后的从句组在一起, 生成由复合从句构成的条件块.

使用循环关键字`it`引用从句中测试表达式的结果. 在条件执行从句中的`return`从句或累积从句的形式的位置处使用`it`. 如果用`and`连接了多个从句, 构造`it`必须在块中第一个从句中.

可选的循环关键字`end`标记从句的结束. 如果没有提供这个关键字, 下一个循环关键字标记结束. 构造`end`可用于区分复合从句的作用域.

#### 6.1.6.1 示例: WHEN从句

``` lisp
;; Signal an exceptional condition.
 (loop for item in '(1 2 3 a 4 5)
       when (not (numberp item))
        return (cerror "enter new value" "non-numeric value: ~s" item))
Error: non-numeric value: A

;; The previous example is equivalent to the following one.
 (loop for item in '(1 2 3 a 4 5)
       when (not (numberp item))
        do (return
            (cerror "Enter new value" "non-numeric value: ~s" item)))
Error: non-numeric value: A
;; This example parses a simple printed string representation from
;; BUFFER (which is itself a string) and returns the index of the
;; closing double-quote character.
 (let ((buffer "\"a\" \"b\""))
   (loop initially (unless (char= (char buffer 0) #\")
                     (loop-finish))
         for i of-type fixnum from 1 below (length (the string buffer))
         when (char= (char buffer i) #\")
          return i))
=>  2

;; The collected value is returned.
 (loop for i from 1 to 10
       when (> i 5)
         collect i
       finally (prin1 'got-here))
>>  GOT-HERE
=>  (6 7 8 9 10)

;; Return both the count of collected numbers and the numbers.
 (loop for i from 1 to 10
       when (> i 5)
         collect i into number-list
         and count i into number-count
       finally (return (values number-count number-list)))
=>  5, (6 7 8 9 10)
```

### 6.1.7 杂项从句

#### 6.1.7.1 控制转移从句

构造`named`建立包含整个循环的一个隐式块的名称, 从而可以使用特殊操作符`return-from`从循环返回值或退出. 每个循环形式只可以有一个名称.
如果被使用, 构造`named`必须是循环表达式的第一个从句.

构造`return`接受一个形式. 这个形式返回的值立即被循环形式返回. 这个构造类似于特殊操作符`return-from`和宏`return`.
构造`return`不执行循环形式中给定的`finally`从句.

##### 6.1.7.1.1 示例: NAMED从句

``` lisp
;; 只是命名并返回.
 (loop named max
       for i from 1 to 10
       do (print i)
       do (return-from max 'done))
>>  1
=>  DONE
```

#### 6.1.7.2 初值和最后的执行

构造`initially`和`finally`求值出现在循环体之前和之后的形式.

构造`initially`导致提供的`compound-forms`在循环序言中求值, 它在除了构造`with`、`for`或`as`提供的初始化设置外的循环代码之前出现. 任何`initially`从句的代码按它们在循环中出现的顺序执行.

构造`finally`导致提供的`compound-forms`在常规迭代终止之后在循环后记中求值. 任何`finally`从句的代码按它们在循环中出现的顺序执行. 收集的代码在循环后记中被执行一次, 在任何隐式值从累积从句返回之前执行. 然而, 循环体中显式的控制转移(例如`return`、`go`或`throw`), 会不执行循环后记代码而退出循环.

诸如`return`、`always`、`never`和`thereis`从句可以跳过`finally`从句. 在`finally`从句之后可以使用`return`(或提供`named`时使用`return-from`)从循环中返回值. 这种在`finally`从句中的显式返回比从诸如`collect`、`nconc`、`append`、`sum`、`count`、`maximize`和`minimize`累积从句返回, 有更高的优先级; 如果使用了`return`或`return-from`, 前述累积从句的累积值不会从循环中返回.

### 6.1.8 示例: 杂项循环特性

``` lisp
(let ((i 0))                     ; 没有使用循环关键字
   (loop (incf i) (if (= i 3) (return i)))) =>  3

(let ((i 0)(j 0))
   (tagbody
     (loop (incf j 3) (incf i) (if (= i 3) (go exit)))
     exit)
   j) =>  9
```

在下面的示例中, 变量`x`在`y`之前步进, 因此`y`的值反映了`x`更新后的的值.

``` lisp
(loop for x from 1 to 10
      for y = nil then x
      collect (list x y))
=>  ((1 NIL) (2 2) (3 3) (4 4) (5 5) (6 6) (7 7) (8 8) (9 9) (10 10))
```

在这个示例中, `x`与`y`并行步进:

``` lisp
(loop for x from 1 to 10
      and y = nil then x
      collect (list x y))
=>  ((1 NIL) (2 1) (3 2) (4 3) (5 4) (6 5) (7 6) (8 7) (9 8) (10 9))
```


#### 6.1.8.1 示例: 从句分组

``` lisp
;; 分组条件从句
 (loop for i in '(1 324 2345 323 2 4 235 252)
       when (oddp i)
         do (print i)
         and collect i into odd-numbers
         and do (terpri)
       else                              ; I is even.
         collect i into even-numbers
       finally
         (return (values odd-numbers even-numbers)))
>>  1
>>
>>  2345
>>
>>  323
>>
>>  235
=>  (1 2345 323 235), (324 2 4 252)
```

``` lisp
;; 收集大于3的数
 (loop for i in '(1 2 3 4 5 6)
       when (and (> i 3) i)
       collect it)                      ; IT引用(and (> i 3) i).
=>  (4 5 6)
```

``` lisp
;; 在列表中查找数
 (loop for i in '(1 2 3 4 5 6)
       when (and (> i 3) i)
       return it)
=>  4

;; 上面的示例与下面的类似.
 (loop for i in '(1 2 3 4 5 6)
       thereis (and (> i 3) i))
=>  4

```

``` lisp
;; 嵌套的条件从句.
 (let ((list '(0 3.0 apple 4 5 9.8 orange banana)))
   (loop for i in list
         when (numberp i)
           when (floatp i)
             collect i into float-numbers
           else                                  ; Not (floatp i)
             collect i into other-numbers
         else                                    ; Not (numberp i)
           when (symbolp i)
             collect i into symbol-list
           else                                  ; Not (symbolp i)
             do (error "found a funny value in list ~S, value ~S~%" list i)
         finally (return (values float-numbers other-numbers symbol-list))))
=>  (3.0 9.8), (0 4 5), (APPLE ORANGE BANANA)
```

``` lisp
;; 如果没有end介词, 最后一个and将用在内层的if中而不是外层的if.
;; Without the END preposition, the last AND would apply to the
;; inner IF rather than the outer one.
 (loop for x from 0 to 3
       do (print x)
       if (zerop (mod x 2))
         do (princ " a")
          and if (zerop (floor x 2))
                do (princ " b")
                end
          and do (princ " c"))
>>  0  a b c
>>  1
>>  2  a c
>>  3
=>  NIL
```

### 6.1.9 备注: 循环

可以指定循环变量的类型. 不需要给每个变量提供类型, 但提供了类型可以保证变量有正确类型的初始值, 同时有助于编译器优化(依赖于实现).

在一些实现中, 从句`repeat n ...`基本上等价于下面的从句:

``` lisp
(loop for internal-variable downfrom (- n 1) to 0 ...)
```

但构造`repeat`可能更高效.

在循环从句内部的可执行部分和整个循环形式周围, 可以使用`let`绑定变量.

因为`it`是一个循环关键字, 可以在特定上下文中的形式中出现, 在循环中使用名称为`IT`的变量时需要特别注意.

没有给用户添加循环扩展的标准机制.

## 6.2 迭代的字典

见[迭代的字典](../Dictionary#6.2).
