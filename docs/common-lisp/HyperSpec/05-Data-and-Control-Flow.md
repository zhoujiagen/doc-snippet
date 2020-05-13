# 5. 数据和控制流

[TOC]

## <span id="5.1">5.1</span> 通用的引用

### <span id="5.1.1">5.1.1</span> 位置和通用的引用概述

==通用的引用== 是对形式的使用, 有时称为 ==位置==, 就像是一个可以读取和写入的变量.
位置的值是位置形式求值结果的对象.
位置的值可以使用`setf`修改.
Common lisp中未定义绑定一个位置的概念, 但实现允许扩展语言支持定义这个概念.

下图包含了使用`setf`的示例. 注意求值第二列中形式的返回值不一定与求值第三列的结果相同.
通常, `setf`形式的精确宏展开是没有保证的, 甚至可以是依赖于实现的; 可以保证的是展开是一个特定实现中可用的更新形式, 保留从左至右求值子形式, 求值`setf`的最终结果是被存储的值或一组值.

<span id="Figure5-1">图 5-1. setf的示例.</span>

``` lisp
访问函数            更新函数           使用setf更新
x                 (setq x datum)    (setf x datum)
(car x)           (rplaca x datum)  (setf (car x) datum)
(symbol-value x)  (set x datum)     (setf (symbol-value x) datum)
```

下图展示了与位置和通用的引用相关的操作符:

<span id="Figure5-2">图 5-2. 与位置和通用的引用相关的操作符.</span>

``` lisp
assert
ccase
ctypecase
decf
define-modify-macro
define-setf-expander
defsetf
get-setf-expansion
getf
incf
pop
psetf
push
remf
rotatef
setf
shiftf
```

上面的操作符中一部分操作位置, 一些操作setf展开器.
setf展开可以从任意位置导出.
使用`defsetf`和`define-setf-expander`定义性的setf展开器.

#### <span id="5.1.1.1">5.1.1.1</span> 子形式到位置的求值

下面的规则应用在一个位置上的子形式的求值:

1. 在一个位置中的子形式的求值顺序, 是由`get-setf-expansion`返回的第一个值描述的顺序确定的. 对于该规范定义的所有位置(例如: `getf`, `ldb`...), 求值顺序是自左向右的. 当位置是由宏展开导出时, 这个规则在宏被展开以找到恰当的位置之后应用.<br><br>
  用`defmacro`或`define-setf-expander`定义的位置, 使用这个定义定义的求值顺序. 例如, 考虑```(defmacro wrong-order (x y) `(getf ,y ,x))```, 后面这个形式首先求值`place2`再求值`place1`, 因为这是它们在这个宏展开中求值顺序: `(push value (wrong-order place1 place2))`.<br>
2. 对操作位置的宏(`push`、`pushnew`、`remf`、`incf`、`decf`、`shiftf`、`rotatef`、`psetf`、`setf`、`pop`和用`define-modify-macro`定义的宏), 宏调用的子形式按从左至右的顺序求值一次, 位置的子形式按(1)中描述的顺序求值.<br><br>
  `push`、`pushnew`、`remf`、`incf`、`decf`、`shiftf`、`rotatef`、`psetf`、`pop`在修改任何位置之前求值所有子形式. `setf`(有多于两个传递参数)按顺序在每对上执行操作. 例如: `(setf place1 value1 place2 value2 ...)`, 子形式`place1`和`value1`被求值, `place1`指定的位置被修改以包含`value1`返回的值, 然后以类似的方式处理`setf`形式中剩余部分.<br>
3. 对`check-type`、`ctypecase`和`ccase`, 位置上子形式按(1)中描述方式求值一次, 但如果`check-type`中类型检查失败, 或`ctypecase`和`ccase`中没有分支, 则可能被再次求值.
4. 对`assert`, 通用的引用的求值顺序未描述.

规则2、3和4包括错有操作位置的标准宏.

##### <span id="5.1.1.1.1">5.1.1.1.1</span> 示例: 子形式到位置的求值

``` lisp
(let ((ref2 (list '())))
  (push (progn (princ "1") 'ref-1)
        (car (progn (princ "2") ref2))))
>>  12
=>  (REF1)
```

``` lisp
(let (x)
   (push (setq x (list 'a))
         (car (setq x (list 'b))))
    x)
=>  (((A) . B))
```

`push`首先求值```(setq x (list 'a)) => (a)```, 然后求值```(setq x (list 'b)) => (b)```, 之后修改最后一个值的car为`((a) . b)`

#### <span id="5.1.1.2">5.1.1.2</span> SETF展开

有时可以避免对位置上子形式求值多次或按错误的顺序求值.
对给定访问形式的setf展看可以表示为5个对象的有序集合:

- 临时变量的列表

  命名了临时变量的符号的列表, 这些临时变量被顺序绑定, 就像`let*`, 被绑定到值形式产生的值.

- 值形式的列表

  形式的列表(位置的子形式), 当这些形式被求值时, 产生对应的临时变量应该绑定到的值.

- 存储变量的列表

  命名了临时存储变量的符号的列表, 这些存储变量用于持有将被指派到位置的新值.

- 存储形式

  可以引用临时和存储变量的形式, 修改位置的值, 保证返回时它的值是存储变量的值, 这些值是正确的`setf`返回值.

- 访问形式

  可以引用临时变量的形式, 返回位置的值.

访问形式返回的值, 受存储形式执行的影响, 但这两个形式可能被多次求值.

可以通过`psetf`、`shitf`和`rotatef`并行的做多个`setf`. 因此, setf展开器必须每次生成新的临时和存储变量名称. 如何生成新名词见`gensym`.

对每个标准的访问器函数`F`, 除非在其它地方说明, 使用`F`形式作为`setf`位置是通过setf展开器或setf函数实现的是依赖于实现的. 同时, 名称`(setf F)`是否是found的, 是依赖于实现的.

##### <span id="5.1.1.2.1">5.1.1.2.1</span> 实例: SETF展开

下面是setf展开的构成内容的示例.

对变量`x`:

<span id="Figure5-3">图 5-3. 变量的setf展开的示例.</span>

``` lisp
()              ;list of temporary variables
()              ;list of value forms
(g0001)         ;list of store variables
(setq x g0001)  ;storing form
x               ;accessing form
```

对`(car exp)`:

<span id="Figure5-4">图 5-4. CAR形式的setf展开的示例.</span>

``` lisp
(g0002)                             ;list of temporary variables
(exp)                               ;list of value forms
(g0003)                             ;list of store variables
(progn (rplaca g0002 g0003) g0003)  ;storing form
(car g0002)                         ;accessing form
```

对`(subseq seq s e)`:

<span id="Figure5-5">图 5-5. SUBSEQ形式的setf展开的示例.</span>

``` lisp
(g0004 g0005 g0006)         ;list of temporary variables
(seq s e)                   ;list of value forms
(g0007)                     ;list of store variables
(progn (replace g0004 g0007 :start1 g0005 :end1 g0006) g0007)
                            ;storing form
(subseq g0004 g0005 g0006)  ; accessing form
```

在一些情况中, 如果位置的子形式本身也是一个位置, 需要展开这个子形式以计算出外部位置中展开中的值. 例如, `(ldb bs (car exp))`:

<span id="Figure5-6">图 5-6. LDB形式的setf展开的示例.</span>

``` lisp
(g0001 g0002)            ;list of temporary variables
(bs exp)                 ;list of value forms
(g0003)                  ;list of store variables
(progn (rplaca g0002 (dpb g0003 g0001 (car g0002))) g0003)
                         ;storing form
(ldb g0001 (car g0002))  ; accessing form
```

### <span id="5.1.2">5.1.2</span> 位置的种类

Common Lisp定义了一些位置的种类, 这一部分列举出它们.
实现和程序员可以扩展位置的种类.

#### <span id="5.1.2.1">5.1.2.1</span> 变量名称作为位置

词法变量或动态变量的名称, 可被用作位置.

#### <span id="5.1.2.2">5.1.2.2</span> 函数调用形式作为位置

函数形式可被用作位置, 如果它是下面类别中的一个:

- 函数调用形式的首元素是下图中任一函数的名称:

<span id="Figure5-7">图 5-7. 可以与setf一起使用的函数---1.</span>

``` lisp
aref
bit
caaaar
caaadr
caaar
caadar
caaddr
caadr
caar
cadaar
cadadr
cadar
caddar
cadddr
caddr
cadr
car
cdaaar
cdaadr
cdaar
cdadar
cdaddr
cdadr
cdar
cddaar
cddadr
cddar
cdddar
cddddr
cdddr
cddr
cdr
char
class-name
compiler-macro-function
documentation
eighth
elt
fdefinition
fifth
fill-pointer
find-class
first
fourth
get
gethash
logical-pathname-translations
macro-function
ninth
nth
readtable-case
rest
row-major-aref
sbit
schar
second
seventh
sixth
slot-value
subseq
svref
symbol-function
symbol-plist
symbol-value
tenth
third
```

在`subseq`情况中, 替换值必须是一个元素可能包含在`subseq`的序列传递参数中的序列, 但不一定要是与指定子序列的序列的类型相同. 如果替换值的长度不等于被替换的子序列的长度, 则较短的长度确定了被存储的元素的数量, 就像`replace`.


- 函数调用形式的首元素是由`defstruct`构造的选择器函数的名称. 这个函数名称必须引用全局函数定义, 而不是本地定义的函数.

- 函数调用形式的首元素是下图中的一个函数的名称, 传递给函数的参数是位置形式; 这种情况下, 新位置的值是应用提供的更新函数后的结果.

<span id="Figure5-8">图 5-8. 可以与setf一起使用的函数---2.</span>

``` lisp
函数名称        是位置的传递参数             使用的更新函数
ldb            second                    dpb
mask-field     second                    deposit-field
getf           first                     依赖于实现
```

当这些形式的setf展开时, 需要调用`get-setf-expansion`, 以确定内层嵌套的通用变量如何处理.

`get-setf-expansion`中的信息按下述方式使用:

`ldb`

在形式`(setf (ldb byte-spec place-form) value-form)`中, `place-form`引用的位置必须总是被读写; 注意更新的是`place-form`指定的通用变量, 而不是类型`integer`的对象.

因此, 这个setf应该生成执行这些步骤的代码:

1. 求值`byte-spec`(将它绑定到一个临时变量)
2. 绑定`place-form`到临时变量
3. 求值`value-form`(绑定它的值到存储变量)
4. 读取`place-form`
5. 写入`place-form`, 用步骤4中读到的值, 用步骤3中值替换后的值.

如果步骤3中求值`value-form`修改了`place-form`中发现的对象, 例如设置整数的不同比特位, 则由`byte-spec`表达的比特位修改是应用在该修改后的对象上的, 因为步骤4是在求值`value-form`之后执行的. 绑定临时变量所需的求值是在步骤1和2中完成的, 因此可以看到预期的自左向右求值顺序. 例如:

``` lisp
(setq integer #x69) =>  #x69
(rotatef (ldb (byte 4 4) integer)
         (ldb (byte 4 0) integer))
integer =>  #x96
;;; This example is trying to swap two independent bit fields
;;; in an integer.  Note that the generalized variable of
;;; interest here is just the (possibly local) program variable
;;; integer.
```

`mask-field`

与`ldb`本质上相同.

`getf`

在形式`(setf (getf place-form ind-form) value-form)`中, `place-form`引用的形式必须总是被读写; 注意到更新是应用在`place-form`指定的通用变量上的, 不一定是特定的属性列表.

因此, 这个setf应该生成执行这些步骤的代码:

1. 绑定`place-form`到临时变量
2. 求值`ind-form`(绑定到临时变量)
3. 求值`value-form`(绑定值到存储变量)
4. 读取`palce-form`
5. 写入`place-form`, 使用通过步骤2-4组合后的可能为新的属性列表值.(注意这个可能为新的属性列表的含义是, 之前的属性列表被破坏性重用, 或部分或全拷贝. 对这个可能为新的属性列表的处理必须像需要一个不同的副本存入通用变量中).

如果步骤3中求值`value-form`修改了`place-form`中发现的对象, 例如在列表中设置不同名称属性, 则由`ind-form`表达的对属性的修改是应用在该修改后的列表上, 因为步骤4是在求值`value-form`后执行的. 绑定临时变量所需的求值是在步骤1和2中完成的, 因此可以看到预期的自左向右求值顺序. 例如:


例如:

``` lisp
(setq s (setq r (list (list 'a 1 'b 2 'c 3)))) =>  ((a 1 b 2 c 3))
(setf (getf (car r) 'b)
      (progn (setq r nil) 6)) =>  6
r =>  NIL
s =>  ((A 1 B 6 C 3))
;;; Note that the (setq r nil) does not affect the actions of
;;; the SETF because the value of R had already been saved in
;;; a temporary variable as part of the step 1. Only the CAR
;;; of this value will be retrieved, and subsequently modified
;;; after the value computation.
```

#### <span id="5.1.2.3">5.1.2.3</span> VALUES形式作为位置

`values`形式可被用作位置, 它的每个子形式也是一个位置形式.

按下面的方式处理形式:

``` lisp
(setf (values place-1 ...place-n) values-form)
```

1. 每个内嵌位置的子形式按从左至右的顺序求值.
2. 求值`values-form`, 像使用`multiple-value-bind`一样将每个位置的首个存储变量绑定到它的返回值.
3. 如果任意位置的setf展开包含多个存储变量, 则额外的存储变量绑定到`nil`.
4. 每个位置的存储形式按从左至右的顺序求值.

`values`的setf展开中存储形式返回多值, 步骤2中存储变量的值.
即, 返回的值的数量与位置形式的数量相同. 返回值的数量可能比`values-form`生成的值多或少.

#### <span id="5.1.2.4">5.1.2.4</span> THE形式作为位置

`the`形式可被用作位置, 声明被转换为新值的形式, 作为结果的setf被分析. 例如:

``` lisp
(setf (the integer (cadr x)) (+ y 3))
```

的处理方式与下面的相同:

``` lisp
(setf (cadr x) (the integer (+ y 3)))
```

#### <span id="5.1.2.5">5.1.2.5</span> APPLY形式作为位置

必须支持这些包含`setf`的`apply`的使用:

- ```(setf (apply #'aref array subscript* more-subscripts) new-element)```
- ```(setf (apply #'bit array subscript* more-subscripts) new-element)```
- ```(setf (apply #'sbit array subscript* more-subscripts) new-element)```

在三种情况中, 数组中由`subsripts`和`more-subscripts`指定的元素(如果不是`setf`形式的以部分则被读取)被修改为有`new-element`给定的值. 函数名称(`aref`、`bit`或`sbit`)必须引用全局函数定义, 而不是本地定义的函数.

不需要支持其他标准函数, 但实现可以支持. 实现页可以支持实现定义的操作符.

如果在这里使用用户定义的函数, 下面的等价为真, 除了注意保持传递参数子形式从左至右求值:

``` lisp
(setf (apply #'name arg*) val)
==  (apply #'(setf name) val arg*)
```

#### <span id="5.1.2.6">5.1.2.6</span> SETF展开与位置

操作符有定义的setf展开器的复合形式可被用作位置.
这个操作符必须引用全局函数定义, 而不是本地定义的函数或宏.

#### <span id="5.1.2.7">5.1.2.7</span> 宏形式作为位置

宏形式可被用作位置, Common Lisp用`macroexpand-1`展开宏形式, 在原始位置处使用宏展开.
只在尝试了除将其展开为对`(setf reader)`函数调用的所有情况外, 尝试使用宏展开.

#### <span id="5.1.2.8">5.1.2.8</span> 符号宏作为位置

对已被建立为符号宏的符号的引用可被张用作位置.
在这种情况中, `setf`展开该引用, 然后分析作为结果的形式.

#### <span id="5.1.2.9">5.1.2.9</span> 其它复合形式作为位置

对任意操作符是符号`f`的复合形式, `setf`形式将其展开为对函数`(setf f)`的调用.
新构造的函数形式的第一个传递参数是新值, 剩余的传递参数是位置的剩余元素.
不管`f`或`(setf f)`被定义为本地函数、全局函数或不是函数, 总是执行这个展开. 例如:

``` lisp
(setf (f arg1 arg2 ...) new-value)
```

展开为与下面的形式效果和值向量:

``` lisp
(let ((#:temp-1 arg1)          ;强制正确的求值顺序
      (#:temp-2 arg2)
      ...
      (#:temp-0 new-value))
  (funcall (function (setf f)) #:temp-0 #:temp-1 #:temp-2...))
```

函数`(setf f)`必须返回它的第一个传递参数作为唯一的返回结果, 以保持`setf`的语义.

### <span id="5.1.3">5.1.3</span> 处理其它基于SETF的宏

对下图中的 **读-修改-写** 操作符, 和程序员用`define-modity-macro`定义的宏, 常规的按从左至右对传递参数进行求值的规则有一个例外.
按从左至右对传递参数形式求值, 对位置参数的处理是个例外, 在所有传递参数形式求值后、新值被计算出且被写入位置之前， 读取位置的旧值.

更具体的说, 这些操作符可被视为包含下述语法的形式:

``` lisp
(operator preceding-form* place following-form*)
```

对这个形式的求值处理如下:

1. 从左至右, 求值`preceding-forms`.
2. 求值`place`子形式, 按该位置的`setf`展开的第二个返回值指定的顺序.
3. 从左至右, 求值`following-form`.
4. 读`place`的旧值.
5. 计算新值.
6. 存储新值到`place`.

<span id="Figure5-9">图 5-9. 读-修改-写宏.</span>

``` lisp
decf
incf
pop
push
pushnew
remf
```

## <span id="5.2">5.2</span> 转移控制到退出点

当使用`go`、`return-from`或`throw`发起控制转移时, 为完成控制转移发生下面的事件.
注意, 对`go`, 退出点是`go`执行时运行的`tagbody`中的形式;
对`return-from`, 退出点是对应的`block`形式;
对`throw`, 退出点是相应的`catch`形式.

1. 其间的退出点之间被放弃(即它们的extent已结束, 通过它们尝试转移控制不再有效).
2. 其间的`unwind-protect`子句的清理子句被求值.
3. 其间的`special`变量的动态绑定、捕获标签、状况处理器和重启器被撤销.
4. 被调用的退出点的extend结束, 控制被传递给目标.

退出的extent被放弃, 是因为发出控制转移时立刻被结束. 即事件1在发起控制转移开始时发生.
如果尝试转移控制到动态extent已结束的退出点, 后果是未定义的.

事件2和3实际上是交叉执行的, 按与建立的顺序相反的顺序执行. 效果是, `unwind-protect`的清理子句看到相同的变量动态绑定, 进入`unwind-protect`时捕获标签可见.

事件4在转移控制结束时发生.

## <span id="5.3">5.3</span> 数据和控制流的字典

见[数据和控制流的字典](../Dictionary#5.3).
