# Common Lisp Terminology

## 通用

### 注释

``` lisp
;;;; 文件头注释

;;; 段落注释, 应用到接下来的一大段代码上
(defun foo (x)
  (dotimes (i x)
    ;; 应用到接下来的代码上
    (some-function-call)
    (another i) ; 行中注释
    (and-another)
    (baz)))

```


### 属性表

property list, plist

``` lisp
(list :a 1 :b 2 :c 3)
```

## S-表达式

Common Lisp定义了两个黑箱:

- 读取器: 定义了如何将文本转化为称为S-表达式的Lisp对象
- 求值器: 定义了一种构建在S-表达式之上的Lisp形式(form)的语法

S-表达式的基本元素是列表(list)和原子(atom).

原子有:

- 数字

``` lisp
123     ; 整数
3/7     ; 比值
1.0     ; 浮点数
1.0e0
1.0d0   ; 双精度浮点数
1.0e-4
+42
-42
-1/4
-2/8
246/2
```

- 字符串

``` lisp
"foo"
"fo\o"    ; "foo"
"fo\\o"
"fo\"o"
```

- 名字
由称为符号(symbol)的对象表示

不使用`\`或`||`转义时, 不允许出现在名字中的符号有:
``` lisp
() [] " ' ` , : ? \ |
```

读取器读取名字时, 将所有名字中未转义的字符都转换为等价的大写形式:

``` lisp
foo     ; FOO
Foo     ; FOO
FOO     ; FOO
\f\o\o  ; foo
|foo|   ; foo
```

## Lisp形式(form)

在读取器将本文转换为S-表达式之后, 这些S-表达式随后可以作为Lisp形式被求值.

Common Lisp的**求值规则**定义了第二层的语法来检测哪些S-表达式可以看作Lisp形式.

合法的Lisp形式:

(1) 原子: 非列表, 空列表

(1.1) 符号
在作为Lisp形式被求值时会被视为一个变量名, 并且会被求值成该变量的当前值.

(1.2) 所有其他原子
自求值对象: 数字, 字符串, T, NIL, 关键字符号

(2) 以符号开始的列表形式

(2.1) 函数调用形式(function call form)

``` lisp
(function-name argument*)
```

(2.2) 特殊形式(special form)

25个特殊操作符: HyperSpec-7-0/HyperSpec/Body/03_ababa.htm

``` lisp
block      let*                  return-from
catch      load-time-value       setq
eval-when  locally               symbol-macrolet
flet       macrolet              tagbody
function   multiple-value-call   the
go         multiple-value-prog1  throw
if         progn                 unwind-protect
labels     progv
let        quote
```

``` lisp
(if test-form then-form [ else-form ])
(quote (+ 1 2)) ; '(+ 1 2)
(let ((x 10)) x)
```

(2.3) 宏形式(macro form)

宏给语言用户提供了一种语法扩展方式.

宏是一个**以S-表达式为参数的函数**, 返回一个Lisp形式(称为展开式expansion), 然后对其求值并用该值取代宏形式.

宏形式的求值过程: (a) 宏形式的元素不经求值即被传递到宏函数中; (b) 宏函数返回的形式按照正常的求值规则进行求值.

使用函数`COMPILE-FILE`编译源文件时, 文件中所有宏形式将被递归展开, 直到代码中只包含函数调用形式和特殊形式.

这些无宏的代码随后被编译成FASL文件, 可被函数`LOAD`加载. 编译后的代码直到文件被加载时才会被执行.

## 真假值

`NIL`是唯一的假值, 其他所有的都是真值. `T`是标准的真值.

`NIL`是唯一一个即是原子又是列表的对象, 其还用来表示空列表`()`.

``` lisp
nil () 'nil '()
t 't
```

### 等价谓词

特定于类型的等价谓词:

``` lisp
=         ; 比较数字
CHAR=     ; 比较字符
...
```

通用的等价谓词:


#### `EQ`

> Returns true if its arguments are the same, identical object; otherwise, returns false.

测试对象标识, 只有当两个对象相同时才是`EQ`等价的.

``` lisp
> (eq 1 1)
T
> (eq 1 1.0)
NIL
> (eq "a" "a")
NIL
> (eq '(1) '(1))
NIL
```

#### `EQL`

> The value of eql is true of two objects, x and y, in the folowing cases:
>
> 1. If x and y are eq.
> 2. If x and y are both numbers of the same type and the same value.
> 3. If they are both characters that represent the same character.
> Otherwise the value of eql is false.

> If an implementation supports positive and negative zeros as distinct values, then (eql 0.0 -0.0) returns false. Otherwise, when the syntax -0.0 is read it is interpreted as the value 0.0, and so (eql 0.0 -0.0) returns true.

与`EQ`相似, 之外可以保证当相同类型的两个对象表示相同的数字或字符值时, 它们是等价的.

``` lisp
> (eql 1 1)
T
> (eql 1 1.0)
NIL
> (eql "a" "a")
NIL
> (eql '(1) '(1))
NIL
```

#### `EQUAL`

> Returns true if x and y are structurally similar (isomorphic) objects. Objects are treated as follows by equal.
>
>> Symbols, Numbers, and Characters
>
> equal is true of two objects if they are symbols that are eq, if they are numbers that are eql, or if they are characters that are eql.
>
>> Conses
>
> For conses, equal is defined recursively as the two cars being equal and the two cdrs being equal.
>
>> Arrays
>
> Two arrays are equal only if they are eq, with one exception: strings and bit vectors are compared element-by-element (using eql). If either x or y has a fill pointer, the fill pointer limits the number of elements examined by equal. Uppercase and lowercase letters in strings are considered by equal to be different.
>
>> Pathnames
>
> Two pathnames are equal if and only if all the corresponding components (host, device, and so on) are equivalent. Whether or not uppercase and lowercase letters are considered equivalent in strings appearing in components is implementation-dependent. pathnames that are equal should be functionally equivalent.
>
>> Other (Structures, hash-tables, instances, ...)
>
> Two other objects are equal only if they are eq.
>

and

> equal does not descend any objects other than the ones explicitly specified above.

> The next figure summarizes the information given in the previous list. In addition, the figure specifies the priority of the behavior of equal, with upper entries taking priority over lower ones.

```
Type          Behavior
number        uses eql
character     uses eql
cons          descends
bit vector    descends
string        descends
pathname      ``functionally equivalent''
structure     uses eq
Other array   uses eq
hash table    uses eq
Other object  uses eq
```

相比`EQL`的宽松之处在于, 将在递归上具有相同结构和内容的列表视为等价.

含相同字符的字符串是等价的.

``` lisp
> (equal 1 1)
T
> (equal 1 1.0)
NIL
> (equal "a" "a")
T
> (equal "a" "A")
NIL
> (equal '(1) '(1))
T
> (equal '(1) '(1.0))
NIL
```

#### `EQUALP`

> Returns true if x and y are equal, or if they have components that are of the same type as each other and if those components are equalp; specifically, equalp returns true in the following cases:
>
>> Characters
>
> If two characters are char-equal.
>
>> Numbers
>
> If two numbers are the same under =.
>
>> Conses
>
> If the two cars in the conses are equalp and the two cdrs in the conses are equalp.
>
>> Arrays
>
> If two arrays have the same number of dimensions, the dimensions match, and the corresponding active elements are equalp. The types for which the arrays are specialized need not match; for example, a string and a general array that happens to contain the same characters are equalp. Because equalp performs element-by-element comparisons of strings and ignores the case of characters, case distinctions are ignored when equalp compares strings.
>
>> Structures
>
> If two structures S1 and S2 have the same class and the value of each slot in S1 is the same under equalp as the value of the corresponding slot in S2.
>
>> Hash Tables
>
> equalp descends hash-tables by first comparing the count of entries and the :test function; if those are the same, it compares the keys of the tables using the :test function and then the values of the matching keys using equalp recursively.

and

> equalp does not descend any objects other than the ones explicitly specified above. The next figure summarizes the information given in the previous list. In addition, the figure specifies the priority of the behavior of equalp, with upper entries taking priority over lower ones.

```
Type          Behavior
number        uses =
character     uses char-equal
cons          descends
bit vector    descends
string        descends
pathname      same as equal
structure     descends, as described above
Other array   descends
hash table    descends, as described above
Other object  uses eq
```

与`EQUAL`类似, 此外比较字符串等价性时忽略大小写的区别.

只要数字标识相同数学意义上的值, 在`EQUALP`下是等价的.

由`EQUALP`等价的元素所组成的列表也是`EQUALP`等价的.

``` lisp
> (equalp 1 1)
T
> (equalp 1 1.0)
T
> (equalp "a" "a")
T
> (equalp "a" "A")
T
> (equalp '(1) '(1))
T
> (equalp '(1) '(1.0))
T
```

## 函数

### 函数的参数

``` lisp
(defun name (parameter*)
  "Optional documentation string."
  body-form*)
