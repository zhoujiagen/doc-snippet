# 3. 求值和编译

[TOC]

## <span id="3.1">3.1</span> 求值(Evaluation)

代码的执行可以通过多种方式完成, 包括从解释表示程序的形式到调用编译器生成的已编译的代码.

求值(Evaluation)是表示在Common Lisp中程序执行的过程.
求值的机制包括隐式的使用Lisp REPL的作用, 显式的使用函数`eval`、`compile`, `compile-file`和`load`.
这些方法可能共享相同的执行策略, 或者每个有不同的策略.

由`eval`和`compile-file`处理的复合标准的程序的行为可能存在差异, 见[3.2.2.3 语义约束](#3.2.2.3).

求值可以被理解为是一个模型, 其中解释器递归的遍历一个形式, 在遍历过程中执行计算的每一步. 这个描述Common Lisp程序的语义的模型, 在[3.1.2 求值模型](#3.1.2)中描述.

### <span id="3.1.1">3.1.1</span> 环境的介绍

==绑定== 是名称与其指示物之间的关联. 特定特殊操作符在词法环境或动态环境中建立绑定.

==环境== 是一组绑定和其他在求值期间使用的信息(例如: 关联名称和它的含义).

环境中的绑定按名称空间划分. 同一个名称可以在每个环境中同时有多于一个的绑定, 但在每个命名空间中只可以有一个绑定.

#### <span id="3.1.1.1">3.1.1.1</span> 全局环境

==全局环境== 是包含无限期作用域和extent的绑定的环境. 包括:

- 动态变量和常值变量的绑定
- 函数、宏和特殊操作符的绑定
- 编译器宏的绑定
- 类型和类名称的绑定
- 公告(Proclamation)相关的信息.

#### <span id="3.1.1.2">3.1.1.2</span> 动态环境

求值的 ==动态环境== 是包含这样的绑定的环境: 在建立绑定的形式的执行中, 绑定时间范围在建立时刻和解建立时刻中.

动态环境包括:

- 动态变量的绑定信息
- 活跃捕获标记的信息
- `unwind-protect`建立的退出点的信息
- 活跃处理器和重启器的信息

在程序执行的任一时间点, 活跃的动态环境被称为 **当前动态环境**.

在给定名称空间中, 称名称是已绑定的(bound), 如果:

- 当前动态环境中有该名称的绑定, 或者, 没有时,
- 在全局环境中有该名称的绑定.

#### <span id="3.1.1.3">3.1.1.3</span> 词法环境

在程序中某一位置求值的 ==词法环境== 是这样的环境, 在包含这一位置的形式中有词法作用域的信息.

词法环境包括:

- 词法变量和符号宏的绑定
- 函数和宏的绑定(包括局部禁止的编译器宏)
- 块标记绑定
- 声明(Declaration)的信息

在程序中任一位置活跃的词法环境, 被称为当前词法环境.

在给定名称红键中, 称名称在词法环境中是已绑定的, 如果:

- 在当前词法环境中有该名称的绑定, 或者, 没有时,
- 在全局环境中有该名称的绑定.

##### <span id="3.1.1.3.1">3.1.1.3.1</span> 空词法环境

==空词法环境== 等价于全局环境.

尽管通常环境对象的表示是依赖于实现的, `nil`可用于需要环境对象的地方, 以表示空词法环境.

#### <span id="3.1.1.4">3.1.1.4</span> 环境对象

一些操作符使用称为 ==环境对象== 的对象, 环境对象表示: 为在给定词法环境中的一个形式上执行语义分析所需的一组词法绑定.
环境对象中的一组绑定可能是实际执行求值所需的绑定集合的子集; 例如, 在相应词法环境中与变量名称和函数名称相关的值, 可能在环境对象中不可用.

环境对象的类型和本质是依赖于实现的. 宏函数的环境参数的值是环境对象的一个例子.

对象`nil`用于环境对象时, 表示空词法环境, 见[3.1.1.3.1 空词法环境](#3.1.1.3.1).

### <span id="3.1.2">3.1.2</span> 求值模型

Common Lisp系统求值形式时, 需要考虑词法、动态和全局环境. 下面的章节描述Common Lisp求值模型.

#### <span id="3.1.2.1">3.1.2.1</span> 形式求值

==形式== 分为三类: 符号, cons和自求值对象.

##### <span id="3.1.2.1.1">3.1.2.1.1</span> 符号作为形式

如果一个形式是 ==符号==, 则它是 **符号宏** 或者 **变量**.

如果符号命名了一个 **符号宏**, 则在当前词法环境中, 存在这个符号到符号宏的绑定(见`define-symbol-macro`和`symbol-macrolet`).
如果这个符号是个符号宏, 则获取它的展开函数.
展开函数是两个参数的函数, 并在调用 **宏展开钩子** 时被调用, 展开函数作为第一个参数, 符号作为第二个参数, 环境对象(当前词法环境)作为第三个参数.
宏展开钩子调用展开函数, 该形式作为第一个参数, 环境作为第二个参数.
由宏展开钩子返回的展开函数的值, 是个形式.
结果形式在原始符号的位置被处理.

如果形式是不是符号宏的符号, 则它是 **变量** 的名称, 返回该变量的值.
存在三种变量: **词法变量** 、**动态变量** 和 **常值变量**.
变量可以保存一个对象. 变量上的主要操作是读和写它的值.

如果引用一个 **未绑定变量**, 则发出类型为`unbound-variable`的错误信号.

非常值变量可以通过使用`setq`赋值或使用`let`绑定. 下图列出了一下可用于赋值、绑定和定义变量的已定义名称。

<span id="Figure3-1">图 3-1. 一些可用于变量的已定义名称.</span>

``` lisp
boundp
defconstant
defparameter
defvar
lambda
let
let*
makunbound
multiple-value-bind
multiple-value-setq
progv
psetq
set
setq
symbol-value
```

###### <span id="3.1.2.1.1.1">3.1.2.1.1.1</span> 词法变量

==词法变量== 是只能在建立该变量的形式的词法作用域中引用的变量.
词法变量有词法作用域.
每当形式创建变量的词法绑定时, 建立一个新的绑定.

在一个词法变量名称绑定的作用域中, 将该名称作为变量使用视为对该绑定的引用, 除了被一个形式建立该变量名称的新绑定时该变量被遮盖, 或者一个形式中局部声明该变量为`special`.

一个词法变量总是有值. 不存在不带初始值引入词法变量绑定的操作符, 也不存在将词法变量置为未绑定的操作符.

词法变量的绑定可以在词法环境中找到.

###### <span id="3.1.2.1.1.2">3.1.2.1.1.2</span> 动态变量

如果下面的一个条件时, 一个变量被称为 ==动态变量==:

- 被局部或全局的公告为`special`
- 文本的出现在创建该名称的变量的动态绑定的形式中, 该绑定没有被一个创建相同变量名的词法绑定的形式遮盖.

在任意程序中, 可以在任意时刻引用动态变量, 不存在引用动态变量的文本限制.
在任意时刻, 给定名称的所有动态变量引用一个绑定, 在动态环境或全局环境中.

动态变量绑定的值部分可能为空, 在这种情况下, 动态变量被称为无值的或未绑定的.
使用`makeunbound`将一个动态变量置为未绑定的.

绑定动态变量的效果是, 为在求值创建该动态绑定的形式期间, 创建新的程序中所有对该动态变量引用的绑定.

动态变量可以在绑定它的形式的动态extent之外引用. 这种变量有时被称为 **全局变量**, 但它仍是一个动态变量, 它的绑定存在于全局环境而不是某个动态环境.

一个动态变量是未绑定的, 直到显式的给它赋值, 除了那些在该规范中或实现中定义了初始值的变量.

###### <span id="3.1.2.1.1.3">3.1.2.1.1.3</span> 常值变量

==常值变量== 又称命名的常量. 尝试给它赋值或创建绑定的后果是未定义的, 除了使用`defconstant`兼容的重定义一个常值变量.

Common Lisp或实现中定义的关键字和符号是常量(例如: `nil`, `t`或`pi`), 使用`defconstant`声明的符号是常值变量.

###### <span id="3.1.2.1.1.4">3.1.2.1.1.4</span> 命名词法和动态变量的符号

同一符号可以同时命名词法变量和动态变量, 但不能在同一词法环境中.

在下面的示例中, 使用符号`x`, 作为词法变量的名称和作为动态变量的名称.

``` lisp
(let ((x 1))            ;Binds a special variable X
  (declare (special x)) ; 声明为特殊变量
  (let ((x 2))          ;Binds a lexical variable X
    (+ x                ;Reads a lexical variable X
       (locally (declare (special x)) ; 局部声明为特殊变量
                x))))   ;Reads a special variable X
=>  3
```

##### <span id="3.1.2.1.2">3.1.2.1.2</span> cons作为形式

用作形式的cons被称为 ==复合形式==.

如果复合形式的car部分是一个符号, 该符号是一个操作符的名称, 依赖于在当前词法环境中操作符的函数绑定, 这个形式可以是 **特殊形式**、**宏形式** 或 **函数形式**.
如果该操作符不是特殊操作符或宏名称, 则被认为是函数名称(甚至不存在该函数定义时).

如果复合形式的car部分不是一个符号, 则该car必须是一个lambda表达式, 这时这个复合形式是 **lambda形式**.

复合形式是如何处理的, 依赖于它是特殊形式、宏形式、函数形式, 还是lambda形式.

###### <span id="3.1.2.1.2.1">3.1.2.1.2.1</span> 特殊形式(Special Forms)

==特殊形式== 是有特殊语法或/和特殊求值规则的形式, 可能操作求值环境和/或控制流.
特殊操作符可以访问当前词法环境和当前动态环境.
每个特殊操作符定义了它的子表达式的行为, 哪些是形式, 哪些是特殊语法等等.

一些特殊操作符, 在求值特殊形式的子形式时, 创建新的词法或动态环境.
例如, `block`创建一个新的词法环境, 它与求值`block`形式时的词法环境相同, 此外创建了将块名称绑定到从该块中退出的退出点的绑定.

Common Lisp中特殊操作符的名称是固定的, 用户无法定义特殊操作符. 下图列出了Common Lisp中被定义为特殊操作符的符号.

<span id="Figure3-2">图 3-2. Common Lisp特殊操作符.</span>

``` lisp
block
catch
eval-when
flet
function
go
if
labels
let
let*
load-time-value
locally
macrolet
multiple-value-call
multiple-value-prog1
progn
progv
quote
return-from
setq
symbol-macrolet
tagbody
the
throw
unwind-protect
```

###### <span id="3.1.2.1.2.2">3.1.2.1.2.2</span> 宏形式(Macro Forms)

(复合形式中, )如果操作符是一个宏的名称, 其关联的宏函数被应用在整个形式上, 应用的结果用来替换原始的形式.

如果在给定词法环境中, 以一个符号和该环境作为参数调用`macro-function`结果为true, 则该符号是一个宏的名称.
`macro-function`返回的函数是有两个参数的函数, 称为 **展开函数**. 宏展开钩子的第一个参数是展开函数, 第二个参数是整个宏形式, 第三个参数是(当前词法环境相关的)环境对象. 宏展开钩子接着调用展开函数, 使用形式作为第一个参数, 使用环境作为第二个参数. 展开函数的值是一个形式. 返回的形式在原始形式处被求值.

如果一个宏函数破坏性的修改了其宏参数的任意部分, 后果是未定义的.

宏名称不是函数指示器, 不能作为函数参数用于`apply`、`funcall`、`map`等.

实现可以自由的将Common Lisp特殊操作符作为宏实现.
实现可以自由的将宏操作符作为特殊操作符实现, 需要提供宏的等价定义.

下图列出了可用于宏的已定义名称:

<span id="Figure3-3">图 3-3. 可用于宏的已定义名称.</span>

``` lisp
*macroexpand-hook*
defmacro
macro-function
macroexpand
macroexpand-1
macrolet
```

###### <span id="3.1.2.1.2.3">3.1.2.1.2.3</span> 函数形式(Function Forms)

如果操作符是命名了函数的符号, 该形式表示 ==函数形式==, 包含该形式的列表的`cdr`将作为参数传递给该函数.

运行时, 当函数名称未定义时, 抛出一个类型为`undefined-function`的错误异常, 见[3.2.2.3 语义约束](#3.2.2.3).

函数形式按如下方式求值:

- 原始形式中的`cdr`中的子形式按从左至右的方式, 在当前词法和动态环境中求值. 这些求值的主值作为该命名的函数的参数, 子形式其他返回值被忽略
- 从词法环境中检索出操作符的函数值, 按上一步求值出的参数调用该函数

尽管参数子形式的求值顺序是严格从左至右的, 但未描述函数形式中操作符是在参数执行时求值之前、时或者之后查找. 例如, 下面可能返回23或24:

``` lisp
(defun foo (x) (+ x 3))
(defun bar () (setf (symbol-function 'foo) #'(lambda (x) (+ x 4))))
(foo (progn (bar) 20))
```

函数名称的绑定可以用一些方法建立.
全局环境中函数名称的绑定, 可以用`defun`、`dfefinition`上`setf`、`symbol-function`上`setf`、`ensure-generic-function`、`defmethod`(隐式的使用`ensure-generic-function`)、`defgeneric`.
词法环境中函数名称的绑定, 可以用`flet`和`labels`.

下图列出了一些函数相关的已定义名称:

<span id="Figure3-4">图 3-4. 一些函数相关的已定义名称.</span>

``` lisp
apply
call-arguments-limit
complement
constantly
defgeneric
defmethod
defun
fboundp
fdefinition
flet
fmakunbound
funcall
function
functionp
labels
map
mapcan
mapcar
mapcon
mapl
maplist
multiple-value-call
reduce
symbol-function
```


###### <span id="3.1.2.1.2.4">3.1.2.1.2.4</span> lambda形式(Lambda Forms)

lambda形式与函数形式类似, 除了用lambda表达式替代了函数名称.

lambda形式等价于在给定参数上使用`funcall`调用lambda表达式的词法闭包.

更多信息, 见[3.1.3 lambda表达式](#3.1.3).

##### <span id="3.1.2.1.3">3.1.2.1.3</span> 自求值对象

不是符号或cons的形式被定义为 ==自求值对象==. 求值这里对象的结果是该对象自身.

一些特定的符号和cons可能恰好是自求值的, 但作为符号和cons求值规则的特例, 不被视为自求值对象.

破坏性修改字面量对象(包括自求值对象)的后果是未定义的.

###### <span id="3.1.2.1.3.1">3.1.2.1.3.1</span> 示例: 自求值对象

数值、路径名和数组是自求值对象:

``` lisp
3 =>  3
#c(2/3 5/8) =>  #C(2/3 5/8)

#p"S:[BILL]OTHELLO.TXT" =>  #P"S:[BILL]OTHELLO.TXT"

#(a b c) =>  #(A B C)
"fred smith" =>  "fred smith"
```

### <span id="3.1.3">3.1.3</span> lambda表达式

在lambda表达式中, 表达式体在这样的词法环境中求值: 在当前词法环境中添加lambda列表中每个参数与实际参数值的绑定.

绑定是如何基于lambda列表建立的, 见[3.4 lambda列表](#3.4).

lambda表达式的体是一个隐式的`progn`, 它返回的值作为lambda表达式的返回值.

### <span id="3.1.4">3.1.4</span> 闭包(Closures)和词法绑定(Lexical Binding)

词法闭包是一个函数, 这个函数可以引用和修改, 文本包含函数定义的绑定形式建立的词法绑定的值.

考虑下面的代码, `x`未被声明为`special`:

``` lisp
(defun two-funs (x)
  (list (function (lambda () x))
        (function (lambda (y) (setq x y)))))

(setq funs (two-funs 6))
(funcall (car funs)) =>  6
(funcall (cadr funs) 43) =>  43
(funcall (car funs)) =>  43
```

`function`特殊形式将lambda表达式封入一个闭包中, 捕获lamabda表达式的同事, 也捕获特殊形式求值时的词法环境.

函数`two-funs`返回元素为两个函数的列表, 每个引用了调用`tow-funs`时创建的变量`x`的绑定. 这个变量初始值为6, `setq`可以修改这个绑定.
第一个lambda表达式创建的闭包, 在闭包创建时, 没有快照`x`的值6, 它捕获了`x`的绑定.
第二个函数可用于修改同一捕获的绑定中的值(修改为43), 接着, 这个被修改的变量绑定影响了第一个函数的返回值.

在同一组绑定上的lambda表达式的闭包可能产生多次的情况下, 这些闭包可能也可能不同一的, 这依赖于实现.
即, 两个行为上不可区分的函数可能也可能不是同一的. 两个行为上可区分的函数是不同的. 例如:

``` lisp
(let ((x 5) (funs '()))
  (dotimes (j 10)
    (push #'(lambda (z)
              (if (null z) (setq x 0) (+ x z)))
          funs))
  funs)
```

上面这个形式的结果是元素为10个闭包的列表. 每个闭包只需要`x`的绑定. 在各个闭包中, 这是同一个绑定, 但这10个比好对象可能也可能不是同一的. 另一方面,

``` lisp
(let ((funs '()))
  (dotimes (j 10)
    (let ((x 5))
      (push (function (lambda (z)
                       (if (null z) (setq x 0) (+ x z))))
            funs)))
 funs)
```

也是元素为10个闭包的列表. 然而, 这这种情况中, 任意两个闭包对象不可是同意的, 因为每个闭包在不同的`x`绑定下封闭, 因使用了`(setq x 0)`这些绑定行为上是可区分的.

``` lisp
(let ((funs '()))
  (dotimes (j 10)
    (let ((x 5))
      (push (function (lambda (z) (+ x z)))
           funs)))
  funs)
```

上面形式的结果是元素为10个闭包对象的列表, 这些闭包对象可以也可以不是同一的. 每个闭包中包含不同的`x`绑定, 但因为这些绑定的值相同且不可变的(没有出现`(setq x ...)`), 是不可区分. 编译器可以内部将其转换为:

``` lisp
(let ((funs '()))
  (dotimes (j 10)
    (push (function (lambda (z) (+ 5 z)))
          funs))
 funs)
```

这里的闭包可能是同一的.

闭包也可能没有在任何变量绑定上封闭. 在下面的代码片段中:

``` lisp
 (mapcar (function (lambda (x) (+ x 2))) y)
```

函数`(lambda (x) (+ x 2))`不包含外部对象的引用. 在这种情况中, `function`形式的所有求值可能返回同一闭包.


### <span id="3.1.5">3.1.5</span> 遮盖(Shadowing)

如果两个使用同一名称`N`建立词法绑定的形式是文本上嵌套的, 在内层形式上对`N`的引用是对在内层形式上建立的绑定的引用; 内层对`N`的绑定 ==遮盖== 外层对`N`的绑定. 在内层形式之外外层形式之内, 对`N`的引用是对外层形式建立的绑定的引用. 例如:

``` lisp
(defun test (x z)
  (let ((z (* x 2)))
    (print z))
  z)
```

由`let`建立的变量`z`的绑定遮盖了函数`test`的参数绑定. `print`形式中引用的变量`z`是对`let`绑定的引用. 函数`test`末尾对`z`的引用是对参数`z`的引用.

词法作用域的构造, 表现为每次执行中为每个对象生成新的名称. 因此, 不会出现动态遮盖. 例如:

``` lisp
(defun contorted-example (f g x)
  (if (= x 0)
      (funcall f)
      (block here
         (+ 5 (contorted-example g
                                 #'(lambda () (return-from here 4))
                                 (- x 1))))))
```

考虑调用`(contorted-example nil nil 2)`, 结果为4. 在执行时, 有三次对`contorted-example`的调用, 有两个块:

``` lisp
(contorted-example nil nil 2)
  (block here1 ...)
    (contorted-example nil #'(lambda () (return-from here1 4)) 1)
      (block here2 ...)
        (contorted-example #'(lambda () (return-from here1 4))
                           #'(lambda () (return-from here2 4))
                           0)
            (funcall f)
                   where f =>  #'(lambda () (return-from here1 4))
                (return-from here1 4)
```

在`funcall`执行时, 有两个块退出点, 每个都有名称`here`. 作为`funcall`操作结果的`return-from`心事引用了外层的退出点`here1`, 而不是内层的`here2`. 它引用在执行函数时(这里的`#'`, 生成被`funcall`调用的函数对象)文本可见的退出点.

如果在这个例子中, 将`(funcall f)`修改为`(funcall g)`, 则`(contorted-example nil nil 2)`的结果为9. 这是因为`funcall`会执行`(return from here2 4)`, 从退出点`here2`返回. 值4从`contorted-example`中间返回, 加上5得到结果9, 这个值从外层块返回.

需要记住的是, 退出点的选择与初始时是最内层或最外层无关, 依赖于函数执行时lambda表达式包装的词法环境.

### <span id="3.1.6">3.1.6</span> 范围(Extent)

`contorted-example`可工作, 是因为在退出点范围内函数`f`被调用. ==一旦执行流离开块, 退出点被解除==. 例如:

``` lisp
(defun invalid-example ()
  (let ((y (block here #'(lambda (z) (return-from here z)))))
    (if (numberp y) y (funcall y 5))))
```

认为`invalid-example`返回5的错误推理如下: `let`将`y`绑定到`block`的值, 这个值是lambda表达式产生的函数. 因为`y`不是一个数值, 用5作为参数调用. `return-from`从退出点`here`返回5, 因此退出块, 赋予`y`值5, `y`称为数值, 作为`invalid-example`的结果返回.

上面的陈述是错误的原因是退出点有 ==动态范围==. 关于`return-from`执行的描述是错误的. `return-from`应该发出类型为`control-error`的错误信号, 不是因为它不能引用退出点, 而是因为它正确的引用了一个退出点, 但那个退出点已被解除.

``` lisp
>(defun invalid-example ()
  (let ((y (block here #'(lambda (z) (return-from here z)))))
    (if (numberp y) y (funcall y 5))))
INVALID-EXAMPLE
> (invalid-example)

debugger invoked on a SB-INT:SIMPLE-CONTROL-ERROR in thread
#<THREAD "main thread" RUNNING {10004F84C3}>:
  attempt to RETURN-FROM a block or GO to a tag that no longer exists
```


名称对动态退出点绑定的引用, 例如捕获标签, 引用 ==最近建立的还未解除的== 对改名称的绑定. 例如:

``` lisp
(defun fun1 (x)
  (catch 'trap (+ 3 (fun2 x))))

(defun fun2 (y)
  (catch 'trap (* 5 (fun3 y))))

(defun fun3 (z)
  (throw 'trap z))
```

考虑`(fun1 7)`, 结果为10. 在`throw`执行时, 有两个名为`trp`的捕获器: 一个在`fun1`中, 另一个在`fun2`中. 后者是最近的, 所以`fun2`中的`catch`返回7. 从`fun3`中看, `fun2`中的`catch`遮盖了`fun1`中的`catch`. 将`fun2`定义为:

``` lisp
(defun fun2 (y)
  (catch 'snare (* 5 (fun3 y))))
```

这样两个退出点有不同的名称, 从而`fun1`中的`catch`不会被遮盖, 结果为7.

### <span id="3.1.7">3.1.7</span> 返回值

通常调用一个函数的结果是一个对象. 有时, 函数计算并返回多个对象是很便利的.

为接收一个形式返回的多个值, 需要调用特殊形式或宏请求这些值. 如果一个形式产生多值, 但不用这种方式请求, 则返回给调用者第一个值, 忽略其他值; 如果一个形式产生零个值, 则返回给调用者`nil`.

下图列出了一些接收多值的操作符. 这些操作符可用于指定一个或多个需要求值的形式, 以及放置这些形式返回值的位置.

<span id="Figure3-5">图 3-5. 一些可用于接收多值的操作符.</span>


``` lisp
multiple-value-bind
multiple-value-call
multiple-value-list
multiple-value-prog1
multiple-value-setq
return
return-from
throw
```

函数`values`可产生多值:

- `(values)`返回零个值
- `(values form)`返回形式`form`返回的主值
- `(values form1 form2)`返回两个值, `form1`的主值和`form2`的主值
- 依次类推.

见[multiple-values-limit](../Symbols#multiple-values-limit)和[values-list](../Symbols##values-list).

## <span id="3.2">3.2</span> 编译(Compilation)

### <span id="3.2.1">3.2.1</span> 编译器术语

下面是这一节中使用的术语.

- compiler(编译器)

将代码转换为依赖于实现形式的工具, 该形式可被高效的表示或执行.
术语编译器包含函数`compile`和`compile-file`.

- compiled code(已编译的代码)

是表示已编译程序的对象, 例如由`compile`或通过`load`已编译文件构造的对象.

- implicit compilation(隐式编译)

表示在求值时执行的编译.

- literal object(字面量对象)

表示被引述的对象、自求值对象、或这种对象的子结构对象.
常值变量不是字面量对象.

- coalesce(合并)

假设`A`和`B`是源代码中连个字面常量, `A'`和`B'`是已编译代码中相应的对象.
如果`A'`和`B'`是`eql`的, 但`A`和`B`不是`eql`的, 则称`A`和`B`被编译器合并.

- minimal compilation(最小编译)

表示编译器在编译时必须执行的动作. 这些动作在[3.2.2 编译的语义](#3.2.2)中描述.

- process(处理)

表示执行最小编译、确定形式的求值时间以及(如果需要的话)求值该形式.

- further compilation(进一步编译)

最小编译之外的依赖于实现的编译. 即, 处理并不意味着完全编译. 块编译和机器特定指令生成是进一步编译的例子.
进一步编译允许在运行时发生.

- environment(环境)

编译相关的环境包括: **启动环境**、**编译环境**、**求值环境** 和 **运行时环境**.

编译环境继承自求值环境, 编译环境和求值环境可能是同一个环境.
求值环境继承自启动环境, 启动环境和求值环境可能是同一个环境.

```
启动环境
^
求值环境 -- 运行时环境
^
编译环境
```

- startup environment(启动环境)

Lisp镜像的环境, 在这里调用编译器.

- compilation environment(编译环境)

由编译器维护, 用于记录内部使用的定义和声明. 只保存正确编译需要的定义.
编译器环境用作编译器调用的宏扩展器的环境参数.
未描述: 编译环境中可用的定义是否在启动环境或求值环境中发起的求值中可用.

- evaluation environment(求值环境)

是运行时环境, 其中宏展开器和由`eval-when`指定的代码已被求值.
编译器发起的所有求值发生在求值环境.

- run-time environment(运行时环境)

已编译的程序可被执行的环境.

- compile time(编译时)

编译器处理源代码的一段时间.
在编译时, 只有 **编译环境** 和 **求值环境** 可用.

- compile-time definition(编译时定义)

表示编译环境中的定义. 例如, 编译文件时, 如果一个函数被声明为`inline`, 则它的定义可能被留存在编译环境中. 这个定义可能在求值环境中不可用.

- run time(运行时)

加载器在加载已编译代码或已编译代码在执行的一段时间. 运行时只有 **运行时环境** 可用.

- run-time definition(运行时定义)

表示运行时环境中的定义.

- run-time compiler(运行时编译器)

表示函数`compile`或隐式编译, 在同一个Lisp镜像中维护编译环境和运行时环境.
当使用运行时编译器时, 运行时环境和启动环境是同一个环境.

### <span id="3.2.2">3.2.2</span> 编译的语义

概念的讲, 编译是一个过程: 遍历代码、使用编译环境中的信息(例如公告和宏定义)执行各种语法和语义分析、生成等价可能更高效的代码.

#### <span id="3.2.2.1">3.2.2.1</span> 编译器宏

编译器宏的名称可以是函数或宏的名称. 即, 一个函数的名称可能命名了一个函数和一个编译器宏.

如果在函数名称出现的词法环境中对其调用`compiler-macro-function`结果为true时, 该函数名称命名了一个编译器宏. 创建函数名的词法绑定, 不仅创建了一个新的局部函数或宏定义, 也遮盖了编译器宏.

`compiler-macro-function`返回的函数是有两个参数的函数, 被称为展开函数.
为展开编译器宏, 通过调用宏展开钩子, 展开函数作为其第一个参数, 整个编译器宏形式作为第二个参数, 当前编译环境(如果这个形式被用`compile-file`之外的方式处理还需带上当前词法环境)作为第三个参数.
宏扩展钩子接着调用扩展函数, 形式作为第一个参数, 环境作为第二个参数.
扩展函数的返回值, 作为宏扩展钩子的返回值, 可能是同一个形式, 也可能是执行展开后的形式, 可用于替换原形式.

<span id="Figure3-6">图 3-6. 可用于编译器宏的已定义名称.</span>

``` lisp
*macroexpand-hook*
compiler-macro-function
define-compiler-macro
```

##### <span id="3.2.2.1.1">3.2.2.1.1</span> 编译器宏的目的

编译器宏的目的是允许有选择性的将代码转换作为对编译器的优化建议.
当编译器处理复合形式时, 如果操作符是编译器宏, 则在该形式和递归的在结果的展开上调用编译器宏函数, 而不是根据作为函数形式或宏形式执行原始形式中的常规处理.

编译器宏函数, 类似于宏函数, 是有两个桉树的函数: 整个形式和环境.
与常规的宏函数不同, 编译器宏函数可以衰退为返回与原始形式相同的值.
如果编译器宏函数破坏性的修改形式中的部分, 结果是未定义的.

作为参数传递给编译器宏函数的形式, 可以是一个car是函数名称的列表, 或者car是`funcall`、caddr是列表`(function name)`的列表; 注意这影响了编译器宏函数的形式参数的解构.
`define-compiler-macro`为可能的参数格式提供了正确的解构.

当`compile-file`选择展开是编译器宏形式的顶级形式时, 这个展开被视为`eval-shen`处理的顶级形式, 见[3.2.3.1 顶级形式的处理](#3.2.3.1).

##### <span id="3.2.2.1.2">3.2.2.1.2</span> 编译器宏中名称

编译器宏的名称可以是命名宏和函数的名称.

编译器宏定义是严格的全局的. 不存在像`macrolet`定义局部宏的定义局部编译器宏的方法.
函数名称的词法绑定遮盖任意与该名称相同的全局函数定义、全局宏定义或编译器宏定义.

注意编译器宏定义的存在不影响访问函数定义或宏定义的函数的返回值(例如`fboundp`或`macroexpand`).
编译器宏是全局的, 函数`compiler-macro-function`可以解决与其他词法和全局定义的交互.

##### <span id="3.2.2.1.3">3.2.2.1.3</span> 何时使用编译器宏

存在函数或宏的编译器宏定义, 表明需要编译器使用编译器宏的展开, 而不是原始的函数形式或宏形式.
然而, 不需要语言处理器(编译器、求值器或其他代码遍历器)实际调用编译器宏函数, 或者如果确实调用了编译器宏函数时使用作为结果的展开.

当编译器处理过程中遇到一个表示调用编译器宏名称(未被声明为`notinline`)的形式时, 编译器可以展开编译器宏, 可以用该展开替换原始形式.

当`eval`处理过程中遇到表示调用编译器宏名称(未被声明为`notinline`)的形式时, `eval`可以展开编译器宏, 可以用该展开替换原始形式.

存在两种编译器宏定义不能用于语言处理器的情况:

- 编译器宏绑定的全局函数名称, 被该函数名称的词法绑定遮盖;
- 函数名称已被声明或公告为`notinline`, 调用形式在声明的作用域中.

其他情况下, 编译器宏是否被展开或使用, 未描述.

###### <span id="3.2.2.1.3.1">3.2.2.1.3.1</span> 备注: 编译器宏的实现

尽管技术上允许, `eval`处理编译器宏的方式与编译器相同, 但在解释实现中不是一个好方法.

编译器宏存在的目的是用编译时速度换取运行时速度.
编写编译器宏的程序员, 倾向于假设编译器宏比常规函数和宏花更长的时间, 以产生运行时优化的代码.
因为解释实现中`eval`可能多次执行同一形式的语义分析, 实现在每次这类求值时调用编译器宏是不高效的.

如何处理这种情况, 留给各实现决定.

#### <span id="3.2.2.2">3.2.2.2</span> 最小编译

==最小编译== 定义如下:

- 在正编译的源代码中出现的 **编译器宏** 被展开, 或者在编译时全部展开, 不会在运行时展开;
- 在正编译的源代码中出现的 **宏** 和 **符号宏** 在编译时按不会在运行时再次展开的方式被展开. `macrolet`和`symbol-macrolet`被它们的体的形式替换, 体中对宏的调用被宏展开替换.
- `compile`处理的源代码中`load-time-value`形式的首个参数, 在编译时求值; `compile-file`处理的源代码中, 编译器安排它在加载时求值. 在两种情况中, 求值结果都被记住, 作为执行时`load-time-value`形式的值.

#### <span id="3.2.2.3">3.2.2.3</span> 语义约束

所有符合标准的程序必须遵循下面的约束, 这些约束被设计用于最小化已编译的程序与已解释的程序之间可观察出的区别:

- 所有引用的宏的定义必须在编译环境中出现. 任何以符号作为列表首元素的列表形式, 如果该符号不是在编译环境中定义的特殊操作符名称或宏名称, 则该形式被编译器视为函数调用.
- `special`公告的动态变量必须在编译环境中. 任何在编译环境中没有`special`声明或公告的绑定被编译器视为词法绑定.
- 在编译环境中定义且被声明为`inline`的函数定义, 必须与运行时为同一定义.
- 在函数`F`中, 编译器可以(但不要求)假设, 对名称为`F`的函数的递归调用会引用`F`的同一个定义, 除非该函数被声明为`notinline`. 在这中递归定义的函数`F`执行时进行重定义的后果是未定义的.
- 在一个文件中, 对在同一文件中定义的命名函数的调用, 会引用那个函数, 除非那个函数被省ing为`notinline`. 运行时函数单独重新定义函数或者在同一文件中多次定义一个函数, 其后果是未描述的.
- 所有函数在编译时声明的`ftype`中参数语法和返回值数量必须与运行时保持一致.
- 编译环境中定义的常值变量在运行时必须有相似的值. 源代码中对一个常值变量的引用, 等价于常值变量的值相应字面量对象的引用.
- 编译环境中使用`deftype`或`defstruct`指定的类型定义, 必须与运行时保持一致. 在编译环境中使用`defclass`定义的类, 必须在运行时定义为有相同超类和元类. 其含义是类型描述符的子类型/超类型关系在编译时和运行时一致.
- 编译环境中出现的类型声明, 必须在运行时精确的描述相应的值; 否则后果是为定义的. 允许编译时声明中出现未知的类型, 尽管这种情况下会产生警告.
- 除了上面显式列出的情况, 允许编译时定义的函数在运行时有不同的定义或不同的签名, 并且运行时的定义优先.

符合标准的程序不应该使用任意其他关于运行时环境与启动环境、求值环境和编译环境的一致性的假设.

除非特别说明, 当编译时和运行时定义不同时, 运行时会出现下述的某个情况:

- 发出类型为`error`的错误信号
- 编译时定义优先
- 运行时定义优先

如果编译器处理一个其操作符未在编译时未定义的函数形式, 编译时不会发出错误信号.

### <span id="3.2.3">3.2.3</span> 文件编译

函数`compile-file`按[3.2.2 编译的语义](#3.2.2)中i奥数的规则编译文件中的形式, 生成可被`load`加载的输出文件.

通常, 用`compile-file`编译的文件中出现的顶级形式, 只在编译输出文件被加载时求值, 而不是编译文件时. 然而, 文件中的一些形式需要在编译时求值, 这样其他部分才可被读取并正确的编译.

特殊形式`eval-when`可用来控制顶级形式是否在编译时或/和加载时求值.
可以使用符号`:compile-toplevel`、`:load-toplevel`、`:execute`指定`eval-when`不同的处理方式.
对顶级的`eval-when`形式, `:compile-toplevel`表示编译器必须在编译时求值这个形式体, `:load-toplevel`表示编译器必须安排在加载时求值这个形式体.
对非顶级的`eval-when`形式, `:execute`表示这个形式体在运行时环境中执行.

这个形式(`eval-when`形式)在描述`compile-file`编译文件中形式的处理模型中更好理解.
有两个处理模式: not-compile-time和compile-time-too.

`compile-file`读取后续形式, 在not-compile-time模式中处理, 在这个模式中, `compile-file`安排在加载时而不是编译时求值形式.
当`compile-file`在compile-time-too模式中, 形式在编译时和加载时求值.

#### <span id="3.2.3.1">3.2.3.1</span> 顶级形式的处理

文件编译器中对顶级形式的处理定义如下:

- 1 如果形式是编译器宏(未使用`notinline`声明禁止), 实现可以选择或不选择计算形式的编译器宏扩展并执行展开, 选择或不选择在同一处理模式(compile-time-too或not-compile-time)中将结果作为顶级形式处理.
如果衰退为获取或使用扩展, 必须处理原始形式.

- 2 如果形式是宏形式, 计算其宏扩展, 在同一处理模式中按顶级形式处理.

- 3 如果形式是`progn`形式, 在同一处理模式中将其体形式作为顶级形式处理.

- 4 如果形式是`locally`、`macrolet`或`symbol-macrolet`, `compile-file`建立相应的绑定, 在同一处理模式中结合这些绑定将体形式作为顶级形式处理. (注意这意味着, 顶级形式被处理的词法环境不一定是空词法环境).

- 5 如果形式是`eval-when`形式, 按下图处理:

<span id="Figure3-7">图 3-7. EVAL-WHEN处理.</span>

``` lisp
CT   LT   E    Mode  Action    New Mode
----------

Yes  Yes  ---  ---   Process   compile-time-too
No   Yes  Yes  CTT   Process   compile-time-too
No   Yes  Yes  NCT   Process   not-compile-time
No   Yes  No   ---   Process   not-compile-time
Yes  No   ---  ---   Evaluate  ---
No   No   Yes  CTT   Evaluate  ---
No   No   Yes  NCT   Discard   ---
No   No   No   ---   Discard   ---
```

列`CT`表示是否使用`:cimpile-toplevel`.<br>
列`LT`表示是否使用`:load-time`.<br>
列`E`表示是否使用`:execute`.<br>
列`Mode`表示处理模型, `---`表示处理模式是不相关的.<br>
列`Action`表示这些动作:<br>
  `Process`: 在指定模式中将体作为顶级形式处理<br>
  `Evaluate`: 在编译器的动态执行上下文中求值体, 使用求值环境作为全局环境和`eval-when`出现的词法环境<br>
  `Discard`: 忽略这个形式<br>
列`New Mode`表示新的处理模式, `---`表示编译器保留在当前模式.

- 6 否则, 这个形式是其他顶级形式.
在compile-time-too模式中, 编译器首先在求值环境中求值该形式, 然后执行最小化编译.
在not-compile-time模式中, 形式被最小化编译.
所有子形式被视为非顶级形式.<br><br>
注意顶级形式按在文件中出现的顺序处理, 在读取下一形式前处理当前的顶级形式.
然而, 不是顶级形式的形式的处理(包括宏展开)顺序, 以及进一步编译的顺序, 是未描述的.


`eval-when`形式只有在顶级时才会编译时求值. 在非顶级形式中, `:compile-toplevel`和`:load-toplevel`被忽略.
对非顶级形式, 指定为`:execute`的`eval-when`被视为包含`eval-when`形式的体的隐式的`progn`; 否则体中形式被忽略.

##### <span id="3.2.3.1.1">3.2.3.1.1</span> 定义宏的处理

在被`compile-file`处理的文件中出现的定义宏(例如`defmacro`或`defvar`), 通常有编译时副作用, 会影响被编译的同一文件中的后续形式.
解释这些副作用的一个便利模型是, 将宏展开定义为一个或多个`eval-when`形式, 产生编译时副作用的调用在形式`(eval-when (:compile-toplevel) ...)`的体中出现.

编译时副作用可能导致关于定义的信息, 与按正常处理定义宏的方式(解释的或加载已编译文件)的信息, 存储方式不同.

通常, 编译时存储的定义宏信息, 可能也可能不可用于解释器(编译前或编译后)或后续对编译器的调用中. 例如, 下面的代码是不可移植的, 因为它假设编译器存储对解释器可用的宏定义`foo`:

``` lisp
(defmacro foo (x) `(car ,x))
(eval-when (:execute :compile-toplevel :load-toplevel)
  (print (foo '(a b c))))
```

可移植的方式是将宏定义包含在`eval-when`形式中:

``` lisp
(eval-when (:execute :compile-toplevel :load-toplevel)
  (defmacro foo (x) `(car ,x))
  (print (foo '(a b c))))
```

下图列出了一些使得定义在编译时环境和运行时环境可用的宏.
在编译环境中可用的定义是否在求值环境中可用, 是未描述的,
编译环境中可用的定义是否在后续编译单元或后续对编译器的调用中可用, 是未描述的.
同`eval-when`一样, 这些编译时副作用只在定义宏出现在顶级形式时发生.

<span id="Figure3-8">图 3-8. 影响编译时环境的定义宏.</span>

``` lisp
declaim
defclass
defconstant
define-compiler-macro
define-condition
define-modify-macro
define-setf-expander
defmacro
defpackage
defparameter
defsetf
defstruct
deftype
defvar
```

##### <span id="3.2.3.1.2">3.2.3.1.2</span> 宏和编译器宏的约束

除非特殊说明, Common Lisp标准中没有定义这样的宏: 产生将宏形式中子形式作为顶级形式的展开.
如果一个实现提供了Common Lisp宏的特殊操作符, 则改特殊操作符定义必须在这个方语义等价.

编译器宏展开必须有与它们所替换的形式相同的顶级形式求值语义.
这是符合标准的实现和程序中都需要考虑的.

### <span id="3.2.4">3.2.4</span> 编译文件中的字面量对象

函数`eval`和`compile`需要保证在已解释的或已编译的代码对象中引用的字面量对象与源代码中相应的对象相同.
`compile-file`必须生成已编译的文件, 当用`load`加载时, 构造源代码中定义的对象, 生成对它们的引用.

在`compile-file`中, 由`load`加载文件构造的对象, 不能说与编译时构造的对象相同, 因为已编译的文件可能被加载入与其被编译的Lisp镜像不同的镜像中.
这一节定义关联求值环境中对象到运行时环境中相应对象间相似性的概念.

这一节描述的字面量对象傻姑娘的约束只适用于`compile-file`, `eval`和`compile`不拷贝或合并常量.

#### <span id="3.2.4.1">3.2.4.1</span> 可外部化对象(Externalizable Objects)

文件编译器在已编译文件中表示字面量对象, 必须在该文件被加载时重新构造出合适的等价对象. 这一事实意味着, 需要在可被用作代码中字面量对象而被文件编译器处理的对象上添加约束.

可被用作代码中字面量对象而被文件编译器处理的对象, 称为 ==可外部化对象==.

两个对象是相似的, 如果它们满足两个位置概念等价谓词, 该谓词独立于Lisp镜像, 因此不同Lisp镜像中的两个对象可被理解为是在该谓词上等价.
通过考察这个概念性谓词的定义, 程序员可以预料到对象的哪些方面可以被文件编译保留.

文件编译器必须与加载器合作, 以确保在各种情况中, 一个可外部化对象作为一个字面量对象处理, 加载器会构造出一个相似的对象.

相似性是定义在可外部化对象上的, 当加载已编译文件, 可以构造出表现为与在文件编译器操作时存在的原始对象相似的对象.

#### <span id="3.2.4.2">3.2.4.2</span> 字面量对象的相似性(Similarity of Literal Objects)

##### <span id="3.2.4.2.1">3.2.4.2.1</span> 聚合对象的相似性(Similarity of Aggregate Objects)

在相似性定义的类型中, 一些对象被视为聚合对象. 对于这些类型, 相似性是递归定义的.
我们说隶属于这些类型的一个对象有一些基础品质; 为满足相似性关系, 两个对象中相应品质的值也必须是相似的.

##### <span id="3.2.4.2.2">3.2.4.2.2</span> 相似性定义(Definition of Similarity)

两个对象`S`(源代码中)与`C`(已编译代码中)是相似的, 当且仅当, 它们都隶属于下面列出的类型(或实现定义的)之一, 同时也满足所有该类型上所需的相似性需求.

- number(数值)

如果有相同的类型, 表示相同的数学值.

- character(字符)

如果有相似的编码属性.

实现定义的属性必须给出非简单字符如何视为相似的定义.

- symbol(符号)

明显未内部化的符号`S`和`C`是相似的, 如果它们的名称是相似的.
两个内部符号`S`和`C`是相似的, 如果它们的名称是相似的, 并且 (1) 编译时`S`在当前包可访问, 加载时`C`在当前包可访问, 或者 (2) `C`在与`S`的主包相似的包中可访问.

(注意, 符号间的相似性不依赖于当前`readtable`或函数`read`如何解析符号的名称中的字符的).

- package(包)

如果它们的名称是相似的.

注意, 尽管包对象是一个可外部化对象, 程序员有义务确保当加载将包视为字面量对象的代码时, 相关的包已存在.
加载器类似于用包的名称作为参数调用`find-package`, 查找相应的包对象. 加载时包不存在, 加载器将发出错误信号.

- random-state(随机状态)

如果`S`总是生成相同的伪随机数序列, 该序列与用`C`的副本作为参数`reandom-state`, 调用函数`random`时生成相同`limit`的序列相同.

(注意, `C`已被文件编译器处理, 它不能直接作为`random`的参数, 因为`random`会产生副作用).

- cons

如果`S`的car与`C`的car相似, 且`S`的cdr与`C`的cdr相似.

- array(数组)

两个一维数组`S`与`C`相似, 如果两者长度相似, 两者的实际数组元素类型相似, 并且`S`中活跃元素与`C`中相应元素相似.

两个高于一维的数组`S`与`C`相似, 如果两者维度数量相似, 每个维度相似, 两者的实际数组元素类型相似, 并且`S`中每个元素与`C`中相应元素类似.

- hash-table(哈希表)

两个哈希表`S`与`C`相似, 如果满足下面三个条件:<br>
(1) 使用了相同的测试: 例如都是`eql`<br>
(2) 两个哈希表中键之间存在唯一的一对一关系, 相应的键是相似的<br>
(3) 对所有的键, 与两个相应键对应的值是相似的.

- pathname(路径名)

如果所有相应的路径名组件是相似的.

- function(函数)

函数不是可外部化对象.

- `structure-object` 和 `standard-object`

不存在结构和标准对象之间的通用的相似性概念.
然而, 允许符合标准的程序, 为程序中定义的是`structure-object`或者是`standard-object`的子类的类`K`, 定义一个`make-load-form`方法.
这个方法的作用是定义: 源代码中类型`K`的对象`S`与已编译代码中类型`K`的对象`C`相似, 如果`C`是从通过在`S`上调用`make-load-form`产生的代码中构造出的.

#### <span id="3.2.4.3">3.2.4.3</span> 相似性规则的扩展(Extensions to Similarity Rules)

一些对象, 像流、`readtable`和方法等, 在上面定义的相似性意义下,  不是可外部化对象.
即, 这些对象不可作为字面量对象出现在可被文件编译器处理的代码中.

允许实现扩展相似性规则, 从而其它种类的对象可以是可外部化对象.

如果对一些种类的对象, 在本规范或实现中均未定义相似性, 则文件编译器在遇到这些字面量常值对象时必须发出错误信号.

#### <span id="3.2.4.4">3.2.4.4</span> 可外部化对象的额外约束

如果在被文件编译器处理的单个文件的源代码中出现的两个字面量对象是同一的(identical), 则在已编译代码中相应的对象也必须是同一的.
除了符号和包, 被文件编译器处理的代码中任意两个字面量对象可被合并, 当且仅当它们是相似的; 如果它们是符号或包, 则它们可被合并, 当且仅当它们是同一的.

包含循环引用的对象可以是可外部化对象.
要求文件编译器保留文件中子结构的`eql`语义. 保留`eql`语义的含义是, 源码中是同一的子对象必须在已编译代码中也是同一的.

此外, 下面是文件编译器处理字面量对象的额外约束:

- array(数组)

如果源码中的数组是简单数组, 则已编译代码中相应数组也是简单数组.
如果源代码中数组有填充指针或者是实际可调整的, 则已编译代码中相应数组可能缺少这些品质.
如果源代码中数组有填充指针, 则已编译代码中相应数组的大小可能是被填充指针指示的大小.

- packages(包)

要求加载器查找相应的包对象, 就像使用包名作为参数调用`find-package`.
如果加载时不存在这个名称的包, 则发出类型为`package-error`的错误信号.

- random-state(随机状态)

常值随机状态对象不能作为函数`random`的状态参数, 因为`random`会修改这个数据结构.

- structure(结构), `standard-object`

类型为`struct-object`和`standard-object`的对象可以出现在已编译常量中, 如果存在合适的为该类型定义的`make-load-form`方法.

如果对象是`standard-object`、`structure-object`、`condition`或依赖于实现的一组类的广义实例, 在该对象被文件编译器引用为字面量对象时, 文件编译器会在该对象上调用`make-load-form`.
文件编译器只可对单个文件中指定对象调用一次`make-load-form`.

- symbol(符号)

为确保已编译文件可被正确的加载, 用户需要保证这些文件中引用的包在编译时和加载时的定义是一致的.
符合标准的程序必须满足:<br><br>
1 文件中顶级形式被`compile-file`处理时的当前包, 必须与已编译文件中该顶级形式的代码被`load`执行时的当前包相同.<br>
1.1 文件中修改当前包的顶级形式, 必须在编译时和加载时将当前包修改为相同的名称<br>
1.2  如果文件中第一个非原子的顶级形式不是`in-package`形式, 则调用`load`时的当前包的名称, 必须与调用`compile-file`时当前包的名称相同.<br>

2 在顶级形式中词法的出现的符号, 在编译时处理该顶级形式的当前包的包中是可访问的, 但它的主包是另一个包;
在加载时, 则必须有相同的名称的符号, 同时在加载时的当前包和编译时的主包中是可访问的.<br>

3 在已编译文件中出现的符号, 这些符号在编译时主包中是外部符号, 则加载时, 必须有相同名称的符号, 在相同名称的包中也是外部符号.<br>

如果这些条件中的一个不满足, 则加载器在哪个包中查找受影响的符号是未描述的. 允许实现发出错误信号或定义这个行为.

### <span id="3.2.5">3.2.5</span> 编译器中异常情况

允许`compile`和`cimpile-file`发出错误或警告, 包括编译时处理`(eval-when (:compile-toplevel) ...)`形式、宏展开、编译器自身发出的状态错误。

在编译在没有外部介入无法继续处理的情况下, 编译器可以发出类型为`error`的状况信号.

在标准标注的类型为`warning`的状态必须或者可以被发出的情况之外, 在编译器检测到后果是未定义或将会发出运行时错误时, 也可以发出警告. 这种情况的示例包括: 违反了类型声明、修改或赋值用`defconstant`定义的常值的值、使用错误数量的参数或错误格式的关键字参数列表调用内置Lisp函数、使用不可识别的类型描述符.

允许编译器发出类型`style-warning`的程序风格警告. 这种情况的示例包括: 使用不同的参数列表重定义函数、使用错误数量的参数调用函数、没有为不被引用的局部参数声明`ignore`、引用声明为`ignore`的变量.

允许`compile`和`compile-file`建立类型为`error`的状况的处理器. 例如, 可以发出警告、从依赖于实现的位置重启编译使得编译在没有手工接入时继续进行.

`compile`和`compile-file`返回三个值: 其中两个指示被编译的源码中是否包含错误、是否有程序风格警告.

一些警告可以推迟到编译结束, 见`with-compilation-unit`.


## <span id="3.3">3.3</span> 声明(Declarations)

声明提供了描述供程序处理器(如求值器或编译器)使用的信息的方法.

可以使用`decalre`将 ==局部声明== 嵌在可执行代码中. 使用`proclaim`或`declaim`建立 ==全局声明== 或 ==公告==.

特殊形式`the`为给定形式的值的类型定义局部声明提供了简写记法.

如果程序违反了声明或公告, 后果是未定义的.

### <span id="3.3.1">3.3.1</span> 声明处理的最小需求

通常实现可以自由的忽略类型描述符, 除了`declaration`、`notinline`、`safety`和`special`类型描述符.

`declaration`声明必须抑制关于其声明的种类的未识别声明警告.
如果实现没有生成未识别声明的警告, 它可以安全的忽略该声明.

`notinline`声明必须被支持内联函数或编译器宏的实现识别, 以关闭这一功能.
没有使用内联函数或编译器宏的实现可以安全的忽略该声明.

增加当前安全等级的`safety`声明必须总被识别.
总是尽可能按高安全等级处理代码的实现可以安全的忽略该声明.

`special`声明必须被所有实现处理.

### <span id="3.3.2">3.3.2</span> 声明描述符(Declaration Specifiers)

声明描述符是一个表达式, 可以出现在`decalre`表达式或`declaim`形式的顶级, 或者作为`proclaim`的参数.
它是一个列表, 其car是声明标识符, 其cdr是根据特定于声明标识符的规则解释的数据.

### <span id="3.3.3">3.3.3</span> 声明标识符(Declaration Identifiers)

下图列出了标准中定义的所有声明标识符:

<span id="Figure-">图 3-9. Common Lisp声明标识符.</span>

``` lisp
declaration
dynamic-extent
ftype
ignorable
ignore
inline
notinline
optimize
special
type
```

实现可以自由的支持其他声明标识符.
如果声明标识符不在上面的定义中、未被实现定义、不是类型名称或未使用`declaration`公告声明, 需要发出警告.

#### <span id="3.3.3.1">3.3.3.1</span> 类型声明的短记法

类型描述符可被用作声明标识符.

`(type-specifier var*)`是`(type type-specier var*)`的短记法.

### <span id="3.3.4">3.3.4</span> 声明的作用域(Declaration Scope)

声明可被分为两种: 用于变量或函数绑定; 不用于绑定.

出现在绑定形式头部的声明, 且应用于该形式的变量或函数绑定, 则该声明被称为 ==绑定的声明==.
这种声明影响声明作用域的绑定和任意引用.

不是绑定的声明的声明, 被称为 ==自由的声明==.

形式`F1`中的自由的声明应用于由形式`F2`建立的名称`N`的绑定, `F1`是只影响在`F1`中对`N`的引用的子形式, 该声明不可应用与`F1`之外对`N`的引用, 或者它不影响`F2`建立的`N`的绑定的行为.

不应用于绑定过的声明只可以作为自由的声明出现.

绑定的声明的作用域与其应用的绑定的词法作用域相同.
对于特殊变量, 这意味着, 这个绑定的作用域有词法绑定.

除非特别说明, 自由的声明的作用域只包括它作为头部出现的形式的形式体子形式, 不包括其他子形式.
自由的声明的作用域不包括包含该声明的形式建立的绑定的初始化形式.

一些出现声明的迭代形式中, `step`、`end-test`或`result`子形式也被包含在声明的作用域中.
迭代形式和子形式包括:

- `do`, `do*`: `step-forms`, `end-test-forms`, `result-forms`
- `dolist`, `dotimes`: `result-form`
- `do-all-symbols`, `do-external-symbols`, `do-symbols`: `result-form`

#### <span id="3.3.4.1">3.3.4.1</span> 示例: 声明的作用域

这是一个展示绑定的声明的作用域的列子:

``` lisp
(let ((x 1))                ;[1] 1st occurrence of x - 动态绑定
  (declare (special x))     ;[2] 2nd occurrence of x
  (let ((x 2))              ;[3] 3rd occurrence of x - 词法绑定
    (let ((old-x x)         ;[4] 4th occurrence of x
          (x 3))            ;[5] 5th occurrence of x - 动态绑定
      (declare (special x)) ;[6] 6th occurrence of x
      (list old-x x))))     ;[7] 7th occurrence of x
=>  (2 3)
```

- `x`的第一次出现建立`x`的动态绑定, 因为第2行中`x`的`special`声明.
- `x`的第三次出现建立了`x`的词法绑定, 因为这个`let`形式中不存在`special`声明.
- `x`的第四次出现是对第三次出现的`x`的词法绑定的引用.
- `x`的第五次出现建立`x`的动态绑定, 因为第6行中出现`special`声明.
- 第4行对`x`的引用不受第6行中`special`声明的影响, 因为这个引用不在第5的变量`x`的可能的词法作用域中.
- 第7行对`x`的引用是对在第5行建立的`x`的动态绑定的引用.

下面是展示自由的省ing的例子:

``` lisp
(lambda (&optional (x (foo 1))) ;[1]
  (declare (notinline foo))     ;[2]
  (foo x))                      ;[3]
```

第1行对`foo`的调用可以内联编译, 第3行对`foo`的调用必须不能内联编译. 这只因为第2行中对`foo`的`notinline`声明, 只应用于第3行的体中. 为抑制两次调用的内联编译, 可以:

``` lisp
(locally (declare (notinline foo)) ;[1]
  (lambda (&optional (x (foo 1)))  ;[2]
    (foo x)))                      ;[3]
```

或者

``` lisp
(lambda (&optional                               ;[1]
           (x (locally (declare (notinline foo)) ;[2]
                (foo 1))))                       ;[3]
  (declare (notinline foo))                      ;[4]
  (foo x))                                       ;[5]
```


最后是展示迭代形式中声明的作用域的例子:

``` lisp
(let ((x  1))                     ;[1] - 动态绑定
  (declare (special x))           ;[2]
    (let ((x 2))                  ;[3] - 词法绑定
      (dotimes (i x x)            ;[4] - 词法绑定, 动态绑定
        (declare (special x)))))  ;[5]
=>  1
```

第4行中第一次对`x`的引用是对在第3行建立的`x`的词法绑定的引用. 第二次出现是在第5行的自由的声明的作用域中, 因为是`dotimes`的`result-form`, 因此引用`x`的动态绑定.


## <span id="3.4">3.4</span> lambda列表

lambda列表是一个列表, 描述了一组参数(有时被称为lambda变量)和接受参数值的协议.

有几种lambda列表:

<span id="Figure3-10">图 3-10. lambda列表的种类.</span>

| 上下文 | lambda列表类别|
|:-----------|:-----------|
| defun形式 | 常规lambda列表|
| defmacro形式 | 宏lambda列表|
| lambda表达式 | 常规lambda列表|
| flet局部函数定义 | 常规lambda列表|
| labels局部函数定义 | 常规lambda列表|
| handler-case子句规范 | 常规lambda列表|
| restart-case子句规范 | 常规lambda列表|
| macrolet局部宏定义 | 宏lambda列表|
| define-method-combination | 常规lambda列表|
| define-method-combination<br>的:arguments选项 | define-method-combination<br>参数lambda列表|
| defstruct的:constructor选项 | 按参数顺序的(boa)lambda列表|
| defgeneric形式 | 广义函数lambda列表|
| defgeneric方法子句 | 特化lambda列表|
| defmethod形式 | 特化lambda列表|
| defsetf形式 | defsetf lambda列表|
| define-setf-expander形式 | 宏lambda列表|
| deftype形式 | deftype lambda列表|
| destructuring-bind形式 | destructuring lambda列表|
| define-compiler-macro形式 | 宏lambda列表|
| define-modify-macro形式 | define-modify-macro lambda列表|


下图列举了可用于lambda列表的已定义名称:

<span id="Figure3-11">图 3-11. 可用于lambda列表的已定义名称.</span>

``` lisp
lambda-list-keywords
lambda-parameters-limit
```

### <span id="3.4.1">3.4.1</span> 常规lambda列表

常规lambda列表用于描述常规函数如何接收一组参数. 下图是使用了常规lambda列表的已定义名称:

<span id="Figure3-12">图 3-12. 使用常规lambda列表的标准操作符.</span>

``` lisp
define-method-combination
defun
flet
handler-case
labels
lambda
restart-case
```

常规lambda列表中可以包含下图中的lambda列表关键字:

<span id="Figure3-13">图 3-13. 常规lambda列表中使用的lambda列表关键字.</span>

``` lisp
&allow-other-keys
&aux
&key
&optional
&rest
```

lambda列表中元素要么是参数描述符, 要么是lambda列表关键字.
允许实现提供额外的lambda列表关键字.
使用`lambda-list-keywords`查看实现中所有的lambda列表关键字.

常规lambda列表的语法如下:


``` EBNF
lambda-list::= (var*
 [&optional { var | (var [init-form [supplied-p-parameter]]) }*]
 [&rest var]
 [&key { var | ({ var | (keyword-name var) } [init-form [supplied-p-parameter]]) }* [&allow-other-keys]]
 [&aux {var | (var [init-form])}*])
```

- `var`或`supplied-p-parameter`必须是一个符号, 该符号不是常值变量名称.
- `init-form`可以是任意形式. 每当对参数描述的`init-form`求值时, 这个形式可以引用该`init-form`出现的描述符左边的任意参数变量, 包括任意`supplied-p-paramenter`变量; 这个形式依赖于没有其他参数变量(包括它自己的参数变量)已被绑定的事实.
- `keyword-name`可以是任意符号, 但一般是关键字符号; 所有的标准函数遵循这一约定.

一个常规的lambda列表有5个部分, 都可以为空. 关于处理参数失配的信息见[3.5 函数调用中错误检测](#3.5).

#### <span id="3.4.1.1">3.4.1.1</span> 必备参数描述符

==必备参数描述符== 是直到首个lambda列表关键字的参数描述符.
如果没有lambda列表关键字, 则所有的描述符是必备参数描述符.
每个必备参数用参数变量`var`描述, `var`作为词法变量绑定, 除非被用`special`声明.

如果有`n`个必备参数(`n`可能为0), 则必须至少有`n`个传递的参数, 必备的参数绑定到前`n`个传递的参数; 见[3.5 函数调用中错误检测](#3.5). 其他参数使用剩余的传递参数处理.

#### <span id="3.4.1.2">3.4.1.2</span> 可选参数描述符

如果`&optional`存在, ==可选参数描述符== 是在`&optional`之后直到下一个lambda列表关键字或列表尾部的参数描述符.

如果指定了可选参数, 则按下述方式处理每个可选参数:

- 如果有未处理的传递参数剩下, 则参数变量`var`绑定到下一个传递参数, 就像必备参数那样.
- 如果没有传递参数剩下, 则求值`init-form`, 参数变量绑定到其结果值(如果不存在`init-form`则绑定到nil).
- 如果描述符中有变量名`supplied-p-parameter`, 如果传递参数可用则它被绑定到true; 如果没有传递参数剩下则它被绑定到false(因此需要求值`init-form`). `supplied-p-parameter`不是绑定到传递参数, 而是绑定到对应于`var`是否有提供专递参数的指示值.

#### <span id="3.4.1.3">3.4.1.3</span> 剩余参数描述符

如果`&rest`存在, 必须后接单个 ==剩余参数描述符==, 其后可以是lambda列表关键字或lambda尾.

- 在处理完所有可选参数描述符之后, 可以有或没有一个剩余参数.
- 如果有剩余参数, 它被绑定到至今未处理的传递参数的列表.
- 如果没有未处理的传递参数剩余, 剩余参数绑定到空列表.
- 如果没有剩余参数和关键字参数, 则如果存在未处理的传递参数时应该发出错误信号, 见[3.5 函数调用中错误检测](#3.5).

剩余参数的值, 允许但不要求, 与`apply`最后一个传递参数共享结构.

#### <span id="3.4.1.4">3.4.1.4</span> 关键字参数描述符

如果`&key`存在, 所有后续的直到lambda列表关键字或列表尾的描述符是 ==关键字参数描述符==.
当处理关键词参数时, 相应的传递参数被处理为剩余参数的列表.
允许同时使用`&rest`和`&key`, 这时, 剩余的传递参数用作这两个用途, 即所有的剩余传递参数处理为剩余参数的列表, 同时也作为`&key`参数处理.
如果指定了`&key`, 则必须有偶数个传递参数, 见[3.5.1.6 奇数个关键字传递参数](#3.5.1.6).
这些传递参数被视为一对, 第一个传递参数被解释为一个名称, 第二个传递参数被作为相应的值.
每对中第一个对象必须是一个符号, 见[3.5.1.5 无效的关键字传递参数].
关键字参数描述符后可选的可以后接lambda列表关键字`&allow-other-keys`.

在每个关键字参数描述符中, 必须有参数变量名称`var`. 如果`var`单独出现或者是`(var init-form)`格式, 则这个用于在传递参数匹配参数的关键字名称必须是包`KEYWORD`中的符号, 它的名称在`string=`语义下与`var`相同.
如果使用`((keyword-name var) init-form)`格式, 则用于传递参数匹配参数的关键字名称是`keyword-name`, 它是任意一个包中的符号.(当然, 如果它不是包`KEYWORD`中的符号, 它不需要是自求值的, 所以在调用函数是需要注意保证正常求值仍产生该关键字名称.)

因此

``` lisp
(defun foo (&key radix (type 'integer)) ...)
```

等价于

``` lisp
(defun foo (&key ((:radix radix)) ((:type type) 'integer)) ...)
```

关键字参数描述符, 与所有参数描述符一样, 从左至右处理.
对每个关键字参数描述符, 如果有传递参数对的名称匹配该描述符的名称(即名称在`eq`语义下相同), 则该描述符的参数变量被绑定到传递参数对的第二项上.
如果没有传递参数对存在, 则该描述符的`init-form`被求值, 参数变量被绑定到该值上(如果没有`init-form`, 则被绑定到nil).
`supplied-p-paramenter`被视为`&optional`参数: 如果存在匹配的传递参数对则绑定为true, 否则绑定为false.

除非关键字参数检查被抑制, 传递参数对必须与参数描述符的名称匹配, 见[3.5.1.4 未识别的关键字参数](#3.5.1.4).

如果关键字参数检查被抑制, 则允许传递参数对不匹配参数描述符, 该传递参数对被忽略, 但如果提供了参数对, 则该参数对通过剩余参数可被访问.
这个机制的目的在于允许在多个lambda表达式间共享参数列表, 允许调用方或被调用的lambda表达式描述这种共享.

注意到, 如果`&key`存在, 总是允许`:allow-other-keys`关键在参数, 不管相应的值是true还是false.
如果值为false, 其他不匹配的关键字不被允许(除非使用了`&allow-other-keys`).

如果接收参数列表中指定了一个标记为`:allow-other-keys`的常规参数, 则`:allow-other-keys`同时有其特殊含义(指定是否可以有额外的关键字参数)和常规含义(流入函数的数据).

##### <span id="3.4.1.4.1">3.4.1.4.1</span> 抑制关键字参数检查

如果函数的lambda列表中指定了`&allow-other-keys`, 调用该函数时关键字传递参数检测被抑制.

如果调用函数时`:allow-other-keys`参数为true, 在该调用中关键字参数检查被抑制.

`:allow-other-keys`参数被允许的情况包括关键字参数, 甚至在相应的值是false时.

###### <span id="3.4.1.4.1.1">3.4.1.4.1.1</span> 示例: 抑制关键字参数检查

``` lisp
;;; The caller can supply :ALLOW-OTHER-KEYS T to suppress checking.
;;; 调用者可以提供抑制
 ((lambda (&key x) x) :x 1 :y 2 :allow-other-keys t) =>  1

;;; The callee can use &ALLOW-OTHER-KEYS to suppress checking.
;;; 被调用者可以抑制
 ((lambda (&key x &allow-other-keys) x) :x 1 :y 2) =>  1

;;; :ALLOW-OTHER-KEYS NIL is always permitted.
;;; :ALLOW-OTHER-KEYS NIL总是被允许
 ((lambda (&key) t) :allow-other-keys nil) =>  T

;;; As with other keyword arguments, only the left-most pair
;;; named :ALLOW-OTHER-KEYS has any effect.
;;; 存在多个时, 最左边的生效
 ((lambda (&key x) x)
  :x 1 :y 2 :allow-other-keys t :allow-other-keys nil)
=>  1

;;; Only the left-most pair named :ALLOW-OTHER-KEYS has any effect,
;;; so in safe code this signals a PROGRAM-ERROR (and might enter the
;;; debugger).  In unsafe code, the consequences are undefined.
;;; 存在多个时, :ALLOW-OTHER-KEYS NIL在安全代码中会产生错误
 ((lambda (&key x) x)                   ;This call is not valid
  :x 1 :y 2 :allow-other-keys nil :allow-other-keys t)
```

#### <span id="3.4.1.5">3.4.1.5</span> `&aux`变量描述符

这些不是实际的参数. 如果lambda列表关键字`&aux`存在, 后面的所有描述符是辅助变量描述符.
在所有参数描述符被处理后, 辅助变量描述符按从左至右的方式处理.
对每个辅助参数描述符, `init-form`被求值, `var`被绑定到该值(没有`init-form`时绑定为`nil`).
`&aut`变量的处理与`let*`的处理类似.

``` lisp
(lambda (x y &aux (a (car x)) (b 2) c) (list x y a b c))
   ==  (lambda (x y) (let* ((a (car x)) (b 2) c) (list x y a b c)))
```

#### <span id="3.4.1.6">3.4.1.6</span> 示例: 一般lambda列表

包含可选参数和剩余参数的示例:

``` lisp
((lambda (a b) (+ a (* b 3))) 4 5) =>  19

((lambda (a &optional (b 2)) (+ a (* b 3))) 4 5) =>  19

((lambda (a &optional (b 2)) (+ a (* b 3))) 4) =>  10

((lambda (&optional (a 2 b) (c 3 d) &rest x) (list a b c d x)))
=>  (2 NIL 3 NIL NIL)

((lambda (&optional (a 2 b) (c 3 d) &rest x) (list a b c d x)) 6)
=>  (6 T 3 NIL NIL)

((lambda (&optional (a 2 b) (c 3 d) &rest x) (list a b c d x)) 6 3)
=>  (6 T 3 T NIL)

((lambda (&optional (a 2 b) (c 3 d) &rest x) (list a b c d x)) 6 3 8)
=>  (6 T 3 T (8))

((lambda (&optional (a 2 b) (c 3 d) &rest x) (list a b c d x))
 6 3 8 9 10 11)
=>  (6 t 3 t (8 9 10 11))
```

包含关键字参数的示例:

``` lisp
((lambda (a b &key c d) (list a b c d)) 1 2) =>  (1 2 NIL NIL)

((lambda (a b &key c d) (list a b c d)) 1 2 :c 6) =>  (1 2 6 NIL)

((lambda (a b &key c d) (list a b c d)) 1 2 :d 8) =>  (1 2 NIL 8)

((lambda (a b &key c d) (list a b c d)) 1 2 :c 6 :d 8) =>  (1 2 6 8)

((lambda (a b &key c d) (list a b c d)) 1 2 :d 8 :c 6) =>  (1 2 6 8)

((lambda (a b &key c d) (list a b c d)) :a 1 :d 8 :c 6) =>  (:a 1 6 8)

((lambda (a b &key c d) (list a b c d)) :a :b :c :d) =>  (:a :b :d NIL)

((lambda (a b &key ((:sea c)) d) (list a b c d)) 1 2 :sea 6) =>  (1 2 6 NIL)

((lambda (a b &key ((c c)) d) (list a b c d)) 1 2 'c 6) =>  (1 2 6 NIL)
```

包含可选参数、剩余参数和关键字参数的示例:

``` lisp
((lambda (a &optional (b 3) &rest x &key c (d a))
   (list a b c d x)) 1)
=>  (1 3 NIL 1 ())

((lambda (a &optional (b 3) &rest x &key c (d a))
   (list a b c d x)) 1 2)
=>  (1 2 NIL 1 ())

((lambda (a &optional (b 3) &rest x &key c (d a))
   (list a b c d x)) :c 7)
=>  (:c 7 NIL :c ())

((lambda (a &optional (b 3) &rest x &key c (d a))
   (list a b c d x)) 1 6 :c 7)
=>  (1 6 7 1 (:c 7))

((lambda (a &optional (b 3) &rest x &key c (d a))
   (list a b c d x)) 1 6 :d 8)
=>  (1 6 NIL 8 (:d 8))

((lambda (a &optional (b 3) &rest x &key c (d a))
   (list a b c d x)) 1 6 :d 8 :c 9 :d 10)
=>  (1 6 9 8 (:d 8 :c 9 :d 10))
```

作为使用`&allow-other-keys`和`:allow-other-keys`的示例, 考虑一个接收两个命名参数和额外传递给`make-array`的命名参数的函数:

``` lisp
(defun array-of-strings (str dims &rest named-pairs
                         &key (start 0) end &allow-other-keys)
  (apply #'make-array dims
         :initial-element (subseq str start end)
         :allow-other-keys t
         named-pairs))
```

这个函数接收一个字符串和维度信息, 返回指定维度的元素为指定字符串的数字.
`:start`和`:end`命名的参数用于指定应该使用的字符串的子串.
此外, lambda列表中的`&allow-other-keys`表明, 调用方可以提供额外的命名参数, 通过剩余参数可以访问它们. 这些额外命名的参数被传递给`make-array`.
函数`make-array`通常不允许使用命名参数`:start`和`:end`, 如果调用时使用这些参数将发出错误信号. 然而, 如果调用`make-array`时传入值为true的`:allow-other-keys`参数, 额外提供的参数, 包括`:start`和`:end`可被接收并被忽略.

### <span id="3.4.2">3.4.2</span> 广义函数lambda列表

广义函数lambda列表被用于描述: 可被冠以函数接受的传递参数列表的整体样式.
独立的方法签名可以为有效方法的lambda列表提供额外的关键字参数.

使用`defgeneric`定义广义函数lambda列表, 该lambda列表有语法:

``` EBNF
lambda-list::= (var*
 [&optional { var | (var) }*]
 [&rest var]
 [&key { var | ({ var | (keyword-name var) }) }* [&allow-other-keys]])
```

广义函数lambda列表中可以使用的lambda列表关键字见下图:

<span id="Figure3-14">图 3-14. 广义函数lambda列表中可以使用的lambda列表关键字.</span>

``` lisp
&allow-other-keys
&key
&optional
&rest
```

广义函数lambda列表与常规lambda列表的不同之处有:

- 必备参数: 必须指定零个或多个必备参数
- 可选和关键字参数: 可选参数和关键字参数不可使用默认初始值形式或使用`supplied-p`参数
- 使用`&aux`: 不允许使用辅助参数

### <span id="3.4.3">3.4.3</span> 特化的lambda列表

特化的lambda列表用于特化特定签名的方法, 描述传递参数如何匹配方法接收的签名.
下面的已定义名称使用了特化的lambda列表, 详细信息见节尾的字典项部分.

<span id="Figure3-15">图 3-15. 使用特化的lambda列表的标准操作符.</span>

``` lisp
defmethod
defgeneric
```

特化的lambda列表可以使用下图中的lambda列表关键字:

<span id="Figure3-16">图 3-16. 特化的lambda列表使用的lambda列表关键字.</span>

``` lisp
&allow-other-keys
&aux
&key
&optional
&rest
```

特化的lambda列表语法上与常规lambda列表一样, 除了每个必备参数可选的与该参数特化的类或对象关联:

``` EBNF
lambda-list::= ({ var | (var [specializer]) }*
  [&optional { var | (var [init-form [supplied-p-parameter]]) }*]
  [&rest var]
  [&key { var | ({ var | (keyword-name var) } [init-form [supplied-p-parameter]]) }* [&allow-other-keys]]
  [&aux { var | (var [init-form]) }*])
```

### <span id="3.4.4">3.4.4</span> 宏lambda列表

宏lambda列表用于描述下图中操作符定义的宏:

<span id="Figure3-17">图 3-17. 使用宏lambda列表的操作符.</span>

``` lisp
define-compiler-macro
define-setf-expander
defmacro
macrolet
```
在环境参数只可出现一次的约束下, 宏lambda列表的语法是:

``` EBNF
reqvars::= var*
optvars::= [&optional { var | (var [init-form [supplied-p-parameter]]) }*]
restvar::= [{ &rest | &body } var]
keyvars::= [&key { var | ({ var | (keyword-name var) } [init-form [supplied-p-parameter]]) }*
            [&allow-other-keys]]
auxvars::= [&aux { var | (var [init-form]) }*]
envvar::= [&environment var]
wholevar::= [&whole var]
lambda-list::= (wholevar envvar  reqvars envvar  optvars envvar
                restvar envvar  keyvars envvar  auxvars envvar) |
               (wholevar envvar  reqvars envvar  optvars envvar .  var)
pattern::= (wholevar reqvars optvars restvar keyvars auxvars) |
           (wholevar reqvars optvars . var)
```

宏lambda列表可以使用下图中的lambda列表关键字:

<span id="Figure3-18">图 3-18. 宏lambda列表使用的lambda列表关键字.</span>

``` lisp
&allow-other-keys
&aux
&body
&environment
&key
&optional
&rest
&whole
```

像常规lambda列表一样, 由`&optional`引入的可选参数和由`&key`引入的关键参数可以用于宏lambda列表. 每个可以有默认的初值形式和`suppiled-p`参数.

`&body`与`&rest`功能一致, 但可用于表示一些输出格式和编辑函数, 将形式中剩余部分作为体, 应该有恰当的缩进. 在特定层级中, 只可以有一个`&body`或`&rest`, 见[3.4.4.1 lambda列表引入的解构(Destructuring by Lambda Lists)](#3.4.4.1). `&body`可以在宏lambda列表的任意层次中使用, 详情见[3.4.4.1 lambda列表引入的解构(Destructuring by Lambda Lists)](#3.4.4.1).

`&whole`后接单个绑定到整个宏调用形式的变量, 这个宏函数作为第一个传递参数的值.
如果`&whole`和后继的变量出现, 必须在lambda列表中首先出现, 在其他参数或lambda列表关键字之前.
`&whole`可以在宏lambda列表的任意层次中出现.
在内层中, 像`&rest`一样, `&whole`变量绑定到传递参数的相应部分, 但与`&rest`不同的是允许其他参数. `&whole`的使用不影响指定的传递参数的模式.

`&environment`后接单个绑定到表示宏调用被解释的词法环境的参数.
在计算宏的展开时, 这个环境应该被`macro-function`、`get-setf-expansion`、`compiler-macro-function`和`macroexapnd`等使用, 以保证使用编译环境中建立的任意词法绑定或定义.
`&environment`只可以在宏lambda列表的顶级出现, 只可出现一次, 但可以在该列表中任意位置出现; `&environment`参数与`&whole`在列表中其他参数之前绑定, 不管`&environment`在列表中出现的位置.
`&environment`参数绑定到有动态extent的环境参数.

允许宏lambda列表使用解构表达宏调用语法的结构. 如果没有lambda列表关键字出现, 则宏lambda列表是一个叶子为参数名称的树.
模式和宏形式必须有兼容的树结构, 即它们的树结构必须是等价的, 或者必须只在匹配宏形式的非原子对象的一些叶子上存在差异. 这种情况中的错误检测见[3.5.1.7 解构失配](#3.5.1.7).

解构lambda列表(在顶级或内嵌层次中)可以包含`.`, 以参数名称结束. 这种情况被视为与结束列表的参数名称在`&rest`之后出现一致.

允许宏形式(或宏形式的子表达式)是包含`.`的列表, 仅当使用`(... &rest var)`或`(... . var)`匹配它时. 需要宏识别和处理这种情况.

#### <span id="3.4.4.1">3.4.4.1</span> lambda列表引入的解构(Destructuring by Lambda Lists)

宏lambda列表中参数名称可以出现但常规lambda列表语法不允许一个列表的任意位置, 解构lambda列表可以作为替代参数名称而出现.
在这种情况下, 匹配参数的传递参数被视为一个列表(可能是包含`.`的列表), 被用作满足内嵌lambda列表的参数的传递参数猎豹. 这被称为 ==解构==.

解构是将复合对象分解为其组件部分的过程, 使用缩写的声明式语法, 而不是使用原始组件访问函数手动写出的. 每个组件部分被绑定到一个变量.

解构操作需要对象是可被分解的, 使用模式描述组件是如何被提取的, 以及其值是这些组件的变量的名称.

##### <span id="3.4.4.1.1">3.4.4.1.1</span> lambda列表引入的数据解构

在数据解构中, 模式是需被分解的类型的一个样例对象.
每当组件被提取时, 模式中出现一个符号, 这个符号是其值是这个组件的变量的名称.

##### <span id="3.4.4.1.1.1">3.4.4.1.1.1</span> 示例: lambda列表引入的数据解构

示例模式`(a b c)`解构一个有三个元素的列表. 变量`a`被指派给第一个元素, 变量`b`被指派给第二个元素, 依次类推. `((first . rest) . more)`是另一个复杂的示例.

数据解构的重要特性是它的语法简单性和可被扩展用于lambda列表解构.

#### <span id="3.4.4.1.2">3.4.4.1.2</span> lambda列表引入的lambda列表解构

lambda列表解构扩展了数据解构用于树对象. 这利用了树元素解构模式`(first second third)`与三个参数的lambda列表`(first second third)`之间的相似性.

如果模式中没有lambda列表关键字, lambda列表解构与数据结构完全一致. 模式中任意包含lambda列表关键字的列表(子列表或整个模式本身)被特殊解释.
这个列表中从左直到首个lambda列表关键字的元素被视为通常的解构模式, 然剩余的元素被视为函数的lambda列表, 除了通常需要一个变量, 允许使用任意解构模式.
注意到存在歧义性时, lambda列表语法是优先于解构语法的.
因此, 在`&optional`之后的元素列表是解构模式列表和一个默认值形式.

lambda列表解构模式中每个lambda列表关键字的详细行为如下:

- `&optional`

后继的元素是一个变量或一个解构模式的列表, 一个默认值形式, 一个`supplied-p`变量. 默认值形式和`supplied-p`变量可被省略. 如果被解构的列表过早结束, 使得没有元素用于匹配这个解构(子)模式, 默认值形式被求值和被用于解构. 如果默认形式被使用`supplied-p`变量的值为`nil`, 否则为`t`.

- `&rest`, `&body`

下一个元素是解构模式, 匹配列表的剩余部分. `&body`与`&rest`一致, 但`&body`声明了被匹配的是构成形式体的形式列表. 除非后面有lambda列表关键字, 下一个元素是最后一个元素.

- `&aux`

剩余的元素不是解构模式, 而是辅助变量绑定.

- `&whole`

下一个元素是解构模式, 匹配整个宏形式或宏内层的整个子表达式.

- `&key`

后续的元素是一个:<br>
变量<br>
变量的列表, 一个可选的初始化形式, 一个可选的`supplied-p`变量<br>
关键字和解构模式的列表的列表, 可选的初始化形式, 可选的`supplied-p`变量<br>
被解构的列表的剩余部分被视为关键字和值.

- `&allow-other-keys`

与常规含义一致.

### <span id="3.4.5">3.4.5</span> 解构lambda列表

`destructuring-bind`使用解构lambda列表.

解构lambda列表与宏lambda列表紧密相关, 见[3.4.4 宏lambda列表](#3.4.4).
解构lambda列表可以包含宏lambda列表中除了`&environment`之外的所有lambda列表关键字, 以同样的方式支持解构.
宏lambda列表中内嵌的内层lambda列表有解构lambda列表的语法.

解构lambda列表的语法:

``` EBNF
reqvars::= var*
optvars::= [&optional { var | (var [init-form [supplied-p-parameter]]) }*]
restvar::= [{ &rest | &body } var]
keyvars::= [&key { var | ({ var | (keyword-name var) } [init-form [supplied-p-parameter]]) }*
            [&allow-other-keys]]
auxvars::= [&aux { var | (var [init-form]) }*]
envvar::= [&environment var]
wholevar::= [&whole var]
lambda-list::= (wholevar reqvars optvars restvar keyvars auxvars) |
               (wholevar reqvars optvars . var)
```

### <span id="3.4.6">3.4.6</span> 按参数顺序(Boa, by order of argument) lambda列表

按参数顺序lambda列表是与常规lambda列表语法相似的lambda列表, 但以按参数顺序风格处理.

按参数顺序lambda列表只在`defstruct`形式中使用, 用于显式的描述构造器函数的lambda列表(有时被称为按参数顺序构造器).

按参数顺序lambda列表中可以使用`&optional`、`&rest`、`&aux`、`&key`和`&allow-other-keys`lambda列表关键字. 但它们的使用方式与常规lambda列表不同.

下面的示例中描述了`defstruct`如何处理`:constructor`选项的:

``` lisp
(:constructor create-foo
        (a &optional b (c 'sea) &rest d &aux e (f 'eff)))
```

将`create-foo`定义为一个或多个传递参数的构造器.<br>
第一个传递参数用于初始化槽`a`.<br>
第二个传递参数用于初始化槽`b`. 如果没有第二个传递参数, 则使用`defstruct`体中给出的默认值.<br>
第三个传递参数用于初始化槽`c`. 如果没有第三个传递参数, 则使用符号`sea`.<br>
第三个传递参数后的所有参数被收集到一个列表中, 用于初始化槽`c`. 如果有三个或更少的传递参数, 则给槽`d`赋予`nil`.<br>
槽`e`未被初始化, 它的初始值是依赖于实现的.<br>
槽`f`被初始化为符号`eff`.<br>

`&key`和`&allow-other-keys`传递参数的默认行为与`&optional`传递参数相似: 如果lambda列表中没有提供默认值, 则使用`defstruct`体中给出的默认值. 例如:

``` lisp
(defstruct (foo (:constructor CREATE-FOO (a &optional b (c 'sea)
                                            &key (d 2)
                                            &aux e (f 'eff))))
  (a 1) (b 2) (c 3) (d 4) (e 5) (f 6))

(create-foo 10) =>  #S(FOO A 10 B 2 C SEA D 2 E implemention-dependent F EFF)

(create-foo 10 'bee 'see :d 'dee)
=>  #S(FOO A 10 B BEE C SEE D DEE E implemention-dependent F EFF)
```

如果使用了形式为`((key var) [default [svar]])`的关键字参数, 则使用`var`匹配槽名称.

`b`和`e`的情况允许用户指定所有可能的行为. `&aux`变量可用于完全覆盖体中的默认初始化.

如果没有给辅助变量提供默认值, 在显式赋予相应槽值之前尝试读取槽值的后果是未定义的. 如果这样的槽有指定的`:type`选项, 这个被抑制的初始化并不意味着类型失配检查, 只要求这个声明的类型在槽最终被赋值时应用.

使用这个定义, 可以使用`(create-foo 1 2)`替代`(make-foo :a 1 :b 2)`, `create-foo`提供了与`make-foo`不同的默认值.

允许其它不与槽名称对应的仅被用于后续初始化计算的传递参数. 例如:

``` lisp
(defstruct (frob (:constructor create-frob
                 (a &key (b 3 have-b) (c-token 'c)
                         (c (list c-token (if have-b 7 2))))))
        a b c)
```

`c-token`传递参数仅用于为初始化槽`c`提供一个值.
与可选参数和关键字参数关联的`supplied-p`参数也可这样使用.

### <span id="3.4.7">3.4.7</span> `defsetf` lambda列表

`defsetf`使用`defsetf` lambda列表, 该lambda列表有语法:

``` EBNF
lambda-list::= (var*
  [&optional { var | (var [init-form [supplied-p-parameter]]) }*]
  [&rest var]
  [&key { var | ({ var | (keyword-name var) } [init-form [supplied-p-parameter]]) }* [&allow-other-keys]]
  [&environment var]
```

`defsetf` lambda列表可以使用这些lambda列表关键字:

<span id="Figure3-19">图 3-19. `defsetf` lambda列表使用的lambda列表关键字.</span>

``` lisp
&allow-other-keys
&environment
&key
&optional
&rest
```

`defsetf` lambda列表与常规lambda列表的不同点是: 不允许使用`&aux`, 允许使用`&environment`引入一个环境参数.

### <span id="3.4.8">3.4.8</span> `deftype` lambda列表

`deftype`使用`deftype` lambda列表.

`deftype` lambda列表的语法与宏lambda列表的语法一致, 从而可以包含lambda列表关键字作为宏lambda列表.

`deftype` lambda列表与宏lambda列表的不同点是: 如果没有给可选参数或关键字参数提供`init-form`, 则该参数的默认值是符号`*`而不是`nil`.

### <span id="3.4.9">3.4.9</span> `define-modify-macro` lambda列表

`define-modify-macro`使用`define-modify-macro` lambda列表.

`define-modify-macro` lambda列表可以使用的lambda列表关键字见下图:

<span id="Figure3-20">图 3-20. `define-modify-macro` lambda列表可以使用的lambda列表关键字.</span>

``` lisp
&optional
&rest
```

`define-modify-macro` lambda列表与常规lambda列表类似, 但不支持关键字参数.
`define-modify-macro`不需要匹配关键字参数, 剩余参数已足够使用.
也不支持辅助变量, 因为`define-modify-macro`没有引用这种绑定的体形式. 见宏`define-modify-macro`.

### <span id="3.4.10">3.4.10</span> `define-method-combination` 传递参数lambda列表

`define-method-combination`的`:arguments`选项中使用`define-method-combination` 传递参数lambda列表.

`define-method-combination` 传递参数lambda列表可以使用的lambda列表关键字见下图:

<span id="Figure3-21">图 3-21. `define-method-combination` 传递参数lambda列表可以使用的lambda列表关键字.</span>

``` lisp
&allow-other-keys
&aux
&key
&optional
&rest
&whole
```

`define-method-combination` 传递参数lambda列表与常规lambda列表相似, 但允许使用`&whole`.

### <span id="3.4.11">3.4.11</span> 文档字符串和声明的语法交互

在一些情况下, 文档字符串可以出现在在一组形式前的一组`declare`表达式中.

如果字符串`S`出现在文档字符串允许出现的位置, 后续没有`decalre`表达式或形式, 则将`S`为形式;
否则`S`为文档字符串.
如果有多个这样的文档字符串存在, 后果是未定义的.

## <span id="3.5">3.5</span> 函数调用中错误检测

### <span id="3.5.1">3.5.1</span> 参数失配检测(Argument Mismatch Detection)

#### <span id="3.5.1.1">3.5.1.1</span> 安全和不安全调用

调用是 ==安全调用==, 如果下面的是安全代码或系统代码(不是程序员代码的宏展开后的系统代码):

- 这个调用
- 被调用的函数的定义
- 函数求值点

下面的特殊情况需要特别注意:

- 如果被调用的函数是广义函数, 它是安全的如果下面的是安全代码或系统代码:<br>
被显式定义的定义<br>
所有可应用方法的方法定义<br>
方法组合的定义<br>

- 对于形式`coerce X 'function`, `X`是一个lamda表达式,`coerce`被执行时全局环境中优化的安全性的值应用在结果函数上.
- 对于对函数`ensure-generic-function`的调用, 在作为`:environment`传递参数的环境对象中优化的安全性的值应用在结果广义函数上.
- 对于以lambda表达式作为传递参数对`compile`的调用, 在`compile`被调用时的全局环境中优化的安全性的值应用在结果已编译函数上.
- 对于以一个传递参数对`compile`的调用, 如果函数的原始定义是安全的, 则结果已编译函数必须也是安全的.

- 通过`call-next-method`对方法的调用必须被认为是安全的, 如果下面的是安全代码或系统代码:<br>
显式定义的广义函数的定义<br>
所有可应用方法的方法定义<br>
方法组合的定义<br>
`call-next-method`建立绑定时, 方法定义形式体的进入点<br>
名称`call-next-method`的函数求值点<br>


==不去安全调用== 是 不是安全调用的调用.

如果采用了合理的株洲确保调用是安全的, 程序员可以依赖于安全的调用, 甚至有系统代码时.
例如, 如果程序员从安全代码调用`mapcar`, 提供了一个被编译为安全的函数, 实现需要保证`mapcar`也是安全调用.

##### <span id="3.5.1.1.1">3.5.1.1.1</span> 安全调用中错误检测时间

如果安全调用中发出错误信号, 信号的具体发出点是依赖于实现的.
通常, 错误信号在编译时或运行时发出, 可以在执行调用前、时和后发出. 但总是在执行被调用的函数的体之前发出.

##### <span id="3.5.1.2">3.5.1.2</span> 过少传递参数

不允许给函数提供过少传递参数. 过少传递参数的含义是函数的传递参数数量比必备参数数量少.

如果在安全调用中出现这种情况, 必须发出类型为`program-error`的错误信号; 在不安全调用中这种情况的后果未定义.

#### <span id="3.5.1.3">3.5.1.3</span> 过多传递参数

不允许给函数提供过多传递参数. 过多传递参数的含义是函数的传递参数数量比必备参数和可选参数数量多;
如果函数使用了`&rest`或`&key`, 可以接收过多参数.

如果这种情况出现在安全调用中, 必须发出类型为`program-error`的错误信号; 在不安全调用中这种情况的后果未定义.

#### <span id="3.5.1.4">3.5.1.4</span> 未识别的关键字参数

不允许使用不能被函数识别的名称给函数提供关键字参数, 除非关键字检查被抑制(见[3.4.1.4.1 抑制关键字参数检查](#3.4.1.4.1)).

如果这种情况出现在安全调用中, 必须发出类型为`program-error`的错误信号; 在不安全调用中这种情况的后果未定义.

#### <span id="3.5.1.5">3.5.1.5</span> 无效的关键字传递参数

不允许使用不是符号的名称给函数提供关键字参数.

如果这种情况出现在安全调用中, 必须发出类型为`program-error`的错误信号, 除非关键字检查被抑制(见[3.4.1.4.1 抑制关键字参数检查](#3.4.1.4.1));
在不安全调用中这种情况的后果未定义.

#### <span id="3.5.1.6">3.5.1.6</span> 奇数个关键字传递参数

不允许给关键词参数提供奇数个传递参数.

如果这种情况出现在安全调用中, 必须发出类型为`program-error`的错误信号, 除非关键字检查被抑制(见[3.4.1.4.1 抑制关键字参数检查](#3.4.1.4.1));
在不安全调用中这种情况的后果未定义.

#### <span id="3.5.1.7">3.5.1.7</span> 解构失配

当使用解构lambda列表匹配一个形式时, 模式与形式必须有兼容的树结构, 见[3.4.4 宏lambda列表](#3.4.4).

否则在安全调用中, 必须发出类型为`program-error`的错误信号; 在不安全调用中这种情况的后果未定义.

#### <span id="3.5.1.8">3.5.1.8</span> 调用下一方法时的错误

如果使用传递参数调用`call-next-method`, `call-next-method`的已改变传递参数集的可应用方法有序集,
必须与广义函数的原始传递参数的可应用方法有序集一样, 否则发出错误信号.

对新传递参数可应用的方法集 与 对原始传递参数可应用的方法集之前的比较, 对有相同描述符的方法中顺序是不敏感的.

如果用描述了不同可应用方法的有序集的传递参数调用`call-next-method`, 且没有下一个方法可用,
对不同方法的测试和相关错误的发出(如果有)在调用`no-next-method`之前执行.

## <span id="3.6">3.6</span> 遍历规则和副作用

在执行对象遍历操作的代码中, 以可能影响后续遍历操作的方式破坏性的修改对象时, 后果是未定义的.
特别的, 有下述规则:

- 列表遍历

对列表遍历操作, 列表中的cdr链不允许被破坏性修改.

- 数组遍历

对数组遍历操作, 数组不允许被调整, 如果存在填充指针, 则填充指针也不允许修改.

- 哈希表遍历

对哈希表遍历操作, 新元素不允许添加或移除, 除了当前哈希键对应的元素被修改或移除.

- 包遍历

对包遍历操作(例如`do-symbols`), 新符号不允许在被遍历的包或它使用的包中内部化或非内部化,
除了当前符号可以从被遍历的包中非内部化.

## <span id="3.7">3.7</span> 破坏性操作(Destructive Operations)

### <span id="3.7.1">3.7.1</span> 修改字面量对象

如果字面量对象被破坏性修改, 后果是未定义的. 下面的操作是破坏性的:

- `random-state`

用作函数`random`的传递参数.

- cons

修改cons的car或cdr, 或者在cons的car或cdr的对象上执行破坏性操作.

- 数组

给数组中某个元素存储新值, 或者在已是元素的对象上执行破坏性操作.
修改数组的填充指针、维度或displacement(不管数组是否是实际可调整的).
在displace到这个数组或者与其共享内容的另一个数组上, 执行破坏性操作.

- 哈希表

在键上执行破坏性操作.
给值存储新值, 或者在值的对象上执行破坏性操作.
从哈希表中添加或者移除项.

- `struct-object`

在槽中存储新值, 或者在一些槽的值对象上执行破坏性操作.

- `standard-object`

在槽中存储新值, 或者在一些槽的值对象上执行破坏性操作.
修改对象的类(例如使用函数`change-class`).

- `readtable`

修改`readtable`大小写设置.
修改`readtable`中任意字符的语法类型.
修改`readtable`中任意字符关联的读取器宏函数, 或者修改其中定义为分发宏字符的字符关联的读取器宏函数.

- 流

在流上执行IO操作, 或者关闭流.

- 所有其他标准类型

这个类别包括诸如: 字符串、状况、函数、方法组合、方法、数值、包、路径名、重启器和符号.

没有在这些类型的对象上定义标准的破坏性操作.

### <span id="3.7.2">3.7.2</span> 破坏性操作中控制转移

破坏性操作中是否可以发生控制转移, 是依赖于实现的.

#### <span id="3.7.2.1">3.7.2.1</span> 示例: 破坏性操作中控制转移

下面的示例展示了修改的依赖于实现的特性:

``` lisp
(let ((a (list 2 1 4 3 7 6 'five)))
  (ignore-errors (sort a #'<))
  a)
=>  (1 2 3 4 6 7 FIVE)
OR=>  (2 1 4 3 7 6 FIVE)
OR=>  (2)

(prog foo ((a (list 1 2 3 4 5 6 7 8 9 10)))
  (sort a #'(lambda (x y) (if (zerop (random 5)) (return-from foo a) (> x y)))))
=>  (1 2 3 4 5 6 7 8 9 10)
OR=>  (3 4 5 6 2 7 8 9 10 1)
OR=>  (1 2 4 3)
```


## <span id="3.8">3.8</span> 求值和编译的字典

见[求值和编译的字典](../Dictionary#3.8).
