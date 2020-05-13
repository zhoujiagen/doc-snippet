# 22. 打印器

[TOC]

## <span id="22.1">22.1</span> Lisp打印器

### <span id="22.1.1">22.1.1</span> Lisp打印器概述

Common Lisp提供了大多数对象的打印文本的表示, 称为打印的表示. 诸如`print`函数接受一个对象, 将它的打印的表示发送给流. 执行这个操作的一组例程称为(Common Lisp)打印器.

读取打印的形式通常生成与原始打印的对象`equal`语义下相同的对象.

#### <span id="22.1.1.1">22.1.1.1</span> 多种可能的文本表示

大多数对象有多个可能的文本表示. 例如, 正数27有下述方式的文本表示:

``` lisp
27    27.    #o33    #x1B    #b11011    #.(* 3 3 3)    81/3
```

包含两个符号A和B的列表可以有多种文本表示:

``` lisp
(A B)    (a b)    (  a  b )    (\A |B|)
(|\A|
 B
)
```

通常, 从Lisp读取器的视角看, 在文本表示中允许出现空格的位置, 标准语法中允许出现任意数量的空白和换行.

诸如`print`函数产生打印的表示时, 必须从多种可能的文本表示中做出选择. 在大多数情况下, 它会选择一个程序可读的表示, 但在特定情况下它会选择更紧凑的但不是程序可读的表示.

一些称为打印器控制变量的可选变量, 用于控制对象打印的表示的各个方面. 下图展示了标准的打印器控制变量; 可能也有一些实现定义的打印器控制变量.

<span id="Figure22-1">图 22-1. 标准的打印器控制变量.</span>

``` lisp
*print-array*
*print-base*
*print-case*
*print-circle*
*print-escape*
*print-gensym*
*print-length*
*print-level*
*print-lines*
*print-miser-width*
*print-pprint-dispatch*
*print-pretty*
*print-radix*
*print-readably*
*print-right-margin*
```

除了打印器控制变量外, 下面的已定义名称与Lisp打印器的行为相关或影响该行为:

<span id="Figure22-2">图 22-2. Lisp打印器的额外影响因素.</span>

``` lisp
*package*
*read-default-float-format*
*read-eval*
*readtable*
readtable-case
```

#### <span id="22.1.1.1.1">22.1.1.1.1</span> 打印器的转义

变量`*print-escape*`控制Lisp打印器是否尝试产生诸如转义字符和包前缀的标记.

变量`*print-readably*`用于在程序可读的输出特别重要时, 覆盖其它打印器控制变量控制的多个独立方面.

将`*print-readably*`设置为true的多个作用中的其中一个是Lisp打印器的行为就像`*print-escape*`同时也设置为true一样. 为便于描述, 我们说如果`*print-readably*`或`*print-escape*`为true, 则打印器的转义是开启的, 如果`*print-readably*`和`*print-escape*`都是false, 则打印器的转义是关闭的.

### <span id="22.1.2">22.1.2</span> 打印器的分发

Lisp打印器就如何打印对象的决策如下:

如果`*print-pretty*`的值为true, 打印由当前美观打印分发表控制; 见[22.2.1.4 美观打印分发表](#22.2.1.4).

否则(如果`*print-pretty*`的值为false), 使用对象的`print-object`方法; 见[22.1.3 默认的print-object方法](#22.1.3).

### <span id="22.1.3">22.1.3</span> 默认的print-object方法

这一节描述标准类型的`print-object`方法的默认行为.

#### <span id="22.1.3.1">22.1.3.1</span> 打印数值

##### <span id="22.1.3.1.1">22.1.3.1.1</span> 打印整数

整数按当前输出基指定的基数打印, 最重要的数字优先. 合适的情况下, 基数描述符也被打印出; 见`*print-radix*`. 如果是负整数, 先打印一个负号, 接着打印出该整数的绝对值. 整数零由单个数字0表示, 不带符号. 依赖于`*print-radix*`的值, 可以打印出小数点.

关于整数的信息见[2.3.2.1.1 整数的语法](../02-Syntax#2.3.2.1.1).

##### <span id="22.1.3.1.2">22.1.3.1.2</span> 打印比值

比值按如下方式打印: 作为整数打印分子的绝对值, 一个`/`, 然后是分母. 分子和分母都按当前输出基指定的基数被打印; 它们是用`numerator`和`denominator`获取的, 所以比值是按化简后的形式打印的. 合适的情况下, 可以打印出基数描述符. 如果比值是负的, 在分子之前打印一个负号.

关于比值的语法信息见[2.3.2.1.2 比值的语法](../02-Syntax#2.3.2.1.2).

##### <span id="22.1.3.1.3">22.1.3.1.3</span> 打印浮点数

如果浮点数的数量级是0或者在10^-3(包含)与10^7(不包含)之间, 先打印数值的整数部分, 然后是一个小数点, 然后是数值的小数部分; 小数点的两边总是至少有一个数字. 如果数值的符号(由`float-sign`确定)是负号, 则在数值之前打印一个负号. 如果数值的格式不与`*read-default-float-format*`指定的格式匹配, 则也打印出该格式的指数标记和数字0. 例如, 作为短浮点数的自然对数的基被打印为2.71828S0.

对于范围10^-3到10^7之外的非零数量级, 浮点数按计算机科学记数法打印. 数值的表示被缩放到1(包括)与10(不包含)之间后再打印, 在小数点之前有一个数字, 之后至少有一个数字. 接着打印出格式的指数标记, 除非数值的格式匹配`*read-default-float-format*`指定的格式, 接着使用指数标记`E`. 最后, 将与小数部分乘后与原始数值相等的10的幂作为十进制整数打印. 例如, 作为短浮点数的Avogadro数被打印为6.02S23.

关于浮点数的语法信息见[2.3.2.2 浮点数的语法](../02-Syntax#2.3.2.2).

##### <span id="22.1.3.1.4">22.1.3.1.4</span> 打印复数

复数打印为`#C`、一个`(`、实数部分的打印表示、一个空格、虚数部分的打印表示和一个`)`.

关于复数的语法信息见[2.3.2.3 复数的语法](../02-Syntax#2.3.2.3)和[2.4.8.11 `#C`](../02-Syntax#2.4.8.11).

##### <span id="22.1.3.1.5">22.1.3.1.5</span> 备注: 打印数值

数值的打印表示必须不能包含转义字符; 见[2.3.1.1.1 转义字符与可能的数值](../02-Syntax#2.3.1.1.1).

#### <span id="22.1.3.2">22.1.3.2</span> 打印字符

当打印器的转义开启时, 字符打印为其自身; 直接被发送给输出流. 当打印器的转义开启时, 使用`#\`语法.

当打印器打出字符的名称时, 使用与读取器宏`#\`相同的表示; 因此任何打出的字符名称可被输入接受(在那个实现中). 如果一个非图形字符有标准的名称, 则在打印`#\`标记时这个名称较非标准名称优先. 对图形标准字符, 这个字符总是在打印`#\`标记中使用, 甚至在这个字符有一个名称时.

关于读取器宏`#\`的详情见[2.4.8.1 `#C`](../02-Syntax#2.4.8.1).

#### <span id="22.1.3.3">22.1.3.3</span> 打印符号

当打印器的转义开启时, 只输出符号名称的字符(但名称中打印字符的大小写由`*print-case*`控制; 见[22.1.3.3.2 readtable的大小写对Lisp打印器的影响](#22.1.3.3.2)).

这一节的剩余部分只应用在打印器的转义开启时.

当打印符号时, 打印器插入足够的单转义和/或多转义字符(`\`和或`|`), 保证如果使用相同的`*readtable*`和绑定到当前输出基的`*read-base*`调用`read`, 会返回相同的符号(如果它不是明显非内部化)或者有相同打印名称的非内部化符号(其它情况下).

例如, 如果打印符号`face`时`*print-base*`的值是16, 因为记号`face`将被读取为十六进制数(十进制数值为64206), 它将被打印为`\FACE`或`\Face`或`|FACE|`.

关于在当前readtable中有非标准语法类型的字符上的约束信息见变量`*print-readably*`.

关于Lisp读取器如何解析符号的信息, 见[2.3.4 符号记号](../02-Syntax#2.3.4)和[2.4.8.5 `#:`(sharpsign colon)](../02-Syntax#2.4.8.5).

当`*print-pretty`为true且打印器的转义开启时, `nil`被打印为`()`.

##### <span id="22.1.3.3.1">22.1.3.3.1</span> 符号的包前缀

必要时要打印出包前缀. 包前缀的打印规则如下. 打印符号时, 如果它在`KEYWORD`包中, 则它使用前缀`:`打印; 否则, 如果在当前包中它是可访问的, 则它不使用任何包前缀打印; 否则使用包前缀打印.

如果`*print-gensym*`为true且打印器的转义开启, 明显非内部化的符号使用前缀`#:`打印; 如果`*print-gensym*`为false或打印器的转义关闭, 则不使用前缀打印, 就像在当前包中一样.

因为`#:`语法不会内部化后续的符号, 如果`*print-circle*`为true且相同的非内部化符号在需要打印出的表达式中出现多次, 需要使用循环列表语法. 例如, `(let ((x (make-symbol "FOO"))) (list x x))`的结果在`*print-circle*`为false时打印为`(#:foo #:foo)`, 在`*print-circle*`为true时打印为`(#1=#:foo #1#)`.

前面描述的包前缀规则总结如下:

* `foo:bar`<br>
当符号`bar`在其主包`foo`中是外部化的, 在当前包中不可访问时打印出`foo:bar`.
* `foo::bar`<br>
当符号`bar`在其主包`foo`中是内部化的, 在当前包中不可访问时打印出`foo::bar`.
* `:bar`<br>
当符号`bar`的主包是`KEYWORD`包时打印出`:bar`.
* `#:bar`<br>
当符号`bar`是明显未内部化的, 甚至在`bar`没有主包但在当前包中可访问时, 初始`#:bar`.

##### <span id="22.1.3.3.2">22.1.3.3.2</span> readtable的大小写对Lisp打印器的影响

当打印器的转义关闭, 或者字符没有使用单转义或多转义语法, 当前readtable的大小写影响Lisp打印器打印符号的方式:

* `:upcase`<br>
当readtable的大小写是`:upcase`, 大写字符按`*print-case*`指定的大小写打印, 小写字符打印为自身.
* `:downcase`<br>
当readtable的大小写是`:downcase`, 大写字符打印为自身, 小写字符按`*print-case*`指定的大小写打印.
* `:preserve`<br>
当readtable的大小写是`:preserve`, 所有字母字符打印为自身.
* `:invert`<br>
当readtable的大小写是`:invert`, 只有一个大小写情况的符号名称中所有字母字符的大小写被翻转. 混合大小写的字符名称打印为自身.

如果打印器的转义开始, 转义符号名称中字母字符的规则受`readtable-case`影响. 字母字符按如下方式转义:

* `:upcase`<br>
当readtable的大小写是`:upcase`, 所有小写字符必须被转义.
* `:downcase`<br>
当readtable的大小写是`:downcase`, 所有大写字符必须被转义.
* `:preserve`<br>
当readtable的大小写是`:preserve`, 不需要转义字母字符.
* `:invert`<br>
当readtable的大小写是`:invert`, 不需要转义字母字符.

###### <span id="22.1.3.3.2.1">22.1.3.3.2.1</span> 示例: readtable的大小写对Lisp 打印器的影响

``` lisp
(defun test-readtable-case-printing ()
  (let ((*readtable* (copy-readtable nil))
        (*print-case* *print-case*))
    (format t "READTABLE-CASE *PRINT-CASE*  Symbol-name  Output~
             ~%--------------------------------------------------~
             ~%")
    (dolist (readtable-case '(:upcase :downcase :preserve :invert))
      (setf (readtable-case *readtable*) readtable-case)
      (dolist (print-case '(:upcase :downcase :capitalize))
        (dolist (symbol '(|ZEBRA| |Zebra| |zebra|))
          (setq *print-case* print-case)
          (format t "~&:~A~15T:~A~29T~A~42T~A"
                  (string-upcase readtable-case)
                  (string-upcase print-case)
                  (symbol-name symbol)
                  (prin1-to-string symbol)))))))
```

`(test-readtable-case-printing)`的输出如下:

``` lisp
READTABLE-CASE *PRINT-CASE*  Symbol-name  Output
--------------------------------------------------
:UPCASE        :UPCASE       ZEBRA        ZEBRA
:UPCASE        :UPCASE       Zebra        |Zebra|
:UPCASE        :UPCASE       zebra        |zebra|
:UPCASE        :DOWNCASE     ZEBRA        zebra
:UPCASE        :DOWNCASE     Zebra        |Zebra|
:UPCASE        :DOWNCASE     zebra        |zebra|
:UPCASE        :CAPITALIZE   ZEBRA        Zebra
:UPCASE        :CAPITALIZE   Zebra        |Zebra|
:UPCASE        :CAPITALIZE   zebra        |zebra|
:DOWNCASE      :UPCASE       ZEBRA        |ZEBRA|
:DOWNCASE      :UPCASE       Zebra        |Zebra|
:DOWNCASE      :UPCASE       zebra        ZEBRA
:DOWNCASE      :DOWNCASE     ZEBRA        |ZEBRA|
:DOWNCASE      :DOWNCASE     Zebra        |Zebra|
:DOWNCASE      :DOWNCASE     zebra        zebra
:DOWNCASE      :CAPITALIZE   ZEBRA        |ZEBRA|
:DOWNCASE      :CAPITALIZE   Zebra        |Zebra|
:DOWNCASE      :CAPITALIZE   zebra        Zebra
:PRESERVE      :UPCASE       ZEBRA        ZEBRA
:PRESERVE      :UPCASE       Zebra        Zebra
:PRESERVE      :UPCASE       zebra        zebra
:PRESERVE      :DOWNCASE     ZEBRA        ZEBRA
:PRESERVE      :DOWNCASE     Zebra        Zebra
:PRESERVE      :DOWNCASE     zebra        zebra
:PRESERVE      :CAPITALIZE   ZEBRA        ZEBRA
:PRESERVE      :CAPITALIZE   Zebra        Zebra
:PRESERVE      :CAPITALIZE   zebra        zebra
:INVERT        :UPCASE       ZEBRA        zebra
:INVERT        :UPCASE       Zebra        Zebra
:INVERT        :UPCASE       zebra        ZEBRA
:INVERT        :DOWNCASE     ZEBRA        zebra
:INVERT        :DOWNCASE     Zebra        Zebra
:INVERT        :DOWNCASE     zebra        ZEBRA
:INVERT        :CAPITALIZE   ZEBRA        zebra
:INVERT        :CAPITALIZE   Zebra        Zebra
:INVERT        :CAPITALIZE   zebra        ZEBRA
```

#### <span id="22.1.3.4">22.1.3.4</span> 打印字符串

字符串中字符按顺序打印. 如果打印器的转义开始, 字符串之前和之后输出`"`, 字符串中所有`"`和单转义需要前继有`\`. 打印字符串不受`*print-array*`影响. 只打印字符串的活跃元素.

关于Lisp读取洗如何解析字符串的信息, 见[2.4.5 `"`(double-quote)](../02-Syntax#2.4.5).

#### <span id="22.1.3.5">22.1.3.5</span> 打印列表和Cons

在可能的情况下, 列表记法优先于点记法. 因此, 使用下述算法打印Cons x:

1. 打印`(`.
2. 打印x的car.
3. 如果x的cdr是一个Cons, 将其设置为当前Cons, 打印器一个空格, 进入步骤2.
4. 如果x的cdr不是空、空格、点, 打印x的cdr.
5. 打印`)`.

实际上上述算法只在`*print-pretty*`为false是使用. 当`*print-pretty*`为true(或者使用`pprint`)时, 额外空格可以替代单个空格, 使用有相似目标但更有表示灵活性的更高效算法; 见[22.1.2 打印器的分发](#22.1.2).

尽管下面的两个表达式是等价的, 读取器均会产生相同的Cons, 打印器总是使用第二种形式:

``` lisp
(a . (b . ((c . (d . nil)) . (e . nil))))
(a b (c d) e)
```

Cons的引起受`*print-level`、`*print-length*`和`*print-circle*`的影响.

下面是列表的打印表示的示例:

``` lisp
(a . b)     ;A dotted pair of a and b
(a.b)       ;A list of one element, the symbol named a.b
(a. b)      ;A list of two elements a. and b
(a .b)      ;A list of two elements a and .b
(a b . c)   ;A dotted list of a and b with c at the end; two conses
.iot        ;The symbol whose name is .iot
(. b)       ;Invalid -- an error is signaled if an attempt is made to read this syntax.
(a .)       ;Invalid -- an error is signaled.
(a .. b)    ;Invalid -- an error is signaled.
(a . . b)   ;Invalid -- an error is signaled.
(a b c ...) ;Invalid -- an error is signaled.
(a \. b)    ;A list of three elements a, ., and b
(a |.| b)   ;A list of three elements a, ., and b
(a \... b)  ;A list of three elements a, ..., and b
(a |...| b) ;A list of three elements a, ..., and b
```

关于Lisp读取器如何解析列表和Cons的信息, 见[2.4.1 `(`(left-parenthesis)](../02-Syntax#2.4.1).

#### <span id="22.1.3.6">22.1.3.6</span> 打印位向量

位向量打印为`#*`后接按序排列的位向量中的位. 如果`*print-array*`为false, 则位向量按简洁但不可读的格式(使用`#<`)打印. 只打印位向量中的活跃元素.

关于Lisp读取器如何解析位向量的信息, 见[2.4.8.4 `#*`(sharpsign asterisk)](../02-Syntax#2.4.8.4).

#### <span id="22.1.3.7">22.1.3.7</span> 打印其它向量

如果`*print-array*`为true, `*print-readably*`为false, 不是字符串或位向量的向量使用通用向量语法打印; 这意味着特化的向量表示的信息不回出现. 长度为零的向量的打印表示是`#()`. 长度非零的向量的打印表示以`#(`开始. 然后打印向量的第一个元素. 如果有其它元素, 它们按需打印, 如果`*print-pretty*`为false时每个元素前继有空格, 为true是有空白. 在最后一个元素之后的`)`结束向量的打印表示. 打印向量受`*print-level*`和`**print-length`影响. 如果向量有填充指针, 只打印在填充指针之前的元素.

如果`*print-array*`和`*print-readably*`都为false, 向量不按上面描述的方式打印, 按使用`#<`的间接但不可读的格式打印.

如果`*print-readably*`为true, 按实现定义的方式打印向量, 见变量`*print-readably*`.

关于Lisp读取器如何解析这些其它向量的信息, 见[2.4.8.3 `#(`(sharpsign left-parenthesis)](../02-Syntax#2.4.8.3).

#### <span id="22.1.3.8">22.1.3.8</span> 打印其它数组

如果`*print-array*`为true, `*print-readably*`为false, 不是向量的数组使用`#nA`格式打印. `n`是数组的维秩. 先打印`#`, 将`n`作为十进制整数打印, 然后打印`A`和`n`个`(`. 接着, 按行主序顺序扫描元素, 在每个元素上使用`write`, 使用空白分隔元素. 数组的维按从左至右的顺序编号为0到`n-1`, 按最右的索引变化最快的方式枚举. 每当维j的索引递增时, 采取下述动作

* 如果 j < n-1, 打印`)`.
* 如果维j的索引递增导致它等于维j, 这个索引被重置为0, 递增维j-1的索引(从而递归的执行这三个步骤), 除非j=0, 这时整个算法结束. 如果维j的索引递增未导致它等于维j, 打印一个空格.
* 如果 j < n-1, 打印`(`.

这导致打印的内容的格式适用于`make-array`的`:initial-contents`. 这个过程打印的列表会被`*print-level`和`*print-length*`截断.

如果列表是特化的类型, 包含位或字符, 则按上述算法生成的最内层列表可以使用位向量或字符串语法打印, 这些内层列表不会被`*print-length*`截断.

如果`*print-array*`和`*print-readably*`都为false, 则数组按简洁但不可读的格式(使用`#<`)打印.

如果`*print-readably*`为true, 数组按实现定义的方式打印; 见变量`*print-readably*`. 通常这对有维0的数组是中药的.

关于Lisp读取器如何解析这些其它数组的信息, 见[2.4.8.12 `#A`(sharpsign A)](../02-Syntax#2.4.8.12).

#### <span id="22.1.3.9">22.1.3.9</span> 示例: 打印数组

``` lisp
(let ((a (make-array '(3 3)))
      (*print-pretty* t)
      (*print-array* t))
  (dotimes (i 3) (dotimes (j 3) (setf (aref a i j) (format nil "<~D,~D>" i j))))
  (print a)
  (print (make-array 9 :displaced-to a)))
>>  #2A(("<0,0>" "<0,1>" "<0,2>")
>>      ("<1,0>" "<1,1>" "<1,2>")
>>      ("<2,0>" "<2,1>" "<2,2>"))
>>  #("<0,0>" "<0,1>" "<0,2>" "<1,0>" "<1,1>" "<1,2>" "<2,0>" "<2,1>" "<2,2>")
=>  #<ARRAY 9 indirect 36363476>
```

#### <span id="22.1.3.10">22.1.3.10</span> 打印随机状态

没有指定打印类型为`random-state`的对象的特定语法. 摊儿, 每个实现必须以下述方式打印随机状态对象, 在相同的实现中, `read`可以从打印的表示中构造随机状态对象的一个副本, 就像使用`make-random-state`生成的副本一样.

如果是使用`defstruct`进制实现打印随机状态的, 可以使用常规的解构语法打印随机状态对象; 可能看到:

``` lisp
#S(RANDOM-STATE :DATA #(14 49 98436589 786345 8734658324 ... ))
```

这里的构成部分是依赖于实现的.

#### <span id="22.1.3.11">22.1.3.11</span> 打印路径名

当打印器的转义开启时, 语法`#P"..."`是使用`write`和这里描述的其它函数打印路径名的方式. `"..."`是路径名的名称字符串表示.

当打印器的转义关闭时, `write`用输出`(namestring P)`替代输出路径名P.

关于Lisp读取器如何解析路径名的信息, 见[2.4.8.14 `#P`(sharpsign P)](../02-Syntax#2.4.8.14).

#### <span id="22.1.3.12">22.1.3.12</span> 打印结构

默认情况下, 类型为S的解构使用`#S`语法发音. 这个行为可以用在定义S的`defstruct`形式中指定`:print-function`或`:print-object`选项, 或者通过编写特化到类型S的对象上的`print-object`方法来定制.

不同的结构可能按不同的方式打印; 结构的默认记法为:

``` lisp
#S(structure-name {slot-key slot-value}*)
```

这里`#S`标记结构语法, `structure-name`是结构名称, 每个`slot-key`是结构中槽的初始化传递参数名称, 每个相应的`slot-value`是槽中对象的表示.

关于Lisp读取器如何解析结构的信息, 见[2.4.8.13 `#S`(sharpsign S)](../02-Syntax#2.4.8.13).

#### <span id="22.1.3.13">22.1.3.13</span> 打印其它对象

其它对象按依赖于实现的方式打印. 不要求实现将这些对象打印为可读的.

例如, 哈希表、readtable、包、流和函数不要求打印为可读的.

用于这种情况的常见记法是`#<...>`. 因为Lisp读取器不可读`#<`, 其后续的文本格式是不重要的, 当宏`print-unreadable-object`提供了一个常见的格式.

关于Lisp读取器如何处理这种记法的信息, 见[2.4.8.20 `#<`(sharpsign less-than-sign)](../02-Syntax#2.4.8.20). 关于如何标记不能打印为可读的对象的信息见[2.4.8.6 `#.`(sharpsign dot)](../02-Syntax#2.4.8.6).

### <span id="22.1.4">22.1.4</span> 示例: 打印器行为

``` lisp
(let ((*print-escape* t)) (fresh-line) (write #\a))
>>  #\a
=>  #\a
```

``` lisp
(let ((*print-escape* nil) (*print-readably* nil))
  (fresh-line)
  (write #\a))
>>  a
=>  #\a
```

``` lisp
(progn (fresh-line) (prin1 #\a))
>>  #\a
=>  #\a
(progn (fresh-line) (print #\a))
>>
>>  #\a
=>  #\a
(progn (fresh-line) (princ #\a))
>>  a
=>  #\a
```

``` lisp
(dolist (val '(t nil))
  (let ((*print-escape* val) (*print-readably* val))
    (print '#\a)
    (prin1 #\a) (write-char #\Space)
    (princ #\a) (write-char #\Space)
    (write #\a)))
>>  #\a #\a a #\a
>>  #\a #\a a a
=>  NIL
```

``` lisp
(progn (fresh-line) (write '(let ((a 1) (b 2)) (+ a b))))
>>  (LET ((A 1) (B 2)) (+ A B))
=>  (LET ((A 1) (B 2)) (+ A B))

(progn (fresh-line) (pprint '(let ((a 1) (b 2)) (+ a b))))
>>  (LET ((A 1)
>>        (B 2))
>>    (+ A B))
=>  (LET ((A 1) (B 2)) (+ A B))

(progn (fresh-line)
       (write '(let ((a 1) (b 2)) (+ a b)) :pretty t))
>>  (LET ((A 1)
>>        (B 2))
>>    (+ A B))
=>  (LET ((A 1) (B 2)) (+ A B))
```

``` lisp
(with-output-to-string (s)
   (write 'write :stream s)
   (prin1 'prin1 s))
=>  "WRITEPRIN1"
```

## <span id="22.2">22.2</span> Lisp美观打印器

### <span id="22.2.1">22.2.1</span> 美观打印器的概念

美观打印器提供的功能允许程序重新定义代码展示的方式, 同时用于美观打印应用到数据结构的复杂组合上.

给定输出风格是否是美观本质上是一个有些主观的问题. 然而, 因为美观打印器的作用可被符合标准的程序定制, 需要给各独立程序提供实现不同程序美观控制的灵活性.

通过提供美观打印器中布局动态决策机制的直接访问, 宏和函数`pprint-logical-block`、`pprint-newline`和`pprint-indent`使得指定美观打印布局规则作为产生输出的函数的一部分成为可能. 同时使得基于这些函数支持的长度和内嵌深度确定循环、共享和缩写变得容易.

美观打印器完全是由基于`*print-pprint-dispatch*`的值分发驱动的. 函数`set-pprint-dispath`使得符合标准的程序将新的美观打印函数关联到类型成为可能.

#### <span id="22.2.1.1">22.2.1.1</span> 输出安排的动态控制

当美观打印器的一段输出过大不能被可用空间容纳时, 美观打印器的动作可被精确控制. 这些操作工作方式下的三个概念是: 逻辑块、条件换行和节. 在继续阐述之前, 需要定义这些术语.

下图中的第一行展示了输出的概要部分. 输出中的每个字符用`-`表示. 条件换行的位置用数字标示. 逻辑块的开始和结束分别用`<`和`>`标示.

输出的整体是一个逻辑块和最外层的节. 这个节用第二行中的0标示. 输出中内嵌的逻辑块用宏`pprint-logical-block`指定. 条件换行位置通过调用`pprint-newline`指定. 每个条件换行定义了两个节(一个在它前面, 一个在它后面)和关联的第三个节(立即包含它的节).

条件换行后面的节的成分有: 直到但不包含这些的所有输出:

(a) 立即包含在同一个逻辑块中的下一个条件换行; 或者如果(a)不适用,<br>
(b) 在内嵌在逻辑块的更小层次中的下一个条件换行; 或者如果(b)不适用,<br>
\(c) 输出的结尾.

条件换行前面的节的成分有: 回溯到但不包含这些的所有输出:

(a) 立即包含在同一个逻辑块中的前一个条件换行; 或者如果(a)不适用,<br>
(b) 立即包含这个节的逻辑块的开始. 图中的最后四行标示出在四个条件换行之前和之后的节.

立即包含条件换行的节是包含这个条件换行的最短的节. 在图中, 第一个条件换行被立即包含在用0标记的节中, 第二个和第三个条件换行立即包含在第四个条件换行前面的节中, 第四个条件换行激励包含在第一个条件换行后面的节中.

<span id="Figure22-3">图 22-3. 逻辑块、条件换行和节的示例.</span>

```
<-1---<--<--2---3->--4-->->
000000000000000000000000000
11 111111111111111111111111
          22 222
             333 3333
       44444444444444 44444
```

美观打印器尽可能的将节的全部内容展示在一行中. 然而, 如果节过长不能被可用空间容纳, 在节中条件换行位置上插入转行.

#### <span id="22.2.1.2">22.2.1.2</span> 格式化指令接口

动态确定输出安排的操作的主要接口是通过美观打印器的函数和宏提供的. 下图列出了与美观打印相关的已定义名称.

<span id="Figure22-4">图 22-4. 与美观打印相关的已定义名称.</span>

``` lisp
*print-lines*
*print-miser-width*
*print-pprint-dispatch*
*print-right-margin*
copy-pprint-dispatch
format
formatter
pprint-dispatch
pprint-exit-if-list-exhausted
pprint-fill
pprint-indent
pprint-linear
pprint-logical-block
pprint-newline
pprint-pop
pprint-tab
pprint-tabular
set-pprint-dispatch
write
```

下图列出了一组作为相同美观打印操作的文本更紧凑形式的接口的格式化指令:

<span id="Figure22-5">图 22-5. 与美观打印相关的格式化指令.</span>

``` lisp
~I
~:T
~W
~/.../
~<...~:>
~_
```

#### <span id="22.2.1.3">22.2.1.3</span> 编译格式化字符串

格式化字符串本质上是执行打印的特殊用途语言编写的程序, 由函数`format`解释. 宏`formatter`提供了做相同打印功能的已编译函数, 但丧失了格式化字符串的文本紧凑型表示.

格式化控制或者是一个格式化字符串, 或者是由宏`formatter`返回的函数.

#### <span id="22.2.1.4">22.2.1.4</span> 美观打印分发表

美观打印分发表是一个键到一对值的映射. 每个键是一个类型描述符. 与键相关的值是一个函数(一个函数指示器或者`nil`)和一个数值优先级(一个实数). 基于键的基本插入和检索操作使用键的`equal`测试语义.

当`*print-pretty*`为true时, 当前美观分发表(在`*print-pprint-dispath*`中)控制如何打印对象. 这个表中的信息比其它指定如何打印对象的机制具有更高的优先级. 通常, 因为当前每段分发表被首先使用, 它比用户定义的`print-object`方法的优先级高.

通过在当前美观分发表中查找与对象匹配的类型描述符对应的最高优先级的函数; 如果有多个最高优先级的函数, 使用哪一个是依赖于实现的.

然而, 如果表中没有关于特定对象的美观打印信息, 调用使用`print-object`的函数打印这个对象. 当这个函数被调用时`*print-pretty*`的值仍为true, `print-object`的方法仍被用于按依赖于`*print-pertty*`值的特殊格式产生输出.

#### <span id="22.2.1.5">22.2.1.5</span> 打印器的边距

美观打印的一个主要目标是保持输出在一对边距之间. 输出开始的列作为左边距. 如果当前列不能在输出开始时确定, 左边距被设置为零. 右边距由`*print-right-margin*`控制.

### <span id="22.2.2">22.2.2</span> 示例: 使用美观打印器

作为逻辑块、条件换行和索引的示例,考虑下面的函数`simple-pprint-defun`. 这个函数假设一个car是`defun`的列表的长度为4, 打印出该列表.

``` lisp
(defun simple-pprint-defun (*standard-output* list)
  (pprint-logical-block (*standard-output* list :prefix "(" :suffix ")")
    (write (first list))
    (write-char #\Space)
    (pprint-newline :miser)
    (pprint-indent :current 0)
    (write (second list))
    (write-char #\Space)
    (pprint-newline :fill)
    (write (third list))
    (pprint-indent :block 1)
    (write-char #\Space)
    (pprint-newline :linear)
    (write (fourth list))))
```

考虑下面的求值:

``` lisp
(simple-pprint-defun *standard-output* '(defun prod (x y) (* x y)))
```

如果可用的行宽度大于等于26, 则所有的输出出现在一行中. 如果可用的行宽度是25, 则在表达式`(* x y )`之前的线性风格条件换行处插入行断, 产生下面的输出. `(pprint-ident :block 1)`导致`(* x y)`在逻辑块中有相对缩进1.

``` lisp
(DEFUN PROD (X Y)
  (* X Y))
```

如果可用的行宽度是15, 则在传递参数列表之前填充风格条件换行处也插入一个行断. `(pprint-ident :current 0)`导致传递参数列表在函数名称下对齐.

``` lisp
(DEFUN PROD
       (X Y)
  (* X Y))
```

因为在吝啬(miser)模式中所有缩进变化被忽略, 且行断在吝啬风格条件换行处插入, 如果`*print-miser-width*`大于等于14, 上面示例的输出如下:


``` lisp
(DEFUN
 PROD
 (X Y)
 (* X Y))
```

作为行前缀的示例, 考虑在行宽度为20且`*print-miser-width*`为`nil`时, 下面的求值产生的输出:

``` lisp
(pprint-logical-block (*standard-output* nil :per-line-prefix ";;; ")
  (simple-pprint-defun *standard-output* '(defun prod (x y) (* x y))))

;;; (DEFUN PROD
;;;        (X Y)
;;;   (* X Y))
```

作为更复杂的(也更实际的)示例, 考虑下面的`pprint-let`函数. 这个函数描述了如何在传统风格下打印一个`let`形式. 因为需要处理内嵌解构, 所以它比上面的示例更为复杂. 同时, 与上面的示例不同的是, 它包含了完整的可读打印出所有以符号`let`开始的列表的代码. 最外层的`pprint-logical-block`形式将输入列表作为整个打印处理, 说明了括号应该打印到输出中. 第二个`ppring-logical-block`形式处理绑定对的列表. 列表中每个对本身是用最内层的`pprint-logical-block`打印的.(使用一个`loop`形式而不是简单的将对分解为两个对象, 从而不管与对相应的列表是包含一个元素、两个元素、还是多于两个元素(错误的). 可以产生可读的输出.) 除最后一个对外, 在每个对之后插入一个空格和一个填充风格的条件换行. 在最顶层的`pprint-logical-block`形式末尾处的循环打印出在`let`形式体中被空格和线性风格条件换行分隔的形式.


``` lisp
(defun pprint-let (*standard-output* list)
  (pprint-logical-block (nil list :prefix "(" :suffix ")")
    (write (pprint-pop))
    (pprint-exit-if-list-exhausted)
    (write-char #\Space)
    (pprint-logical-block (nil (pprint-pop) :prefix "(" :suffix ")")
      (pprint-exit-if-list-exhausted)
      (loop (pprint-logical-block (nil (pprint-pop) :prefix "(" :suffix ")")
              (pprint-exit-if-list-exhausted)
              (loop (write (pprint-pop))
                    (pprint-exit-if-list-exhausted)
                    (write-char #\Space)
                    (pprint-newline :linear)))
            (pprint-exit-if-list-exhausted)
            (write-char #\Space)
            (pprint-newline :fill)))
    (pprint-indent :block 1)
    (loop (pprint-exit-if-list-exhausted)
          (write-char #\Space)
          (pprint-newline :linear)
          (write (pprint-pop)))))
```

考虑`*print-level*`为4且`*print-circle*`为true时下面的求值:

``` lisp
(pprint-let *standard-output*
            '#1=(let (x (*print-length* (f (g 3)))
                      (z . 2) (k (car y)))
                  (setq x (sqrt z)) #1#))
```

如果行长度大于等于77, 产生的输出出现在一行中. 然而, 如果行长度是76, 在体中分隔形式的线性风格条件换行处插入行断, 产生下面的输出. 注意, 尽管绑定`x`的对不是一个列表也被打印为可读的; 在`(g 3)`处打印出一个深度缩略标记; 绑定对`(z . 2)`不是合式列表但也被打印为可读的; 同时也打印出一个恰当的循环标记.

``` lisp
#1=(LET (X (*PRINT-LENGTH* (F #)) (Z . 2) (K (CAR Y)))
     (SETQ X (SQRT Z))
     #1#)
```

如果行长度为35, 在分隔绑定对的填充风格条件换行中的一处插入行断.

``` lisp
#1=(LET (X (*PRINT-PRETTY* (F #))
         (Z . 2) (K (CAR Y)))
     (SETQ X (SQRT Z))
     #1#)
```

假设行长度为22且`*print-length*`为3. 在这种情况下, 在第一个和第二个绑定对之后都插入行断. 此外, 第二个绑定对被拆分成两行. 填充风格条件换行描述中的句子(b)(见函数`pprint-newline`)阻止对`(z . 2)`打印在在第三行末尾. 注意长度缩写隐藏了循环, 因此没有循环标记.

``` lisp
(LET (X
      (*PRINT-LENGTH*
       (F #))
      (Z . 2) ...)
  (SETQ X (SQRT Z))
  ...)
```

下面的函数使用`#(...)`记法打印向量.

``` lisp
(defun pprint-vector (*standard-output* v)
  (pprint-logical-block (nil nil :prefix "#(" :suffix ")")
    (let ((end (length v)) (i 0))
      (when (plusp end)
        (loop (pprint-pop)
              (write (aref v i))
              (if (= (incf i) end) (return nil))
              (write-char #\Space)
              (pprint-newline :fill))))))
```

在行长度为15时:

``` lisp
(pprint-vector *standard-output* '#(12 34 567 8 9012 34 567 89 0 1 23))

#(12 34 567 8
  9012 34 567
  89 0 1 23)
```

作为使用格式化字符串描述美观打印的示例, 考虑上面的函数`simple-pprint-defun`和`pprint-let`可以按下面的方式紧凑的定义. (因为`pprint-vector`遍历的数据结构不是列表, 所以它不能用`format`定义).


``` lisp
(defun simple-pprint-defun (*standard-output* list)
  (format T "~:<~W ~@_~:I~W ~:_~W~1I ~_~W~:>" list))

(defun pprint-let (*standard-output* list)
  (format T "~:<~W~^~:<~@{~:<~@{~W~^~_~}~:>~^~:_~}~:>~1I~@{~^~_~W~}~:>" list))
```

在下面的示例中, 第一个形式将`*print-pprint-dispatch*`恢复到其初始值. 下面两个形式设置了美观打印比值的特殊方法. 注意, 更特殊的类型描述符需要与更高的优先级关联.

``` lisp
(setq *print-pprint-dispatch* (copy-pprint-dispatch nil))

(set-pprint-dispatch 'ratio
  #'(lambda (s obj)
      (format s "#.(/ ~W ~W)"
                (numerator obj) (denominator obj))))

(set-pprint-dispatch '(and ratio (satisfies minusp))
  #'(lambda (s obj)
      (format s "#.(- (/ ~W ~W))"
              (- (numerator obj)) (denominator obj)))
  5)

(pprint '(1/3 -2/3))
(#.(/ 1 3) #.(- (/ 2 3)))
```

下面的两个形式展示了代码类型的美观打印函数的定义. 第一个形式展示了如何描述打印使用单引号的引用对象的传统方法. 注意, 恰好以`quote`开始的数据列表列表需要被可读打印. 第二个形式描述了在初始美观打印分发表生效时, 以符号`my-let`开始的列表应该用与以`let`开始的列表同样的方式打印.

``` lisp
(set-pprint-dispatch '(cons (member quote)) ()
  #'(lambda (s list)
      (if (and (consp (cdr list)) (null (cddr list)))
         (funcall (formatter "'~W") s (cadr list))
         (pprint-fill s list))))

(set-pprint-dispatch '(cons (member my-let))
                     (pprint-dispatch '(let) nil))
```

下一个示例描述了打印不与函数调用对应的列表的默认方法. 注意, 函数`pprint-linear`、`pprint-fill`和`pprint-tabular`都有选项`colon-p`和`at-sign-p`, 因此它们可以用作美观打印分发函数和`~/.../`函数.

``` lisp
(set-pprint-dispatch '(cons (not (and symbol (satisfies fboundp))))
                     #'pprint-fill -5)

;; Assume a line length of 9
(pprint '(0 b c d e f g h i j k))
(0 b c d
 e f g h
 i j k)
```

最后一个例子展示了如何为用户定义的数据结构定义美观打印函数.

``` lisp
(defstruct family mom kids)

(set-pprint-dispatch 'family
  #'(lambda (s f)
      (funcall (formatter "~@<#<~;~W and ~2I~_~/pprint-fill/~;>~:>")
              s (family-mom f) (family-kids f))))
```

结构`family`的美观打印函数描述了如何调整输出的布局, 以美观的适用于多种行宽. 此外, 它受控制变量`*print-level*`、`*print-length*`、`*print-lines*`、`*print-circle*`和`*print-escape*`控制, 可以容忍数据结构中多种类型的错误. 在右边局为25、`*print-pertty*`为true、`*print-escape*`为false和一个错误的`kids`列表时:

``` lisp
(write (list 'principal-family
             (make-family :mom "Lucy"
                          :kids '("Mark" "Bob" . "Dan")))
       :right-margin 25 :pretty T :escape nil :miser-width nil)
(PRINCIPAL-FAMILY
 #<Lucy and
     Mark Bob . Dan>)
```

注意, 结构的美观打印函数与结构的`print-object`方法不同. `print-object`方法是与结构永久关联的, 而美观打印函数存储在美观打印分发表中, 可以为满足不同打印需要而快速改变. 如果在当前美观打印分发表中没有结构的美观打印函数, 则使用它的`print-object`方法.

### <span id="22.2.3">22.2.3</span> 备注: 美观打印器的背景

关于这一节描述的抽象概念的背景引用, 见 **XP: A Common Lisp Pretty Printing System**[^1].这篇文档的细节不是与该文档绑定的, 但在建立理解这些材料的概念基础上有帮助.

[^1]: XP: A Common Lisp Pretty Printing System https://dspace.mit.edu/bitstream/handle/1721.1/6503/AIM-1102.pdf

## <span id="22.3">22.3</span> 格式化的输出

`format`用于生成漂亮的格式化文本、消息等. `format`可以生成并返回一个字符串或到目的地的输出.

`format`的`control-string`传递参数实际上是一个格式控制. 即, 它是格式化字符串或一个函数, 例如宏`formatter`返回的函数.

如果它是一个函数, 这个函数被与作为其起一个传递参数的恰当的输出流和传递给`format`的数据传递参数一起调用. 这个函数应该执行必要的输出并返回未使用的传递参数尾(如果有的话).

`formatter`执行的编译过程生成一个函数, 这个函数按与`format`解释器一样的方式处理它的传递参数.

这一节的剩下部分描述如果`control-string`是格式化字符串时发生什么.

`control-string`由简单文本(字符)和内嵌的指令组成.

`format`原样输出简单文本; 每个内嵌的指令描述了在简单文本中响应位置出现的文本输出. 大多数指令使用一个或多个`args`创建输出.

指令由变音符(tilde, `~`)、可选的按`,`分隔的前缀参数、可选的`:`和`@`修饰符和一个标识指令种类的单个字符组成. 在`:`与`@`之间不要求顺序. 指令字符的大小写被忽略. 前缀参数记为有符号的十进制数值(符号是可选的), 或单个字符的引用. 例如, `~5,'0d`用于将十进制基的整数按前缀0的5列打印, 或者使用`~5,'*d`指定前缀为`*`.

在指令的前缀参数处, 可以使用`V`(或`v`). 在这种情况下, `format`从`args`取一个传递参数作为指令的参数. 这个传递参数应该是一个整数或字符. 如果被v参数使用的`arg`是`nil`, 其效果是该参数被省略. `#`可用于前缀参数处; 它表示剩下的需要处理的`args`的数量. 当在递归格式的`~?`或`~{`上下文中, `#`前缀参数表示在递归调用中格式化传递参数的数量.

格式化字符串的示例:

<span id="Figure22-6">图 22-6. 格式控制字符串的示例.</span>

``` lisp
"~S"        ; S指令, 没有参数和修饰符
"~3,-4:@s"  ; S指令, 有两个参数: 3和-4, 有:和@标签
"~,+4S"     ; 第一个参数被省略, 取默认值; 第二个参数为4
```

`format`将输出发送到目的地. 如果目的地是`nil`, `format`创建并返回一个包含按`control-string`产生的输出的字符串. 如果目的地不是`nil`, 它必须是一个有填充指针的字符串、一个流或符号`t`. 如果目的地是有填充指针的字符串, 输出被添加到字符串的末尾. 如果目的地是一个流, 输出被发送到这个流. 如果目的地是`t`, 输出被发送到标准输出流.

在下文指令的描述中, 项`arg`通常表示`args`中下一个要被处理的项. 每个描述开始处的单词或短语是指令的助记词句. 除非在后面说明, `format`指令不会绑定任何打印器控制变量(`*print-...*`). 实现可以为每个`format`指令定义新的特定于实现的打印器控制变量, 但它们不能绑定任何在`format`指令描述中未涉及的标准打印器控制变量, 或者不能绑定到描述中任何标准打印器控制变量.

### <span id="22.3.1">22.3.1</span> FORMAT的基本输出

#### <span id="22.3.1.1">22.3.1.1</span> `~C`: 字符

下一个`arg`应该是一个字符, 根据修饰符标志打印.

在字符是简单字符时, `~C`就像使用`write-char`打印这个字符. 不是简单字符的字符不一定要像使用`write-char`打印, 但按时限定义的简洁格式展示. 例如:

``` lisp
(format nil "~C" #\A) =>  "A"
(format nil "~C" #\Space) =>  " "
```

`~:C`与`~C`在可打印字符方面相同, 但其它字符是被拼出的. 意图是这是打印字符的美观的格式. 对不可打印的简单字符, 被拼出的是字符的名称(见`char-name`). 对不简单的不可打印字符, 被拼出的是由实现定义的. 例如:

``` lisp
(format nil "~:C" #\A) =>  "A"
(format nil "~:C" #\Space) =>  "Space"

;; This next example assumes an implementation-defined "Control" attribute.
(format nil "~:C" #\Control-Space)
=>  "Control-Space"
OR=>  "c-Space"
```

`~:@C`打印出`~:C`打印的内容, 然后如果字符需要键盘上不常用的Shift键打出, 会提及这个事实. 例如:

``` lisp
(format nil "~:@C" #\Control-Partial) =>  "Control-<PARTIAL> (Top-F)"
```

这是用于告知关于他期望打出的键的格式, 例如在提示中. 具体的输出可能不仅依赖于实现, 也依赖于使用的I/O设备.

`~@C`使用`#\`语法, 按Lisp读取器可以理解的方式打印字符.

`~@C`绑定`*print-escape*`为`t`.

#### <span id="22.3.1.2">22.3.1.2</span> `~%`: 换行

这会输出`#\Newline`字符, 从而结束当前输入行病开启新行. `~n%`输出`n`个新行. 不使用`arg`.

#### <span id="22.3.1.3">22.3.1.3</span> `~&`: 新行

除非可以确定输出流当前在行的开始处, 这会输出新行. `~n%`调用`fresh-line`, 然后输出`n-1`个新行. `~0&`什么也不做.

#### <span id="22.3.1.4">22.3.1.4</span> `~|`: 页

可能的情况下, 这会输出一个页分隔字符. `~n|`输出`n`个.

#### <span id="22.3.1.5">22.3.1.5</span> `~~`: `~`

这输出一个变音符(`~`). `~n~`输出`n`个.

### <span id="22.3.2">22.3.2</span> FORMAT基数控制

#### <span id="22.3.2.1">22.3.2.1</span> `~R`: 基数

`~nR`按基数`n`打印`arg`. 修饰符标志和任何剩下的参数用于`~D`指令. `~D`与`~10R`相同. 完整的格式是`~` `radix,mincol,padchar,commachar,comma-interval` `R`.

如果`~R`没有前缀参数, 则给出不同的解释. 这个参数需要是一个整数. 例如, 如果`arg`是4:

* `~R`将`arg`打印为基数英语数值: `four`.
* `~:R`将`arg`打印为英语序数: `fourth`.
* `~@R`将`arg`打印为罗马数字: `IV`.
* `~:@R`将`arg`打印为旧式罗马数字: `IIII`.

例如:

``` lisp
(format nil "~,,' ,4:B" 13) =>  "1101"
(format nil "~,,' ,4:B" 17) =>  "1 0001"
(format nil "~19,0,' ,4:B" 3333) =>  "0000 1101 0000 0101"
(format nil "~3,,,' ,2:R" 17) =>  "1 22"
(format nil "~,,'|,2:D" #xFFFF) =>   "6|55|35"
```

当且仅当提供了第一个参数`n`, `~R`绑定`*print-escape*`为false、`*print-radix*`为false、`*print-base*`为`n`、`*print-readably*`为false.

当且仅当没有提供参数, `~R`绑定`*print-base*`为10.

#### <span id="22.3.2.2">22.3.2.2</span> `~D`: 十进制

是整数的`arg`, 应该按十进制基数打印. `~D`从不在数值后打印一个小数点.

`~` `mincol` `D`使用列宽`mincol`; 如果数值需要比`mincol`少的列展示它的数字和符号, 在数值的左边插入空格. 如果数值不能纳入`mincol`列中, 使用额外的列.

`~` `mincol,padchar` `D`使用`padchar`作为填充字符而不是空格.

如果`arg`不是整数, 它被使用`~A`格式和十进制基数打印.

修饰符`@`导致数值的符号总被但引出; 默认只在数值是负数时打印出符号. 修饰符`:`导致在数字组之间打印出逗号; `commachar`用于修改用于逗号的字符. `comma-interval`必须是一个整数, 默认值为3. 当在这些指令中使用了修饰符`:`时, 在`comma-interval`个数字作为一组的组之间打印出`commachar`.

因此, `~D`的通用形式是`~` `mincol,padchar,commachar,comma-interval` `D`.

`~D`绑定`*print-escape*`为false、`*print-radix*`为false、`*print-base*`为10、`*print-readably*`为false.

#### <span id="22.3.2.3">22.3.2.3</span> `~B`: 二进制

与`~D`类似, 这会按二进制基数打印数值. 因此完整的形式是`~` `mincol,padchar,commachar,comma-interval` `B`.

`~B`绑定`*print-escape*`为false、`*print-radix*`为false、`*print-base*`为2、`*print-readably*`为false.

#### <span id="22.3.2.4">22.3.2.4</span> `~O`: 八进制

与`~D`类似, 这会按八进制基数打印数值. 因此完整的形式是`~` `mincol,padchar,commachar,comma-interval` `O`.

`~O`绑定`*print-escape*`为false、`*print-radix*`为false、`*print-base*`为8、`*print-readably*`为false.

#### <span id="22.3.2.5">22.3.2.5</span> `~X`: 十六进制

与`~D`类似, 这会按十六进制基数打印数值. 因此完整的形式是`~` `mincol,padchar,commachar,comma-interval` `X`.

`~X`绑定`*print-escape*`为false、`*print-radix*`为false、`*print-base*`为16、`*print-readably*`为false.

### <span id="22.3.3">22.3.3</span> FORMAT浮点数打印器

#### <span id="22.3.3.1">22.3.3.1</span> `~F`: 固定格式的浮点数

将下一个`arg`作为浮点数打印.

完整的形式是`~` `w,d,k,overflowchar,padchar` `F`. 参数`w`是被打印的字段的宽度; `d`在小数点之后打印的数字的数量; `k`是默认为0的缩放因子.

会输出`w`个字符. 首先, 必要时先打印字符`padchar`(默认为空格)的副本, 在字段左端填充. 如果`arg`是负数, 则打印一个负号; 如果`arg`非负, 当且仅当使用了修饰符`@`是打印一个正号. 然后是一个数字序列, 包含一个内嵌的小数点; 这表示`arg`乘以10^k的量级, 四舍五入到`d`位小数. 如果向上和向下舍入的值与`arg`的缩放值等距, 则实现可以使用任意一个. 例如, 使用`~4,2F`打印传递参数`6.375`可以正确的产生`6.37`或`6.38`. 不允许有前缀0, 除非打印的值比1小时小数点前有单个零, 如果`w=d+1`, 不需要打印出这个零.

如果不能按宽度`w`打印出值, 则采取这两个动作的一个. 如果提供了参数`overflowchar`, 则打印`w`个`overflowchar`替代`arg`的缩放值. 如果省略了参数`overflowchar`, 则使用多于`w`个字符打印出缩放值.

如果省略了参数`w`, 则字段是变宽的. 实际上, 按不需要头部填充字符和小数点后`d`个字符的方式选择`w`的值. 例如, 指令`~,2F`将在小数点后打印两个数字, 之前打印必要数量的数字.

如果省略了参数`d`, 则在小数点后出现的数字数量上没有限制. `d`值得选择方式是, 由参数`w`引入的宽度和小数部分没有尾部零约束下尽可能多的数字数量, 如果小数部分是零, 则在宽度约束允许的情况下在小数点后有一个零.

如果同时省略了参数`w`和`d`, 则使用常规的自由格式输出打印值; `prin1`使用这个格式打印量级或者是0或者在10^-3(包含)与10^7之间的数值.

如果省略了`w`,且`arg`的量级很大(或者省略了`d`且`arg`的量级很小), 需要打印出多于100个数字时, 允许实现使用指数记法打印数字, 就像使用指令`~E`(使用`~E`的默认参数, 不使用`~F`中的参数).

如果`arg`是一个有理数, 则被转换为单浮点数并被打印. 也允许实现用其它方法处理有理数, 这些方法本质上有相同的行为、但避免了因转换带来的精度丢失或上溢. 如果没有指定`w`和`d`, 数值没有精确的十进制表示, 例如`1/3`, 因为只能打印出有限数量的数字, 实现必须选择精度截止值.

如果`arg`是一个复数或非数值对象, 则使用格式指令`~WD`打印, 从而按十进制基数和最小字段宽度`w`打印.

`~F`绑定`*print-escape*`为false、`*print-readably*`为false.

#### <span id="22.3.3.2">22.3.3.2</span> `~E`: 指数浮点数

下一个`arg`作为浮点数按指数记法打印.

完整的格式是`~` `w,d,e,k,overflowchar,padchar,exponentchar` `E`. 参数`w`是被打印的字段的宽度; `d`是小数点之后打印的数字的数量; `e`是打印的指数的数字的数量; `k`是默认为1(不是零)的缩放因子.

会输出`w`个字符. 首先, 必要时先打印字符`padchar`(默认为空格)的副本, 在字段左端填充.  如果`arg`是负数, 则打印一个负号; 如果`arg`非负, 当且仅当使用了修饰符`@`是打印一个正号. 然后是一个数字序列, 包含一个内嵌的小数点. 这个数字序列的形式依赖于缩放因子`k`. 如果`k`是零, 则小数点之后打印`d`个数字, 且如果宽度允许时在小数点之前打印一个零. 如果`k`是正数, 则它必须必`d+2`小; 在小数点之前打印`k`个显著数字, 在小数点之后打印`d-k+1`个数字. 如果`k`是负数, 则它必须大于`-d`; 如果宽度允许时在小数点之前打印一个零, 在小数点后先打印`-k`个零和`d+k`个显著数字. 打印的小数部分必须被恰当的舍入. 当向上和向下舍入的值与`arg`的缩放值等距是, 实现可以使用任意一个. 例如, 使用格式`~8,2E`打印传递参数`637.5`可以正确的生成`6.37E+2`或`6.38E+2`.

在数字序列之后打印指数. 首先打印字符参数`exponentchar`; 如果省略了这个参数, 则打印出`prin1`使用的指数标记, 它是根据浮点数的类型和`*read-default-float-format*`的当前值确定的. 然后, 打印一个正号或负号, 然后是`e`个表示10的幂的数字, 与打印的小数部分相乘可以恰当的表示`arg`的舍入值.

如果不能按宽度`w`打印出值, 可能是因为`k`过大或多小、指数不能在`e`个字符位置中打印出, 则采取这两个动作的一个. 如果提供了参数`overflowchar`, 则打印`w`个`overflowchar`替代`arg`的缩放值. 如果省略了参数`overflowchar`, 则使用多于`w`个字符打印出缩放值; 如果问题是对于提供的`k`, `d`太小或`e`太小, 则为`d`或`e`使用一个更大的值.

如果省略了参数`w`, 则字段是变宽的. 实际上, 按不需要头部填充字符的方式选择`w`的值.

如果省略了参数`d`, 则出现的数字的数量上没有约束. `d`值得选择方式是, 由参数`w`引入的宽度、缩放因子`k`和小数部分没有尾部零数字的约束下尽可能多的数字数量, 如果打印的小数部分是零, 则小数点后应该出现单个零数字.

如果省略了参数`e`, 则指数按最少的表示其值的必要的数字数量方式打印.

如果同时省略了`w`、`d`和`e`, 则使用常规的自由格式的指数记法输出打印值; `prin1`为任何量级小于10^-3或大于10^7的非零数值使用相似的格式. 唯一的区别是, 指令`~E`总是在指数前打印一个正号或负号, 而`prin1`在指数为非负时省略正号.

如果`arg`是一个有理数, 则被转换为单浮点数并被打印. 也允许实现用其它方法处理有理数, 这些方法本质上有相同的行为、但避免了因转换带来的精度丢失或上溢. 如果没有指定`w`和`d`, 数值没有精确的十进制表示, 例如`1/3`, 因为只能打印出有限数量的数字, 实现必须选择精度截止值.

如果`arg`是一个复数或非数值对象, 则使用格式指令`~WD`打印, 从而按十进制基数和最小字段宽度`w`打印.

`~E`绑定`*print-escape*`为false、`*print-readably*`为false.

#### <span id="22.3.3.3">22.3.3.3</span> `~G`: 常规浮点数

下一个`arg`作为浮点数按固定格式或指数记法打印.

完整的形式是`~` `w,d,e,k,overflowchar,padchar,exponentchar` `G`. 打印`arg`的格式依赖于`arg`(的绝对值)的量级. 整数 $n$ 满足 $10^{n-1} \le |arg| \lt 10^n$. $ee$ 等于 $e+2$, 在省略了`e`时等于4. $ww$ 等于 $w-ee$, 在省略了`w`是等于`nil`. 如果省略了`d`, $q$ 是不丢失信息和没有头尾零条件下打印`arg`需要的数字数量; 则`d`等于`(max q (min n 7))`. $dd = d-n$.

如果 $0 \le dd \lt d$, 则使用下面的格式指令打印`arg`:

``` lisp
~ww,dd,,overflowchar,padcharF~ee@T
```

注意缩放因子`k`没有传递给指令`~F`. 对所有其它`dd`值, 使用下面的格式指令打印`arg`:

``` lisp
~w,d,e,k,overflowchar,padchar,exponentcharE
```

在两种情况中, 当且仅当给指令`~G`提供了修饰符`@`时, 它会提供给指令`~F`或`~E`.

`~G`绑定`*print-escape*`为false、`*print-readably*`为false.

#### <span id="22.3.3.4">22.3.3.4</span> `~$`: 货币浮点数

下一个`arg`作为浮点数按固定格式记法打印.

完整的格式是`~` `d,n,w,padchar` `$`. 参数`d`是小数点后数字的数量(默认值是2); `n`是小数点之前最小的数字数量(默认值是1); `w`是被打印的字段的最小总宽度(默认值是0).

首先输出填充和符号. 如果`arg`是负数, 则打印一个符号; 如果`arg`非负, 当且仅当提供了修饰符`@`时打印一个正号. 如果使用了修饰符`:`, 则符号在填充之前出现, 否则在填充之后出现. 如果提供了`w`且输出的其它字符的数量小于`w`, 则输出一些`padchar`(默认为空格)的副本使得字段总宽度等于`w`. 然后, 打印`arg`整数部分的`n`个数字, 必要时有前缀零; 接着是一个小数点; 接着是恰当舍入的n个小数数字.

如果`arg`的量级需要打印出多于`m`个数字, `m`是`w`和100的较大者, 则允许实现按指令`~` `w,q,,,,padchar` `E`使用指数记法打印数值, 这里, `w`和`padchar`根据它们是否在指令`~$`中出现或省略而出现或省略, `q=d+n-1`, `d`和`n`是提供给指令`~$`的值(可能是默认值).

如果`arg`是一个有理数, 则被转换为单浮点数并被打印. 也允许实现用其它方法处理有理数, 这些方法本质上有相同的行为、但避免了因转换带来的精度丢失或上溢.

如果`arg`是一个复数或非数值对象, 则使用格式指令`~WD`打印, 从而按十进制基数和最小字段宽度`w`打印.

`~$`绑定`*print-escape*`为false、`*print-readably*`为false.

### <span id="22.3.4">22.3.4</span> FORMAT打印器操作

#### <span id="22.3.4.1">22.3.4.1</span> `~A`: 美观的

作为任意对象的`arg`, 不使用转义字符打印(就像使用`princ`). 如果`arg`是一个字符串, 它的字符被原样输出. 如果`arg`是`nil`, 它将被打印为`nil`; 修饰符`:`(`~:A`)将为`nil`的`arg`打印为`()`, 但如果`arg`是聚合结构, 例如列表或向量, 其中包含的`nil`仍被打印为`nil`.

`~` `mincol` `A`必要时在右边插入空格, 使得宽度至少为`mincol`列. 修饰符`@`导致在左边插入空格.

`~A`的完整形式是`~` `mincol,colinc,minpad,padchar` `A`, 它允许控制填充. 在字符串的右边填充至少`minpad`个`padchar`(如果使用了修饰符`@`在左边填充); 然后一次插入`colinc`个填充字符直到总宽度至少为`mincol`. `mincol`和`minpad`的默认值为0, `colinc`的默认值为1, `padchar`的默认值为空格字符.

`~A`绑定`*print-escape*`为false、`*print-readably*`为false.

#### <span id="22.3.4.2">22.3.4.2</span> `~S`: 标准的

与`~A`类似, 但使用转义字符打印`arg`(就像使用`prin1`而不是`princ`). 因此输出可被`read`读取. `~S`接受`~A`可以接受的所有传递参数和修饰符.

`~S`绑定`*print-escape*`为`t`.

#### <span id="22.3.4.3">22.3.4.3</span> `~W`: `write`

作为任意对象的传递参数的打印遵循每个打印器控制变量(就像使用`write`). 此外, `~W`正确的处理深度缩写, 但不将深度计数器充值为零. `~W`不接受参数. 如果给定了修饰符`:`, `~W`绑定`*print-pretty*`为true. 如果给定了修饰符`@`, `~W`绑定`*print-level*`和`*print-length*`为`nil`.

`~W`自动支持检测循环和共享. 如果`*print-circle*`的值不是`nil`, `~W`被应用在一个循环的(或共享的)引用的传递参数上, 在输出中传递参数的位置插入恰当的`#n#`标记.

### <span id="22.3.5">22.3.5</span> FORMAT美观打印器操作

下面的构造支持对美观打印器的访问.

#### <span id="22.3.5.1">22.3.5.1</span> `~_`: 条件换行

不带任何修饰符的`~_`与`(pprint-newline :linear)`相同. `~@_`与`(pprint-newline :miser)`相同. `~:_`与`(pprint-newline :fill)`相同. `~:@_`与`(pprint-newline :mandatory)`相同.

#### <span id="22.3.5.2">22.3.5.2</span> `~<`: 逻辑块

``` lisp
~<...~:>
```

当使用`~:>`结束`~<...~:>`时, 这个指令等价于调用`pprint-logical-block`. 与指令`~<...~:>`相应的传递参数被视为`pprint-logical-block`的列表传递参数, 从而提供了非列表传递参数和循环、共享和深度缩写的自动支持. 在`~<...~:>`中内嵌的`control-string`部分指定了`pprint-logical-block`的`:prefix`(或`:per-line-prefix`)、`:suffix`和体.

`~<...~:>`中的`control-string`部分可以用指令`~;`拆分为片段`~<prefix~;body~:suffix~:>`. 如果第一节用`~@;`结束, 它指定了一个行前缀而不是简单前缀. `prefix`和`suffix`不能包含格式指令. 如果`prefix`或`suffix`不是常量字符串或包含的部分被拆分为多于三个片段, 则发出错误信号.

如果包含的部分只被拆分为两个片段, `suffix`默认为空字符串. 如果包含的部分只有一个片段构成, 则`prefix`和`suffix`默认为空字符串. 如果使用了修饰符`:`(即`~:<...~:>`), `prefix`和`suffix`分别默认为`"("`和`")"`.

体片段可以是任意格式字符串. 这个格式字符串作为整体应用在与指令`~<...~:>`对应的列表的元素上. 使用`pprint-pop`从这个列表中提取元素, 从而提供了不良格式的列表和循环、共享、长度缩写的自动支持. 在体片段中, `~^`就像`pprint-exit-if-list-exhausted`一样.

`~<...~:>`支持一些`pprint-logical-block`不支持的特性. 如果`~:@>`用于结束指令(即`~<...~:@>`), 则在体重立即包含的每个空白组之后插入填充风格的条件换行(除了换行指令后的空白). 这为支持段落填充提供了便利.

如果将`~<...~:>`与`@`一起使用, 整个剩下的传递参数列表作为该指令的传递参数. 所有剩下的传递参数总是被`~@<...~:>`消费, 甚至它们没有在该指令的内嵌格式字符串中被全部使用时. 除了传递参数的不同, `~@<...~:>`与`~<...~:>`相同, 除了如果在格式字符串的顶层遇到`~@<...~:>`时循环检测不适用. 这确保了循环检测只适用于数据列表, 而不是格式传递参数列表.

如果循环或共享必须被指定为整体作为传递参数, 则打印出`" . #n#"`.

在一定长度上, 指令`~<...~>`的基本形式与使用`~W`、`~_`、`~<...~:>`、`~I`和`~:T`输出安排的动态控制不兼容. 所以, 如果这些指令出现在`~<...~>`中会发出错误信号. 此外, 如果`~<...~>`的形式`~<...~:;...~>`用于`~W`、`~_`、`~<...~:>`、`~I`和`~:T`的格式字符串中, 也会发出错误信号.

见[22.3.6.2 `~<`: 调整](#22.3.6.2).

#### <span id="22.3.5.3">22.3.5.3</span> `~I`: 缩进

`~nI`与`(pprint-indent :block n)`相同.

`~n:I`与`(pprint-indent :current n)`相同. 在这两种情况下, 如果省略了`n`, 默认值为零.

#### <span id="22.3.5.4">22.3.5.4</span> `~/`: 调用函数

``` lisp
~/name/
```

使用指令`~/name/`可以在格式字符串中调用用户定义的函数. 可以将指令`~/name/`与修饰符`:`、`@`和任意数量的参数一起使用. `name`可以是任意不包含`"/"`的字符串. `name`中所有字符被视为是大写的. 如果`name`包含`:`或`::`, 则直到但不包括第一个`:`或`::`的字符串被视为命名了包的字符串. 第一个`:`或`::`之后的字符串(如果有的话)被视为命名了符号的字符串. 与指令`~/name/`对应的函数通过从指定包中查找指定名称的符号获得. 如果`name`不包含`:`或`::`, 则在包`COMMON-LISP-USER`中查找字符串`name`.

当遇到指令`~/name/`时, 使用四个或更多个传递参数调用相应的函数. 头四个传递参数是: 输出流、指令对应的格式传递参数、使用修饰符`:`时为true的广义布尔值、使用修饰符`@`时为true的广义布尔值. 剩下的传递参数由指令中指定的任意参数构成. 这个函数应该恰当的打印这些传递参数. 这个函数的返回值被忽略.

特殊设计的三个函数`pprint-linear`、`pprint-fill`和`pprint-tabular`可被`~/.../`调用(即`~/pprint-linear/`、`~/pprint-fill/`和`~/pprint-tabular/`). 通常它们有传递参数`:`和`@`.

### <span id="22.3.6">22.3.6</span> FORMAT布局控制

#### <span id="22.3.6.1">22.3.6.1</span> `~T`: 制表

这在给定列上指定空格. `~` `colnum,colinc` `T`会输出足够多的空格将游标移动到列`colnum`处. 如果游标已在列`colnum`或在列`colnum`之后, 除非`colinc`为零, 它会输出空格将游标移动到列`colnum+k*colinc`, 取最小的正整数`k`; 如果`colinc`为零, 不会输出空格. `colnum`和`colinc`的默认值为1.

如果出于某些原因当前绝对列位置不能通过直接查询确定, `format`可以根据一些指令(例如`~%`、`~&`和包含换行的字符串作为传递参数的`~A`)将列位置重置为零推断出当前列位置, 并从那个位置开始计数字符串的数量. 如果还是不能确定, `format`将会在其被调用时目的地在列零上的冒险假设上尝试类似的推断. 甚至在尝试失败或不方便实现的情况下, `~T`操作最坏的情况下简单的输出两个空格.

`~@T`执行相对制表. `~` `colrel,colinc` `@T`输出`colrel`个空格, 然后输出需要将游标移动到`colinc`倍数位置列的最小非负数量的额外空格. 例如, 指令`~3,8T`输出三个空格, 然后如果游标不在标准8倍制表停止处, 将游标移动到这个标准8倍制表停止处. 如果不能确定当前输出列, 忽略`colinc`, 只输出`colrel`个空格.

如果将修饰符`:`与指令`~T`一起使用, 则制表计算是相对于立即包含指令的节的开始处水平位置的, 而不是相对于水平位置零. 数值参数被解释为字符宽度单元, 默认值为1. `n,m:T`与`(pprint-tab :section n m)`相同. `~n,m:@T`与`(pprint-tab :section-relative n m)`相同.

#### <span id="22.3.6.2">22.3.6.2</span> `~<`: 对齐

``` lisp
~mincol,colinc,minpad,padchar<str~>
```

这个指令调整在宽度至少为`mincol`列的字段中处理`str`生成的字符串. 可以用`~;`将`str`划分为片段, 在这种情况下, 间隔在文本片段之间均匀划分.

不带修饰符时, 最左边的文本片段在字段中被左对齐, 最右边的文本片段被右对齐. 如果只有一个文本元素, 它被右对齐. 修饰符`:`在第一个文本片段之前引入间隔; 修饰符`@`在最后一个文本片段之后加上间隔. 参数`minpad`是在每个片段之间输出的填充字符的最小数量, 默认为0. 参数`padchar`指定填充字符, 默认为空格字符. 如果需要满足这些约束的总宽度大于`mincol`, 则使用宽度`mincol+k*colinc`, 取最小的非负整数值`k`. `colinc`默认为1, `mincol`默认为0.

注意, `str`可能包含格式化指令. 按序处理`str`中的子句; 对齐的是子句的结果文本.

指令`~^`可用于提前结束处理子句, 在这种情况下, 只对齐完成处理的子句.

如果用`~:;`而不是`~;`结束`~<`的第一个子句, 则这个子句被按特殊方式使用. 所有子句已被处理(当然是对`~^`而言的), 但第一个不被用于执行生成间隔和填充. 当已确定填充后的结果时, 如果它可纳入当前输出行, 则输出它, 忽略第一个子句的文本. 然而, 如果填充后的文本不能纳入当前行, 则在填充后的文本之前输出第一个子句的文本片段. 第一个子句应该包含换行(例如指令`~%`). 第一个子句总被处理, 它引用的任意传递参数总会被使用; 是是否使用结果文本片段的决策, 而不是是否处理第一个子句的决策. 如果`~:;`有前缀参数`n`, 则在避免输出第一个子句的文本而节省`n`个字符位置的情况下填充后的文本必须可以纳入当前行. 例如, 控制字符串:

``` lisp
"~%;; ~{ ~<~%;; ~1:; ~S~>~^ ,~} .~%"
```

可用于不在行边界上折断项下打印由逗号分隔项的列表, 每行以`;;`开始. `~1:;`中的前缀参数1是被对齐的元素不是列表中最后一个元素时后面出现的逗号的宽度, 或者是最后一个元素时句号的宽度. 如果`~:;`由第二个前缀参数, 则它被用作行的宽度, 因此覆盖了输出流的自然行宽度. 将前面的列子中行宽度设置为50, 可以用:

``` lisp
"~%;; ~{ ~<~%;; ~1,50:; ~S~>~^ ,~}  .~%"
```

如果没有提供第二个传递参数, 则`format`使用目的地输出流的行宽度. 如果不能确定这个航宽度(例如生成字符串结果时), `format`使用72作为行长度.

见[22.3.5.2 `~<`: 逻辑块](#22.3.5.2).

#### <span id="22.3.6.3">22.3.6.3</span> `~>`: 结束对齐

`~>`结束`~<`. 在其它地方使用它的后果是未定义的.

### <span id="22.3.7">22.3.7</span> FORMAT控制流操作

#### <span id="22.3.7.1">22.3.7.1</span> `~*`: 跳转到

忽略下一个`arg`. `~n*`忽略后面的`n`个传递参数.

`~:*`在传递参数列表中备份, 从而最后处理的传递参数可被再次处理. `~n:*`备份`n`个传递参数.

在构造`~{`中时, (任意方向上的)忽略是相对于被迭代处理的传递参数列表.

`~n@*`跳转到第`n`个`arg`, 0表示第一个; `n`默认为0, 所以`~@*`跳转到第一个`arg`. `~n@*`之后的指令将使用序列中从跳转到的参数开始的参数. 当在构造`~{`中时, 跳转时相对于被迭代处理的传递参数列表.

#### <span id="22.3.7.2">22.3.7.2</span> `~[`: 条件表达式

``` lisp
~[str0~;str1~;...~;strn~]
```

这是一组称为子句的控制字符串, 选择和使用其中的一个. 这些子句是用`~;`分隔的, 这个构造用`~]`结束. 例如:

``` lisp
"~[Siamese~;Manx~;Persian~] Cat"
```

选择第`arg`个子句, 这里的第一个子句是数0. 如果给定了一个前缀参数(`n[`), 则使用这个参数而不是传递参数. 如果`arg`在可选范围之外, 则不会选择子句且不会发出错误信号. 在处理了选择的子句之后, 继续处理在`~]`之后的控制字符串.

`~[str0~;str1~;...~;strn~:;default~]`有一个默认子句. 如果最后一个用于分隔子句的是`~:;`, 则最后一个是没有选择其它子句可选择时选择的子句. 例如:

``` lisp
"~[Siamese~;Manx~;Persian~:;Alley~] Cat"
```

`~:[alternative~;consequent~]`在`arg`为false时选择控制字符串`alternative`, 否则选择`consequent`.

`~@[consequent~]`测试传递参数. 如果传递参数是true, 则它不被`~[`命名使用, 而是作为下一个被处理的传递参数, 子句`consequent`被处理. 如果`arg`是false, 则它被使用, 子句不被处理. 所以子句通常应该使用一个传递参数, 并期望它是非`nil`的. 例如:

``` lisp
(setq *print-level* nil *print-length* 5)
(format nil
       "~@[ print level = ~D~]~@[ print length = ~D~]"
       *print-level* *print-length*)
=>   " print length = 5"
```

同时注意到有:

``` lisp
(format stream "...~@[str~]..." ...)
==  (format stream "...~:[~;~:*str~]..." ...)
```

可以组合使用`~[`和`#`, 例如用于以英语习惯处理打印列表时:

``` lisp
(setq foo "Items:~#[ none~; ~S~; ~S and ~S~
          ~:;~@{~#[~; and~] ~S~^ ,~}~].")
(format nil foo) =>   "Items: none."
(format nil foo 'foo) =>   "Items: FOO."
(format nil foo 'foo 'bar) =>   "Items: FOO and BAR."
(format nil foo 'foo 'bar 'baz) =>   "Items: FOO, BAR, and BAZ."
(format nil foo 'foo 'bar 'baz 'quux) =>   "Items: FOO, BAR, BAZ, and QUUX."
```

#### <span id="22.3.7.3">22.3.7.3</span> `~]`: 结束条件表达式

`~]`结束`~[`. 在其它地方使用它的后果是未定义的.

#### <span id="22.3.7.4">22.3.7.4</span> `~{`: 迭代

``` lisp
~{str~}
```

这是一个迭代构造. 传递参数应该是一个列表, 就像递归调用`format`的一组传递参数. 字符串`str`作为控制字符串被重复使用. 每个迭代吸收列表中的元素作为传递参数; 如果`str`自身使用了两个传递参数, 则在循环中每次会使用列表中的两个元素. 如果在任意迭代步骤前列表是空的, 则迭代终止. 同时, 如果给定了前缀参数`n`, 则最多会处理`str` `n`次. 最后, 可以使用指令`~^`提前结束迭代.

例如:

``` lisp
(format nil "The winners are:~{ ~S~}."
        '(fred harry jill))
=>  "The winners are: FRED HARRY JILL."

(format nil "Pairs:~{ <~S,~S>~}."
        '(a 1 b 2 c 3))
=>  "Pairs: <A,1> <B,2> <C,3>."
```

`~:{str~}`是类似的, 但传递参数应该是子列表的列表. 在每个重复步骤中, 使用一个子列表作为处理`str`的一组传递参数; 在下一个重复步骤中, 使用新的子列表, 不管最后一个子列表是否已被处理. 例如:

``` lisp
(format nil "Pairs:~:{ <~S,~S>~} ."
                '((a 1) (b 2) (c 3)))
=>  "Pairs: <A,1> <B,2> <C,3>."
```

`~@{str~}`与`~{str~}`类似, 但不是使用一个列表传递参数, 而是将所有剩下的传递参数作为迭代的传递参数列表使用. 例如:

``` lisp
(format nil "Pairs:~@{ <~S,~S>~} ." 'a 1 'b 2 'c 3)
=>  "Pairs: <A,1> <B,2> <C,3>."
```

如果迭代在消费所有剩余参数之前终止, 则任何未被迭代处理的传递参数被保留给迭代构造后面的指令处理.

`~:@{str~}`组合了`~:{str~}`和`~@{str~}`的特性. 使用了所有的剩余传递参数, 且每个传递参数必须是一个列表. 在每个迭代中, 下一个参数被用作`str`的传递参数列表. 例如:

``` lisp
(format nil "Pairs:~:@{ <~S,~S>~} ."
             '(a 1) '(b 2) '(c 3))
=>  "Pairs: <A,1> <B,2> <C,3>."
```

使用`~:}`而不是`~}`结束迭代构造会强制至少处理一次`str`, 甚至在处理传递参数列表是空时. 然而, 这不会覆盖显式前缀参数为零的情况.

如果`str`为空, 则一个传递参数作为`str`使用. 它必须是一个格式控制, 并且在任何迭代处理的传递参数之前出现. 例如有:

``` lisp
(apply #'format stream string arguments)
==  (format stream "~1{~:}" string arguments)
```

这会将`string`作为格式字符串使用. `~1{`说明它之多被处理一次, `~:}`说明它将被至少处理一次. 所以它会被使用`arguments`作为传递参数处理一次. 这种情况使用指令`~?`可能更侵袭, 但`~{`的特性比`~?`更强.

#### <span id="22.3.7.5">22.3.7.5</span> `~}`: 结束迭代

`~}`结束`~{`. 在其它地方使用它的后果是未定义的.

#### <span id="22.3.7.6">22.3.7.6</span> `~?`: 递归处理

下一个`arg`必须是一个格式控制, 它后面是一个列表; 都被指令`~?`消费. 这两个被作为`control-string`处理, 将列表中元素作为传递参数. 一旦递归处理已经完成, 恢复包含指令`~?`的控制字符串的处理. 例如:

``` lisp
(format nil "~? ~D" "<~A ~D>" '("Foo" 5) 7) =>  "<Foo 5> 7"
(format nil "~? ~D" "<~A ~D>" '("Foo" 5 14) 7) =>  "<Foo 5> 7"
```

注意, 在第二个示例中给格式化字符串`"<~A ~D>"`提供了三个传递参数, 但只处理了两个, 第三个被忽略.

带修饰符`@`时, 只有一个`arg`被直接消费. 这个`arg`必须是一个字符串; 就像它在控制字符串的`~@?`构造处出现一样处理, 递归处理的控制字符串中的任何指令可以消费包含指令`~@?`的控制字符串的传递参数. 例如:

``` lisp
(format nil "~@? ~D" "<~A ~D>" "Foo" 5 7) =>  "<Foo 5> 7"
(format nil "~@? ~D" "<~A ~D>" "Foo" 5 14 7) =>  "<Foo 5> 14"
```

### <span id="22.3.8">22.3.8</span> FORMAT杂项操作

#### <span id="22.3.8.1">22.3.8.1</span> `~(`: 大小写转换

``` lisp
~(str~)
```

被包含的控制字符串`str`被处理, 它生成的内容会被大小写转换处理.

不带修饰符标志时, 每个大写字符被转换为相应的小写字符.

`~:(`将所有单词的首字母转换为大写, 就像使用了`string-capitalize`.

`~@(`只将第一个单词的首字母转换为大写, 将后续的单词转换为小写.

`~:@(`将每个小写字符转换为相应的大写字符.

在下面的示例中, 使用`~@(`将`~@R`生成的首个单词的首字母大写:

``` lisp
(format nil "~@R ~(~@R~)" 14 14)
=>  "XIV xiv"

(defun f (n) (format nil "~@(~R~) error~:P detected." n)) =>  F
(f 0) =>  "Zero errors detected."
(f 1) =>  "One error detected."
(f 23) =>  "Twenty-three errors detected."
```

当出现嵌套的大小写转换时, 最外层的转换起支配作用, 例如:

``` lisp
(format nil "~@(how is ~:(BOB SMITH~)?~)")
=>  "How is bob smith?"
NOT=>  "How is Bob Smith?"
```

#### <span id="22.3.8.2">22.3.8.2</span> `~)`: 结束大小写转换

`~)`结束`~(`. 在其它地方使用它的后果是未定义的.

#### <span id="22.3.8.3">22.3.8.3</span> `~P`: 复数(Plural)

如果`arg`不与整数1在`eql`下相等, 则打印出一个小写的`s`; 如果相等则不会打印内容. 如果`arg`是浮点数1.0, 则打印出`s`.

`~:P`在使用`~:*`备份一个传递参数后做同样的事情, 即如果前一个传递参数不是1时它打印出一个小写的`s`.

`~@P`在传递参数是1时打印出`y`, 不是时打印出`ies`. `~:@P`做同样的事情, 但会先备份一个传递参数.

``` lisp
(format nil "~D tr~:@P/~D win~:P" 7 1) =>  "7 tries/1 win"
(format nil "~D tr~:@P/~D win~:P" 1 0) =>  "1 try/0 wins"
(format nil "~D tr~:@P/~D win~:P" 1 3) =>  "1 try/3 wins"
```

### <span id="22.3.9">22.3.9</span> FORMAT杂项伪操作

#### <span id="22.3.9.1">22.3.9.1</span> `~;`: 子句分隔

这会在`~[`和`~<`构造中分隔子句. 在其它地方使用它的后果是未定义的.

#### <span id="22.3.9.2">22.3.9.2</span> `~^`: 向上转义

``` lisp
~^
```

这是一个转义构造. 如果没有剩余传递参数需要处理, 则立即包含它的`~{`或`~<`构造终止. 如果外部没有这样的构造, 则这个格式化操作终止. 在`~<`情况中, 执行格式化, 但在对齐之前不处理其它片段. `~^`可以在`~{`构造中的任何位置出现.

``` lisp
(setq donestr "Done.~^ ~D warning~:P.~^ ~D error~:P.")
=>  "Done.~^ ~D warning~:P.~^ ~D error~:P."
(format nil donestr) =>  "Done."
(format nil donestr 3) =>  "Done. 3 warnings."
(format nil donestr 1 5) =>  "Done. 1 warning. 5 errors."
```

如果给定了前缀参数, 且参数为零则立即终止. (因此`~^`等价于`~#^`). 如果给定了两个参数, 且它们相等时立即终止. 如果给定了三个参数, 且第一个小于等于第二个, 且第二个小于等于第三个时, 立即终止. 当然, 这在所有前缀参数是常量时没什么用; 至少有一个参数是`#`或`V`参数.

如果在`~:{`构造中使用`~^`, 因为在标准情形下它只会测试当前步骤的剩余传递参数, 它会结束当前迭代步骤; 立即开始下一个迭代步骤. `~:^`用于结束迭代处理. `~:^`只在它将结束的命令是`~:{`或`~:@{`时使用. 当且仅当为当前迭代步骤提供传递参数的子列表是`~:{`中的最后一个子列表或`~:@{`中的最后一个格式化传递参数时, 结束整个迭代处理. `~:^`不等价于`~#:^`; 当且仅当没有用于当前迭代步骤的剩余传递参数时, 后者终止整个迭代. 例如:

``` lisp
(format nil "~:{ ~@?~:^ ...~} " '(("a") ("b"))) =>  "a...b"
```

如果`~^`出现在指令`~?`控制下处理的控制字符串中, 但不在这个字符串的任何`~{`或`~<`构造中出现, 则被处理的字符串会终止, 从而结束处理指令`~?`. 在包含指令`~?`的字符串中继续处理它后面的内容.

如果`~^`出现在`~[`或`~(`构造中, 则所有到`~^`的命令被恰当的选择或大小写转换, 结束处理`~[`或`~(`, `~{`或`~<`构造的外向搜索终止. 例如:

``` lisp
(setq tellstr "~@(~@[~R~]~^ ~A!~)")
=>  "~@(~@[~R~]~^ ~A!~)"
(format nil tellstr 23) =>  "Twenty-three!"
(format nil tellstr nil "losers") =>  " Losers!"
(format nil tellstr 23 "losers") =>  "Twenty-three losers!"
```

下面是在`~<`构造中使用`~^`的示例:

``` lisp
(format nil "~15<~S~;~^~S~;~^~S~>" 'foo)
=>   "            FOO"
(format nil "~15<~S~;~^~S~;~^~S~>" 'foo 'bar)
=>   "FOO         BAR"
(format nil "~15<~S~;~^~S~;~^~S~>" 'foo 'bar 'baz)
=>   "FOO   BAR   BAZ"
```

#### <span id="22.3.9.3">22.3.9.3</span> `~%`: 忽略的换行

紧跟在换行后的变音符(`~`)会略换行和任何后续的非换行的空白字符. 带修饰符`:`时, 忽略换行, 但保留后续的空白符. 带修饰符`@`是, 保留换行, 但忽略后续的空白符. 例如:

``` lisp
(defun type-clash-error (fn nargs argnum right-type wrong-type)
  (format *error-output*
          "~&~S requires its ~:[~:R~;~*~]~
          argument to be of type ~S,~%but it was called ~
          with an argument of type ~S.~%"
          fn (eql nargs 1) argnum right-type wrong-type))

(type-clash-error 'aref nil 2 'integer 'vector)  prints:
AREF requires its second argument to be of type INTEGER,
but it was called with an argument of type VECTOR.
NIL

(type-clash-error 'car 1 1 'list 'short-float)  prints:
CAR requires its argument to be of type LIST,
but it was called with an argument of type SHORT-FLOAT.
NIL
```

注意到这个示例中, 只出现指令`~&`和`~%`指定的换行; 控制字符串中的实际换行被抑制, 因为行末有`~`.

### <span id="22.3.10">22.3.10</span> FORMAT操作的额外信息

#### <span id="22.3.10.1">22.3.10.1</span> FORMAT操作的嵌套

大小写转换、条件、迭代和对齐构造可以使用括弧包含其它格式构造. 这些构造必须互相恰当的嵌套. 例如, 将大小写转换构造的开始放在条件构造的分支中, 而大小写转换的构造结尾放在条件构造的外部是非法的:

``` lisp
(format nil "~:[abc~:@(def~;ghi~
:@(jkl~]mno~)" x) ;Invalid!
```

因为`~[...~;...~]`和`~(...~)`构造没有恰当的嵌套, 这个记法是无效的.

由指令`~?`产生的间接处理也是一种这种恰当嵌套规则需要考虑的嵌套. 不允许在指令`~?`控制下处理的字符串中开始一个括弧构造, 且在包含该构造的字符串中`~?`后面结束这个构造, 或者反之. 例如, 这种情况是无效的:

``` lisp
(format nil "~@?ghi~)" "abc~@(def") ;Invalid!
```

因为`~?`和`~(...~)`构造没有恰当的嵌套, 这个记法是无效的.

#### <span id="22.3.10.2">22.3.10.2</span> FORMAT的缺失和额外传递参数

如果在指令需要一个传递参数时没有剩余的`arg`, 后果是未定义的. 然而, 允许有一个或多个未被指令处理的`args`; 这些`args`被忽略.

#### <span id="22.3.10.3">22.3.10.3</span> 额外的FORMAT参数

如果给格式化指令提供了这里描述为可接受的参数之外的参数, 后果是未定义的.

#### <span id="22.3.10.4">22.3.10.4</span> 未定义的FORAMT修饰符组合

如果个格式化指令提供了这里没有描述为有意义的修饰符`:`或`@`, 后果是未定义的.

### <span id="22.3.11">22.3.11</span> 示例: FORMAT

``` lisp
(format nil "foo") =>  "foo"
(setq x 5) =>  5
(format nil "The answer is ~D." x) =>  "The answer is 5."
(format nil "The answer is ~3D." x) =>  "The answer is   5."
(format nil "The answer is ~3,'0D." x) =>  "The answer is 005."
(format nil "The answer is ~:D." (expt 47 x))
=>  "The answer is 229,345,007."
(setq y "elephant") =>  "elephant"
(format nil "Look at the ~A!" y) =>  "Look at the elephant!"
(setq n 3) =>  3
(format nil "~D item~:P found." n) =>  "3 items found."
(format nil "~R dog~:[s are~; is~] here." n (= n 1))
=>  "three dogs are here."
(format nil "~R dog~:*~[s are~; is~:;s are~] here." n)
=>  "three dogs are here."
(format nil "Here ~[are~;is~:;are~] ~:*~R pupp~:@P." n)
=>  "Here are three puppies."
```

``` lisp
(defun foo (x)
  (format nil "~6,2F|~6,2,1,'*F|~6,2,,'?F|~6F|~,2F|~F"
          x x x x x x)) =>  FOO
(foo 3.14159)  =>  "  3.14| 31.42|  3.14|3.1416|3.14|3.14159"
(foo -3.14159) =>  " -3.14|-31.42| -3.14|-3.142|-3.14|-3.14159"
(foo 100.0)    =>  "100.00|******|100.00| 100.0|100.00|100.0"
(foo 1234.0)   =>  "1234.00|******|??????|1234.0|1234.00|1234.0"
(foo 0.006)    =>  "  0.01|  0.06|  0.01| 0.006|0.01|0.006"
```

``` lisp
(defun foo (x)
   (format nil
          "~9,2,1,,'*E|~10,3,2,2,'?,,'$E|~
           ~9,3,2,-2,'%@E|~9,2E"
          x x x x))
(foo 3.14159)  =>  "  3.14E+0| 31.42$-01|+.003E+03|  3.14E+0"
(foo -3.14159) =>  " -3.14E+0|-31.42$-01|-.003E+03| -3.14E+0"
(foo 1100.0)   =>  "  1.10E+3| 11.00$+02|+.001E+06|  1.10E+3"
(foo 1100.0L0) =>  "  1.10L+3| 11.00$+02|+.001L+06|  1.10L+3"
(foo 1.1E13)   =>  "*********| 11.00$+12|+.001E+16| 1.10E+13"
(foo 1.1L120)  =>  "*********|??????????|%%%%%%%%%|1.10L+120"
(foo 1.1L1200) =>  "*********|??????????|%%%%%%%%%|1.10L+1200"
```

作为多种缩放因子的效果的实力, 下面的代码:

``` lisp
(dotimes (k 13)
  (format t "~%Scale factor ~2D: |~13,6,2,VE|"
          (- k 5) (- k 5) 3.14159))
```

生成下面的输出:

``` lisp
Scale factor -5: | 0.000003E+06|
Scale factor -4: | 0.000031E+05|
Scale factor -3: | 0.000314E+04|
Scale factor -2: | 0.003142E+03|
Scale factor -1: | 0.031416E+02|
Scale factor  0: | 0.314159E+01|
Scale factor  1: | 3.141590E+00|
Scale factor  2: | 31.41590E-01|
Scale factor  3: | 314.1590E-02|
Scale factor  4: | 3141.590E-03|
Scale factor  5: | 31415.90E-04|
Scale factor  6: | 314159.0E-05|
Scale factor  7: | 3141590.E-06|
```

``` lisp
(defun foo (x)
  (format nil "~9,2,1,,'*G|~9,3,2,3,'?,,'$G|~9,3,2,0,'%G|~9,2G"
         x x x x))
(foo 0.0314159) =>  "  3.14E-2|314.2$-04|0.314E-01|  3.14E-2"
(foo 0.314159)  =>  "  0.31   |0.314    |0.314    | 0.31    "
(foo 3.14159)   =>  "   3.1   | 3.14    | 3.14    |  3.1    "
(foo 31.4159)   =>  "   31.   | 31.4    | 31.4    |  31.    "
(foo 314.159)   =>  "  3.14E+2| 314.    | 314.    |  3.14E+2"
(foo 3141.59)   =>  "  3.14E+3|314.2$+01|0.314E+04|  3.14E+3"
(foo 3141.59L0) =>  "  3.14L+3|314.2$+01|0.314L+04|  3.14L+3"
(foo 3.14E12)   =>  "*********|314.0$+10|0.314E+13| 3.14E+12"
(foo 3.14L120)  =>  "*********|?????????|%%%%%%%%%|3.14L+120"
(foo 3.14L1200) =>  "*********|?????????|%%%%%%%%%|3.14L+1200"
```

``` lisp
(format nil "~10<foo~;bar~>")   =>  "foo    bar"
(format nil "~10:<foo~;bar~>")  =>  "  foo  bar"
(format nil "~10<foobar~>")     =>  "    foobar"
(format nil "~10:<foobar~>")    =>  "    foobar"
(format nil "~10:@<foo~;bar~>") =>  "  foo bar "
(format nil "~10@<foobar~>")    =>  "foobar    "
(format nil "~10:@<foobar~>")   =>  "  foobar  "
```

``` lisp
(FORMAT NIL "Written to ~A." #P"foo.bin")
=>  "Written to foo.bin."
```

### <span id="22.3.12">22.3.12</span> 备注: FORMAT

格式化的输出不仅仅由`format`生成, 也可以通过其它与`format`一样接受格式化控制的其它函数生成. 例如, 诸如`cerror`的错误发出函数接受格式化控制.

注意作为`format`目的地的`nil`和`t`的含义与它们作为流设计器的含义不同.

因为`~^`中断它所在的整个子句(以及后面的子句), 它应该只在`~<`子句的开始处出现.

## <span id="22.4">22.4</span> 打印器的字典

见[打印器的字典](../Dictionary#22.4).