```

文档字符串可以用`DOCUMENTATION`函数获取.

主体由任意数量的Lisp表达式构成, 在函数被调用时依次求值, 最后一个表达式的值作为整个函数的值返回.

`RETURN-FROM`特殊操作符可用于从函数的任意位置立即返回.

#### 可选形参: `&optional`

``` lisp
; 必要形参: a, b
; 可选形参: c, d
(defun foo (a b &optional c d) (list a b c d))
```

#### 形参默认值

``` lisp
; 指定形参默认值: 将形参名替换成一个含有名字跟一个表达式的列表
(defun foo (a &optional (b 10)) (list a b))

; 默认值表达式可以引用早先出现在形参列表中的形参
(defun make-rectangle (width &optional (height width)))

; -supplied-p: 区分形参值是被调用者明确指定还是使用了默认值
(defun foo (a b &optional (c 3 c-supplied-p))
  (list a b c c-supplied-p))
```

#### 剩余形参: `&rest`

在`&rest`之后包括一组形参, 实参作为一个列表传入.

``` lisp
(defun foo (&rest args) args)
(defun format (stream string &rest values) ...)
```

#### 关键字形参: `&key`

- 在任何必要的`&optional`和`&rest`形参之后, 可以加上`&key`和任意数量的关键字形参标识符.
- 关键字是以`:`开始的名字, 被自动定义为自求值常量.
- 可按任意顺序传递实参.
- 关键字形参也可以提供一个默认值形式和一个`supplied-p`变量名.


``` lisp
(defun foo (a b c) (list a b c))
(defun foo (&key a b c) (list a b c))
(foo :a 1 :b 2 :c 3)
(foo :c 3 :b 2 :a 1)
; 未给某个关键字形参传递实参, 该形参的值为NIL
(foo :a 1 :c 3) ; b = NIL
(foo)
 ; 将简单的名称替换为形参名/默认值/supplied-p形参
