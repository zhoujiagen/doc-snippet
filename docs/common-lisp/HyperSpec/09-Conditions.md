# 9. 状况

[TOC]

## <span id="9.1">9.1</span> 状况系统的概念

Common Lisp的构造不仅是按在它们预期被使用的场景下的行为描述的(见每个操作符规范的 **Description** 部分), 也按其它场景描述(见每个操作符规范的 **Exceptional Situations** 部分).

==场景(situation)== 是在特定上下文中对表达式的求值过程. ==状况(conditon)== 是表示被检测到的特定场景的对象.
状况是类`condition`的通用实例. Common Lisp中定义了状况类的层次.
状况中有与它表示的场景相关的数据的槽.

==错误(error)== 是一个场景, 其中正常的程序执行在没有某种形式的干预(由用户或程序控制)时不能继续执行.
不是所有的错误都可被检测出的. 当一个错误不能被检测时, 它的作用是实现依赖的、实现定义的、未描述的或未定义的. 见[1.4 定义](#1.4).
所有可被检测出的错误可以用状况表示, 但不是所有状况表示错误.

==发出(signaling)== 是一个过程, 过程中通过抛出可被后续处理的状况来修改程序的控制流. 函数`error`、`cerror`、`signal`和`warn`可用于发出状况.

发布过程包括从一组活跃处理器中选择和调用一个处理器.
==处理器(handler)== 是一个只有一个状况传递参数的函数, 用于处理状况.
每个处理器与一个状况类型关联, 一个处理器只在处理器关联的类型的状况上调用.

**活跃处理器** 是动态建立的(见`handler-bind`或`handler-case`).
处理器在与状况发出者等价的动态环境中被调用, 除了那些已特殊方式绑定的活跃处理器: 只包含在建立被调用的处理器时的活跃处理器.
发出状况在状况上没有副作用, 状况中没有动态字段.

如果处理器被调用, 它按如下方式处理状况:

- 拒绝(Decline): 拒绝处理状况: 简单的返回而不是转移控制. 这种情况下, 处理器的返回值被忽略, 调用下一个最近建立的处理器. 如果没有这样的处理器, 发出函数是`error`或`cerror`, 在发出者的动态环境中进入调试器. 如果没有这样的处理器, 发出函数是`signal`或`warn`, 发出函数返回`nil`.
- 处理(Handle): 通过执行非本地控制转移来处理状况, 可以是`go`、`return`、`throw`或`abort`、`invoke-restart`.
- 退出(Defer): 通过一组动作延期是否处理还是拒绝的决定, 这些动作通常是发出其它状况、重新发出同一状况或者强制进入调试器.

### <span id="9.1.1">9.1.1</span> 状况类型

下图列举了标准状况类型. 可以使用`define-condition`定义额外的状况类型.

<span id="Figure9-1">图 9-1. 标准状况类型.</span>

``` lisp
arithmetic-error
cell-error
condition
control-error
division-by-zero
end-of-file
error
file-error
floating-point-inexact
floating-point-invalid-operation
floating-point-overflow
floating-point-underflow
package-error
parse-error
print-not-readable
program-error
reader-error
serious-condition
simple-condition
simple-error
simple-type-error
simple-warning
storage-condition
stream-error
style-warning
type-error
unbound-slot
unbound-variable
undefined-function
warning
```

所有的状况类型是类型`condition`的子类型, 即: 当且仅当`c`是一个状况,

``` lisp
(typep c 'condition) => true
```

实现必须定义所有描述的子类型关系. 除非特殊说明, 该文档中所有的子类型关系不是互斥的.
状况继承它的超类型的结构.

类`condition`的元类未指定.
状况类型的名称可用于在`define-conditon`中指定超类型关系, 但尝试在`defclass`形式中将状况类型作为超类的后果是未描述的.

下图展示了定义状况类型和创建状况的操作符:

<span id="Figure9-2">图 9-2. 定义和创建状况的操作符.</span>

``` lisp
define-condition
make-condition
```

下图展示了读取状况槽值的操作符:

<span id="Figure9-3">图 9-3. 读取状况操作的操作符.</span>

``` lisp
arithmetic-error-operands
arithmetic-error-operation
cell-error-name
file-error-pathname
package-error-package
print-not-readable-object
simple-condition-format-arguments
simple-condition-format-control
stream-error-stream
type-error-datum
type-error-expected-type
unbound-slot-instance
```

#### <span id="9.1.1.1">9.1.1.1</span> 严重的状况

==严重的状况== 是严重到未处理时需要交互式干预的状况.
典型的严重的状况是由`error`或`cerror`发出的; 非严重的状况是由`signal`或`warn`发出的.

### <span id="9.1.2">9.1.2</span> 创建状况

函数`make-condition`可用于显式的构造状况对象.
诸如`error`、`cerror`、`signal`和`warn`操作状况, 可能隐式的创建状况对象.
诸如`ccase`、`ctypecase`、`ecase`、`etypecase`、`check-type`和`assert`可能隐式的创建(和发出)状况.

#### <span id="9.1.2.1">9.1.2.1</span> 状况指示器

状况系统中的一些函数有称为状况指示器的传递参数. 通常这些传递参数记为:

``` lisp
datum &rest arguments
```

`datum`和`arguments`是默认类型`default-type`的状况的指示器.
被表示的状况是依赖于`datum`的类型计算出的:

* 如果`datum`是一个命名了状况类型的符号

被表示的状况是`(apply #'make-condition datum arguments)`的结果.

* 如果`datum`是一个格式化控制

被表示的状况是`(make-condition defaulted-type :format-control datum :format-arguments arguments)`的结果, 这里`defaulted-type`值`default-type`的子类型.

* 如果`datum`是一个状况

被表示的状况是`datum`自身. 在这种情况中, 除非在相关的操作符的描述中特别说明, `argument`必须为空; 即提供`arguments`的后果是未定义的.

注意到`default-type`只在提供`datum`字符串时使用. 在其它情况下, 结果状况不一有类型`default-type`.

这些例子显示不同的状况指示器可以表示等价的状况对象:

``` lisp
(let ((c (make-condition 'arithmetic-error :operator '/ :operands '(7 0))))
  (error c))
==  (error 'arithmetic-error :operator '/ :operands '(7 0))

(error "Bad luck.")
==  (error 'simple-error :format-control "Bad luck." :format-arguments '())
```

### <span id="9.1.3">9.1.3</span> 打印状况

如果`define-conditon`使用了`:report`传递参数, 则定义了一个打印函数, 每当定义的函数被打印时且`*print-escape*`是false时, 会调用这个打印函数.
这个函数称为 ==状况报告器==; 它输出的文本称为报告信息.

当打印状况且`*print-escape*`是false时, 会调用该状况的状况打印器.
状况被诸如函数`invoke-debugger`、`break`和`warn`自动打印.

当`*print-escape*`是true时, 应该根据实现风格中简略方式打印对象(`print-unreadble-object`).
不要求读取窗框的打印表示时重建状况.

没有提供直接访问或调用状况报告器的函数.

#### <span id="9.1.3.1">9.1.3.1</span> 状况报告的推荐风格

为确保将报告信息美观的呈现给用户, 这里推荐一些风格性约定.

有状况报告器输出消息的内容的风格推荐, 但没有对程序的形式化要求.
如果程序违背了推荐的消息风格, 输出的消息可能是不美观的, 但程序仍然是复合标准的程序.

对调用状况报告器的程序或实现的要求是有些高. 必须复合标准的程序做出假设: 如果遵循了这些风格指南, 会保持一定程度的美观.
下面显式的列出对这种程序的要求.

##### <span id="9.1.3.1.1">9.1.3.1.1</span> 状况报告中的大小写和标点符号

推荐报告消息是一个完整的句子, 有合适的大小写和标点符号.
在英文中, 这意味着首字母应该大写, 尾部应该有个点号.

``` lisp
(error "This is a message")  ; 不推荐
(error "this is a message.") ; 不推荐

(error "This is a message.") ; 推荐
```

##### <span id="9.1.3.1.2">9.1.3.1.2</span> 状况报告中的首尾新行

推荐报告消息不以介绍性文本开始, 例如`Error: `或`Warning: `或新行.
可以在调用状况报告器的例程上下文中添加这中文本.

推荐报告消息最后没有新行. 可以在调用状况报告器的例程上下文中添加这中文本.

``` lisp
(error "This is a message.~%")   ; 不推荐
(error "~&This is a message.")   ; 不推荐
(error "~&This is a message.~%") ; 不推荐

(error "This is a message.")     ; 推荐
```

##### <span id="9.1.3.1.3">9.1.3.1.3</span> 状况报告中的内嵌新行

当报告消息过长时, 可以内嵌一个或多个新行.

如果调用例程在消息的第一行插入一些前缀(例如`Error: `或`;; Error: `), 必须确保输出中后续行中有合适的前缀, 从而输出消息的左端会对齐.

``` lisp
(defun test ()
  (error "This is an error message.~%It has two lines."))

;; Implementation A
(test)
This is an error message.
It has two lines.

;; Implementation B
(test)
;; Error: This is an error message.
;;        It has two lines.

;; Implementation C
(test)
>> Error: This is an error message.
          It has two lines.
```

##### <span id="9.1.3.1.4">9.1.3.1.4</span> 状况报告中的`<Tab>`

因为报告消息中的缩进可能被左移或右移, 使用版标准字符`<Tab>`时需要特别注意.
除非实现描述了它在这个上下文中行为, 否则应该避免使用.

##### <span id="9.1.3.1.5">9.1.3.1.5</span> 状况报告中的涉及函数

涉及函数名称通常不应该出现在报告消息中. 调试器会在必要时显示这个信息.

### <span id="9.1.4">9.1.4</span> 发出和处理状况

状况系统中的操作依赖于从最近到最远排序的活跃的可应用处理器.

每个处理器与一个类型描述符关联, 这个类型描述符必须指定类型`condition`的一个子类型.
如果一个状况的类型是一个处理器关联的类型描述符指定的类型, 称 **这个处理器可用于这个状况**.

使用`handler-bind`(或者在`handler-bind`上的抽象, 例如`handler-case`或`ignore-errors`), 建立活跃处理器.

活跃处理器可以在其它活跃处理器的动态作用域中建立.
在程序执行中的任意一点, 有一个活跃处理器的集合.
当发出一个状况时, 从这个集合中选择与这个状况关联的最近的活跃的可应用的处理器.
给定一个状况, 活跃的可应用的处理器的相近性顺序是由下述两个规则定义的:

1. 活跃处理器集合H1和H2, 如果建立H1中处理器时, H2中处理器是活跃的, 则H1中每个处理器与H2中每个处理器相比, 更近.
2. h1和h2是使用同一个形式建立的两个个可应用的活跃的处理器, 如果在形式中h1在h2左边家里, 则h1比h2更近.

一旦在处理器绑定形式(例如`handler-bind`或`handler-case`)中选择了一个处理器, 在后续的发出过程中, 这个形式中的所有处理器变为不活跃的.
当被选择的处理器运行时, 这个形式建立的其它处理器都不是活跃的. 即, 如果这个处理器拒绝处理状况, 这个形式建立的其它处理器不可为调用.

下图展示了与处理状况相关的操作符:

<span id="Figure9-4">图 9-4. 与处理状况相关的操作符.</span>

``` lisp
handler-bind
handler-case
ignore-errors
```

#### <span id="9.1.4.1">9.1.4.1</span> 发出

当状况被发出时, 会调用最近的可应用的活跃的处理器.
有时处理器会通过简单返回而不转移控制拒绝处理. 在这种情况中, 下一个最近的可应用的活跃的处理器被调用.

如果发出了一个没有可应用的处理器的状况, 或者所有可应用的处理器拒绝处理, 这个状况未被处理.

不管发出的状况的类型, 如果是未处理的, 函数`cerror`和`error`会调用交互式状况处理器(调试器)而不是返回.
与之相反, 不管发出的状况的类型, 如果是未处理的, 函数`signal`返回`nil`.

变量`*break-on-signals*`可用于在开始发出过程之前进入调试器.

下图展示了与发出状况相关的已定义名称.

<span id="Figure9-5">图 9-5. 与发出状况相关的已定义名称.</span>

``` lisp
*break-on-signals*
cerror
error
signal
warn
```

##### <span id="9.1.4.1.1">9.1.4.1.1</span> 重新发出状况

在发出特定状况对象过程的动态extent时, 允许再次发出同一个状况对象, 当且仅当两种情形中表达式的场景相同.

例如, 一个处理器可以合法的发出作为参数的状况对象, 允许外部处理器处理这个状况(这种处理器有时称为默认处理器). 这个动作被允许的原因是第二个发出过程处理的场景是同一场景.

另一方面, 在通过调用`signal`中断用户进程实现了异步键盘事件的实现中, 对不同场景, 不允许两个不同的异步键盘事件同一发出同一个状况对象.

#### <span id="9.1.4.2">9.1.4.2</span> 重启

交互式状况处理器只可以通过非本地的将控制转移到特别定义的重启器返回, 重启器可以是系统或用户代码设置的.
将控制转移到重启器被称为调用重启器.
与处理器类似, 活跃的重启器是动态建立的, 只能调用活跃的处理器.
活跃的重启器可以被用户从调试器或者被使用`invoke-restart`的程序调用.

重启器包含一个当它被调用是调用的函数、一个可选的可用于查找和调用重启器的名称、一组可选的调试器为用户提供的手动调用重启器的交互式信息.

重启器的名称被`invoke-restart`使用. 只在调试器中被调用的重启器不需要名称.

可以使用`restart-bind`、`restart-case`和`with-simple-restart`建立重启器.
一个重启器函数自身可以调用另一个在建立该函数所属的重启器时活跃的重启器.

通过`restart-bind`形式、`restart-case`形式或`with-simple-restart`形式建立的重启器, 有扩展形式执行周期的动态extent.

有相同名称的重启器可以依据如下规则按最远到最近排序:

1. 如果当活跃重启器集R1中重启器建立时, 重启器集R2中重启器都是活跃的, 则R1中每个重启器比R2中每个重启器近;
2. r1和r2是由同一个形式建立的相同名称的活跃处理器, 如果在形式中r1在r2左边定义, 则r1比r2近.

如果一个重启器被调用, 但没有转移控制, 则调用重启器的函数(`invoke-restart`或`invoke-restart-interactively`)返回重启器函数的结果值.

##### <span id="9.1.4.2.1">9.1.4.2.1</span> 重启的交互式使用

对于交互式处理, 需要重启器的两类信息: 报告函数和交互函数.

报告函数被诸如调试器的称为使用, 以表示重启器将采取的动作的描述.
报告函数通过`restart-bind`的关键字参数`:report-function`或`restart-case`的关键字参数`:report`指定和建立.

交互式函数, 可以使用`restart-bind`的关键字参数`:interactive-function`或`restart-case`的关键字参数`:interactive`指定; 用于注入调试器交互式调用重启器时, 已生成一个恰当的传递参数列表.

`invoke-restart`调用最近建立的重启器, 该重启器的名称与其第一个传递参数相同.
如果重启器被调试器交互式的调用, 没有转移控制但返回值, 调试器在这些值上的详细动作是依赖于实现的.

##### <span id="9.1.4.2.2">9.1.4.2.2</span> 重启的接口

一个重启器有功能性接口, 例如`abort`、`continue`、`muffle-warning`、`store-value`和`use-value`.
它们是内部使用`find-restart`和`invoke-restart`的常规函数, 但有与它们操作的重启器相同的名称.

下图展示了与重启器相关的已定义名称.

<span id="Figure9-6">图 9-6. 与重启器相关的已定义名称.</span>

``` lisp
abort
compute-restarts
continue
find-restart
invoke-restart
invoke-restart-interactively
muffle-warning
restart-bind
restart-case
restart-name
store-value
use-value
with-simple-restart
```

##### <span id="9.1.4.2.3">9.1.4.2.3</span> 重启的测试

每个重启器有关联的测试, 它是有一个传递参数(一个状况或`nil`)的函数, 在重启器应该在当前场景中可见时返回true.
这个测试可以用`restart-bind`的选项`:test-function`或`restart-case`的选项`:test`创建.

##### <span id="9.1.4.2.4">9.1.4.2.4</span> 关联重启与状况

重启器可以与状况关联, 用`with-conditon-restarts`显式的关联, 或者用`restart-case`隐式的关联. 这种关联有动态extent.

单个重启器可以同时与多个状况关联. 单个状况可以同时与多个重启器关联.

与特定状况关联的活跃重启器, 可以通过调用诸如`find-restart`的函数获得, 将状况作为状况传递参数.
不关心是否有关联的状况时, 可以使用这种不带状况传递参数或状况传递参数为`nil`, 获取活跃的重启器.

### <span id="9.1.5">9.1.5</span> 断言

在键匹配、形式求值和类型上条件的发出状况, 使用断言操作符处理的.
下图展示了与断言相关的操作符.

<span id="Figure9-7">图 9-7. 与断言相关的操作符.</span>

``` lisp
assert
ccase
check-type
ctypecase
ecase
etypecase
```

### <span id="9.1.6">9.1.6</span> 备注: 状态系统的背景

本节描述的抽象概念的背景信息见 **Exceptional Situations in Lisp**[^1].
这个文章的细节不与本文档绑定, 但可以为理解这些材料建立概念基础提供帮助.

[^1]: https://www.nhplace.com/kent/Papers/Exceptional-Situations-1990.html

## <span id="9.2">9.2</span> 状况的字典

见[状况的字典](../Dictionary#9.2).
