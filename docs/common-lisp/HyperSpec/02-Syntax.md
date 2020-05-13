# 2. 语法

[TOC]

## <span id="2.1">2.1</span> 字符语法

Lisp读取器从流中接收字符串, 将它们解释为对象的打印表示, 构造对象并返回该对象.

这一章描述的语法称为标准语法(Standard Syntax). Common Lisp提供了操作, 因此通过`readtable`表示的语法信息可以在程序控制下修改, 见[23 读取器](../23-Reader).
除非特别说明, 文档中使用的语法是标准语法.


### <span id="2.1.1">2.1.1</span> `readtable`

Lisp读取器使用的语法信息是被称为`readtable`的对象. `readtable`中包含字符和语法类型之间的关联.

下面是可用于`readtable`的已定义名称

<span id="Figure2-1">图 2-1. `readtable`相关的已定义名称.<span>

```
*readtable*                    readtable-case
copy-readtable                 readtablep
get-dispatch-macro-character   set-dispatch-macro-character
get-macro-character            set-macro-character
make-dispatch-macro-character  set-syntax-from-char
```

#### <span id="2.1.1.1">2.1.1.1</span> 当前`readtable`

多个描述不同语法的`readtable`可以同时存在, 但在任意时刻只能有一个可以影响Lisp读取器解析表达式成对象的方式, 称为当前`readtable`.
给定动态环境中的当前`readtable`是该环境的`*readtable*`的值.
为将另一个`readtable`设置成当前`readtable`, `*readtable*`可以被赋值或绑定.

#### <span id="2.1.1.2">2.1.1.2</span> 标准`readtable`

标准`readtable`符合标准语法. 如果尝试修改标准`readtable`, 其后果是未定义的.
为达到修改或扩展标准语法的效果, 可以创建标准`readtable`的一个副本, 见函数`copy-readtable`.

在标准`readtable`上调用函数`readtable-case`的结果是`:upcase`.

#### <span id="2.1.1.3">2.1.1.3</span> 初始`readtable`

初始`readtable`是Lisp镜像启动时的当前`readtable`, 这时它符合标准语法.
初始`readtable`与标准`readtable`不同, 允许符合标准的程序修改初始`readtable`.

### <span id="2.1.2">2.1.2</span> 影响Lisp读取器的变量

Lisp读取器不仅受当前`readtable`的影响, 还受多个动态变量的影响. 下图列出了影响Lisp读取器行为的变量.

<span id="Figure2-2">图 2-2. 影响Lisp读取器的变量.<span>

```
*package*    *read-default-float-format*  *readtable*
*read-base*  *read-suppress*
```

### <span id="2.1.3">2.1.3</span> 标准字符

实现必须支持称为`standard-char`的字符表, 表中字符称为标准字符.

`standard-char`字符表由非图形字符**换行符**、图形字符**空白**和下面的92个**图形符号**或其等价物构成。


<span id="Figure2-3">图 2-3. 标准字符子表(1/3 拉丁字符).<span>

```
Graphic ID  Glyph  Description  Graphic ID  Glyph  Description
LA01        a      small a      LN01        n      small n
LA02        A      capital A    LN02        N      capital N
LB01        b      small b      LO01        o      small o
LB02        B      capital B    LO02        O      capital O
LC01        c      small c      LP01        p      small p
LC02        C      capital C    LP02        P      capital P
LD01        d      small d      LQ01        q      small q
LD02        D      capital D    LQ02        Q      capital Q
LE01        e      small e      LR01        r      small r
LE02        E      capital E    LR02        R      capital R
LF01        f      small f      LS01        s      small s
LF02        F      capital F    LS02        S      capital S
LG01        g      small g      LT01        t      small t
LG02        G      capital G    LT02        T      capital T
LH01        h      small h      LU01        u      small u
LH02        H      capital H    LU02        U      capital U
LI01        i      small i      LV01        v      small v
LI02        I      capital I    LV02        V      capital V
LJ01        j      small j      LW01        w      small w
LJ02        J      capital J    LW02        W      capital W
LK01        k      small k      LX01        x      small x
LK02        K      capital K    LX02        X      capital X
LL01        l      small l      LY01        y      small y
LL02        L      capital L    LY02        Y      capital Y
LM01        m      small m      LZ01        z      small z
LM02        M      capital M    LZ02        Z      capital Z
```

<span id="Figure2-4">图 2-4. 标准字符子表(2/3 数值字符).<span>

```
Graphic ID  Glyph  Description  Graphic ID  Glyph  Description
ND01        1      digit 1      ND06        6      digit 6
ND02        2      digit 2      ND07        7      digit 7
ND03        3      digit 3      ND08        8      digit 8
ND04        4      digit 4      ND09        9      digit 9
ND05        5      digit 5      ND10        0      digit 0
```

<span id="Figure2-5">图 2-5. 标准字符子表(3/3 特殊字符).<span>