(defun foo (&key a (b 20) (c 30 c-p)) (list a b c c-p))
```

#### 混合使用不同的形参类型

声明顺序: 必要形参 -> 可选形参 -> 剩余形参 -> 关键字形参.

应该避免使用的组合:

- `&optional`, `&key`
- `&rest`, `&key`


### 函数返回值

- 最后一个表达式的值: Common Lisp中函数自动返回其最后求值的那个表达式的值.
- 使用`RETURN-FROM`特殊操作符: 立即以任何值从函数中间返回.

`RETURN-FROM`的第一个参数是其想要返回的代码块名, 该名字不被求值因此无需引用.

``` lisp
(defun foo (n)
  (dotimes (i 10)
    (dotimes (j 10)
      (when (> (* i j) n)
        (return-from foo (list i j))))))
```

### 作为数据的函数

#### 函数对象

使用`DEFUN`定义一个函数时, 创建一个新的函数对象, 并赋予其一个名字.

`FUNCTION`特殊操作符用来获取一个函数对象.

``` lisp
> (defun foo (x) (* 2 x))
FOO
> (function foo)
#<FUNCTION FOO>
> #'foo
#<FUNCTION FOO>
```

`#'`: 是"获取函数, 其名称如下"的简称

``` lisp
; evenp函数: 参数是偶数时返回真
(remove-if-not #'evenp '(1 2 3 4 5))
```

通过函数对象调用函数:

- `FUNCALL`函数

用于在编写代码时已知道需要传递给函数的实参时. 第一个实参是被调用的函数对象, 其余的实参被传递到该函数中.

``` lisp
> (funcall #'foo 1)
2
```

``` lisp
> (defun plot (fn min max step)
  (loop for i from min to max by step do
    (loop repeat (funcall fn i) do (format t "*"))
    (format t "~%")))
PLOT
> (plot #'exp 0 4 1/2)
*
**
***
*****
********
*************
*********************
**********************************
*******************************************************
NIL
```

- `APPLY`函数

第一个参数是一个函数对象, 之后预期一个列表而非单独的实参.

``` lisp
; plot-data: (fn min max step)
(apply #'plot plot-data)
```

接受孤立(loose)的实参, 只要最后一个参数是列表.


``` lisp
; plot-data: (min max step)
; #'exp为孤立的实参
(apply #'plot #'exp plot-data)
```

