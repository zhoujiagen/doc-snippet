# 24. 系统构造

[TOC]

## <span id="24.1">24.1</span> 系统构造的概念

### <span id="24.1.1">24.1.1</span> 加载

`load`文件将其内容视为代码, 并执行代码. 文件可以包含源代码或编译后的代码.

包含源代码的文件称为源文件. 加载源文件是通过顺序的读文件中形式、读取后立即求值完成的.

包含已编译的代码的文件称为已编译的文件. 加载已编译的文件与加载源文件类似, 除非这个文件不包含文本而是包含由编译器创建的预先理解的表达式的依赖于实现的表示. 通常, 加载已编译的文件比加载源文件快. 见[3.2 编译](../03-Evaluation-and-Compilation#3.2).

区分源文件与已编译文件的方法是依赖于实现的.

### <span id="24.1.2">24.1.2</span> 特性

特性是Common Lisp的实现或环境的一个方面或属性. 特性由符号标识.

当且仅当命名特性的符号是变量`*features*`(称为特性列表)中的一个元素时, 称特性在Lisp镜像中存在.

#### <span id="24.1.2.1">24.1.2.1</span> 特性表达式

特性的布尔组合称为特性表达式, 使用读取器宏`#+`和`#-`指导Lisp读取器对表达式的条件读取.

解释特性表达式的规则如下:

* `feature`<br>
如果命名了特性的符号被用作特性表达式, 且该特性存在, 则该表达式(求值)成功, 否则失败.
* `(not feature-conditional)`<br>
如果这个表达式中`feature-conditional`(求值)失败, 则该表达式(求值)成功, 否则失败.
* `(and feature-conditional*)`<br>
如果这个表达式中所有`feature-conditional`(求值)成功, 则该表达式(求值)成功, 否则失败.
* `(or feature-conditional*)`<br>
如果这个表达式中有一个`feature-conditional`(求值)成功, 则该表达式(求值)成功, 否则失败.

##### <span id="24.1.2.1.1">24.1.2.1.1</span> 示例: 特性表达式

例如, 在实现A中存在特性`spice`和`perq`, 不存在`lispm`; 在实现B中存在特性`lispm`, 不存在`spice`和`perq`; 在实现C中, 不存在`spice`,`lispm`和`perq`.

下图展示了一些示例特性表达式, 以及它们如何被实现中读取的:

<span id="Figure24-1">图 24-1. 特性示例.</span>

```  lisp
(cons #+spice "Spice" #-spice "Lispm" x)

in implementation A ...  (CONS "Spice" X)
in implementation B ...  (CONS "Lispm" X)
in implementation C ...  (CONS "Lispm" X)

(cons #+spice "Spice" #+LispM "Lispm" x)

in implementation A ...  (CONS "Spice" X)
in implementation B ...  (CONS "Lispm" X)
in implementation C ...  (CONS X)

(setq a '(1 2 #+perq 43 #+(not perq) 27))

in implementation A ...  (SETQ A '(1 2 43))
in implementation B ...  (SETQ A '(1 2 27))
in implementation C ...  (SETQ A '(1 2 27))

(let ((a 3) #+(or spice lispm) (b 3)) (foo a))

in implementation A ...  (LET ((A 3) (B 3)) (FOO A))
in implementation B ...  (LET ((A 3) (B 3)) (FOO A))
in implementation C ...  (LET ((A 3)) (FOO A))

(cons #+Lispm "#+Spice" #+Spice "foo" #-(or Lispm Spice) 7 x)

in implementation A ...  (CONS "foo" X)
in implementation B ...  (CONS "#+Spice" X)
in implementation C ...  (CONS 7 X)
```

## <span id="24.2">24.2</span> 系统构造的字典

见[系统构造的字典](../Dictionary#24.2).
