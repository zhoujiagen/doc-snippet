# 1. 介绍

[TOC]

## 1.1 Scope, Purpose, and History

> TODO(zhoujiagen) 1.1 Scope, Purpose, and History

## 1.2 文档的组织

该文档使用的记号约定, 见[1.4 定义](#1.4).

标准符合性的信息, 见[1.5 标准符合性](#1.5).

语言扩展和子集的信息, 见[1.6 语言扩展](#1.6)和[语言子集](#1.7).

关于程序如何被Lisp reader解析的信息, 见[2 语法](02-Syntax.md).

关于程序如何被编译和执行的信息, 见[3 求值和编译](03-Evaluation-and-Compilation.md).

关于数据类型的信息, 见[4 类型和类](04-Types-and-Classes.md); 这一章没有定义所有的类型和类, 例如[12 数值](12-Numbers.md)中定义了数值类型. 图4-2给出了标准类型的完整列表.

关于通用的控制和数据流的信息, 见[5 数据和控制流](05-Data-and-Control-Flow.md)或[6 迭代](06-Iteration.md)

## 1.3 引用的出版物

- The Anatomy of Lisp, John Allen, McGraw-Hill, Inc., 1978.
- The Art of Computer Programming, Volume 3, Donald E. Knuth, Addison-Wesley Company (Reading, MA), 1973.
- The Art of the Metaobject Protocol, Kiczales et al., MIT Press (Cambridge, MA), 1991.
- ``Common Lisp Object System Specification,'' D. Bobrow, L. DiMichiel, R.P. Gabriel, S. Keene, G. Kiczales, D. Moon, SIGPLAN Notices V23, September, 1988.
- Common Lisp: The Language, Guy L. Steele Jr., Digital Press (Burlington, MA), 1984.
- Common Lisp: The Language, Second Edition, Guy L. Steele Jr., Digital Press (Bedford, MA), 1990.
- Exceptional Situations in Lisp, Kent M. Pitman, Proceedings of the First European Conference on the Practical Application of LISP (EUROPAL '90), Churchill College, Cambridge, England, March 27-29, 1990.
- Flavors: A Non-Hierarchical Approach to Object-Oriented Programming, Howard I. Cannon, 1982.
- IEEE Standard for Binary Floating-Point Arithmetic, ANSI/IEEE Std 754-1985, Institute of Electrical and Electronics Engineers, Inc. (New York), 1985.
- IEEE Standard for the Scheme Programming Language, IEEE Std 1178-1990, Institute of Electrical and Electronic Engineers, Inc. (New York), 1991.
- Interlisp Reference Manual, Third Revision, Teitelman, Warren, et al, Xerox Palo Alto Research Center (Palo Alto, CA), 1978.
- ISO 6937/2, Information processing---Coded character sets for text communication---Part 2: Latin alphabetic and non-alphabetic graphic characters, ISO, 1983.
- Lisp 1.5 Programmer's Manual, John McCarthy, MIT Press (Cambridge, MA), August, 1962.
- Lisp Machine Manual, D.L. Weinreb and D.A. Moon, Artificial Intelligence Laboratory, MIT (Cambridge, MA), July, 1981.
- Maclisp Reference Manual, Revision 0, David A. Moon, Project MAC (Laboratory for Computer Science), MIT (Cambridge, MA), March, 1974.
- ``NIL---A Perspective,'' JonL White, Macsyma User's Conference, 1979.
- Performance and Evaluation of Lisp Programs, Richard P. Gabriel, MIT Press (Cambridge, MA), 1985.
- ``Principal Values and Branch Cuts in Complex APL,'' Paul Penfield Jr., APL 81 Conference Proceedings, ACM SIGAPL (San Francisco, September 1981), 248-256. Proceedings published as APL Quote Quad 12, 1 (September 1981).
- The Revised Maclisp Manual, Kent M. Pitman, Technical Report 295, Laboratory for Computer Science, MIT (Cambridge, MA), May 1983.
- ``Revised^3 Report on the Algorithmic Language Scheme,'' Jonathan Rees and William Clinger (editors), SIGPLAN Notices V21, #12, December, 1986.
- ``S-1 Common Lisp Implementation,'' R.A. Brooks, R.P. Gabriel, and G.L. Steele, Conference Record of the 1982 ACM Symposium on Lisp and Functional Programming, 108-113, 1982.
- Smalltalk-80: The Language and its Implementation, A. Goldberg and D. Robson, Addison-Wesley Company, 1983.
- ``Standard LISP Report,'' J.B. Marti, A.C. Hearn, M.L. Griss, and C. Griss, SIGPLAN Notices V14, #10, October, 1979.
- Webster's Third New International Dictionary the English Language, Unabridged, Merriam Webster (Springfield, MA), 1986.
- XP: A Common Lisp Pretty Printing System, R.C. Waters, Memo 1102a, Artificial Intelligence Laboratory, MIT (Cambridge, MA), September 1989.

## <span id="1.4">1.4</span> 定义

### 1.4.1 标记约定

#### 1.4.1.1 字体说明

|符号|说明|
|:--|:--|
| name | 术语 |
| `name` | `COMMON-LISP`包中出现的符号, 代码 |

#### <span id="1.4.1.2">1.4.1.2</span> 修改的BNF语法

该规范使用扩展的BNF描述Common Lisp中宏形式和特殊形式的语法. 这一部分讨论BNF表达式的语法.

##### 1.4.1.2.1 粘接(Splicing)

最主要的扩展是: `[[O]]`. 这种形式的表达式在一组元素被粘接入一个较大结构时出现, 这些元素可以按任意顺序出现.

符号`O`表示一些数量的被粘接的句法元素的语法描述; 这一描述必须具有形式: `O1 | ... | Ol`, 其中每个`Oi`可以是形式`S`或`S*`或`{S}1`.

表达式`[[O]]`的含义是一组形式`Oi1...Oij`(`1 <= j`)被粘接入外围包裹的表达式, 使得如果`n /= m`, `1 <= n,m <= j`, 则`Oin /= Oim`或者`Oin = Oim = Qk`, 这里`1 <= k <= n`, `Ok`有形式`Qk*`. 此外, 对每个有形式`{Qk}1`的`Oin`, `Oin`必须出现在被粘接的列表中.

例如表达式`(x [[A | B* | C]] y)`的含义是最多一个`A`、任意数量的`B`和最多一个`C`可以按任意顺序出现. 是下列表达式的描述:

``` lisp
(x y)
(x B A C y)
(x A B B B B B C y)
(x C B A B B B y)
```

但不是这些表达式的描述:

``` lisp
(x B B A A C C y) ; A出现过多
(x C B C y)       ; C出现过多
```

记法`[[O1 | O2 | ...]]+`添加额外约束: 至少一项必须被使用.
例如: `(x [[A | B* | C]]+ y)`的含义是, 最多一个`A`、任意数量的`B`和最多一个`C`可以按任意顺序出现, 但至少选择其中一个选项. 是下列表达式的描述:

``` lisp
(x B y)
(x B A C y)
(x A B B B B B C y)
(x C B A B B B y)
```

但不是这些表达式的描述:

``` lisp
(x y)             ; 没有使用选项
(x B B A A C C y) ; A和C出现过多
(x C B C y)       ; C出现过多
```

表达式`(x [[{A}1 | {B}1 | C]] y)`只可以描述这些:

``` lisp
(x A B C y)
(x A C B y)
(x A B y)
(x B A C y)
(x B C A y)
(x B A y)
(x C A B y)
(x C B A y)
```

##### 1.4.1.2.2 间接(Indirection)

引入间接扩展是为了使得这种新语法更可读: `O`

如果`O`是非终结符, 其定义的右部分可以替换整个表达式`O`.

例如下面的BNF等价于前一个示例中的BNF

``` EBNF
(x [[O]] y)
O ::= A | B* | C
```

##### 1.4.1.2.3 间接定义的额外使用

在一些案例中, BNF中的辅助定义可能在BNF中未被使用, 但在其他地方可能有用.

例如, 考虑如下定义:

```
case keyform {normal-clause}* [otherwise-calues] => result*
ccase keyplace {normal-clause}* => result*
ecase keyform {normal-clause}* => result*
normal-caluse ::= (key form*)
otherwise-caluse ::= ({otherwise | t} form*)
clause ::= normal-cluase | otherwise-clause
```

这里`clause`项看上去并未在BNF中使用过, 但BNF的用途不仅仅是指导解析, 同时还定义了可在后续描述性文本中引用的项. 这样,`clause`项可以在后续文本中徐出现, 作为`normal-clause`或`otherwise-clause`的简写.

#### 1.4.1.3 特殊符号

这里描述的特殊符号在该文档中作为符号约定使用, 不是Common Lisp语言或其环境的一部分.

##### `=>` 求值

例如:

```
(+ 4 5) =>  9
```

表示求值形式`(+ 4 5)`的结果为`9`.

如果一个形式返回多值, 这些值可以按空格、行或逗号分隔. 例如:

```
(truncate 7 5)
=>  1 2
(truncate 7 5)
=>  1
  2
(truncate 7 5)
=>  1, 2
```

上面的三个示例是等价的, 描述`(truncate 7 5)`返回两个值: `1`和`2`.


##### `OR=>` 几个可选结果中的一个

示例:

```
(char-name #\a)
=>  NIL
OR=>  "LOWERCASE-a"
OR=>  "Small-A"
OR=>  "LA01"
```

指出`nil`、`"LOWERCASE-a"`、`"Small-A"`、`"LA01"`是`(char-name #\a)`的可能结果.
除非特别指出, 否则不应该假设这些可能的结果是完备的. 形式的说, 上面的实例等价于

```
(char-name #\a) => implementation-depdendent
```

但其提供了展示被允许的实现差异的额外信息.

##### `NOT=>` 不可能的结果

`NOT=>`可被用于强调预期的错误理解可以引导读者不会认同产生的结果的情况. 例如:

```
(function-lambda-expression
   (funcall #'(lambda (x) #'(lambda () x)) nil))
=>  NIL, true, NIL
OR=>  (LAMBDA () X), true, NIL
NOT=>  NIL, false, NIL
NOT=>  (LAMBDA () X), false, NIL
```

##### `==` 代码等价

例如:

```
(gcd x (gcd y z)) ==  (gcd (gcd x y) z)
```

其含义是对任意的`x`、`y`、`z`, 求值形式`(gcd x (gcd y z))`的结果和可观察的副作用总是与`(gcd (gcd x y) z)`的结果和可观察的副作用相同.

##### `>>` 指示输入和输出

Common Lisp按非交互式流模型描述输入和输出. 交互式输入输出如何映射到非交互式模型的特定细节是由实现定义的.

例如复合标准的实现被允许在交互输入如何结束这一问题上存在差异. 例如函数`read`在非交互式流中键入最终的定界符时终止.
在一些实现中, 交互式的调用`read`在最终定界符键入后立即返回, 甚至定界符不是新行符号时也是这样.
在其他实现中, 总是需要最终的新行符号.
在另外的一些实现中, 可能存在一个激活输入缓冲区已满的命令, 该命令在程序输入流中不可见.

在该文档的示例中, 在有交互式输入和输出出现时, 记号`>>`出现在行首.

例如:

```
(+ 1 (print (+ (sqrt (read)) (sqrt (read)))))
>>  9 16
>>  7
=>  8
```

展示了一个交互, 其中`(+ 1 (print (+ (sqrt (read)) (sqrt (read)))))`是被求值的形式, `9 16`是交互式输入, `7`是交互式输出, `8`是求值产生的值.

一些情况下, 非交互式流需要新行符号. 新行符号字符如何被交互式的键入是用户接口的实现定义的细节, 可以使用标记`<Newline>`或`<NEWLINE>`表示.

```
(progn (format t "~&Who? ") (read-line))
>>  Who? Fred, Mary, and Sally<NEWLINE>
=>  "Fred, Mary, and Sally", false
```

#### 1.4.1.4 有多个记法的对象

Common Lisp中一些对象可以用多于一种的方式标记. 在这样的情况中, 选用何种标记是技术自由的, 但可能存在观点或意图方面的约定.

##### 1.4.1.4.1 符号的大小写

Lisp读取器尽管在处理符号内部化(interning)时是却分大小写的, 但默认在内部化之前尝试规范化, 见[23.1.2 Lisp读取器中readtable大小写的作用](../23-Reader#23.1.2). 从而符号中大小写是默认不区分的. 在该文档中, 除非特别指出, 符号是不区分大小写的; 即`HELLO`、`Hello`、`HelLo`和`hello`均表示名称为`HELLO`的符号.

字符`\`和`|`用于显式的引述字符的大小写和其他解析相关方面. 记法`|hello|`和`\h\e\l\l\o`均表示名称为`hello`的符号, 这个符号与名称为`HELLO`的符号不同.

与Common Lisp已定义名称相关的符号有大写的名称, 但在该文档中以小写形式出现.

##### 1.4.1.4.2 数值

尽管Common Lisp提供了编程处理输入和输出有理数基数的多种方法, 除非特别说明, 该文档中的所有数值使用十进制记法.

##### 1.4.1.4.3 `.`的使用

`.`可以在表达式中出现, 例如:

```
(item1 item2 . tail)
```

其含义是`tail`表示一个列表尾部的对象的列表.
例如 `(A B C . (D E F))`与`(A B C D E F)`是记法上等价的.

尽管`.`是有效的符号中字符要素, 但不在标准符号中出现, 所以该文档的语句中如果在符号后出现`.`, 不将其作为符号名称的一部分解释. 例如 "这个示例语句引用了符号car.", 其中符号名称为`CAR`而不是`CAR.`.

##### 1.4.1.4.4 `NIL`

`nil`有多种含义. 它是在`COMMON-LISP`包中的一个名称为`NIL`的符号、布尔值(广义布尔值)false、空列表、空类型的名称.

在Common Lisp中, `nil`可以记为`NIL`或者`()`. 通常记法的选择提供了其扮演角色的暗示.

<span id="Figure1-1">图 1-1. NIL的记法.<span>

|求值? |记法 | 扮演的角色|
|:----|:---|:---------|
|Yes|`nil`|布尔值|
|Yes|`'nil`|符号|
|Yes|`'()`|空列表|
|No|`nil`|符号或布尔值|
|No|`()`|空列表|

在该文档中, `nil`有时作为布尔值false. 例如:

```
(print ())                          ; 应该避免
(defun three nil 3)                 ; 应该避免
'(nil nil)                          ; 两个符号的列表
'(() ())                            ; 空列表的列表
(defun three () 3)                  ; 强调: 空参数列表.
(append '() '()) =>  ()             ; 强调: 使用空列表.
(not nil) =>  true                  ; 强调: 使用布尔值false.
(get 'nil 'color)                   ; 强调: 使用符号.
```

函数有时在特定环境中被称为false或true. 因没有函数对象与`nil`相同, 所有函数对象在布尔值视角表示true, 而在字面量视角称为true或false都是没有意义的. 作为替代, 传统上称函数返回false或返回true.

#### 1.4.1.5 指示器

指示器(Designators)是指示另一个对象的对象.

在操作符的参数被描述为指示器时, 操作符的描述中存在参数的值是被指示的对象的假设; 即参数已经有被指示的类型.(被指示为`<<type>> designator`或`<<type>>的disignator`的对象的性质见[指示器](../Glossary#designator))


例如: `nil`和`*standard-output*`的值可以作为流指示器, 类似的符号`foo`和字符串`"FOO"`可以作为字符串指示器.

除非特别指出, 被指示的对象在一些场景下可能被多次使用, 对象被求值(coerce)一次还是每次该对象出现时均求值是特定于实现的.

例如, `mapcar`接收函数指示器作为参数, 它的描述中假设这是个函数. 实际上, 这个函数指示器是被立即求值, 或者在内部以作为参数的形式携带, 并在每次没使用时求值, 是依赖于特定的实现的. 在大多数情况下, 符合标准的程序不能检测出这种差异性, 但是存在一些病态情况(包括自重定义或互相重定义函数), 在符合标准的同时可以检测出差异性. 下面的程序是符合标准的, 但依赖于其正确性是否依赖于哪一个结果, 可能或不可能有可移植的正确结果:

```
(defun add-some (x)
   (defun add-some (x) (+ x 2))
   (+ x 1)) =>  ADD-SOME
(mapcar 'add-some '(1 2 3 4))
=>  (2 3 4 5)
OR=>  (2 4 5 6)
```

> 在SBCL中执行的结果:
>
> ```
> > (defun add-some (x)
>    (defun add-some (x) (+ x 2))
>    (+ x 1))
> ADD-SOME
> (mapcar 'add-some '(1 2 3 4))
> WARNING: redefining COMMON-LISP-USER::ADD-SOME in DEFUN
> WARNING: redefining COMMON-LISP-USER::ADD-SOME in DEFUN
> WARNING: redefining COMMON-LISP-USER::ADD-SOME in DEFUN
> WARNING: redefining COMMON-LISP-USER::ADD-SOME in DEFUN
> (2 3 4 5)
> ```

在一些很少出现的情况中, 存在需要引用参数的原始指示器对象的字典项. 因为给参数命名可以引用被指示的对象, `the <<parameter-name>> designator`可被用于引用这个指示器, 它是用于计算`<<parameter-name>>`的值的参数.


#### 1.4.1.6 无含义单词

当一个单词不需要预先附加含义时, Lisp社区通常使用这些单词: `foo`, `bar`, `baz`, `quux`. 例如:

``` lisp
(defun foo (x) (+ x 1))
```

使用名称`foo`的含义是可以在这里将其替换为你喜欢的名称.

### 1.4.2 有关错误的术语

- 安全的代码(Safe code)

使用最高设置(3)的安全性优化处理的代码. 安全性是代码的词法属性. "函数`F`应该发出错误信息"的含义是,
如果`F`被经最高安全性优化处理的代码调用, 会发出一个错误信号. 是由`F`还是由调用代码发出错误信号, 是依赖于实现的.

- 不安全的代码(Unsafe code)

使用较低安全性优化等级处理的代码. 不安全的代码可能执行错误检测. 实现被允许将所有代码视为总是安全的代码.

- 发出错误信号(An error is signaled)

其含义是在安全和不安全代码中, 发布了一个错误信号. 符合标准的代码可以依赖于错误信号是在安全和不安全代码中发出的事实. 每个实现被要求检测安全和不安全代码重发出的错误信号. 例如:
"如果给`unexport`传入当前包中不能访问的符号, 会发出一个错误信号".

如果没有显式指定错误类型, 默认为`error`.

- 应该发出错误信号(An error should be signaled)

其含义是安全代码中发出了错误信号, 不安全代码中可能发出了错误信号. 符合标准的代码可以依赖于错误信号是在安全代码中发出的事实. 每个实现被要求至少在安全代码中检测错误. 当无错误信号时, 后果是未定义的. 例如:
"如果`+`的任意一个参数不是`number`类型的, 应该发出类型为`type-error`的错误信号".


- 应该准备发出错误信息(Should be prepared to signal an error)

与**应该发出错误信号**类似, 但其意味着, 使用懒检查, 如果操作符中的正常动作可以被正确的执行, 不需要操作符中额外的发现错误状况的工作. 实现总是被允许发出错误信号, 但是甚至在安全的代码中, 只需要在发出错误信号失败可能导致错误结果时发出错误信号. 在不安全的代码中, 其后果是未定义的.

例如, 定义"如果`find`的第二个参数不是合式列表时, 它应该准备发出类型为`type-error`的错误信号"并不表示总是抛出一个错误信息. 形式

``` lisp
(find 'a '(a b . c))
```

必须或者在安全代码中发出类型为`type-error`的错误信号, 或者返回`A`. 在不安全代码中后果未定义.

与之相反,

``` lisp
(find 'd '(a b . c))
```
必须按安全代码中发出类型为`type-error`的错误信号. 在不安全代码中后果未定义.

``` lisp
(find 'd '#1=(a b . #1#))
```
在安全代码中可能返回`nil`(作为实现定义的扩展), 也可能永不返回, 或发出类型为`type-error`的错误信号.
在不安全代码中后果未定义.

通常, 在类型检查场景中使用**应该该准备发出**, 这里有检测与操作符的正确操作不相关的错误是不实际的效率方面的考虑.


- 后果未描述(The consequences are unspecified)

其含义是后果不可预测但是有害的. 允许实现描述这种状况的后果. 不符合标准的代码可能依赖于结果或这种状况的作用, 所有复合标准的代码要求将这种状况的结果和作用视为不可预测的但有害的.
例如: "如果`shared-initialize`的第二个参数是一个与对象中任意可访问的槽不相关的名称, 结果未描述".

- 后果未定义(The consequences are undefined)

其含义是后果是不可预测的. 后果的范围可能是有害的到致命的(fatal). 不符合标准的代码可能依赖于结果或作用.
符合标准的代码必须将后果视为不可预测的. 在"必须"、"必须不"或"不能"出现的地方, 如果被陈述的需求无法满足, 并且没有陈述特定的后果, 后果是未定义的. 允许实现在这种情况下发出错误信号.

例如: "一旦一个名称被`defconstant`声明为常量, 后续的对该变量的赋值或绑定未定义".


- 可能发出错误信号(An error might be signaled)

其含义是这种状况的后果未定义, 然而, 如果发出了一个错误信号, 它的类型是被指定的类型.
例如: "`open`可能发出类型为`file-error`的错误信号".

- 返回值未描述(The return values are unspecified)

其含义是仅一个形式的返回值的数量和性质未描述. 然而, 是否有副作用或控制转义仍被描述.

甚至在使用了返回值未描述的函数时, 一个程序仍可被描述. 例如, 如果某个函数`F`的返回值未被描述, 像`(lenght (list (F)))`仍被恰当描述了, 这是因为它不依赖于`F`的返回值.

- 实现中可以涵盖这种状况(Implementations may be extended to cover this situation)

其含义是状况有未定义的后果, 然而, 允许符合标准的实现按更特定的方式处理这种状况. 例如, 一个实现可以定义发出错误信号、应该发出错误信号, 甚至详细定义的非错误行为.

不符合标准的代码可能依赖于这种状况的后果, 符合标准的代码必须将这种状况视为未定义的. 实现被要求记录状况如何被处理的.

例如: "实现可以扩展来定义其他由相关类的类型描述符".

- 实现可以自由扩展语法(Implementations are free to extend the syntax)

其含义是在这种状况下, 允许实现定义描述中形式的无歧义语法扩展.
不符合标准的代码可能依赖于这种扩展. 实现被要求记录每个扩展.
符合标准的代码被要求将这种语法视为无意义的.
标准科恩那个不允许一些扩展, 但允许另外一些扩展. 例如: "不允许实现扩展`defclass`的语法".

- 可能产生警告(A warning might be issued)

其含义是如果上下文不合适时(编译时)鼓励实现产生警告信息. 然而, 符合标准的实现不要求产生警告.


### 1.4.3 不属于标准的部分

目录、索引、图片、荣誉和附录, 不属于标准.

在各章节中, 以"备注"或"示例"未标题开头的子章节, 不属于标准.

字典项中的"备注"和"示例", 不属于标准.

### 1.4.4 解释字典项

#### 1.4.4.1 被影响(Affected By)

对操作符, 任何可以影响它的副作用或返回值的事物.

对变量, 任何可以影响它的值的事物, 包括绑定它或给它赋值的函数.

#### 1.4.4.2 参数(Arguments)

描述目录项的语法信息, 包括声明和特殊表达式(从不作为形式求值, 不返回值)等.

#### 1.4.4.3 参数和值(Arguments and Values)

操作符接受的参数和返回的值的描述, 包括参数默认值(可选参数和关键字参数).

对于特殊操作符和宏, 其参数不被求值, 除非在描述中被显式指出需要求值.

#### 1.4.4.4 影响的绑定类型(Binding Types Affected)

作为声明可能潜在影响的绑定的提醒. 特定绑定是否实际被影响可能依赖于其他因素.

#### 1.4.4.5 类优先级列表(Class Precedence List)

出现在类的目录字典中, 包含Common Lisp定义的在这个类的类优先级列表中出现的类的有序列表.

允许实现定义的类出现在实现的类优先级列表中.

允许`standard-object`或`struct-object`出现在实现的类优先级列表中, 详情见[4.2.2 类型关系](../04-Types-and-Classes/#4.2.2)

除非特别指出, 不允许额外的标准类出现在实现的类优先级列表中.

通过定义类和类型之间的关系, 这里的类是被该类指示的类型的超类型.

#### 1.4.4.6 类型描述符的字典项

原子类型描述符是图4-2中列出的已定义名称. 这些目录项包括: 类、条件类型、系统类和类型. 如何将这些类型和类的符号解释为原子类型描述符的描述见描述一节.

复合类型描述符是图4-3中列出的已定义名称. 这些目录项包括: 类、系统类、类型和类型描述符. 如何将其`car`的符号解释为复合类型描述符的描述见类别、语法、参数和描述的章节.

##### 1.4.4.6.1 复合类型描述符类别

- 缩写(abbreviating)类型描述符 描述了这样的子类型: 原理上可能枚举元素的, 但实际上不可行.
- 特化(specilalizing)类型描述符 描述了这样的子类型: 约束类型的一个或多个成分的类型, 例如元组类型或复数部分类型
- 断言(predicating)类型描述符 描述了这样的子类型: 只包含满足给定谓词的对象
- 组合(combining)类型描述符 描述了这样的子类型: 使用组合的方式, 在其他类型上使用组合操作(`and`, `or`, `not`)

##### 1.4.4.6.2 复合类型描述符语法

这里的信息描述了类型的复合类型描述符的语法.

没有描述类型是否被认为是原子类型描述符.

##### 1.4.4.6.3 复合类型描述符参数

这里的信息描述了语法一节中定义的结构信息.

##### 1.4.4.6.4 复合类型描述符描述

这里的信息描述了语法一节中定义的结构的含义.

#### 1.4.4.7 常量值(Constant Value)

这里的信息描述了常值变量的不可变的类型和值.

#### 1.4.4.8 描述(Description)

操作符的总结和其他意图相关的方面, 但不一定需要包括下面的一些章节(副作用、异常状况等).

#### 1.4.4.9 示例(Examples)

操作符的使用示例. 这些示例不属于标准.

#### 1.4.4.10 异常状况(Exceptional Situations)

这里可以记录三种信息:

- 被函数检测到的状况
- 被函数处理的状况
- 可能被函数检测到的状况

这里的信息不包括这些被发出的条件:

- 作为参数或动态变量传递给操作符的函数
- 如果操作符是宏或特殊操作符, 执行操作符的子形式

#### 1.4.4.11 初始值(Initial Value)

这里的信息描述了动态变量的初始值. 因为这个变量可能被修改, 见值类型一节中类型约束部分.

#### 1.4.4.12 参数优先级顺序(Argument Precedence Order)

这里的信息描述了参数优先级顺序. 如果被省略掉了, 参数优先级顺序是默认的: 从左至右.

#### 1.4.4.13 方法签名(Method Signature)

广义函数的描述包括定义在广义函数上的方法的描述.
方法的签名用于描述方法的参数和参数特化符.
广义函数上定义的方法必须由这里描述的方法签名的形式.

```
F (x class)(y t) &optional z &key k
```

这个签名表明, 广义函数`F`上的这个方法需要两个必备参数: `x`和`y`, `x`必须是类`class`的泛化实例, `y`可以是任意对象(类`t`的泛化实例). 此外, 有可选参数`z`和关键字参数`k`.
这个签名还表明, 这个方法没有限定符, 是主方法.

对每个参数, 提供的参数必须在广义函数中描述的类型和方法签名中的类型的交中, 方法包括标准中定义的方法、实现定义或用户定义的方法.

#### 1.4.4.14 名称(Name)

这一节介绍目录项:

- 访问器(Accessor) 访问器
- 类(Class)
- 条件类型(Condition Type) 类型`condition`的子类型
- 常值变量(Constant Variable)
- 声明(Declaration) 声明描述符
- 函数(Function)
- 本地函数(Local Function) 只在其他宏形式词法作用域中定义的函数
- 本地宏(Local Macro) 只在其他宏形式词法作用域中定义的宏
- 宏(Macro)
- 重启器(Restart)
- 特殊操作符(Special Operator)
- 标准广义函数(Standard Generic Function)
- 符号(Symbol) 在特定场景中识别的符号, 例如宏语法中
- 系统类(System Class) 将类标记为内置类
- 类型(Type) 原子类型描述符
- 类型描述符(Type Specifier) 已定义的名称, 不是原子类型描述符, 但可用于构造有效的类型描述符
- 变量(Variable) 动态变量


#### 1.4.4.15 备注(Notes)

这里的信息作为操作符其他部分描述的补充.
可能包括交叉引用信息、代码等价性、风格建议、实现建议和典型使用方法的.
这里的信息不属于标准, 任何符合标准的实现和程序允许忽略这些信息.

#### 1.4.4.16 读法(Pronunciation)

提供了建议的已定义名称的读法. 不属于标准.

#### 1.4.4.17 参见(See Also)

标准中操作符相关的信息的连接. 不属于标准.

#### 1.4.4.18 副作用(Side Effects)

求值包含操作符的形式时, 任何被改变的事物.

#### 1.4.4.19 超类型(Supertypes)

出现在类型的目录项中, 包括这个类型的标准超类型的列表.

在有相关类的实现中, 类优先级列表的顺序与这里的一致.

#### 1.4.4.20 语法(Syntax)

这一部分描述如何在代码中使用已定义名称.
广义函数的语法部分描述其自身的lambda列表, 方法签名描述定义的方法的lambda列表.
常规函数、宏和特殊操作符的语法部分描述其参数。

例如: 一个操作符描述

```
F x y &optional z &key k
```

这个描述表明函数`F`有两个必备参数`x`和`y`, 一个可选参数`z`和一个关键字参数`k`.

对于宏和特殊操作符, 使用[1.4.1.2 修改的BNF语法](#1.4.1.2)中定义的语法. 对于函数, 给出lambda列表. 省略了外围的括号和默认值信息.

##### 1.4.4.20.1 重载操作符的特护语法记法

如果相同操作由两个描述, 但由不同数量的参数, 则额外参数被视为可选参数.

例如:

```
file-position stream => position
file-position stream position-spec => success-p
```

等价于:

```
file-position stream &optional position-spec => result
```

多行记法用于操作符重载中提供参数数量不同或返回值不同的情况.

##### 1.4.4.20.2 剩余参数的命名约定

剩余参数的名称选为复数名词, 使用单数名词引用其中一个元素.

例如:

```
F &rest arguments
```

通过`arguments`引用剩余参数列表, 通过`argument`引用其中一个元素.


##### 1.4.4.20.3 非空剩余参数

```
F &rest arguments+
```

等价于

```
F &rest argument
```

但引入了至少有一个参数的要求.


##### 1.4.4.20.4 返回值

在返回值前使用求值箭头`=>`. 例如:

```
F a b c => x
```

表明`F`是有三个必备参数(`a`, `b`, `c`)的操作符, 返回一个值(`x`).

如果操作符返回多个值, 按逗号分隔:

```
F a b c => x,y,z
```

###### 1.4.4.20.4.1 无参数或值

如果不允许有参数或没有返回值时, 使用特殊的记法:

```
F <no arguments> => <no values>
```

表明`F`是无参数和返回值的操作符.

###### 1.4.4.20.4.2 无条件控制转移

一些操作符执行无条件控制转移, 所以永远不会由返回值. 使用如下记法:

```
F a b c =>|
```

#### 1.4.4.21 有效上下文(Valid Context)

被声明一节使用, 用于显示声明可以出现的上下文.

声明可以出现在`declare`表达式、`declaim`或`proclaim`形式中.

#### 1.4.4.22 值类型(Value Type)

这里的信息描述动态变量的类型约束.

除非特别说明, 违背这个类型约束的后果未定义.

## <span id="1.5">1.5</span> 标准符合性

标准给出了符合标准的实现中需要实现的语法和语义. 此外, 对符合标准的程序也提出要求.

### 1.5.1 符合标准的实现

符合标准的实现需要满足这里的需求.

#### 1.5.1.1 要求的语言特性

符合标准的实现需要具备标准中描述的所有特性(包括废弃的特性)和含义.

符合标准的实现不应该要求在代码中包含替换或额外语言元素以满足标准中描述的语言特性.

#### 1.5.1.2 记录实现相关的特性

符合标准的实现需要提供规范中定义的语言实现定义内容的文档.

此外, 鼓励(但不要求)符合标准的实现提供标准中标注为实现相关的内容.

#### 1.5.1.3 记录扩展

符合标准的实现应该提供标准中未描述的支持的特性的文档, 当添加到语言标准时不应该导致歧义和矛盾.
这些扩展应该描述为"ANSI <<标准号>>中描述的Common Lisp扩展".

#### 1.5.1.4 处理异常情况

符合标准的实现应该以与规范一致的方式处理异常情况.

##### 1.5.1.4.1 解决异常情况中明显的冲突

如果规范中有多处内容可用于同一情况但存在冲突, 则采用按最特殊方式(不要求是提供了最受限类别的异常检测)描述情况的内容.

###### 1.5.1.4.1.1 示例: 解决异常情况中明显的冲突

假设函数`foo`是操作数值的函数集合`S`中的一个.
一段描述指出如果没有给`S`中函数传递参数`17`则必须发出错误信号.
另一段明显冲突的描述指出如果给`foo`传递参数`17`后果未定义.

则采用第二段描述, 因为其描述的情况上下文更特殊. 不要求`foo`在收到参数`17`时发出错误信号, 尽管`S`中其他函数被要求这么做.

#### 1.5.1.5 符合性陈述

符合标准的实现应该生成符合性陈述作为使用实现的结果, 或者该陈述应该纳入相关文档中.

如果实现完全符合标准, 则符合性陈述应该是: "<<实现>>符合ANSI <<标准号>>的需求".

如果实现部分复合标准, 则符合性陈述应该是: "<<实现>>复合ANSI <<标准号>>的需求, 除了<<没有复合的标准的需求的引用或完整列表>>".

### 1.5.2 符合标准的程序

符合标准的程序应该满足:

1. 只使用标准或使用标准扩展机制描述的语言语法和语义中特性.
2. 可以使用实现线管的特性和值, 但不能依赖于这些特性和值的特定解释, 通过执行代码发现的解释除外.
3. 不应该依赖于未定义或未描述情况的后果.
4. 不可使用标准禁止使用的构造.
5. 不可依赖于实现中包括的扩展.

#### 1.5.2.1 使用实现定义的语言特性

注意符合标准的程序可以依赖于特定的实现定义的值或特性.
注意符合标准的程序和实现的需求, 不要求符合标准的程序产生的结果总是与符合标准的实现产生的结果相同.

符合标准的程序可以在所有符合标准的实现中运行, 但有允许的实现定义的行为使其不可一致. 例如, 下面的形式示例是符合标准的, 但在不同的实现中可能返回不同的值:

```
(evenp most-positive-fixnum) =>  implementation-dependent
(random) =>  implementation-dependent
(> lambda-parameters-limit 93) =>  implementation-dependent
(char-name #\A) =>  implementation-dependent
```

##### 1.5.2.1.1 使用实时状况

使用`#+`和`#-`不自动将程序分类为不符合标准的. 如果不存在不符合标准的特性, 使用`#+`和`#-`的程序被认为是符合标准的. 当然, 符合标准的程序不一定是可用的程序. 下面的程序是符合标准的:

```
(defun foo ()
  #+ACME (acme:initialize-something)
  (print 'hello-there))
```

然而, 这个程序可能工作也可能不工作, 依赖于特性`ACME`和函数`acme:initialize-something`是否存在.

实际上, 在符合标准的程序中使用`#+`和`#-`, 将变量`*feature*`作为额外的程序输入. 像其他程序输入一样, 程序员有责任确保程序没有对输入数据做无法保证的假设.

#### 1.5.2.2 可移植代码的字符集

可移植的代码只使用标准字符编写.

## <span id="1.6">1.6</span> 语言扩展

> TODO(zhoujiagen) 1.6 语言扩展

## <span id="1.7">1.7</span> 语言子集

> TODO(zhoujiagen) 1.7 语言子集

## 1.8 废弃的语言特性

> TODO(zhoujiagen) 1.8 废弃的语言特性

## 1.9 COMMON-LISP包中的符号

见[符号](Symbols.md).