#### 函数作为参数

``` lisp
(defun select (selector-fn)
  (remove-if-not selector-fn *db*))
```

#### 返回函数

``` lisp
(defun artist-selector (artist)
  #'(lambda (cd) (equal (getf cd :artist) artist)))
```

### 匿名函数

`LAMBDA`表达式:

``` lisp
(lambda (parameters) body)
```

将`LAMBDA`表达式视为一种特殊类型的函数名:

``` lisp
> (funcall #'(lambda (x y) (+ x y)) 2 3)
5
> ((lambda (x y) (+ x y)) 2 3)
5

> (plot #'(lambda (x) (* 2 x)) 0 10 1)

**
****
******
********
**********
************
**************
****************
******************
********************
NIL
```

## 变量

Common Lisp中的变量是一些可以保存值的命名位置.

动态类型的, 强类型: 变量可以保存任何类型的值, 并且这些值带有可用于运行时类型检查的类型信息.

命名约定使用连字符`-`. 全局变量以`*`开始和结尾; 常量名以`+`开始和结尾. 语言标准定义的名字只能使用: `A-Z * + - / 1 2 < = > &`

### 值与引用

Common Lisp中所有的值都是对象的引用.

将一个变量赋予新值, 就会改变该变量所指向的对象, 而对之前被引用的对象没有影响.

如果一个变量保存了对一个可变对象的引用, 可以通过该引用来修改此对象, 这种改动将应用于任何带有相同对象引用的代码.

### 引入新变量

- 函数形参

每当函数被调用时, Lisp会创建新的 **绑定** 来保存由函数调用者传递的实参.

**绑定** 表示变量在运行时的存在. 单个变量甚至可以同时带有多种绑定, 例如递归函数的形参会在每一次函数调用中被重新绑定.

函数形参也可以保存对象引用: (1) 在函数体内为函数形参赋予新值, 不会影响到该函数的另一个调用所创建的绑定; (2) 如果改变了传递给函数的可变对象, 这些改动会被调用者看到.

- `LET`特殊操作符

`LET`形式:

``` lisp
(let (variable*)
  body-form*)
```

`variable`是一个 **变量初始化形式**, 每个初始化形式是: (1) 含有变量名和初始值形式的列表; (2) 简单的变量名.

``` lisp
(let ((x 10) (y 20) z)
  ...)
```

`LET`形式被求值时, 所有的初始化形式将首先被求值, 然后创建出新的绑定, 并在形式体被执行之前, 这些绑定将初始化到适当的初始值上.

在`LET`形式体中, 变量名将引用新创建的绑定. 在`LET`形式体执行结束后, 这些变量名将重新引用在执行`LET`之前所引用的内容, 如果有的话.

形式体中最后一个表达式的值作为`LET`形式的值返回.

由`LET`所引入的变量将在每次进入`LET`时被重新绑定.

- `LET*`

与`LET`的区别是:
在`LET`形式中, 被绑定的变量名只能用在`LET`的形式体(`body-form`)中.
在`LET*`形式中, 每个变量的初始化形式都可以引用在变量列表中早先引入的变量.

``` lisp
(let* ((x 10)
        (y (+ x 10))) ; 引用x
  (list x y))
```

等价的使用嵌套`LET`的方法:

``` lisp
(let ((x 10))
  (let ((y (+ x 10))))
    (list x y))
```


### 绑定形式

函数形参和`LET`变量的作用域被限定在引入改变量的形式之中, 该形式(即函数定义或`LET`)被称为 **绑定形式**.

词法变量和动态变量使用两种略有不同的 **作用域机制**, 但两者的作用域都被界定在绑定形式之内.

如果 **嵌套** 了引入同名变量的绑定形式, 则最内层的变量绑定将 **覆盖** 外层的绑定.

### 词法(lexical)变量和闭包

默认情况下, Common Lisp中所有的绑定形式都将引入 **词法作用域** 变量. 词法作用域的变量只能由那些在文本上位于绑定形式中的代码所引用.

``` lisp
(let ((count 0)) #'(lambda () (setf count (1+ count))))
```

讨论:

- 按词法作用域规则, `LAMBDA`形式中对`count`的引用是合法的
- 含有`count`引用的匿名函数作为`LET`形式的值返回, 可能会通过`FUNCALL`被不在`LET`形式作用域中的代码所调用
- 只要某处保存了一个对`LET`形式返回的函数对象的引用, 控制流进入`LET`形式时创建的`count`绑定被尽可能的保留下来
- 这个匿名函数被称为一个 **闭包** (closure), 因为它封闭包装了由`LET`创建的绑定