```
Graphic ID  Glyph  Description
SP02        !      exclamation mark
SC03        $      dollar sign
SP04        "      quotation mark, or double quote
SP05        '      apostrophe, or [single] quote
SP06        (      left parenthesis, or open parenthesis
SP07        )      right parenthesis, or close parenthesis
SP08        ,      comma
SP09        _      low line, or underscore
SP10        -      hyphen, or minus [sign]
SP11        .      full stop, period, or dot
SP12        /      solidus, or slash
SP13        :      colon
SP14        ;      semicolon
SP15        ?      question mark
SA01        +      plus [sign]
SA03        <      less-than [sign]
SA04        =      equals [sign]
SA05        >      greater-than [sign]
SM01        #      number sign, or sharp[sign]
SM02        %      percent [sign]
SM03        &      ampersand
SM04        *      asterisk, or star
SM05        @      commercial at, or at-sign
SM06        [      left [square] bracket
SM07        \      reverse solidus, or backslash
SM08        ]      right [square] bracket
SM11        {      left curly bracket, or left brace
SM13        |      vertical bar
SM14        }      right curly bracket, or right brace
SD13        `      grave accent, or backquote
SD15        ^      circumflex accent
SD19        ~      tilde
```

**Graphic ID** 未在Common Lisp中未使用, 仅作为ISO 6937/2的引用参考. 注意其首字母的含义: L---Latin(拉丁), N---Numeric(数值), S---Special(特殊).

### <span id="2.1.4">2.1.4</span> 字符语法类型

Lisp读取器通过根据字符的语法类型解释每个字符, 从输入文本中构造对象.
Lisp读取器不是接受所有由Lisp打印器产生的输入, Lisp读取器由未在Lisp打印器中使用的特性.
Lisp读取器可以被用作更通用的解析器中的词法解析器.

当Lisp读取器被调用时, 它从输入流中读取单个字段, 根据该字符的语法类型执行分发(dispatch).
可以出现在输入流中的字符具备图2-6中的一个语法类型.

<span id="Figure2-6">图 2-6. 可能的字符语法类型.<span>

```
constituent         ; 要素
invalid             ; 无效的
macro character     ; 宏字符
multiple escape     ; 多转义
single escape       ; 单转义
whitespace          ; 空白
```

当前`readtable`中字符的语法类型, 决定了该字符如何被Lisp读取器解释. 在任意时刻, 每个字符有且只有一个语法类型.

图2-7列出了标准语法中每个字符的语法类型

<span id="Figure2-7">图 2-7. 标准语法中字符的语法类型.<span>

```
character  syntax type                 character  syntax type
Backspace  constituent                 0--9       constituent
Tab        whitespace                  :          constituent
Newline    whitespace                  ;          terminating macro char
Linefeed   whitespace                  <          constituent
Page       whitespace                  =          constituent
Return     whitespace                  >          constituent
Space      whitespace                  ?          constituent*
!          constituent*                @          constituent
"          terminating macro char      A--Z       constituent
#          non-terminating macro char  [          constituent*
$          constituent                 \          single escape
%          constituent                 ]          constituent*
&          constituent                 ^          constituent
'          terminating macro char      _          constituent
(          terminating macro char      `          terminating macro char
)          terminating macro char      a--z       constituent
*          constituent                 {          constituent*
+          constituent                 |          multiple escape
,          terminating macro char      }          constituent*
-          constituent                 ~          constituent
.          constituent                 Rubout     constituent
/          constituent
```

由`*`标注的字符初始时是要素, 但不在任何标准Common Lisp记法中使用. 这些字符显式的保留给程序员使用.
`~`未在Common Lisp中使用, 保留给实现者.
`$`和`%`是字母字符, 但不在任何标准Common Lisp已定义名称中使用.

空白字符作为分割符使用, 其他情况可以忽略. 要素和转义字符累积成记号(token), 接着被解释为数值或符号.
宏字符触发调用可以执行任意解析动作的函数(可能是用户提供的). 宏字符按是否终结一个记号, 分为两个类别: 终结、非终结.

下面描述每类语法类型.

#### <span id="2.1.4.1">2.1.4.1</span> 要素字符

要素字符在记号中使用. 记号是数值或符号的表示. 要素字符的示例是字母(letter)或数字(digit).

符号名称中字符在名称被读取时, 有时被转换大小写, 见[23.1.2 Lisp读取器中readtable大小写的作用](../23-Reader#23.1.2). 大小写转换可用通过使用单转义或多转义字符抑制.

#### <span id="2.1.4.2">2.1.4.2</span> 要素特质

当字符是要素字符时, 它有一个或多个要素特质, 定义了字符如何被Lisp读取器解释.
这些要素特质是字母、数字、包标记、加号、减号、点、小数点、比值标记、指数标记和无效的.
图2-8展示了标准字符和一些半标准字符的要素特质; 没有机制可以修改字符的要素特质.
图中任何有`alphadigit`的要素特质, 如果当前输入基大于该字符的数字值时, 是一个数字, 否则是字母.
单转义标注的任意字符被视为字母要素, 不管其常规语法.

<span id="Figure2-8">图 2-8. 标准字符和半标准字符的要素特质.<span>

```
要素字符      特质             要素字符      特质
----------

Backspace    invalid         {            alphabetic
Tab          invalid*        }            alphabetic
Newline      invalid*        +            alphabetic   , plus sign
Linefeed     invalid*        -            alphabetic   , minus sign
Page         invalid*        .            alphabetic   , dot, decimal point
Return       invalid*        /            alphabetic   , ratio marker
Space        invalid*        A, a         alphadigit
!            alphabetic      B, b         alphadigit
"            alphabetic*     C, c         alphadigit
#            alphabetic*     D, d         alphadigit, double-float exponent marker
$            alphabetic      E, e         alphadigit, float exponent marker
%            alphabetic      F, f         alphadigit, single-float exponent marker
&            alphabetic      G, g         alphadigit
'            alphabetic*     H, h         alphadigit
(            alphabetic*     I, i         alphadigit
)            alphabetic*     J, j         alphadigit
*            alphabetic      K, k         alphadigit
,            alphabetic*     L, l         alphadigit, long-float exponent marker
0-9          alphadigit      M, m         alphadigit
:            package marker  N, n         alphadigit
;            alphabetic*     O, o         alphadigit
<            alphabetic      P, p         alphadigit
=            alphabetic      Q, q         alphadigit
>            alphabetic      R, r         alphadigit
?            alphabetic      S, s         alphadigit, short-float exponent marker
@            alphabetic      T, t         alphadigit
[            alphabetic      U, u         alphadigit
\            alphabetic*     V, v         alphadigit
]            alphabetic      W, w         alphadigit
^            alphabetic      X, x         alphadigit
_            alphabetic      Y, y         alphadigit
`            alphabetic*     Z, z         alphadigit
|            alphabetic*     Rubout       invalid
~            alphabetic
```

这个表的解释只适用与语法类型为要素的字符. 被`*`标注的项通常被遮盖, 因为被标注的字符的语法类型是空白、宏字符、单转义或多转义; 只有当其语法类型变为要素时这些要素特质才适用.

#### <span id="2.1.4.3">2.1.4.3</span> 无效的字符

要素特质是无效的字符不能在记号中出现, 除非在单转义字符的控制下.
如果在读取一个对象时遇到无效的字符, 发出类型为`reader-error`的错误信号.
如果无效的字符前有一个单转义字符, 它被视为字母的要素.

#### <span id="2.1.4.4">2.1.4.4</span> 宏字符

当Lisp读取器在输入流中遇到宏字符时, 会执行对输入流中后续字符的特殊解析操作.

宏字符有一个称为 **读取器宏函数** 的相关函数, 这个函数实现了特殊的解析行为.
在符合标准的程序中, 可以使用函数`set-macro-character`和`set-dispatch-macro-character`建立或修改这类相关.

Lisp读取器在遇到宏字符时, 调用其读取器宏函数, 该函数从输入流中解析特殊格式的对象.
这个函数或者返回解析出的对象, 或者不返回值以表明函数扫描过的字符被忽略.
宏字符的示例有反引号、单引号、左括号和右括号.

宏字符是终结的或非终结的, 其区别在于当这样的符号出现在记号的中间时会发生什么.
当非终结宏字符出现在记号中间时, 该符号相关的函数不被调用, 该符号不会终结记号的名称, 该符号像要素字符似的称为名称的一部分.
终结宏字符终结任意记号, 不管该字符在何处出现, 其相关的读取器宏函数总被调用.
标准语法中唯一的非终结宏字符是井号符号.

如果一个字符是分发宏字符`C1`, 其读取器函数是由实现提供的一个函数.
这个函数读取数字字符直到读到一个非数字字符`C2`.
如果读到任何数字, 这些数字被转换为一个对应的整数中缀参数`P`, 否则`P`是`nil`.
终结的非数字字符`C2`是个用于在与分发宏字符关联的分发表中进行查找的字符(有时称为子字符, 用以强调其在分发中的子角色).
读取器宏函数关联上子字符`C2`后, 调用时传入三个参数: 流、`C2`和中缀参数`P`.
关于分发符号的更新信息, 见函数`set-dispatch-macro-character`.

关于在标准语法中可用的宏字符, 见[2.4 标准宏字符](#2.4)


#### <span id="2.1.4.5">2.1.4.5</span> 多转义字符

一对多转义字符用于将被其包裹的字符序列, 包括可能存在的宏字符和空白字符, 视为保留了大小写的字母字符.

这个序列中出现的任何单转义和多转义字符的前面必须由一个单转义字符.

##### <span id="2.1.4.5.1">2.1.4.5.1</span> 示例: 多转义字符

```
;; The following examples assume the readtable case of *readtable*
;; and *print-case* are both :upcase.
(eq 'abc 'ABC) =>  true
(eq 'abc '|ABC|) =>  true
(eq 'abc 'a|B|c) =>  true
(eq 'abc '|abc|) =>  false
```

#### <span id="2.1.4.6">2.1.4.6</span> 单转义字符

单转义字符用于将下一个字符视为保留了大小写的字母字符, 不管下一个字符是什么或它由什么要素特质.

反斜杠是标准语法中的单转义字符.

##### <span id="2.1.4.6.1">2.1.4.6.1</span> 示例: 单转义字符

```
;; The following examples assume the readtable case of *readtable*
;; and *print-case* are both :upcase.
(eq 'abc '\A\B\C) =>  true
(eq 'abc 'a\Bc) =>  true
(eq 'abc '\ABC) =>  true
(eq 'abc '\abc) =>  false
```

#### <span id="2.1.4.7">2.1.4.7</span> 空白字符

空白字符用于分隔记号.

空格符和换行符是标准语法中的空白字符.

##### <span id="2.1.4.7.1">2.1.4.7.1</span> 示例: 空白字符

```
(length '(this-that)) =>  1
(length '(this - that)) =>  3
(length '(a
          b)) =>  2
(+ 34) =>  34
(+ 3 4) =>  7
```


## <span id="2.2">2.2</span> 读取器算法

这一部分描述Lisp读取器从输入自负留解析出对象时使用的算法, 包括Lisp读取器如何处理宏字符.

当处理记号时, 读取器的基本功能是区分符号和数值的表示.
当累积记号时, 如果它满足图2-9列出的数值的语法, 则被认为是表示一个数值.
如果它不表示一个数值, 如果它满足可能的数值的语法规则则被认为是可能的数值.
如果一个有效的不是数值和可能数值的标识, 则它表示一个符号.

Lisp读取器算法如下:


1. 如果在文件尾, 文件尾处理按`read`中描述的方式处理. 否则, 从输入流中读入一个字符`x`, 并根据其语法类型分发到 **步骤2-7** 中的一个.

2. 如果`x`是 ==无效字符==, 发出类型为`reader-error`的错误信号.

3. 如果`x`是 ==空白字符==, 忽略它, **转步骤1**.

4. 如果`x`是 ==终结或非终结宏字符==, 则其相关的读取器宏函数以输入流和`x`这两个参数调用. <br><br>
  读取器宏函数可能从输入流中读取字符; 如果它不读取字符, 它会看到宏字符后面的字符. Lisp读取器可能在读取器宏函数中被递归调用. <br>
  读取器宏函数一定不能有除输入流外的任何副作用; 因为`read`操作的回溯和重启动, Lisp读取器的前端(编辑器等)可能导致在读取`x`只出现一次的单个表达式时, 读取器宏函数被重复调用.<br>
  读取器宏函数可能返回零个值或一个值. 如果返回了一个值, 则该值作为`read`操作的返回值, 算法结束. 如果没有返回值, **转步骤1**.

5. 如果`x`是 ==单转义字符==, 则读取下一个字符`y`, 如果到文件尾则发出类型为`end-of-file`的错误信号. `y`被视为要素特质为字母的要素. `y`用于开始记号, **转步骤8**.

6. 如果`x`是 ==多转义字符==, 则开始一个记号(初始时不包含字符), **转步骤9**.

7. 如果`x`是 ==要素字符==, 则开始一个记号. 在记号被读入后, 它将被解释为一个Lisp对象或无效的语法. 如果记号标识一个对象, 则该对象作为`read`操作的结果返回. 如果记号是无效语法, 发出错误信号. 如果`x`是有大小写的字符, 依赖于当前`readtable`中大小写设置可能被转换大小写, 见[23.1.2 Lisp读取器中readtable大小写的作用](../23-Reader#23.1.2). `X`用于开始记号, **转步骤8**.

8. 这时累积了一个记号, 遇到 ==偶数数量的多转义字符==. 如果在文件尾, **转步骤10**. 否则读入一个字符`y`, 根据其语法类型执行下面的一个操作: <br><br>
  \* 如果`y`是 ==要素或非终结宏字符==:<br>
    \-\- 如果`y`是有大小写的字符, 按23.1.2节中所述, 可能切换大小写<br>
    \-\- 将`Y`追加到构建中的记号中<br>
    \-\- **重复步骤8**.<br>
  \* 如果`y`是 ==单转义字符==, 读取下一个字符`z`, 如果在文件尾则发出类型为`end-of-file`的错误信号. `Z`视为要素特质是字母的要素, 追加到构建中的记号中, **重复步骤8**.<br>
  \* 如果`y`是 ==多转义字符==, **转步骤9**.<br>
  \* 如果`y`是 ==无效字符==, 发出类型为`read-error`的错误信号.<br>
  \* 如果`y`是 ==终结宏字符==, 则结束记号. 放回`y`(见`unread-char`), **转步骤10**.<br>
  \* 如果`y`是 ==空白字符==, 则结束记号. 必要时放回`y`(见`read-preserving-whitespace`),**转步骤10**.<br>

9. 这时累积了一个记号, 遇到 ==奇数数量的多转义字符==. 如果在文件尾, 发布类型为`end-of-file`的错误信号. 否则读入字符`y`, 根据其语法类型执行下面的一个操作:<br><br>
  \* 如果`y`是 ==要素字符、宏字符或空白字符==, `y`被视为要素特质为字母的要素. `Y`被追加到构建中的记号中, **重复步骤9**.<br>
  \* 如果`y`是 ==单转义字符==, 读取下一个字符`z`, 如果在文件尾则发出类型为`end-of-file`的错误信号. `Z`被视为要素特质为字母的要素, 被追加到构建中的记号中, **重复步骤9**.<br>
  \* 如果`y`是 ==多转义字符==, **转步骤8**.<br>
  \* 如果`y`是 ==无效字符==, 则发出类型为`reader-error`的错误信号.

10. 已累积了一个完整的机号. 记号标识的对象作为`read`操作的结果返回, 如果该记号不是有效语法则发出类型为`reader-error`的错误信号.


## <span id="2.3">2.3</span> 解释记号

### <span id="2.3.1">2.3.1</span> 数值记号

当读取一个记号时, 它被解释为一个数值或符号. 如果满足下述数值语法, 记号被解释为数值:

<span id="Figure2-9">图 2-9. 数值记号的语法.</span>

``` enbf
numeric-token  ::=  integer |
				   ratio   |
				   float
integer        ::=  [sign]
				   decimal-digit+
				   decimal-point |
				   [sign]
				   digit+
ratio          ::=  [sign]
				   {digit}+
				   slash
				   {digit}+
float          ::=  [sign]
				   {decimal-digit}*
				   decimal-point
				   {decimal-digit}+
				   [exponent]
                    |
				   [sign]
				   {decimal-digit}+
				   [decimal-point
					   {decimal-digit}*]
				   exponent
exponent       ::=  exponent-marker
				   [sign]
				   {digit}+

sign              ; + -
slash             ; /
decimal-point     ; .
exponent-marker   ; 指数标记: D E F L S
decimal-digit     ; 进制为10的数字
digit             ; 当前输入基数的数字
```

#### <span id="2.3.1.1">2.3.1.1</span> 可能的数值的记号

为允许实现者和将来的Common Lisp标准扩展数值的语法, 定义了比数值语法更通用的可能的数值的语法.
一个记号如果满足下述条件, 则是可能的数值:

1. 完全由数字、正负号、比值标记、小数点(`.`)、扩展字符(`^`或`_`)和数值标记构成. 数值标记是个字母. 一个字母是否被认为是数值标记是依赖于上下文的, 但不存在与字母相邻的字母是数值标记的情况. 指数标记是数值标记.
2. 包含至少一个数字. 依赖于当前输入基, 字母可以被视为数值, 但只在不包含小数点的记号中.
3. 以数字、正负号、小数点或扩展字符开始, 但不以包标记开始. 以包标记开始后跟可能的数值的语法不是良式定义的. 诸如`:1`、`:1/2`、`:2^3`在`read`需要读入表达式的位置出现时, 其后果未描述.
4. 不以正负号结束.

如果可能的数值有数值语法, 且数值在实现中可被表示, 则构造并返回恰当类型的数值.
如果数值超出了依赖于实现的数值常量的边界, 则数值在实现中不可表示. 例如给浮点数指定了太大或太小的指数, 使得数值在实现中不可表示. 分布为0的比值(例如`-35/000`)在任意实现中不可标识.
如果有数值语法的记号不能转换为内部数值, 则发出类型为`reader-error`的错误信号.
给浮点数指定了大量数字不可发出错误信号, 应该生成截断或舍入后的值.

如果存在一个字母被认为是数字或数值标记的歧义情况, 则该字母被认为是数字.

##### <span id="2.3.1.1.1">2.3.1.1.1</span> 转义字符与可能的数值

可能的数值中不能有任何转义字符.
转义字符强制将后面的字符解释为字母的, 因此不适合用于可能的数值. 下面的示例应该被解释为符号而不是数值:

```
\256   25\64   1.0\E6   |100|   3\.14159   |3/4|   3\/4   5||
```

在每个情况中, 移除转义字符后可以生成可能的数值.

##### <span id="2.3.1.1.2">2.3.1.1.2</span> 示例: 可能的数值

下图中的记号是可能的数值, 但不是数值, 因此作为保留记号; 符合标准的实现允许但不要求定义它们的含义:

<span id="Figure2-10">图 2-10. 保留记号示例.</span>

```
1b5000                       777777q                1.7J  -3/4+6.7J  12/25/83
27^19                        3^4/5                  6//7  3.1.2.6    ^-43^
3.141_592_653_589_793_238_4  -3.7+2.6i-6.17j+19.6k
```

下图中的记号不是可能的数字, 它们被认为是符号:

<span id="Figure2-11">图 2-11. 符号示例.</span>

```
/     /5     +  1+  1-
foo+  ab.cd  _  ^   ^/-
```

如果当前输入基是`16`时, 下图中记号是可能的数值, 如果当前输入基是`10`则这些是符号:

<span id="Figure2-12">图 2-12. 符号或可能的数值的示例.</span>

```
bad-face  25-dec-83  a/b  fad_cafe  f^
```

### <span id="2.3.2">2.3.2</span> 从记号构造数值

实数直接从相关的数值记号构建, 见[图 2-9. 数值记号的语法.](#Figure2-9).

复数记为`#C`或`#c`, 后跟两个实数的列表, 见[`#C`](#2.4.8.11).

读取器宏`#B`、`#O`、`#X`和`#R`可用于控制解析有理数时的输入基数, 见[`#B`](#2.4.8.7)、[`#O`](#2.4.8.8)、[`#X`](#2.4.8.9)和[`#R`](#2.4.8.10).

这一部分汇总了数值的完整语法.

#### <span id="2.3.2.1">2.3.2.1</span> 有理数的语法

##### <span id="2.3.2.1.1">2.3.2.1.1</span> 整数的语法

整数可以写作数字的序列, 可选的在开始处放置正负号, 可选的后接小数点, 见[图 2-9. 数值记号的语法.](#Figure2-9).
当使用小数点时, 数字的基数为`10`; 如果没有使用小数点, 则数字的基数为当前输入基.

关于整数如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

##### <span id="2.3.2.1.2">2.3.2.1.2</span> 比值的语法

比值写作可选的正负号, 后接两个按`/`分隔的两个非空数字序列, 见[图 2-9. 数值记号的语法.](#Figure2-9).
第二个数字序列不能都是0. 示例:

<span id="Figure2-13">图 2-13. 比值示例.</span>

``` lisp
2/3                 ;This is in canonical form
4/6                 ;A non-canonical form for 2/3
-17/23              ;A ratio preceded by a sign
-30517578125/32768  ;This is (-5/2)^15
10/5                ;The canonical form for this is 2
#o-101/75           ;Octal notation for -65/61
#3r120/21           ;Ternary notation for 15/7
#Xbc/ad             ;Hexadecimal notation for 188/173
#xFADED/FACADE      ;Hexadecimal notation for 1027565/16435934
```

关于比值如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.3.2.2">2.3.2.2</span> 浮点数的语法

浮点数可以写作小数形式或科学记数法形式: 可选的正负号, 后接有内嵌小数点的非空数字序列, 后接可选的小数指数规格描述.
如果没有指数描述符, 则需要小数点, 且小数点后必须还有数字.
指数描述符由指数标记, 一个可选的正负号和一个非空的数字列表构成.
如果没有指数描述符, 或者使用了指数描述符`e`(或`E`), 则使用`*read-default-float-format*`指定的格式.
见[图 2-9 数值记号的语法.](#Figure2-9).

实现可以提供构成类型`float`的一个或多个浮点数. 字母`s, f, d, l`(获取相应的大写形式)分别显式描述了使用类型`short-float`, `single-float`, `double-float`和`long-float`.

用于外部表示的内部格式仅依赖于指数标记, 不依赖于外部表示中数字的数量.

下图有浮点数记法的示例:

<span id="Figure2-14">图 2-14. 浮点数的示例.</span>

``` lisp
0.0       ;Floating-point zero in default format
0E0       ;As input, this is also floating-point zero in default format.
          ;As output, this would appear as 0.0.
0e0       ;As input, this is also floating-point zero in default format.
          ;As output, this would appear as 0.0.
-.0       ;As input, this might be a zero or a minus zero,
          ; depending on whether the implementation supports
          ; a distinct minus zero.
          ;As output, 0.0 is zero and -0.0 is minus zero.
0.        ;On input, the integer zero---not a floating-point number!
          ;Whether this appears as 0 or 0. on output depends
          ;on the value of *print-radix*.
0.0s0     ;A floating-point zero in short format
0s0       ;As input, this is a floating-point zero in short format.
          ;As output, such a zero would appear as 0.0s0
          ; (or as 0.0 if short-float was the default format).
6.02E+23  ;Avogadro's number, in default format
602E+21   ;Also Avogadro's number, in default format
```

关于浮点数如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.3.2.3">2.3.2.3</span> 复数的语法

复数由笛卡尔结构, 一个实部和一个虚部, 每部分都是一个实数.
复数的各部分不一定要是浮点数, 但两部分必须是同类型的: 或者都是有理数, 或者是同一浮点数子类型.
构造复数时, 如果指定的两个部分不是同一类型, 则内部将其转换为同一类型(有理数部分转换为浮点数).
如果虚部是整数`0`, 有类型`(complex rational)`的对象内部作为有理数表示.

更多信息见[`#C`](#2.4.8.11)和[22 打印器](../22-Printer/)中相应部分.

### <span id="2.3.3">2.3.3</span> cons中的`.`

如果记号只由点号构成(没有转义符号), 则发出类型为`reader-error`的错误信号, 除了这种情况:
如果记号是单个点号, 且出现在点对单元中允许出现点号的位置, 则它被作为该语法的一部分被接受而不发出错误信号.
见[`(`](#2.4.1)

### <span id="2.3.4">2.3.4</span> 符号记号

不是可能的数值、不包含包标记且不完全由点号构成的记号总是被解释为符号(Symbol).
是可能的数值但不满足数值语法的机号是保留记号, 有依赖于实现的解释.
在所有其他情况中, 记号构成符号的名称.

下图展示了符号的打印表示, 这里假设当前`readtable`的大小写是`:upcase`.

<span id="Figure2-15">图 2-15. 符号的打印表示示例(1/2).</span>

```
FROBBOZ         The symbol whose name is FROBBOZ.
frobboz         Another way to notate the same symbol.
fRObBoz         Yet another way to notate it.
unwind-protect  A symbol with a hyphen in its name.
+$              The symbol named +$.
1+              The symbol named 1+.
+1              This is the integer 1, not a symbol.
pascal_style    This symbol has an underscore in its name.
file.rel.43     This symbol has periods in its name.
\(              The symbol whose name is (.
\+1             The symbol whose name is +1.
+\1             Also the symbol whose name is +1.
\frobboz        The symbol whose name is fROBBOZ.
3.14159265\s0   The symbol whose name is 3.14159265s0.
3.14159265\S0   A different symbol, whose name is 3.14159265S0.
3.14159265s0    A possible short float approximation to <PI>.
```

<span id="Figure2-16">图 2-16. 符号的打印表示示例(2/2).</span>

```
APL\\360            The symbol whose name is APL\360.
apl\\360            Also the symbol whose name is APL\360.
\(b^2\)\-\4*a*c     The name is (B^2) - 4*A*C. Parentheses and two spaces in it.
\(\b^2\)\-\4*\a*\c  The name is (b^2) - 4*a*c. Letters explicitly lowercase.
|"|                 The same as writing \".
|(b^2) - 4*a*c|     The name is (b^2) - 4*a*c.
|frobboz|           The name is frobboz, not FROBBOZ.
|APL\360|           The name is APL360.
|APL\\360|          The name is APL\360.
|apl\\360|          The name is apl\360.
|\|\||              Same as \|\| ---the name is ||.
|(B^2) - 4*A*C|     The name is (B^2) - 4*A*C. Parentheses and two spaces in it.
|(b^2) - 4*a*c|     The name is (b^2) - 4*a*c.
```

在处理解析符号时, 从构成表示符号的记号的字符中移除实现定义的属性是依赖于实现的.

当解析符号的语法时, Lisp读取器在当前包中查找符号的名字. 这个查找过程可能包括在另外一个包中查找, 当前包继承了这个保的外部符号.
如果找到了名称, 则返回相应的符号.
如果没有找到符号(即当前包中没有可访问的同名符号), 则创建一个新符号, 并作为内部符号放在当前包中.当前包称为符号的属主(主包), 这个符号在当前包中被内部化. 如果这个名称后续被读取且这个包仍是当前包, 则会找到相同的符号并返回.


### <span id="2.3.5">2.3.5</span> 记号的有效模式

记号的有效模式汇总在下图中:

<span id="Figure2-17">图 2-17. 记号的有效模式.</span>

```
nnnnn              ; 数值
xxxxx              ; 当前包中的符号
:xxxxx             ; KEYWORD包中的符号
ppppp:xxxxx        ; ppppp包中的外部符号
ppppp::xxxxx       ; 可能是ppppp包中的内部符号
:nnnnn             ; 未定义
ppppp:nnnnn        ; 未定义
ppppp::nnnnn       ; 未定义
::aaaaa            ; 未定义
aaaaa:             ; 未定义
aaaaa:aaaaa:aaaaa  ; 未定义
```

注意: `nnnnn`有数值语法, `xxxxx`和`ppppp`没有数值语法, `aaaaa`有任意语法.

关于包标记的规则汇总如下. 每种情况中均提供了示例, 这里假设当前`readtable`的大小写是`:upcase`.

1. 如果由单个包标记, 出现在记号的开始处, 则该记号解释为`KEYWORD`包中的符号. 同时将新创建符号的`symbol-value`设置为同一符号, 从而该符号可以自求值. <br>
  例如: 读取`:bar`时, 将`BAR`作为`KEYWORD`的外部符号.
2. 如果由单个包标记不在记号的开始或结尾处, 将记号分为两个部分. 第一部分描述包, 第二部分是该包中可用的外部符号的名称. <br>
  例如: 读取`foo:bar`, 在名为`FOO`的包中查找外部符号`BAR`.
3. 如果由两个相邻的包记号, 且不在记号的开始或结尾处, 将记号分为两个部分. 第一部分描述包, 第二部分是该包中的符号的名称(可能是内部符号). <br>
  例如: 读取`foo::bar`, 在名称`FOO`的包中查找符号`BAR`.
4. 如果记号中没有包标记, 且没有可能的数值语法, 则整个记号是符号的名称. 在当前包中查找该符号. <br>
  例如: 读取`bar`, 在当前包中查找符号`BAR`.
5. 如果在记号中使用了其他包标价, 则后果是未描述的. 符号名称中使用包标记的其他用法在标准中未定义, 但保留给依赖于实现使用.

例如, 当前`readtable`的大小写是`:upcase`, `editor:buffer`引用包`editor`中外部符号`BUFFER`, 而不管当前包中是否有名为`BUFFER`的符号.
如果没有包名称为`editor`, 或者包`editor`中没有名称为`BUFFER`的符号, 或者包`editor`未导出`BUFFER`, 则读取器发出可被修复的错误.
如果看到`editor:buffer`, 其效果与当前包是`EDITOR`读取符号`buffer`一致.

### <span id="2.3.6">2.3.6</span> 包系统一致性规则

下面的规则在`*package*`的值未改变时, 可用于包系统:

- 读-读一致性
  读相同的符号名称, 总是返回同一符号.
- 打印-读一致性
  内部符号总是打印为字符序列, 读回时产生同一符号.
- 打印-打印一致性
  如果两个内部符号不相同, 则它们的打印表示也会是不同的字符序列.

不管任何隐式的内部化, 这些规则为真.
只要当前包为改变, 不管加载文件顺序或实际键入符号的历史, 结果总是可重现的.
如果`*package*`的值被修改, 之后再改为前一个值, 一致性被保持.
违背这些规则的方法有: 可以通过从一个错误开始修改`*package*`的值来强制修改符号或包、调用了这些函数:
`unintern`、`unexport`、`shadow`、`shadowing-import`、`unuse-package`.

这些约束中的一个在两个命名的符号间被违背, 才产生不一致性.
`shadow`、`unexport`、`unintern`和`shadowing-import`只会影响相同名称(在`string=`含义下相同)的符号的一致性.

## <span id="2.4">2.4</span> 标准宏字符

如果读取器遇到宏字符, 则调用宏字符相关的读取器宏函数, 可能生成对象并返回.
这个函数可能读取流中宏字符后后面的字符, 以任意语法解析出对象后返回该对象.

任意字符可以设置为宏字符. 复合标准的实现中初始定义的宏字符包括:

### <span id="2.4.1">2.4.1</span> `(`(left-parenthesis)

`(`开始读取一个列表. `read`被递归调用读取输入流中后续的对象, 直到遇到`)`. 返回对象的列表.
`(a b c)`被读取为一个三个对象(符号`a`,`b`,`c`)的列表.
`)`不需要在列表中最后一对象的打印表示之后, 它的前面可以有空白字符和注释.

如果`)`之前没有对象, 则读取为零个对象的列表(空列表).

如果一个记号是不在转义字符后的点号, 并在其他对象之后被读取, 点号后有且只有一个对象: `(a b c . d)`.
这意味着列表中最后一个cons的cdr不是`nil`, 而是点号后的对象. 上面的示例等价于求值: `(cons 'a (cons 'b (cons 'c 'd)))`.
类似的:

```
(cons 'this-one 'that-one) => (this-one . that-one)
```

允许点号之后的对象是一个列表:

```
(a b c d . (e f .  (g))) == (a b c d e f g)
```

关于列表如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

### <span id="2.4.2">2.4.2</span> `)`(right-parenthesis)

`)`只有在与`(`联合使用时才有效.

### <span id="2.4.3">2.4.3</span> `'`(single-quote)

**语法**: `'<<expr>>`

单引号引入了被引用的表达式. 被Lisp读取器解析为`(quote exp)`. 见特殊操作符[quote](../Symbols#quote).

#### <span id="2.4.3.1">2.4.3.1</span> 示例: 单引号

```
'foo =>  FOO
''foo =>  (QUOTE FOO)
(car ''foo) =>  QUOTE
```

### <span id="2.4.4">2.4.4</span> `;`(semicolon)

**语法**: `;<<text>>`

分号引入可被忽略的字符, 例如注释. 分号和其后直到下一个换行符或文件结束符杜呗忽略.

#### 示例: 分号

```
(+ 3 ; three
   4)
=>  7
```

#### <span id="2.4.4.2">2.4.4.2</span> 备注: 分号的风格

一些文本编辑器有基于开始注释的分号数量的所需缩进的假设. 下面的风格约定是常见的, 但并不是普遍适用的.

##### <span id="2.4.4.2.1">2.4.4.2.1</span> 使用单个分号

以单个分号开始的注释在右端同一列对齐(有时称其为注释列).
这种注释通常只用于注释它出现的行的代码.
偶尔有两个或三个一起的行中均有这种注释, 除第一行外, 其他行前加一个空格字符(在分号之后).

##### <span id="2.4.4.2.2">2.4.4.2.2</span> 使用两个分号

以两个分号开始的注释与其注释的代码中形式的缩进层次相同.
这种注释通常描述程序在这里的状态.

##### <span id="2.4.4.2.3">2.4.4.2.3</span> 使用三个分号

以三个分号开始的注释居左对齐.
这种注释通常用于一个定义或一组定义, 而不是在定义中.

##### <span id="2.4.4.2.4">2.4.4.2.4</span> 使用四个分号

以四个分号开始的注释居左对齐.
这种注释通常包含作为代码标题的短文本, 也可以是用于生成文档形式的程序的头和脚注.

##### <span id="2.4.4.2.5">2.4.4.2.5</span> 示例: 分号的风格


``` lisp
;;;; Math Utilities

;;; FIB computes the the Fibonacci function in the traditional
;;; recursive way.

(defun fib (n)
  (check-type n integer)
  ;; At this point we're sure we have an integer argument.
  ;; Now we can get down to some serious computation.
  (cond ((< n 0)
         ;; Hey, this is just supposed to be a simple example.
         ;; Did you really expect me to handle the general case?
         (error "FIB got ~D as an argument." n))
        ((< n 2) n)             ;fib[0]=0 and fib[1]=1
        ;; The cheap cases didn't work.
        ;; Nothing more to do but recurse.
        (t (+ (fib (- n 1))     ;The traditional formula
              (fib (- n 2)))))) ; is fib[n-1]+fib[n-2].
```

### <span id="2.4.5">2.4.5</span> `"`(double-quote)

**语法**: `"<<text>>"`

双引号用于开始和结束一个字符串. 当读取器遇到双引号时, 继续读取后续字符并累积直到遇到另一个双引号.
如果遇到单转义字符, 忽略该字符, 下一字符被累积.
累积的字符作为简单字符串返回.
在处理过程中移除哪些累积字符的属性是依赖于实现的.

示例:

<span id="Figure2-18">图 2-18. 双引号使用示例.</span>

```
"Foo"                      ;A string with three characters in it
""                         ;An empty string
"\"APL\\360?\" he cried."  ;A string with twenty characters
"|x| = |-x|"               ;A ten-character string
```

注意: 在字符串中放置单转义字符或一个双引号时, 这些字符前需要有一个单转义字符. 多转义字符在字符串中不需要转义.

### <span id="2.4.6">2.4.6</span> ``` ` ```(backquote)

反引号引入了需要构建的数据结构的模板. 例如:

``` lisp
`(cond ((numberp ,x) ,@y) (t (print ,x) ,@y))
```

基本上等价于:

``` lisp
(list 'cond
      (cons (list 'numberp x) y)
      (list* 't (list 'print x) y))
```

当模板中出现`,`时, `,`后紧随的表达式需要被求值后插入在那一点.
例如, 假设`b`有值`3`, 求值形式````(a b ,b ,(+ b 1) b)```的结果为`(a b 3 4 b)`.

如果在模板中, `,`后紧随`@`, 则`@`后紧随的形式被求值为一个对象列表, 列表中对象插入那一点.
例如, 如果`x`有值`(a b c)`, 则

``` lisp
`(x ,x ,@x foo ,(cadr x) bar ,(cdr x) baz ,@(cdr x))
=>  (x (a b c) a b c foo b bar (b c) baz b c)
```

反引号的语法汇总如下:

* **``` `basic ```** 与`'basic`相同, 即`(quote basic)`, 这里表达式`basic`不是列表或向量.
* **``` `,form ```** 与`form`相同, 这里`form`的表示不以`@`或`.`开始.
* **``` `,@form ```** 有未定义的后果.
* **``` `(x1 x2 x3 ... xn . atom) ```** 被解释为: <br>
  `(append [x1] [x2] [x3] ... [xn] (quote atom))` <br><br>
  这里的`[xj]`用于表示`xj`上的转换: <br>
    - **`[form]`** 解释为``` (list `form`) ```, 有必须再次解释的反引用形式
    - **`[,form]`** 解释为`(list form)`
    - **`[,@form]`** 解释为`form`.
* **``` `(x1 x2 x3 ... xn) ```** 可被解释为````(x1 x2 x3 ... xn . nil)```.
* **``` `(x1 x2 x3 ... xn . ,form) ```** 被解释为: <br>
  `(append [x1] [x2] [x3] ... [xn] form)`
* **``` `(x1 x2 x3 ... xn . ,@form) ```** 有未定义的后果.
* **``` `#(x1 x2 x3 ... xn) ```** 被解释为``` (apply #'vector `(x1 x2 x3 ... xn)`) ```.

任何使用`,@`的地方, 可以使用 **`,.`** 作为替代, 表示允许在其后的列表结构上执行破坏性操作(实际上是使用`nconc`替换`append`).

如果使用了 ==嵌套== 的反引用语法, 则最内层的反引用形式应该首先被展开.
这意味着如果一行中出现多个`,`, 则最左边的属于最内层的``` ` ```.

实现可以自由的将反引用形式`F1`解释为任意形式`F2`, 只需要在应用上述定义求值时在`equal`语义下生成同样的结果, 替换形式`F2`的副作用行为也与上述定义一致. 构造出的模板副本可能也不可能与模板本身共享列表结果. 作为示例, 上面的定义将

``` lisp
`((,a b) ,c ,@d)
```

解释为

``` lisp
(append (list (append (list a) (list 'b) 'nil)) (list c) d 'nil)
```

但解释为下面的任何一个也是合法的:

``` lisp
(append (list (append (list a) (list 'b))) (list c) d)
(append (list (append (list a) '(b))) (list c) d)
(list* (cons a '(b)) c d)
(list* (cons a (list 'b)) c d)
(append (list (cons a '(b))) (list c) d)
(list* (cons a '(b)) c (copy-list d))
```

#### <span id="2.4.6.1">2.4.6.1</span> 备注: 反引用

因为Lisp读取器如何解析包含反引用读取器宏的表达式的精确行为未被描述, 允许实现自由的选择保留了已被描述的语义的表示.

通常实现会选择便于表达式美化打印的表示, 所以``` (pprint `(a ,b)`) ```会显示``` `(a, b) ```, 而不是`(list 'a b)`. 但这不是必须的.

没有办法做出选择的实现者可以参考 **IEEE Standard for the Scheme Programming Language**, 这个标准中有对兼容一些用户社区有用的这类表达式的表示的常见选择. 但是不要求任何符合标准的实现使用这些表示.

### <span id="2.4.7">2.4.7</span> `,`(comma)

逗号是反引用语法的一部分, 见[2.4.6 ``` ` ```(backquote)](#2.4.6). 在其他地方使用逗号是无效的.

### <span id="2.4.8">2.4.8</span> `#`(sharpsign)

`#`是非终结的分发宏字符, 读取可选的数字序列, 在读取一个字符, 并使用这个字符选择一个读取器宏函数.

标准语法中包含`#`字符引入的构造. 这些构造的语法是: 一个表明构造的类型的字符, 后跟一些形式的参数.
如果这个字符是字符, 其大小写是不重要的, 例如`#O`和`#o`是等价的.

一些`#`构造允许在`#`和字符质检出现一个无符号的数值.

与分发宏字符`#`相关的读取器宏总结如下:

<span id="Figure2-19">图 2-19. 标准`#`分发宏字符语法.</span>

```
分发字符        意图                      分发字符        意图
Backspace      signals error            {              undefined*
Tab            signals error            }              undefined*
Newline        signals error            +              read-time conditional
Linefeed       signals error            -              read-time conditional
Page           signals error            .              read-time evaluation
Return         signals error            /              undefined
Space          signals error            A, a           array
!              undefined*               B, b           binary rational
"              undefined                C, c           complex number
#              reference to = label     D, d           undefined
$              undefined                E, e           undefined
%              undefined                F, f           undefined
&              undefined                G, g           undefined
'              function abbreviation    H, h           undefined
(              simple vector            I, i           undefined
)              signals error            J, j           undefined
*              bit vector               K, k           undefined
,              undefined                L, l           undefined
:              uninterned symbol        M, m           undefined
;              undefined                N, n           undefined
<              signals error            O, o           octal rational
=              labels following object  P, p           pathname
>              undefined                Q, q           undefined
?              undefined*               R, r           radix-n rational
@              undefined                S, s           structure
[              undefined*               T, t           undefined
\              character object         U, u           undefined
]              undefined*               V, v           undefined
^              undefined                W, w           undefined
_              undefined                X, x           hexadecimal rational
`              undefined                Y, y           undefined
|              balanced comment         Z, z           undefined
~              undefined                Rubout         undefined
```

标记了`*`的项是显式预留给用户的, 符合标准的实现不存在定义.

注意到上面的表中没有出现数字, 这是因为记法`#0`, `#1`, ... `#9`保留做其他用途. 当数字在`#`之后出现, 不作为分发字符, 累积一个无符号整数参数, 并作为参数传递给数字后面的字符的读取器宏.
例如`#2A((1 2) (3 4))`使用带有参数`2`的`#A`.

#### <span id="2.4.8.1">2.4.8.1</span> `#\`(sharpsign backslash)

**语法**: `#\<<x>>`

当记号`x`是单一字符时, 被解析为字面量字符. `#\`后区分字符的大小写: `#\A`与`#\a`表示不同的字符对象.
`#\`后可以使用任意单个字符, 甚至是`(`和`)`.

在单字符情况中, `x`必须后接非要素字符.
读取`#\`后, 读取器重读回`\`, 接着读取一个记号, 将初始的`\`视为单转义字符(不管它是否早当前`readtable`中).

当记号`x`多于一个字符时, `x`必须有不含包标记的符号语法. 在这种情况中, `#\`记法被解析为名称为`(string-upcase x)`的字符, 见[13.1.7 字符名称](../13-Characters#13.1.7).

关于字符对象如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.4.8.2">2.4.8.2</span> `#'`(sharpsign single-quote)

**语法**: `#'<<expr>>`

Lisp读取器将`#'expression`解析为`(function expression)`, 见[function](../Symbols#function).

例如:

``` lisp
(apply #'+ l) ==  (apply (function +) l)
```

#### <span id="2.4.8.3">2.4.8.3</span> `#(`(sharpsign left-parenthesis)

`#(`与`)`用于表示简单向量.

如果`#`与`(`之间出现了无符号整数, 它被解释为向量的长度.
如果在`)`之前的对象的数量超过这个无符号整数时, 后果未定义.
如果在`)`之前的对象的数量小于这个无符号整数但大于0时, 最后一个对象用于填充向量中的剩余元素.
如果无符号整数不是0, `)`之前的对象数量是0, 后果未定义.

``` lisp
#(a b c c c c)
#6(a b c c c c)
#6(a b c)
#6(a b c c)
```

均表示同一事物: 长度为6的想想两, 元素为`a b c c c c`.


``` lisp
#(a b c)               ;A vector of length 3
#(2 3 5 7 11 13 17 19 23 29 31 37 41 43 47)
                       ;A vector containing the primes below 50
#()                    ;An empty vector
```

`#()`和`#0()`表示空向量.

关于向量如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.4.8.4">2.4.8.4</span> `#*`(sharpsign asterisk)

**语法**: `#*<<bits>>`

构造出包含`bits`(0或1构成)标识的简单位向量, 索引从0开始.

**语法**: `#<<n>>*<<bits>>`

构造出的位向量的长度为`n`, 如果`bits`的数量小于`n`但大于0, 则用最后一位填充位向量中剩余部分.

`#*`和`#0*`表示空的位向量.

不管是否提供了可选的数值参数`n`, `*`后面的记号按常见记号定界符定界.
除非`*read-suppress`的值为true, 否则, 在这些情况中将发出类型为`reader-error`的错误信号:

- 记号不完全由0和1组成
- 提供了`n`, 记号超过`n`位
- 提供了`n`, 且`n` > 0, 但记号长度为0

记号中不允许出现单转义字符或多转义字符.

关于位向量如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

##### <span id="2.4.8.4.1">2.4.8.4.1</span> 示例: `#*`

``` lisp
#*101111
#6*101111
#6*101
#6*1011
```

均表示元素为`1 0 1 1 1 1`的向量.

``` lisp
#*         ;An empty bit-vector
```

#### <span id="2.4.8.5">2.4.8.5</span> `#:`(sharpsign colon)

**语法**: `#:<<symbol-name>>`

`#:`引入了名称为`symbol-name`的未内部化符号(uninterned symbol).
每次遇到这个语法时, 都会创建一个不同的符号.
`symbol-name`必须有无包前缀的符号的语法.

关于未内部化符号如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.4.8.6">2.4.8.6</span> `#.`(sharpsign dot)

`#.foo`被解释为对`foo`表示的对象的求值结果.
当遇到`#.`时, 求值在`read`处理中完成. `#.`语法执行`foo`的读时求值.

当`*read-eval*`的值为false时, 禁用`#.`, 并发出类型为`reader-error`的错误信号.

对没有合适的打印表示的对象, 一个形式可以使用`#.`记法计算该对象.

#### <span id="2.4.8.7">2.4.8.7</span> `#B`(sharpsign B)

`#B`按二进制读取有理数, 例如:

``` lisp
#B1101 ==  13 ;11012
#b101/11 ==  5/3
```

如果`#B`后面的记号没有二进制有理数的语法, 后果是未定义的.

#### <span id="2.4.8.8">2.4.8.8</span> `#O`(sharpsign O)

`#O`按八进制读取有理数, 例如:

``` lisp
#o37/15 ==  31/13
#o777 ==  511
#o105 ==  69 ;1058
```

如果`#O`后面的记号没有八进制有理数的语法, 后果是未定义的.

#### <span id="2.4.8.9">2.4.8.9</span> `#X`(sharpsign X)

`#X`按十六进制读取有理数. 9之上的数字是字母`A`-`F`(及其小写形式). 例如:

``` lisp
#xF00 ==  3840
#x105 ==  261 ;10516
```

如果`#X`后面的记号没有十六进制有理数的语法, 后果是未定义的.

#### <span id="2.4.8.10">2.4.8.10</span> `#R`(sharpsign R)

`#nR`按`n`进制读取有理数, `n`必须是只有数字构成的整数, 其值范围为2-36. 只使用`n`中有效的数字.

例如, `#3r102`是十进制`11`的另一种写法, `#11R32`是十进制`35`的另一种写法.
对进制数大于10的情况, 使用字母表中的字母. 没有十进制的`#`语法, 因为使用小数点已可满足.

<span id="Figure2-20">图 2-20. 进制指示器示例.</span>

``` lisp
#2r11010101  ;Another way of writing 213 decimal
#b11010101   ;Ditto
#b+11010101  ;Ditto
#o325        ;Ditto, in octal radix
#xD5         ;Ditto, in hexadecimal radix
#16r+D5      ;Ditto
#o-300       ;Decimal -192, written in base 8
#3r-21010    ;Same thing in base 3
#25R-7H      ;Same thing in base 25
#xACCEDED    ;181202413, in hexadecimal radix
```

如果`#nR`后面的记号没有`n`进制有理数的语法, 后果是未定义的.

#### <span id="2.4.8.11">2.4.8.11</span> `#C`(sharpsign C)

`#C`后面的对象必须是一个两个元素均为实数的列表. 这两个实数分别表示复数的实部和虚部.
如果这两个部分不是同一数据类型的, 将使用[12.1.1.2 数值操作中的传染](../12-Numbers#12.1.1.2).

`#C(real imag)`等价于`#.(complex (quote real) (quote imag))`, 除了`#C`不受`*read-eval*`的影响. 见[complex](../Symbols#complex).

下面是`#C`的使用示例:

<span id="Figure2-21">图 2-21. 复数示例.</span>

``` lisp
#C(3.0s1 2.0s-1)  ;A complex with small float parts.
#C(5 -3)          ;A ``Gaussian integer''
#C(5/3 7.0)       ;Will be converted internally to #C(1.66666 7.0)
#C(0 1)           ;The imaginary unit; that is, i.
```

更多细节见[22 打印器](../22-Printer/)中相应部分和[2.3.2.3 复数的语法](#2.3.2.3).

#### <span id="2.4.8.12">2.4.8.12</span> `#A`(sharpsign A)

`#nAobject`构造维度为`n`的数组, 使用`obect`作为`make-array`的参数`:inital-contents`的值.

例如, `#2A((0 1 5) (foo 2 (hot dog)))`表示2X3的矩阵:

```
0       1       5
foo     2       (hot dog)
```

与之相反, `#1A((0 1 5) (foo 2 (hot dog)))`表示长度为2的向量, 每个元素是个列表:

```
 (0 1 5) (foo 2 (hot dog))
```

`#0A((0 1 5) (foo 2 (hot dog)))`表示0维数组, 其唯一的元素是个列表:

```
((0 1 5) (foo 2 (hot dog)))
```

`#0A foo`表示0维数组, 其唯一的元素的符号`foo`.
`#1A foo`不是有效的, 因为`foo`不是个序列.

如果列表中一些维度的表示被解析为0, 则所有后续维度也被视为0.

关于数组如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.4.8.13">2.4.8.13</span> `#S`(sharpsign S)

`#s(name slot1 value1 slot2 value2 ...)`表示一个结构.
这只在`name`是已用`defstruct`定义的结构类型的名称, 且该结构类型有标准的构造器函数. `cm`为构造器函数的名称, 则这个语法等价于:

```
#.(cm keyword1 'value1 keyword2 'value2 ...)
```

每个`keywordj`是计算这个的结果:

```
(intern (string slotj) (find-package 'keyword))
```

其效果是使用指定的槽和值调用构造器函数.

`#S`语法返回构造函数返回的对象.

关于结构如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.4.8.14">2.4.8.14</span> `#P`(sharpsign P)

`#P`后面的对象必须是个字符串.

`#P<<expression>>`等价于`#.(parse-namestring '<<expression>>')`, 除了`#P`不受`read-eval`的影响.

关于路径名如何打印的信息见[22 打印器](../22-Printer/)中相应部分.

#### <span id="2.4.8.15">2.4.8.15</span> `#n=`(sharpsign equal-sign)

`#n=object`读取有打印表示`object`的对象. 这个对象被标记上`n`, 一个必备的无符号整数, 用于被语法`#n#`引用.
这个标记的作用域是最外层调用`read`的表达式, 在同一表达式中同一比较不可出现两次.

#### <span id="2.4.8.16">2.4.8.16</span> `#n#`(sharpsign sharpsign)

`#n#`, `n`是个必须的无符号整数, 引用被`#n=`标记的相同对象.
例如下面代码中在变量`y`中创建的结构:

``` lisp
(setq x (list 'p 'q))
(setq y (list (list 'a 'b) x 'foo x))
(rplacd (last y) (cdr y))
```

可以使用这种方式表示:

``` lisp
((a b) . #1=(#2=(p q) foo #2# . #1#))
```

不使用这种标记, 将`print-length`设置为10, `print-circle`设置为`nil`时, 这个结构的打印输出为:

``` lisp
((a b) (p q) foo (p q) (p q) foo (p q) (p q) foo (p q) ...)
```

引用`#n#`只能出现在标签`#n=`之后, 不允许前向引用.
引用不允许出现在被标记的对象中(`#n=#n#`), 因为这种情况下被`#n=`标记的对象没有被很好的定义.

#### <span id="2.4.8.17">2.4.8.17</span> `#+`(sharpsign plus)

**语法**: #+test expression```

`#+`提供了读时条件化便利.
如果特性表达式`test`成功, 则这个文本记法表示打印形式为`expression`的对象.
如果特性表达式`test`失败, 则这个文本记法被视为空白.

关于特性表达式的成功和失败的详细描述, 见[24.1.2.1 特性表达式](../24-System-Construction#24.1.2.1).

`#+`首先读取特性表达式, 如果特性表达式失败, 则跳过后续的形式.
读取`test`时, 当前包是`KEYWORD`. 跳过形式由将`*read-suppress*`绑定为true后再调用`read`完成.

#### <span id="2.4.8.18">2.4.8.18</span> `#-`(sharpsign minus)

`#-`类似于`#+`, 只不过他会在`test`成功时跳过`expression`, 即:

```
#-test expression ==  #+(not test) expression
```

示例见[24.1.2.1.1 示例: 特性表达式](../24-System-Construction#24.1.2.1.1).


#### <span id="2.4.8.19">2.4.8.19</span> `#|...|#`(sharpsign vertica-bar)

`#|...|#`被读取器视为注释. `#|`与`|#`必须成对出现, 此外可以包含任意字符.

##### <span id="2.4.8.19.1">2.4.8.19.1</span> 示例: `#|...|#`

下面的示例使用了`#|...|#`记法:

``` lisp
;;; In this example, some debugging code is commented out with #|...|#
;;; Note that this kind of comment can occur in the middle of a line
;;; (because a delimiter marks where the end of the comment occurs)
;;; where a semicolon comment can only occur at the end of a line
;;; (because it comments out the rest of the line).
 (defun add3 (n) #|(format t "~&Adding 3 to ~D." n)|# (+ n 3))

;;; The examples that follow show issues related to #| ... |# nesting.

;;; In this first example, #| and |# always occur properly paired,
;;; so nesting works naturally.
 (defun mention-fun-fact-1a ()
   (format t "CL uses ; and #|...|# in comments."))
=>  MENTION-FUN-FACT-1A
 (mention-fun-fact-1a)
>>  CL uses ; and #|...|# in comments.
=>  NIL
 #| (defun mention-fun-fact-1b ()
      (format t "CL uses ; and #|...|# in comments.")) |#
 (fboundp 'mention-fun-fact-1b) =>  NIL
```

``` lisp
;;; In this example, vertical-bar followed by sharpsign needed to appear
;;; in a string without any matching sharpsign followed by vertical-bar
;;; having preceded this.  To compensate, the programmer has included a
;;; slash separating the two characters.  In case 2a, the slash is
;;; unnecessary but harmless, but in case 2b, the slash is critical to
;;; allowing the outer #| ... |# pair match.  If the slash were not present,
;;; the outer comment would terminate prematurely.
 (defun mention-fun-fact-2a ()
   (format t "Don't use |\# unmatched or you'll get in trouble!"))
=>  MENTION-FUN-FACT-2A
 (mention-fun-fact-2a)
>>  Don't use |# unmatched or you'll get in trouble!
=>  NIL
 #| (defun mention-fun-fact-2b ()
      (format t "Don't use |\# unmatched or you'll get in trouble!") |#
 (fboundp 'mention-fun-fact-2b) =>  NIL
```

``` lisp
;;; In this example, the programmer attacks the mismatch problem in a
;;; different way.  The sharpsign vertical bar in the comment is not needed
;;; for the correct parsing of the program normally (as in case 3a), but
;;; becomes important to avoid premature termination of a comment when such
;;; a program is commented out (as in case 3b).
 (defun mention-fun-fact-3a () ; #|
   (format t "Don't use |# unmatched or you'll get in trouble!"))
=>  MENTION-FUN-FACT-3A
 (mention-fun-fact-3a)
>>  Don't use |# unmatched or you'll get in trouble!
=>  NIL
 #|
 (defun mention-fun-fact-3b () ; #|
   (format t "Don't use |# unmatched or you'll get in trouble!"))
 |#
 (fboundp 'mention-fun-fact-3b) =>  NIL
```

##### <span id="2.4.8.19.2">2.4.8.19.2</span> 备注: `#|...|#`

一些文本编辑器将`|...|`视为不能嵌套的平衡对. 为绕过这种限制, 一些程序员使用`#||...#||...||#...||#`, 而不是`#|...#|...|#...|#`.
注意到这种用法不是一个新的读取器宏, 有时可以看到这样的代码:

``` lisp
#|| (+ #|| 3 ||# 4 5) ||#
```
它等价于:

``` lisp
#| (+ #| 3 |# 4 5) |#
```

#### <span id="2.4.8.20">2.4.8.20</span> `#<`(sharpsign less-than-sign)

`#<`不是有效的读取器语法. Lisp读取器在遇到`#<`时会发出类型为`reader-error`的错误信号.

这种语法通常用于打印不能读对的对象.

#### <span id="2.4.8.21">2.4.8.21</span> `# `(sharpsign whitespace)

`#`后跟空白不是有效的读取器语法. Lisp读取器在遇到`#<Newline>`或`#<Space>`时会发出类型为`reader-error`的错误信号.

#### <span id="2.4.8.22">2.4.8.22</span> `#)`(sharpsign right-parenthesis)

`#)`不是有效的读取器语法. Lisp读取器在遇到`#)`时会发出类型为`reader-error`的错误信号.

### <span id="2.4.9">2.4.9</span> 重读缩写表达式

注意到, Lisp读取器在读取到因长度或等级限制的缩写的表达式(见`*print-level*`, `*print-length*`, `*print-lines*`)、`#`后跟空白和`#)`时, 会发出类型为`reader-error`的错误信号.