闭包捕捉的是 **绑定**, 而不是变量的值:

- 闭包可以访问它所闭合的变量的值
- 可以给该变量赋予在闭包被调用时不断变化的值

``` lisp
> (defparameter *fn* (let ((count 0)) #'(lambda () (setf count (1+ count)))))
*FN*
> (funcall *fn*)
1
> (funcall *fn*)
2
> (funcall *fn*)
3
```

- 单一闭包可以通过引用变量来闭合多个变量绑定
- 多个闭包可以捕捉相同的绑定

``` lisp
(let ((count 0))
  (list
    #'(lambda () (incf count))
    #'(lambda () (decf count))
    #'(lambda () count)))
```

### 动态(dynamic)变量

Common Lisp提供了两种创建全局变量的方法: `DEFVAR`, `DEFPARAMETER`.

``` lisp
(defvar *count* 0
  "Count of widgets made so far.")

(defparameter *gap-tolerance* 0.001
  "Tolerance to be allowed in widget gaps.")
```

区别:

- `DEFPARAMETER`总是将初始值赋给命名的变量
- `DEFVAR`只有当变量未定义时才这样做, 可以不带初始值来使用

动态变量与词法变量讨论:

- 当 **绑定了一个动态变量** 时, 在被绑定项上所创建的绑定, 替换了在绑定形式期间的对应全局绑定.
- 与词法绑定(只能被绑定形式的词法作用域之内的代码所引用)不同的是, 动态绑定可以被 **任何在绑定形式执行期间所调用的代码** 所引用
- 所有 **全局变量** 都是动态变量

例:

``` lisp
(let ((*standard-output* *some-other-stream*))
  (stuff))
```

- 在任何由于调用`stuff`而运行的代码中, 对`*standard-output*`的引用将使用由`LET`所创建的绑定
- 当`stuff`返回且程序控制流离开`LET`时, 这个对`*standard-output*`的新绑定将随之消失, 之后
- 对`*standard-output*`的引用将看到`LET`之前的绑定
- 在任何时刻, 最近建立的绑定会覆盖所有其他的绑定, 对变量的引用总是使用最近的绑定
- 当绑定形式返回时, 它们所创建的绑定会从栈上弹出, 暴露出前一个绑定

``` lisp
> (defvar *x* 10)
*X*
> (defun foo () (format t "X: ~d~%" *x*))
FOO
> (foo)
X: 10
NIL
> (let ((*x* 20)) (foo))
X: 20
NIL
> (foo)
X: 10
NIL
```

``` lisp
> (defun bar ()
    (foo)
    (let ((*x* 20)) (foo))
    (foo))
BAR
> (bar)
X: 10
X: 20
X: 10
NIL
> (defun foo ()
    (format t "Before: ~d~%" *x*)
    (setf *x* (+ 1 *x*))
    (format t "After: ~d~%" *x*))
WARNING: redefining COMMON-LISP-USER::FOO in DEFUN
FOO
> (foo)
Before: 10
After: 11
NIL
> (foo)
Before: 11
After: 12
NIL
> (bar)
Before: 12
After: 13
Before: 20
After: 21
Before: 13
After: 14
NIL
> (foo)
Before: 14
After: 15
NIL
>
```

全局变量的 **超距作用**:

- 可以改变下游代码的行为
- 使得下游代码可以为栈的上一级所建立的绑定赋予一个新的值

`t`: 是`*standard-output*`的简称

`*query-io*`: 关联到当前终端的输入流的全局变量

#### 全局特殊的, 局部特殊的

由`DEFVAR`和`DEFPARAMETER`所定义的变量, 其名字被自动声明为 **全局特殊的**.

也可以将一个名字声明为 **局部特殊的**: 在一个绑定形式中将一个名字声明为特殊的, 则为该变量创建的绑定将是动态的而不是词法的. 其他代码可以局部的声明一个名字为特殊的, 从而指向该动态绑定. 见`DECLARE`, `SPECIAL`, `LOCALLY`.


### 常量

``` lisp
(defconstant name intial-value-form [documentation-string])
```

### 赋值

`SETF`宏: 为绑定赋予新值

`SETF`检查`place`上的形式, 并展开成适当的底层操作来修改那个位置. 当`place`是变量时, `SETF`展开成对`SETQ`特殊操作符的调用, 后者可以访问到词法和动态绑定.

``` lisp
(setf x 10)
```

`SETF`可以依次对多个位置赋值; 返回最近被赋予的值.

``` lisp
; (setf place value)

> (let (x) (setf x 1))
1
> (let (y) (setf y 2))
2
> (let (x y) (setf x 1 y 2))
2
```

为绑定赋予新值, 对改变量的任何其他绑定没有效果, 对赋值之前绑定上所保存的值也没有任何效果.

``` lisp
> (defun foo (x) (setf x 10)) ; 为绑定赋予新值
FOO
> (let ((y 20))
  (foo y)
  (print y))

20
20
```

广义赋值: 见 **复合数据结构**

其他修改位置的方式:

- 修改宏(modify macro): `INCF`, `DECF`, `PUSH`, `POP`, `PUSHNEW`, `ROTATEF`, `SHIFTF`

``` lisp
> (let ((x 1) (y 2))
  (rotatef x y)
  (list x y))
(2 1)

> (let ((x 1) (y 2))
  (print (shiftf x y 10))
  (list x y))

1
(2 10)
```

## 数据类型

Common Lisp提供了内置支持的数据类型:

- 数字: 整数, 浮点数和复数
- 字符
- 字符串: 字符的序列
- 数组: 包括多维数组
- 列表
- 哈希表
- 输入输出流
- 一种可移植的表示文件名的抽象

### 数字, 字符和字符串

Common Lisp中整数可以是任意大, 两个整数相除得到一个确切的壁纸而非截断的值.

#### 字面数值

整数:

``` lisp
> 123
123
> +123
123
> -123
-123
> 123.
123
```
比值:

``` lisp
> 2/3
2/3
> -2/3
-2/3
> 4/6
2/3
> 6/3
2
```

进制: `#B`, `#b`, `#O`, `#o`, `#X`, `#x`, `#nR`, `#nr`

`#nR`中`n`是十进制书写的, 从2到36的进制数.

``` lisp

> #b10101
21
> #b1010/1011
10/11
> #o777
511
> #xDADA
56026
> #36rABCDEFGHIJKLMNOPQRSTUVWXYZ
8337503854730415241050377135811259267835
```

浮点数: 短型(`s`, `S`), 单精度(`f`, `F`), 双精度(`d`, `D`), 长型(`l`, `L`).

`e`, `E`: 表示默认方式(单浮点数).

没有指数标记的数字以默认方式读取, 必须含有一个`.`且后面至少由一个数字. 其中数字总是以十进制表示.

``` lisp
> 1.0
1.0
> 1e0
1.0
> 1d0
1.0d0
> 123.0
123.0
> 123e0
123.0
> 0.123
0.123
> .123
0.123
> 123e-3
0.123
> 123E-3
0.123
> 0.123e20
1.23e19
> 123d23
1.23d25
```

复数: `#c`, `#C`, 后跟由两个实数组成的列表.

``` lisp
> #c(2 1)
#C(2 1)
> #c(2/3 3/4)
#C(2/3 3/4)
> #c(2 1.0)
#C(2.0 1.0)
> #c(2.0 1.0d0)
#C(2.0d0 1.0d0)
> #c(1/2 1.0)
#C(0.5 1.0)
> #c(3 0)
3
> #c(3.0 0.0)
#C(3.0 0.0)
> #c(1/2 0)
1/2
> #c(-6/3 0)
-2
```

### 集合

> TODO(zhoujiagen)

### 列表

> TODO(zhoujiagen)

### 文件

> TODO(zhoujiagen)

## 宏

### `'`
阻止求值

``` lisp
> '(1 2 3)
(1 2 3)
```

### `` ` ``

阻止求值和部分求值

``` lisp
> `(1 2 3)
(1 2 3)
> `(1 2 (+ 1 2))
(1 2 (+ 1 2))
; 部分求值
> `(1 2 ,(+ 1 2))
(1 2 3)
```

### `,@`

将后续的表达式的值嵌入到其外围的列表中, 这个表达式必须求值成一个列表.

``` lisp
> `(and ,(list 1 2 3))
(AND (1 2 3))
> `(and ,@(list 1 2 3))
(AND 1 2 3)
> `(and ,@(list 1 2 3) 4)
(AND 1 2 3 4)
```

## 重新审视面向对象: 广义函数, 类

> TODO(zhoujiagen)

## 状况(codition)和重启(restart)

> TODO(zhoujiagen)

## 包和符号

> TODO(zhoujiagen)

## `LOOP`

> TODO(zhoujiagen)

## 一些函数

### list

``` lisp
(list 1 2 3)
(list :a 1 :b 2 :c 3)
```

### getf

``` lisp
(getf (list :a 1 :b 2 :c 3) :a)
```

### format

``` lisp
; t是*standard-output*的简称
; ~a是美化指令, 消耗一个实参
; ~t是制表指令
; ~{~}指令消耗一个列表实参
; ~%是换行指令
; cd形式为(list :title title :artist artist :rating rating :ripped ripped)
(format t "~{~a: ~10t~a~%~}~%" cd)
 ; force-output: 确保在打印提示信息之前不会等待换行
```

### read-line

``` lisp
; read-line: 读取字符串, 不包括换行符
(defun prompt-read (prompt)
  (format *query-io* "~a: " prompt)
  (force-output *query-io*)
  (read-line *query-io*))
```

### parse-integer

``` lisp
; 字符串 => 整数, 失败直接报错
(parse-integer (prompt-read "Rating"))
; 失败不报错, 返回NIL
(parse-integer (prompt-read "Rating") :junk-allowed t)
; y-or-n-p: yes or no input
(y-or-n-p "Ripped [y/n]: ")
```

### mapcar

``` lisp
; mapcar: 在列表每个元素上调用函数, 返回新列表
(defun update (selector-fn &key title artist rating (ripped nil ripped-p))
  (setf *db*
    (mapcar
      #'(lambda (row)
          (when (funcall selector-fn row)
            (if title (setf (getf row :title) title))
            (if artist (setf (getf row :artist) artist))
            (if rating (setf (getf row :rating) rating))
            (if ripped-p (setf (getf row :ripped) ripped)))
            row) *db*)))
```

### reverse

``` lisp
(reverse '(1 2 3)) ; (3 2 1)
```

## 一些宏

### defun: 定义函数

``` lisp
(defun make-cd (title artist rating ripped)
  (list :title title :artist artist :rating rating :ripped ripped))
(make-cd "Roses" "Kathy Mattea" 7 t)
```

### defvar: 全局变量

``` lisp
(defvar *db* nil)
```

### push: 添加项

``` lisp
(defun add-record (cd) (push cd *db*))
```

### dolist: 遍历列表

``` lisp
(defun dump-db ()
  (dolist (cd *db*)
    (format t "~{~a: ~10t~a~%~}~%" cd)))
```

### or

``` lisp
; or: 返回第一个非空的值, 如果全为空值则返回空值
(or (parse-integer (prompt-read "Rating") :junk-allowed t) 0)
```

### loop: 循环

``` lisp
; loop: 循环执行表达式体
; return: 退出循环
(defun prompt-for-cd ()
  (make-cd
    (prompt-read "Title")
    (prompt-read "Artist")
    (or (parse-integer (prompt-read "Rating") :junk-allowed t) 0)
    (y-or-n-p "Ripped [y/n]: ")))
(defun add-cds ()
  (loop (add-record (prompt-for-cd))
    (if (not (y-or-n-p "Another? [y/n]: ")) (return))))
```

### with-open-file, with-standard-io-syntax

``` lisp
; with-open-file: 打开一个文件, 将文件流绑定到一个变量上, 执行一组表达式后关闭文件
; print函数: 将Lisp对象打印成可以被Lisp读取器读回的形式
; with-standard-io-syntax: 确保影响PRINT行为的特定变量可以被设置为它们的标准值
(defun save-db (filename)
  (with-open-file (out filename
                  :direction :output
                  :if-exists :supersede)
    (with-standard-io-syntax
      (print *db* out))))
```

### setf: 赋值操作符

``` lisp
; read函数: Lisp读取器读取形式
(defun load-db (filename)
  (with-open-file (in filename)
    (with-standard-io-syntax
      (setf *db* (read in)))))
```

### defmacro: 定义宏

``` lisp
(defmacro backwards (expr) (reverse expr))
(backwards ("hello, world" t format))
```

### macroexpand-1: 查看宏展开的形式

``` lisp
; macroexpand-1
> (macroexpand-1 '(backwards ("hello, world" t format)))
(FORMAT T "hello, world")
T
```


## TODO

``` lisp
and
if
when
```
