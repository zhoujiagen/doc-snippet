# 词汇表

缩写:

|Abbreviation  |Meaning|
|:-------------|:------|
|adj.          |adjective|
|adv.          |adverb|
|ANSI          |compatible with one or more ANSI standards|
|Comp.         |computers|
|Idiom.        |idiomatic|
|IEEE          |compatible with one or more IEEE standards|
|ISO           |compatible with one or more ISO standards|
|Math.         |mathematics|
|Trad.         |traditional|
|n.            |noun|
|v.            |verb|
|v.t.          |transitive verb|

### ()

!!! note "()"
    n.
    符号`nil`的另一个记法, 强调`nil`表示空列表.


### <span id="absolute">absolute</span>

!!! note "绝对的"
    adj.
    1 特定的时间点, 见[time](#time).
    2 目录层次中特定位置, 见[pathname](#pathname).

    见[relative](#relative)

### <span id="access">access</span>

!!! note "访问"
    n., v.t.
    1 读或写位置的值或数组中元素的值.
    2 尝试访问位置的值.

    见[place](#place), [value](#value), [array](#array)

### <span id="accessibility">accessibility</span>

!!! note "可访问性"
    n.
    可访问的状态.

### <span id="accessible">accessible</span>

!!! note "可访问的"
    adj.
    1 (对象)被引用的能力.
    2 (类的实现的共享或本地槽)已在实例的类或超类中定义.
    3 (包中符号)在当前包中不使用包前缀可被引用的能力, 不管符号存在于包中还是继承获得的.

    见[object](#object), [instance](#instance), [symbol](#symbol)


### <span id="accessor">accessor</span>

!!! note "访问器"
    n.
    执行访问的操作符.

    见[operator](#operator), [reader](#reader), [writer](#writer)

### <span id="active">active</span>

!!! note "活跃的"
    adj.
    1 (处理器, 重启器或捕获标签)已被建立但还未被解除
    2 (数组中元素)由大于等于0但小于填充指针的索引. 对没有填充指针的数组, 所有元素被认为是获取的.

    见[handler](#handler), [restart](#restart), [catch tag](#catch tag), [fill pointer](#fill pointer)

### <span id="actual adjustability">actual adjustability</span>

!!! note "实际可调整性"
    n.
    数组相关的一个广义布尔值, 标识数组是否是实际可调整的.

    见[actually adjustable](#actually adjustable), [expressed adjustability](#expressed adjustability), [adjustable-array-p](../Symbols#adjustable-array-p)


### <span id="actual argument">actual argument</span>

!!! note "实参"
    n. Trad.
    参数.

    见[argument](#argument)

### <span id="actual array element type">actual array element type</span>

!!! note "实际数组元素类型"
    n.
    数组实际被特化后的类型, 是数组的被表达的数组元组类型的升级的数组元素类型.

    见[array](#array), [array-element-type](../Symbols#array-element-type), [expressed array element type](#expressed array element type), [upgraded array element type](#upgraded array element type)


### <span id="actual complex part type">actual complex part type</span>

!!! note "实际复数部分类型"
    n.
    复数的实部和虚部的实际表示的类型, 是复数的被表达的复数部分类型的升级的复数部分类型.

    见[complext](#complext), [expressed complex part type ](#expressed complex part type ), [upgraded complex part type](#upgraded complex part type)

### <span id="actual parameter">actual parameter</span>

!!! note "实参"
    n. Trad.
    参数.

    见[argument](#argument)


### <span id="actually adjustable">actually adjustable</span>

!!! note "实际可调整的"
    adj.
    可用函数`ADJUST-ARRAY`直接修改数组特性. 需要满足: 数组被表达为可调整的 或 显式用`ADJUSTABLE-ARRY-P`测试.

    见[expressly adjustable](#expressly adjustable), [adjust-array](../Symbols#adjust-array), [adjustable-array-p](../Symbols#adjustable-array-p)


### <span id="adjustability">adjustability</span>

!!! note "可调整性"
    n.
    1 被表达的可调整性
    2 实际可调整性

    见[expressed adjustability](#expressed adjustability), [actual adjustability](#actual adjustability)


### <span id="adjustable">adjustable</span>

!!! note "可调整的"
    adj.
    1 表达的可调整性
    2 实际可调整性

    见[expressly adjustable](#expressly adjustable), [actually adjustable](#actually adjustable)


### <span id="after method">after method</span>

!!! note ":after方法"
    n.
    由限定符`:after`的方法.

    见[method](#method), [qualifier](#qualifier)


### <span id="alist">alist</span>

!!! note "关联列表"
    n.
    关联列表.

    见[association list](#association list)


### <span id="alphabetic">alphabetic</span>

!!! note "字母的"
    n. adj.
    1 字符: 标准字符, 实现定义的字符, 图形字符
    2 字符的要素特质, 见2.1.4.1和2.2
    3 当前`readtable`中有语法类型要素和有要素特质的字符, 见图2-8
    见[character](#character)


### <span id="alphanumeric">alphanumeric</span>

!!! note "字母或数字的"
    adj.
    字母字符或数值字符.

    见[alphabetic](#alphabetic), [numeric](#numeric)


### <span id="ampersand">ampersand</span>

!!! note "&"
    n.
    `&`的标准字符. 见图2-5.

    见[standard character](#standard character)


### <span id="anonymous">anonymous</span>

!!! note "匿名的"
    adj.
    1 (类或函数)没有名字
    2 (重启器)有名字nil

    见[mame](#name)


### <span id="apparently uninterned">apparently uninterned</span>

!!! note "明显未内部化的"
    adj.
    主包为`nil`.

    见[home package](#home package)


### <span id="applicable">applicable</span>

!!! note "可应用的"
    adj.
    1 处理器可应用
    2 方法可应用
    3 重启器可应用

    见[applicable handler](#applicable handler), [applicable method](#applicable method), [applicable restart](#applicable restart)


### <span id="applicable handler">applicable handler</span>

!!! note "可应用的处理器"
    n.
    活跃处理器关联的类型包含给定条件.

    见[condition](#condition), [handler](#handler)


### <span id="applicable method">applicable method</span>

!!! note "可应用的方法"
    n.
    广义函数的方法, 其参数满足参数特化符, 见7.6.6.1.1

    见[method](#method), [parameter specializers](#parameter specializers)


### <span id="applicable restart">applicable restart</span>

!!! note "可应用的重启器"
    n.
    1 活跃处理器关联的测试在给定条件作为参数时返回真值
    2 活跃处理器关联的测试在`nil`作为参数时返回真值

    见[handler](#handler)


### <span id="apply">apply</span>

!!! note "APPLY函数"
    v.t.
    使用列表中元素作为参数调用函数

    见[call](#call), [function](#function)


### <span id="argument">argument</span>

!!! note "参数"
    n.
    1 作为函数被调用时的数据
    2 格式控制中的格式化参数

    见[function](#function), [format argument](#format argument)


### <span id="argument evaluation order">argument evaluation order</span>

!!! note "参数求值顺序"
    n.
    参数在函数调用中的被求值的顺序, 见3.1


### <span id="argument precedence order">argument precedence order</span>

!!! note "参数优先级顺序"
    n.
    广义函数排序可应用的方法时考虑的参数顺序

    见[applicable method](#applicable method)


### <span id="around method">around method</span>

!!! note ":around方法"
    n.
    有限定符`:around`的方法.

    见[method](#method), [qualifier](#qualifier)

### <span id="array">array</span>

!!! note "数组"
    n.
    有类型`ARRAY`的对象

### <span id="array element type">array element type</span>

!!! note "数组元素类型"
    n.
    1 数组关联的类型
    2 数组的实际数据元素类型
    3 数组的被表达的数组元素类型

    见[actual array element type](#actual array element type), [expressed array element type](#expressed array element type)


### <span id="array total size">array total size</span>

!!! note "数组总大小"
    n.
    数组中元素总数量, 按数组维度之积计算.

    见[dimension](#dimension)


### <span id="assign">assign</span>

!!! note "赋值"
    v.t.
    改变已建立的绑定中变量的值.

    见[binding](#binding), [variable](#variable), [setq](../Symbols#setq)


### <span id="association list">association list</span>

!!! note "关联列表"
    n.
    表示关联的键值的cons列表, 每个cons的car是键, cdr是键对应的值.

    见[cons](#cons)


### <span id="asterisk">asterisk</span>

!!! note "*"
    n.
    标准字符`*`, 见图2-5.


### <span id="at-sign">at-sign</span>

!!! note "@"
    n.
    标准字符`@`, 见图2-5.


### <span id="atom">atom</span>

!!! note "原子"
    n.
    不是cons的对象

    见[](#)


### <span id="atomic">atomic</span>

!!! note "原子的"
    adj.
    原子的.

    见[atom](#atom)


### <span id="atomic type specifier">atomic type specifier</span>

!!! note "原子类型描述符"
    n.
    对每个原子类型描述符`x`, 存在一个等价的的复合类型描述符`(x)`

    见[compound type specifier](#compound type specifier)


### <span id="attribute">attribute</span>

!!! note "属性"
    n.
    字符的程序可见的方面, 字符的标准属性是其编码, 见13.1.3

    见[code](#code)


### <span id="aux variable">aux variable</span>

!!! note "辅助变量"
    n.
    在lambda列表中由`&aux`引入的变量, 辅助变量不是参数.

    见[lambda list](#lambda list)


### <span id="auxiliary method">auxiliary method</span>

!!! note "辅助方法"
    n.
    构成方法对应的广义函数的完备方法集合中的一个方法.

    见[primary methods](#primary methods), [method combination](#method combination)


### <span id="backquote">backquote</span>

!!! note
    .

    见[](#)


### <span id="backslash">backslash</span>

!!! note "\\"
    n.
    `\`, 称为逆斜线(reverse solidus)或反斜线(backslash). 见图2-5.


### <span id="base character">base character</span>

!!! note
    .

    见[](#)


### <span id="base string">base string</span>

!!! note
    .

    见[](#)


### <span id="before method">before method</span>

!!! note
    .

    见[](#)


### <span id="bidirectional">bidirectional</span>

!!! note
    .

    见[](#)


### <span id="binary">binary</span>

!!! note
    .

    见[](#)


### <span id="bind">bind</span>

!!! note
    .

    见[](#)


### <span id="binding">binding</span>

!!! note
    .

    见[](#)


### <span id="bit">bit</span>

!!! note
    .

    见[](#)


### <span id="bit array">bit array</span>

!!! note
    .

    见[](#)


### <span id="bit vector">bit vector</span>

!!! note
    .

    见[](#)


### <span id="bit-wise logical operation specifier">bit-wise logical operation specifier</span>

!!! note
    .

    见[](#)


### <span id="block">block</span>

!!! note
    .

    见[](#)


### <span id="block tag">block tag</span>

!!! note
    .

    见[](#)


### <span id="boa lambda list">boa lambda list</span>

!!! note
    .

    见[](#)


### <span id="body parameter">body parameter</span>

!!! note
    .

    见[](#)


### <span id="boolean">boolean</span>

!!! note
    .

    见[](#)


### <span id="boolean equivalent">boolean equivalent</span>

!!! note
    .

    见[](#)


### <span id="bound">bound</span>

!!! note
    .

    见[](#)


### <span id="bound declaration">bound declaration</span>

!!! note
    .

    见[](#)


### <span id="bounded">bounded</span>

!!! note
    .

    见[](#)


### <span id="bounding index">bounding index</span>

!!! note
    .

    见[](#)


### <span id="bounding index designator">bounding index designator</span>

!!! note
    .

    见[](#)


### <span id="break loop">break loop</span>

!!! note
    .

    见[](#)


### <span id="broadcast stream">broadcast stream</span>

!!! note
    .

    见[](#)


### <span id="built-in class">built-in class</span>

!!! note
    .

    见[](#)


### <span id="built-in type">built-in type</span>

!!! note
    .

    见[](#)


### <span id="byte">byte</span>

!!! note
    .

    见[](#)


### <span id="byte specifier">byte specifier</span>

!!! note
    .

    见[](#)


### <span id="cadr">cadr</span>

!!! note
    .

    见[](#)


### <span id="call">call</span>

!!! note
    .

    见[](#)


### <span id="captured initialization form">captured initialization form</span>

!!! note
    .

    见[](#)


### <span id="car">car</span>

!!! note
    .

    见[](#)


### <span id="case">case</span>

!!! note
    .

    见[](#)


### <span id="case sensitivity mode">case sensitivity mode</span>

!!! note
    .

    见[](#)


### <span id="catch">catch</span>

!!! note
    .

    见[](#)


### <span id="catch tag">catch tag</span>

!!! note
    .

    见[](#)


### <span id="cddr">cddr</span>

!!! note
    .

    见[](#)


### <span id="cdr">cdr</span>

!!! note
    .

    见[](#)


### <span id="cell">cell</span>

!!! note
    .

    见[](#)


### <span id="character">character</span>

!!! note
    .

    见[](#)


### <span id="character code">character code</span>

!!! note
    .

    见[](#)


### <span id="character designator">character designator</span>

!!! note
    .

    见[](#)


### <span id="circular">circular</span>

!!! note
    .

    见[](#)


### <span id="circular list">circular list</span>

!!! note
    .

    见[](#)


### <span id="class">class</span>

!!! note
    .

    见[](#)


### <span id="class designator">class designator</span>

!!! note
    .

    见[](#)


### <span id="class precedence list">class precedence list</span>

!!! note
    .

    见[](#)


### <span id="close">close</span>

!!! note
    .

    见[](#)


### <span id="closed">closed</span>

!!! note
    .

    见[](#)


### <span id="closure">closure</span>

!!! note
    .

    见[](#)


### <span id="coalesce">coalesce</span>

!!! note
    .

    见[](#)


### <span id="code">code</span>

!!! note
    .

    见[](#)


### <span id="coerce">coerce</span>

!!! note
    .

    见[](#)


### <span id="colon">colon</span>

!!! note
    .

    见[](#)


### <span id="comma">comma</span>

!!! note
    .

    见[](#)


### <span id="compilation">compilation</span>

!!! note
    .

    见[](#)


### <span id="compilation environment">compilation environment</span>

!!! note
    .

    见[](#)


### <span id="compilation unit">compilation unit</span>

!!! note
    .

    见[](#)


### <span id="compile">compile</span>

!!! note
    .

    见[](#)


### <span id="compile time">compile time</span>

!!! note
    .

    见[](#)


### <span id="compile-time definition">compile-time definition</span>

!!! note
    .

    见[](#)


### <span id="compiled code">compiled code</span>

!!! note
    .

    见[](#)


### <span id="compiled file">compiled file</span>

!!! note
    .

    见[](#)


### <span id="compiled function">compiled function</span>

!!! note
    .

    见[](#)


### <span id="compiler">compiler</span>

!!! note
    .

    见[](#)


### <span id="compiler macro">compiler macro</span>

!!! note
    .

    见[](#)


### <span id="compiler macro expansion">compiler macro expansion</span>

!!! note
    .

    见[](#)


### <span id="compiler macro form">compiler macro form</span>

!!! note
    .

    见[](#)


### <span id="compiler macro function">compiler macro function</span>

!!! note
    .

    见[](#)


### <span id="complex">complex</span>

!!! note
    .

    见[](#)


### <span id="complex float">complex float</span>

!!! note
    .

    见[](#)


### <span id="complex part type">complex part type</span>

!!! note
    .

    见[](#)


### <span id="complex rational">complex rational</span>

!!! note
    .

    见[](#)


### <span id="complex single float">complex single float</span>

!!! note
    .

    见[](#)


### <span id="composite stream">composite stream</span>

!!! note
    .

    见[](#)


### <span id="compound form">compound form</span>

!!! note
    .

    见[](#)


### <span id="compound type specifier">compound type specifier</span>

!!! note
    .

    见[](#)


### <span id="concatenated stream">concatenated stream</span>

!!! note
    .

    见[](#)


### <span id="condition">condition</span>

!!! note
    .

    见[](#)


### <span id="condition designator">condition designator</span>

!!! note
    .

    见[](#)


### <span id="condition handler">condition handler</span>

!!! note
    .

    见[](#)


### <span id="condition reporter">condition reporter</span>

!!! note
    .

    见[](#)


### <span id="conditional newline">conditional newline</span>

!!! note
    .

    见[](#)


### <span id="conformance">conformance</span>

!!! note
    .

    见[](#)


### <span id="conforming code">conforming code</span>

!!! note
    .

    见[](#)


### <span id="conforming implementation">conforming implementation</span>

!!! note
    .

    见[](#)


### <span id="conforming processor">conforming processor</span>

!!! note
    .

    见[](#)


### <span id="conforming program">conforming program</span>

!!! note
    .

    见[](#)


### <span id="congruent">congruent</span>

!!! note
    .

    见[](#)


### <span id="cons">cons</span>

!!! note
    .

    见[](#)


### <span id="constant">constant</span>

!!! note
    .

    见[](#)


### <span id="constant form">constant form</span>

!!! note
    .

    见[](#)


### <span id="constant object">constant object</span>

!!! note
    .

    见[](#)


### <span id="constant variable">constant variable</span>

!!! note
    .

    见[](#)


### <span id="constituent">constituent</span>

!!! note
    .

    见[](#)


### <span id="constituent trait">constituent trait</span>

!!! note
    .

    见[](#)


### <span id="constructed stream">constructed stream</span>

!!! note
    .

    见[](#)


### <span id="contagion">contagion</span>

!!! note
    .

    见[](#)


### <span id="continuable">continuable</span>

!!! note
    .

    见[](#)


### <span id="control form">control form</span>

!!! note
    .

    见[](#)


### <span id="copy">copy</span>

!!! note
    .

    见[](#)


### <span id="correctable">correctable</span>

!!! note
    .

    见[](#)


### <span id="current input base">current input base</span>

!!! note
    .

    见[](#)


### <span id="current logical block">current logical block</span>

!!! note
    .

    见[](#)


### <span id="current output base">current output base</span>

!!! note
    .

    见[](#)


### <span id="current package">current package</span>

!!! note
    .

    见[](#)


### <span id="current pprint dispatch table">current pprint dispatch table</span>

!!! note
    .

    见[](#)


### <span id="current random state">current random state</span>

!!! note
    .

    见[](#)


### <span id="current readtable">current readtable</span>

!!! note
    .

    见[](#)


### <span id="data type">data type</span>

!!! note
    .

    见[](#)


### <span id="debug I/O">debug I/O</span>

!!! note
    .

    见[](#)


### <span id="debugger">debugger</span>

!!! note
    .

    见[](#)


### <span id="declaration">declaration</span>

!!! note
    .

    见[](#)


### <span id="declaration identifier">declaration identifier</span>

!!! note
    .

    见[](#)


### <span id="declaration specifier">declaration specifier</span>

!!! note
    .

    见[](#)


### <span id="declare">declare</span>

!!! note
    .

    见[](#)


### <span id="decline">decline</span>

!!! note
    .

    见[](#)


### <span id="decoded time">decoded time</span>

!!! note
    .

    见[](#)


### <span id="default method">default method</span>

!!! note
    .

    见[](#)


### <span id="defaulted initialization argument list">defaulted initialization argument list</span>

!!! note
    .

    见[](#)


### <span id="define-method-combination arguments lambda list">define-method-combination arguments lambda list</span>

!!! note
    .

    见[](#)


### <span id="define-modify-macro lambda list">define-modify-macro lambda list</span>

!!! note
    .

    见[](#)


### <span id="defined name">defined name</span>

!!! note
    .

    见[](#)


### <span id="defining form">defining form</span>

!!! note
    .

    见[](#)


### <span id="defsetf lambda list">defsetf lambda list</span>

!!! note
    .

    见[](#)


### <span id="deftype lambda list">deftype lambda list</span>

!!! note
    .

    见[](#)


### <span id="denormalized">denormalized</span>

!!! note
    .

    见[](#)


### <span id="derived type">derived type</span>

!!! note
    .

    见[](#)


### <span id="derived type specifier">derived type specifier</span>

!!! note
    .

    见[](#)


### <span id="designator">designator</span>

!!! note
    .

    见[](#)


### <span id="destructive">destructive</span>

!!! note
    .

    见[](#)


### <span id="destructuring lambda list">destructuring lambda list</span>

!!! note
    .

    见[](#)


### <span id="different">different</span>

!!! note
    .

    见[](#)


### <span id="digit">digit</span>

!!! note
    .

    见[](#)


### <span id="dimension">dimension</span>

!!! note
    .

    见[](#)


### <span id="direct instance">direct instance</span>

!!! note
    .

    见[](#)


### <span id="direct subclass">direct subclass</span>

!!! note
    .

    见[](#)


### <span id="direct superclass">direct superclass</span>

!!! note
    .

    见[](#)


### <span id="disestablish">disestablish</span>

!!! note
    .

    见[](#)


### <span id="disjoint">disjoint</span>

!!! note
    .

    见[](#)


### <span id="dispatching macro character">dispatching macro character</span>

!!! note
    .

    见[](#)


### <span id="displaced array">displaced array</span>

!!! note
    .

    见[](#)


### <span id="distinct">distinct</span>

!!! note
    .

    见[](#)


### <span id="documentation string">documentation string</span>

!!! note
    .

    见[](#)


### <span id="dot">dot</span>

!!! note
    .

    见[](#)


### <span id="dotted list">dotted list</span>

!!! note
    .

    见[](#)


### <span id="dotted pair">dotted pair</span>

!!! note
    .

    见[](#)


### <span id="double float">double float</span>

!!! note
    .

    见[](#)


### <span id="double-quote">double-quote</span>

!!! note
    .

    见[](#)


### <span id="dynamic binding">dynamic binding</span>

!!! note
    .

    见[](#)


### <span id="dynamic environment">dynamic environment</span>

!!! note
    .

    见[](#)


### <span id="dynamic extent">dynamic extent</span>

!!! note
    .

    见[](#)


### <span id="dynamic scope">dynamic scope</span>

!!! note
    .

    见[](#)


### <span id="dynamic variable">dynamic variable</span>

!!! note
    .

    见[](#)


### <span id="echo stream">echo stream</span>

!!! note
    .

    见[](#)


### <span id="effective method">effective method</span>

!!! note
    .

    见[](#)


### <span id="element">element</span>

!!! note
    .

    见[](#)


### <span id="element type">element type</span>

!!! note
    .

    见[](#)


### <span id="em">em</span>

!!! note
    .

    见[](#)


### <span id="empty list">empty list</span>

!!! note
    .

    见[](#)


### <span id="empty type">empty type</span>

!!! note
    .

    见[](#)


### <span id="end of file">end of file</span>

!!! note
    .

    见[](#)


### <span id="environment">environment</span>

!!! note
    .

    见[](#)


### <span id="environment object">environment object</span>

!!! note
    .

    见[](#)


### <span id="environment parameter">environment parameter</span>

!!! note
    .

    见[](#)


### <span id="error">error</span>

!!! note
    .

    见[](#)


### <span id="error output">error output</span>

!!! note
    .

    见[](#)


### <span id="escape">escape</span>

!!! note
    .

    见[](#)


### <span id="establish">establish</span>

!!! note
    .

    见[](#)


### <span id="evaluate">evaluate</span>

!!! note
    .

    见[](#)


### <span id="evaluation">evaluation</span>

!!! note
    .

    见[](#)


### <span id="evaluation environment">evaluation environment</span>

!!! note
    .

    见[](#)


### <span id="execute">execute</span>

!!! note
    .

    见[](#)


### <span id="execution time">execution time</span>

!!! note
    .

    见[](#)


### <span id="exhaustive partition">exhaustive partition</span>

!!! note
    .

    见[](#)


### <span id="exhaustive union">exhaustive union</span>

!!! note
    .

    见[](#)


### <span id="exit point">exit point</span>

!!! note
    .

    见[](#)


### <span id="explicit return">explicit return</span>

!!! note
    .

    见[](#)


### <span id="explicit use">explicit use</span>

!!! note
    .

    见[](#)


### <span id="exponent marker">exponent marker</span>

!!! note
    .

    见[](#)


### <span id="Marker  Meaning">Marker  Meaning</span>

!!! note
    .

    见[](#)


### <span id="D or d  double-float">D or d  double-float</span>

!!! note
    .

    见[](#)


### <span id="E or e  float (see *read-default-float-format*)">E or e  float (see *read-default-float-format*)</span>

!!! note
    .

    见[](#)


### <span id="F or f  single-float">F or f  single-float</span>

!!! note
    .

    见[](#)


### <span id="L or l  long-float">L or l  long-float</span>

!!! note
    .

    见[](#)


### <span id="S or s  short-float">S or s  short-float</span>

!!! note
    .

    见[](#)


### <span id="Figure 26-1. Exponent Markers">Figure 26-1. Exponent Markers</span>

!!! note
    .

    见[](#)


### <span id="export">export</span>

!!! note
    .

    见[](#)


### <span id="exported">exported</span>

!!! note
    .

    见[](#)


### <span id="expressed adjustability">expressed adjustability</span>

!!! note
    .

    见[](#)


### <span id="expressed array element type">expressed array element type</span>

!!! note
    .

    见[](#)


### <span id="expressed complex part type">expressed complex part type</span>

!!! note
    .

    见[](#)


### <span id="expression">expression</span>

!!! note
    .

    见[](#)


### <span id="expressly adjustable">expressly adjustable</span>

!!! note
    .

    见[](#)


### <span id="extended character">extended character</span>

!!! note
    .

    见[](#)


### <span id="extended function designator">extended function designator</span>

!!! note
    .

    见[](#)


### <span id="extended lambda list">extended lambda list</span>

!!! note
    .

    见[](#)


### <span id="extension">extension</span>

!!! note
    .

    见[](#)


### <span id="extent">extent</span>

!!! note
    .

    见[](#)


### <span id="external file format">external file format</span>

!!! note
    .

    见[](#)


### <span id="external file format designator">external file format designator</span>

!!! note
    .

    见[](#)


### <span id="external symbol">external symbol</span>

!!! note
    .

    见[](#)


### <span id="externalizable object">externalizable object</span>

!!! note
    .

    见[](#)


### <span id="false">false</span>

!!! note
    .

    见[](#)


### <span id="fbound">fbound</span>

!!! note
    .

    见[](#)


### <span id="feature">feature</span>

!!! note
    .

    见[](#)


### <span id="feature expression">feature expression</span>

!!! note
    .

    见[](#)


### <span id="features list">features list</span>

!!! note
    .

    见[](#)


### <span id="file">file</span>

!!! note
    .

    见[](#)


### <span id="file compiler">file compiler</span>

!!! note
    .

    见[](#)


### <span id="file position">file position</span>

!!! note
    .

    见[](#)


### <span id="file position designator">file position designator</span>

!!! note
    .

    见[](#)


### <span id="file stream">file stream</span>

!!! note
    .

    见[](#)


### <span id="file system">file system</span>

!!! note
    .

    见[](#)


### <span id="filename">filename</span>

!!! note
    .

    见[](#)


### <span id="fill pointer">fill pointer</span>

!!! note
    .

    见[](#)


### <span id="finite">finite</span>

!!! note
    .

    见[](#)


### <span id="fixnum">fixnum</span>

!!! note
    .

    见[](#)


### <span id="float">float</span>

!!! note
    .

    见[](#)


### <span id="for-value">for-value</span>

!!! note
    .

    见[](#)


### <span id="form">form</span>

!!! note
    n.
    1 要被求值的对象.
    2 一个符号, 一个复合形式或一个自求值对象.
    3 一个复合形式, 首元素是操作符, 例如`quote`形式是个常量形式.

    见[symbol](#symbol), [compound form](#compound form), [self-evaluating object](#self-evaluating object), [operator](#operator).


### <span id="formal argument">formal argument</span>

!!! note
    .

    见[](#)


### <span id="formal parameter">formal parameter</span>

!!! note
    .

    见[](#)


### <span id="format">format</span>

!!! note
    .

    见[](#)


### <span id="format argument">format argument</span>

!!! note
    .

    见[](#)


### <span id="format control">format control</span>

!!! note
    .

    见[](#)


### <span id="format directive">format directive</span>

!!! note
    .

    见[](#)


### <span id="format string">format string</span>

!!! note
    .

    见[](#)


### <span id="free declaration">free declaration</span>

!!! note
    .

    见[](#)


### <span id="fresh">fresh</span>

!!! note
    .

    见[](#)


### <span id="freshline">freshline</span>

!!! note
    .

    见[](#)


### <span id="funbound">funbound</span>

!!! note
    .

    见[](#)


### <span id="function">function</span>

!!! note
    .

    见[](#)


### <span id="function block name">function block name</span>

!!! note
    .

    见[](#)


### <span id="function cell">function cell</span>

!!! note
    .

    见[](#)


### <span id="function designator">function designator</span>

!!! note
    .

    见[](#)


### <span id="function form">function form</span>

!!! note
    .

    见[](#)


### <span id="function name">function name</span>

!!! note
    .

    见[](#)


### <span id="functional evaluation">functional evaluation</span>

!!! note
    .

    见[](#)


### <span id="functional value">functional value</span>

!!! note
    .

    见[](#)


### <span id="further compilation">further compilation</span>

!!! note
    .

    见[](#)


### <span id="general">general</span>

!!! note
    .

    见[](#)


### <span id="generalized boolean">generalized boolean</span>

!!! note
    .

    见[](#)


### <span id="generalized instance">generalized instance</span>

!!! note
    .

    见[](#)


### <span id="generalized reference">generalized reference</span>

!!! note
    .

    见[](#)


### <span id="generalized synonym stream">generalized synonym stream</span>

!!! note
    .

    见[](#)


### <span id="generic function">generic function</span>

!!! note
    .

    见[](#)


### <span id="generic function lambda list">generic function lambda list</span>

!!! note
    .

    见[](#)


### <span id="gensym">gensym</span>

!!! note
    .

    见[](#)


### <span id="global declaration">global declaration</span>

!!! note
    .

    见[](#)


### <span id="global environment">global environment</span>

!!! note
    .

    见[](#)


### <span id="global variable">global variable</span>

!!! note
    .

    见[](#)


### <span id="glyph">glyph</span>

!!! note
    .

    见[](#)


### <span id="go">go</span>

!!! note
    .

    见[](#)


### <span id="go point one of possibly several exit points that are established by tagbody (or other abstractions, such as prog, which are built from tagbody).">go point one of possibly several exit points that are established by tagbody (or other abstractions, such as prog, which are built from tagbody).</span>

!!! note
    .

    见[](#)


### <span id="go tag">go tag</span>

!!! note
    .

    见[](#)


### <span id="graphic">graphic</span>

!!! note
    .

    见[](#)


### <span id="handle">handle</span>

!!! note
    .

    见[](#)


### <span id="handler">handler</span>

!!! note
    .

    见[](#)


### <span id="hash table">hash table</span>

!!! note
    .

    见[](#)


### <span id="home package">home package</span>

!!! note
    .

    见[](#)


### <span id="I/O customization variable">I/O customization variable</span>

!!! note
    .

    见[](#)


### <span id="*debug-io*        *error-io*         query-io*">*debug-io*        *error-io*         query-io*</span>

!!! note
    .

    见[](#)


### <span id="*standard-input*  *standard-output*  *trace-output*">*standard-input*  *standard-output*  *trace-output*</span>

!!! note
    .

    见[](#)


### <span id="Figure 26-2. Standardized I/O Customization Variables">Figure 26-2. Standardized I/O Customization Variables</span>

!!! note
    .

    见[](#)


### <span id="identical">identical</span>

!!! note
    .

    见[](#)


### <span id="identifier">identifier</span>

!!! note
    .

    见[](#)


### <span id="immutable">immutable</span>

!!! note
    .

    见[](#)


### <span id="implementation">implementation</span>

!!! note
    .

    见[](#)


### <span id="implementation limit">implementation limit</span>

!!! note
    .

    见[](#)


### <span id="implementation-defined">implementation-defined</span>

!!! note
    .

    见[](#)


### <span id="implementation-dependent">implementation-dependent</span>

!!! note
    .

    见[](#)


### <span id="implementation-independent">implementation-independent</span>

!!! note
    .

    见[](#)


### <span id="implicit block">implicit block</span>

!!! note
    .

    见[](#)


### <span id="implicit compilation">implicit compilation</span>

!!! note
    .

    见[](#)


### <span id="implicit progn">implicit progn</span>

!!! note
    .

    见[](#)


### <span id="implicit tagbody">implicit tagbody</span>

!!! note
    .

    见[](#)


### <span id="import">import</span>

!!! note
    .

    见[](#)


### <span id="improper list">improper list</span>

!!! note
    .

    见[](#)


### <span id="inaccessible">inaccessible</span>

!!! note
    .

    见[](#)


### <span id="indefinite extent">indefinite extent</span>

!!! note
    .

    见[](#)


### <span id="indefinite scope">indefinite scope</span>

!!! note
    .

    见[](#)


### <span id="indicator">indicator</span>

!!! note
    .

    见[](#)


### <span id="indirect instance">indirect instance</span>

!!! note
    .

    见[](#)


### <span id="inherit">inherit</span>

!!! note
    .

    见[](#)


### <span id="initial pprint dispatch table">initial pprint dispatch table</span>

!!! note
    .

    见[](#)


### <span id="initial readtable">initial readtable</span>

!!! note
    .

    见[](#)


### <span id="initialization argument list">initialization argument list</span>

!!! note
    .

    见[](#)


### <span id="initialization form">initialization form</span>

!!! note
    .

    见[](#)


### <span id="input">input</span>

!!! note
    .

    见[](#)


### <span id="instance">instance</span>

!!! note
    .

    见[](#)


### <span id="integer">integer</span>

!!! note
    .

    见[](#)


### <span id="interactive stream">interactive stream</span>

!!! note
    .

    见[](#)


### <span id="intern">intern</span>

!!! note
    .

    见[](#)


### <span id="internal symbol">internal symbol</span>

!!! note
    .

    见[](#)


### <span id="internal time">internal time</span>

!!! note
    .

    见[](#)


### <span id="internal time unit">internal time unit</span>

!!! note
    .

    见[](#)


### <span id="interned">interned</span>

!!! note
    .

    见[](#)


### <span id="interpreted function">interpreted function</span>

!!! note
    .

    见[](#)


### <span id="interpreted implementation">interpreted implementation</span>

!!! note
    .

    见[](#)


### <span id="interval designator">interval designator</span>

!!! note
    .

    见[](#)


### <span id="invalid">invalid</span>

!!! note
    .

    见[](#)


### <span id="iteration form">iteration form</span>

!!! note
    .

    见[](#)


### <span id="iteration variable">iteration variable</span>

!!! note
    .

    见[](#)


### <span id="key">key</span>

!!! note
    .

    见[](#)


### <span id="keyword">keyword</span>

!!! note
    .

    见[](#)


### <span id="keyword parameter">keyword parameter</span>

!!! note
    .

    见[](#)


### <span id="keyword/value pair">keyword/value pair</span>

!!! note
    .

    见[](#)


### <span id="lambda combination">lambda combination</span>

!!! note
    .

    见[](#)


### <span id="lambda expression">lambda expression</span>

!!! note
    .

    见[](#)


### <span id="lambda form">lambda form</span>

!!! note
    .

    见[](#)


### <span id="lambda list">lambda list</span>

!!! note
    .

    见[](#)


### <span id="lambda list keyword">lambda list keyword</span>

!!! note
    .

    见[](#)


### <span id="lambda variable">lambda variable</span>

!!! note
    .

    见[](#)


### <span id="leaf">leaf</span>

!!! note
    .

    见[](#)


### <span id="leap seconds">leap seconds</span>

!!! note
    .

    见[](#)


### <span id="left-parenthesis">left-parenthesis</span>

!!! note
    .

    见[](#)


### <span id="length">length</span>

!!! note
    .

    见[](#)


### <span id="lexical binding">lexical binding</span>

!!! note
    .

    见[](#)


### <span id="lexical closure">lexical closure</span>

!!! note
    .

    见[](#)


### <span id="lexical environment">lexical environment</span>

!!! note
    .

    见[](#)


### <span id="lexical scope">lexical scope</span>

!!! note
    .

    见[](#)


### <span id="lexical variable">lexical variable</span>

!!! note
    .

    见[](#)


### <span id="Lisp image">Lisp image</span>

!!! note
    .

    见[](#)


### <span id="Lisp printer">Lisp printer</span>

!!! note
    .

    见[](#)


### <span id="Lisp read-eval-print loop">Lisp read-eval-print loop</span>

!!! note
    .

    见[](#)


### <span id="Lisp reader">Lisp reader</span>

!!! note
    .

    见[](#)


### <span id="list">list</span>

!!! note
    .

    见[](#)


### <span id="list designator">list designator</span>

!!! note
    .

    见[](#)


### <span id="list structure">list structure</span>

!!! note
    .

    见[](#)


### <span id="literal">literal</span>

!!! note
    .

    见[](#)


### <span id="load">load</span>

!!! note
    .

    见[](#)


### <span id="load time">load time</span>

!!! note
    .

    见[](#)


### <span id="load time value">load time value</span>

!!! note
    .

    见[](#)


### <span id="loader">loader</span>

!!! note
    .

    见[](#)


### <span id="local declaration">local declaration</span>

!!! note
    .

    见[](#)


### <span id="local precedence order">local precedence order</span>

!!! note
    .

    见[](#)


### <span id="local slot">local slot</span>

!!! note
    .

    见[](#)


### <span id="logical block">logical block</span>

!!! note
    .

    见[](#)


### <span id="logical host">logical host</span>

!!! note
    .

    见[](#)


### <span id="logical host designator">logical host designator</span>

!!! note
    .

    见[](#)


### <span id="logical pathname">logical pathname</span>

!!! note
    .

    见[](#)


### <span id="long float">long float</span>

!!! note
    .

    见[](#)


### <span id="loop keyword">loop keyword</span>

!!! note
    .

    见[](#)


### <span id="lowercase">lowercase</span>

!!! note
    .

    见[](#)


### <span id="macro">macro</span>

!!! note
    n.
    1 一个宏形式.
    2 一个宏函数.
    3 一个宏的名称.

    见[macro form](#macro form), [macro function](#macro function), [macro name](#macro name).


### <span id="macro character">macro character</span>

!!! note
    n.
    一个字符, 当Lisp读取器在其主分发循环中遇到时, 引入读取器宏.
    宏字符与宏没有关系.

    见[](#)


### <span id="macro expansion">macro expansion</span>

!!! note
    n.
    1 将宏形式转换为另一个形式的过程.
    2 作为该过程结果产生的宏.


    见[reader macro](#reader macro).


### <span id="macro form">macro form</span>

!!! note
    n.
    代表另一个形式的形式(为了抽象、信息隐藏、语法遍历), 即或者是首元素为宏名称的复合形式, 或者是一个命名了符号宏的符号的形式.

    见[compound form](#compound form), [macro name](#macro name), [symbol macro](#symbol macro).


### <span id="macro function">macro function</span>

!!! note
    n.
    一个有两个参数的函数: 形式和环境, 这个函数实现了宏扩展, 生成作为原始参数的形式求值后的形式.

    见[form](#form), [environment](#environment), [macro expansion](#macro expansion).


### <span id="macro lambda list">macro lambda list</span>

!!! note
    n.
    用在形式中以建立宏定义的扩展的lambda列表, 例如`defmacro`、`macrolet`.

    见[3.4.4 宏lambda列表](../03-Evaluation-and-Compilation#3.4.4)


### <span id="macro name">macro name</span>

!!! note
    n.
    一个名称, 作为`macro-function`参数时返回true,
    用在复合形式的首元素处, 标识该形式为宏形式.

    见[macro-function](../Symbols#macro-function), [macro form](#macro form).


### <span id="macroexpand hook">macroexpand hook</span>

!!! note
    n.
    `*macroexpand-hook*`的值对应的函数.

    见[`*macroexpand-hook*`](../Symbols#*macroexpand-hook*)


### <span id="mapping">mapping</span>

!!! note
    .

    见[](#)


### <span id="metaclass">metaclass</span>

!!! note
    .

    见[](#)


### <span id="Metaobject Protocol">Metaobject Protocol</span>

!!! note
    .

    见[](#)


### <span id="method">method</span>

!!! note
    .

    见[](#)


### <span id="method combination">method combination</span>

!!! note
    .

    见[](#)


### <span id="method-defining form">method-defining form</span>

!!! note
    .

    见[](#)


### <span id="method-defining operator">method-defining operator</span>

!!! note
    .

    见[](#)


### <span id="minimal compilation">minimal compilation</span>

!!! note
    .

    见[](#)


### <span id="modified lambda list">modified lambda list</span>

!!! note
    .

    见[](#)


### <span id="most recent">most recent</span>

!!! note
    .

    见[](#)


### <span id="multiple escape">multiple escape</span>

!!! note
    .

    见[](#)


### <span id="multiple values">multiple values</span>

!!! note
    .

    见[](#)


### <span id="name">name</span>

!!! note
    .

    见[](#)


### <span id="named constant">named constant</span>

!!! note
    .

    见[](#)


### <span id="namespace">namespace</span>

!!! note
    .

    见[](#)


### <span id="namestring">namestring</span>

!!! note
    .

    见[](#)


### <span id="newline">newline</span>

!!! note
    .

    见[](#)


### <span id="next method">next method</span>

!!! note
    .

    见[](#)


### <span id="nickname">nickname</span>

!!! note
    .

    见[](#)


### <span id="nil">nil</span>

!!! note
    .

    见[](#)


### <span id="non-atomic">non-atomic</span>

!!! note
    .

    见[](#)


### <span id="non-constant variable">non-constant variable</span>

!!! note
    .

    见[](#)


### <span id="non-correctable">non-correctable</span>

!!! note
    .

    见[](#)


### <span id="non-empty">non-empty</span>

!!! note
    .

    见[](#)


### <span id="non-generic function">non-generic function</span>

!!! note
    .

    见[](#)


### <span id="non-graphic">non-graphic</span>

!!! note
    .

    见[](#)


### <span id="non-list">non-list</span>

!!! note
    .

    见[](#)


### <span id="non-local exit">non-local exit</span>

!!! note
    .

    见[](#)


### <span id="non-nil">non-nil</span>

!!! note
    .

    见[](#)


### <span id="non-null lexical environment">non-null lexical environment</span>

!!! note
    .

    见[](#)


### <span id="non-simple">non-simple</span>

!!! note
    .

    见[](#)


### <span id="non-terminating">non-terminating</span>

!!! note
    .

    见[](#)


### <span id="non-top-level form">non-top-level form</span>

!!! note
    .

    见[](#)


### <span id="normal return">normal return</span>

!!! note
    .

    见[](#)


### <span id="normalized">normalized</span>

!!! note
    .

    见[](#)


### <span id="null">null</span>

!!! note
    .

    见[](#)


### <span id="null lexical environment">null lexical environment</span>

!!! note
    .

    见[](#)


### <span id="number">number</span>

!!! note
    .

    见[](#)


### <span id="numeric">numeric</span>

!!! note
    .

    见[](#)


### <span id="object">object</span>

!!! note
    .

    见[](#)


### <span id="object-traversing">object-traversing</span>

!!! note
    .

    见[](#)


### <span id="open">open</span>

!!! note
    .

    见[](#)


### <span id="operator">operator</span>

!!! note
    .

    见[](#)


### <span id="optimize quality">optimize quality</span>

!!! note
    .

    见[](#)


### <span id="optional parameter">optional parameter</span>

!!! note
    .

    见[](#)


### <span id="ordinary function">ordinary function</span>

!!! note
    .

    见[](#)


### <span id="ordinary lambda list">ordinary lambda list</span>

!!! note
    .

    见[](#)


### <span id="otherwise inaccessible part">otherwise inaccessible part</span>

!!! note
    .

    见[](#)


### <span id="output">output</span>

!!! note
    .

    见[](#)


### <span id="package">package</span>

!!! note
    .

    见[](#)


### <span id="package cell">package cell</span>

!!! note
    .

    见[](#)


### <span id="package designator">package designator</span>

!!! note
    .

    见[](#)


### <span id="package marker">package marker</span>

!!! note
    .

    见[](#)


### <span id="package prefix">package prefix</span>

!!! note
    .

    见[](#)


### <span id="package registry">package registry</span>

!!! note
    .

    见[](#)


### <span id="pairwise">pairwise</span>

!!! note
    .

    见[](#)


### <span id="parallel">parallel</span>

!!! note
    .

    见[](#)


### <span id="parameter">parameter</span>

!!! note
    .

    见[](#)


### <span id="parameter specializer">parameter specializer</span>

!!! note
    .

    见[](#)


### <span id="parameter specializer name">parameter specializer name</span>

!!! note
    .

    见[](#)


### <span id="pathname">pathname</span>

!!! note
    .

    见[](#)


### <span id="pathname designator">pathname designator</span>

!!! note
    .

    见[](#)


### <span id="physical pathname">physical pathname</span>

!!! note
    .

    见[](#)


### <span id="place">place</span>

!!! note
    .

    见[](#)


### <span id="plist">plist</span>

!!! note
    .

    见[](#)


### <span id="portable">portable</span>

!!! note
    .

    见[](#)


### <span id="potential copy">potential copy</span>

!!! note
    .

    见[](#)


### <span id="potential number">potential number</span>

!!! note "可能的数值"
    n.
    在一些符合标准的实现中被Lisp读取器解析为数值的文本内容, 但不要求被解析为数值.
    没有对象是可能的数值: 一个对象或者是数值, 或者不是, 见[2.3.1.1 可能的数值的记号](../02-Syntax#2.3.1.1).

    见[number](#number)


### <span id="pprint dispatch table">pprint dispatch table</span>

!!! note
    .

    见[](#)


### <span id="predicate">predicate</span>

!!! note
    .

    见[](#)


### <span id="present">present</span>

!!! note
    .

    见[](#)


### <span id="pretty print">pretty print</span>

!!! note
    .

    见[](#)


### <span id="pretty printer">pretty printer</span>

!!! note
    .

    见[](#)


### <span id="pretty printing stream">pretty printing stream</span>

!!! note
    .

    见[](#)


### <span id="primary method">primary method</span>

!!! note
    .

    见[](#)


### <span id="primary value">primary value</span>

!!! note
    .

    见[](#)


### <span id="principal">principal</span>

!!! note
    .

    见[](#)


### <span id="print name">print name</span>

!!! note
    .

    见[](#)


### <span id="printer control variable">printer control variable</span>

!!! note
    .

    见[](#)


### <span id="printer escaping">printer escaping</span>

!!! note
    .

    见[](#)


### <span id="printing">printing</span>

!!! note
    .

    见[](#)


### <span id="process">process</span>

!!! note
    .

    见[](#)


### <span id="processor">processor</span>

!!! note
    .

    见[](#)


### <span id="proclaim">proclaim</span>

!!! note
    .

    见[](#)


### <span id="proclamation">proclamation</span>

!!! note
    .

    见[](#)


### <span id="prog tag">prog tag</span>

!!! note
    .

    见[](#)


### <span id="program">program</span>

!!! note
    .

    见[](#)


### <span id="programmer">programmer</span>

!!! note
    .

    见[](#)


### <span id="programmer code">programmer code</span>

!!! note
    .

    见[](#)


### <span id="proper list">proper list</span>

!!! note
    .

    见[](#)


### <span id="proper name">proper name</span>

!!! note
    .

    见[](#)


### <span id="proper sequence">proper sequence</span>

!!! note
    .

    见[](#)


### <span id="proper subtype">proper subtype</span>

!!! note
    .

    见[](#)


### <span id="property">property</span>

!!! note
    .

    见[](#)


### <span id="property indicator">property indicator</span>

!!! note
    .

    见[](#)


### <span id="property list">property list</span>

!!! note
    .

    见[](#)


### <span id="property value">property value</span>

!!! note
    .

    见[](#)


### <span id="purports to conform">purports to conform</span>

!!! note
    .

    见[](#)


### <span id="qualified method">qualified method</span>

!!! note
    .

    见[](#)


### <span id="qualifier">qualifier</span>

!!! note
    .

    见[](#)


### <span id="query I/O">query I/O</span>

!!! note
    .

    见[](#)


### <span id="quoted object">quoted object</span>

!!! note
    .

    见[](#)


### <span id="radix">radix</span>

!!! note
    .

    见[](#)


### <span id="random state">random state</span>

!!! note
    .

    见[](#)


### <span id="rank">rank</span>

!!! note
    .

    见[](#)


### <span id="ratio">ratio</span>

!!! note
    .

    见[](#)


### <span id="ratio marker">ratio marker</span>

!!! note
    .

    见[](#)


### <span id="rational">rational</span>

!!! note
    .

    见[](#)


### <span id="read">read</span>

!!! note
    .

    见[](#)


### <span id="readably">readably</span>

!!! note
    .

    见[](#)


### <span id="reader">reader</span>

!!! note
    .

    见[](#)


### <span id="reader macro">reader macro</span>

!!! note
    .

    见[](#)


### <span id="reader macro function">reader macro function</span>

!!! note
    .

    见[](#)


### <span id="readtable">readtable</span>

!!! note
    .

    见[](#)


### <span id="readtable case">readtable case</span>

!!! note
    .

    见[](#)


### <span id="readtable designator">readtable designator</span>

!!! note
    .

    见[](#)


### <span id="recognizable subtype">recognizable subtype</span>

!!! note
    .

    见[](#)


### <span id="reference">reference</span>

!!! note
    .

    见[](#)


### <span id="registered package">registered package</span>

!!! note
    .

    见[](#)


### <span id="relative">relative</span>

!!! note
    .

    见[](#)


### <span id="repertoire">repertoire</span>

!!! note
    .

    见[](#)


### <span id="report">report</span>

!!! note
    .

    见[](#)


### <span id="report message">report message</span>

!!! note
    .

    见[](#)


### <span id="required parameter">required parameter</span>

!!! note
    .

    见[](#)


### <span id="rest list">rest list</span>

!!! note
    .

    见[](#)


### <span id="rest parameter">rest parameter</span>

!!! note
    .

    见[](#)


### <span id="restart">restart</span>

!!! note
    .

    见[](#)


### <span id="restart designator">restart designator</span>

!!! note
    .

    见[](#)


### <span id="restart function">restart function</span>

!!! note
    .

    见[](#)


### <span id="abort     muffle-warning  use-value">abort     muffle-warning  use-value</span>

!!! note
    .

    见[](#)


### <span id="continue  store-value">continue  store-value</span>

!!! note
    .

    见[](#)


### <span id="Figure 26-4. Standardized Restart Functions">Figure 26-4. Standardized Restart Functions</span>

!!! note
    .

    见[](#)


### <span id="return">return</span>

!!! note
    .

    见[](#)


### <span id="return value">return value</span>

!!! note
    .

    见[](#)


### <span id="right-parenthesis">right-parenthesis</span>

!!! note
    .

    见[](#)


### <span id="Rubout">Rubout</span>

!!! note
    n.
    删除字符.

    见[13.1.7 字符名称](../13-Characters#13.1.7)

### <span id="run time">run time</span>

!!! note
    .

    见[](#)


### <span id="run-time compiler">run-time compiler</span>

!!! note
    .

    见[](#)


### <span id="run-time definition">run-time definition</span>

!!! note
    .

    见[](#)


### <span id="run-time environment">run-time environment</span>

!!! note
    .

    见[](#)


### <span id="safe">safe</span>

!!! note
    .

    见[](#)


### <span id="safe call">safe call</span>

!!! note
    .

    见[](#)


### <span id="same">same</span>

!!! note
    .

    见[](#)


### <span id="satisfy the test">satisfy the test</span>

!!! note
    .

    见[](#)


### <span id="scope">scope</span>

!!! note
    .

    见[](#)


### <span id="script">script</span>

!!! note
    .

    见[](#)


### <span id="secondary value">secondary value</span>

!!! note
    .

    见[](#)


### <span id="section">section</span>

!!! note
    .

    见[](#)


### <span id="self-evaluating object">self-evaluating object</span>

!!! note
    .

    见[](#)


### <span id="semi-standard">semi-standard</span>

!!! note
    .

    见[](#)


### <span id="semicolon">semicolon</span>

!!! note
    .

    见[](#)


### <span id="sequence">sequence</span>

!!! note
    .

    见[](#)


### <span id="sequence function">sequence function</span>

!!! note
    .

    见[](#)


### <span id="sequential">sequential</span>

!!! note
    .

    见[](#)


### <span id="sequentially">sequentially</span>

!!! note
    .

    见[](#)


### <span id="serious condition">serious condition</span>

!!! note
    .

    见[](#)


### <span id="session">session</span>

!!! note
    .

    见[](#)


### <span id="set">set</span>

!!! note
    .

    见[](#)


### <span id="setf expander">setf expander</span>

!!! note
    .

    见[](#)


### <span id="setf expansion">setf expansion</span>

!!! note
    .

    见[](#)


### <span id="setf function">setf function</span>

!!! note
    .

    见[](#)


### <span id="setf function name">setf function name</span>

!!! note
    .

    见[](#)


### <span id="shadow">shadow</span>

!!! note
    .

    见[](#)


### <span id="shadowing symbol">shadowing symbol</span>

!!! note
    .

    见[](#)


### <span id="shadowing symbols list">shadowing symbols list</span>

!!! note
    .

    见[](#)


### <span id="shared slot">shared slot</span>

!!! note
    .

    见[](#)


### <span id="sharpsign">sharpsign</span>

!!! note "#"
    n.
    井号符号. 见图2-5.

    见[standard character](#standard character)


### <span id="short float">short float</span>

!!! note
    .

    见[](#)


### <span id="sign">sign</span>

!!! note
    .

    见[](#)


### <span id="signal">signal</span>

!!! note
    .

    见[](#)


### <span id="signature">signature</span>

!!! note
    .

    见[](#)


### <span id="similar">similar</span>

!!! note
    .

    见[](#)


### <span id="similarity">similarity</span>

!!! note
    .

    见[](#)


### <span id="simple">simple</span>

!!! note
    .

    见[](#)


### <span id="simple array">simple array</span>

!!! note
    .

    见[](#)


### <span id="simple bit array">simple bit array</span>

!!! note
    .

    见[](#)


### <span id="simple bit vector">simple bit vector</span>

!!! note
    .

    见[](#)


### <span id="simple condition">simple condition</span>

!!! note
    .

    见[](#)


### <span id="simple general vector">simple general vector</span>

!!! note
    .

    见[](#)


### <span id="simple string">simple string</span>

!!! note
    .

    见[](#)


### <span id="simple vector">simple vector</span>

!!! note
    .

    见[](#)


### <span id="single escape">single escape</span>

!!! note
    .

    见[](#)


### <span id="single float">single float</span>

!!! note
    .

    见[](#)


### <span id="single-quote">single-quote</span>

!!! note
    .

    见[](#)


### <span id="singleton">singleton</span>

!!! note
    .

    见[](#)


### <span id="situation">situation</span>

!!! note
    .

    见[](#)


### <span id="slash">slash</span>

!!! note
    .

    见[](#)


### <span id="slot">slot</span>

!!! note
    .

    见[](#)


### <span id="slot specifier">slot specifier</span>

!!! note
    .

    见[](#)


### <span id="source code">source code</span>

!!! note
    .

    见[](#)


### <span id="source file">source file</span>

!!! note
    .

    见[](#)


### <span id="space">space</span>

!!! note
    .

    见[](#)


### <span id="special form">special form</span>

!!! note
    n
    不是宏形式的一个列表, 是一个有特殊语法或特殊求值规则形式, 可能操作求值环境或控制流.
    特殊形式的首个元素是特殊操作符.

    见[form](#form), [macro form](#macro form), [evaluation](#evaluation), [special operator](#special operator).


### <span id="special operator">special operator</span>

!!! note
    图3-2中列举的一组固定符号, 出现在形式的car中将该形式标识为特殊形式..

    见[symbol](#symbol), [special form](#special form).


### <span id="special variable">special variable</span>

!!! note
    .

    见[](#)


### <span id="specialize">specialize</span>

!!! note
    .

    见[](#)


### <span id="specialized">specialized</span>

!!! note
    .

    见[](#)


### <span id="specialized lambda list">specialized lambda list</span>

!!! note
    .

    见[](#)


### <span id="spreadable argument list designator">spreadable argument list designator</span>

!!! note
    .

    见[](#)


### <span id="stack allocate">stack allocate</span>

!!! note
    .

    见[](#)


### <span id="stack-allocated">stack-allocated</span>

!!! note
    .

    见[](#)


### <span id="standard character">standard character</span>

!!! note
    .

    见[](#)


### <span id="standard class">standard class</span>

!!! note
    .

    见[](#)


### <span id="standard generic function a function of type standard-generic-function.">standard generic function a function of type standard-generic-function.</span>

!!! note
    .

    见[](#)


### <span id="standard input">standard input</span>

!!! note
    .

    见[](#)


### <span id="standard method combination">standard method combination</span>

!!! note
    .

    见[](#)


### <span id="standard object">standard object</span>

!!! note
    .

    见[](#)


### <span id="standard output">standard output</span>

!!! note
    .

    见[](#)


### <span id="standard pprint dispatch table">standard pprint dispatch table</span>

!!! note
    .

    见[](#)


### <span id="standard readtable">standard readtable</span>

!!! note
    .

    见[](#)


### <span id="standard syntax">standard syntax</span>

!!! note
    .

    见[](#)


### <span id="standardized">standardized</span>

!!! note
    .

    见[](#)


### <span id="startup environment">startup environment</span>

!!! note
    .

    见[](#)


### <span id="step">step</span>

!!! note
    .

    见[](#)


### <span id="stream">stream</span>

!!! note
    .

    见[](#)


### <span id="stream associated with a file">stream associated with a file</span>

!!! note
    .

    见[](#)


### <span id="stream designator">stream designator</span>

!!! note
    .

    见[](#)


### <span id="stream element type">stream element type</span>

!!! note
    .

    见[](#)


### <span id="stream variable">stream variable</span>

!!! note
    .

    见[](#)


### <span id="stream variable designator">stream variable designator</span>

!!! note
    .

    见[](#)


### <span id="string">string</span>

!!! note
    .

    见[](#)


### <span id="string designator">string designator</span>

!!! note
    .

    见[](#)


### <span id="string equal">string equal</span>

!!! note
    .

    见[](#)


### <span id="string stream">string stream</span>

!!! note
    .

    见[](#)


### <span id="structure">structure</span>

!!! note
    .

    见[](#)


### <span id="structure class">structure class</span>

!!! note
    .

    见[](#)


### <span id="structure name">structure name</span>

!!! note
    .

    见[](#)


### <span id="style warning">style warning</span>

!!! note
    .

    见[](#)


### <span id="subclass">subclass</span>

!!! note
    .

    见[](#)


### <span id="subexpression">subexpression</span>

!!! note
    .

    见[](#)


### <span id="subform">subform</span>

!!! note
    .

    见[](#)


### <span id="subrepertoire">subrepertoire</span>

!!! note
    .

    见[](#)


### <span id="subtype">subtype</span>

!!! note
    .

    见[](#)


### <span id="superclass">superclass</span>

!!! note
    .

    见[](#)


### <span id="supertype">supertype</span>

!!! note
    .

    见[](#)


### <span id="supplied-p parameter">supplied-p parameter</span>

!!! note
    .

    见[](#)


### <span id="symbol">symbol</span>

!!! note
    .

    见[](#)


### <span id="symbol macro">symbol macro</span>

!!! note
    .

    见[](#)


### <span id="synonym stream">synonym stream</span>

!!! note
    .

    见[](#)


### <span id="synonym stream symbol">synonym stream symbol</span>

!!! note
    .

    见[](#)


### <span id="syntax type">syntax type</span>

!!! note
    .

    见[](#)


### <span id="system class">system class</span>

!!! note
    .

    见[](#)


### <span id="system code">system code</span>

!!! note
    .

    见[](#)


### <span id="t">t</span>

!!! note
    .

    见[](#)


### <span id="tag">tag</span>

!!! note
    .

    见[](#)


### <span id="tail">tail</span>

!!! note
    .

    见[](#)


### <span id="target">target</span>

!!! note
    .

    见[](#)


### <span id="terminal I/O">terminal I/O</span>

!!! note
    .

    见[](#)


### <span id="terminating">terminating</span>

!!! note
    .

    见[](#)


### <span id="tertiary value">tertiary value</span>

!!! note
    .

    见[](#)


### <span id="throw">throw</span>

!!! note
    .

    见[](#)


### <span id="tilde">tilde</span>

!!! note
    .

    见[](#)


### <span id="time">time</span>

!!! note
    .

    见[](#)


### <span id="time zone">time zone</span>

!!! note
    .

    见[](#)


### <span id="token">token</span>

!!! note "记号"
    数值或符号的文本表示. 见[2.3 解释记号](../02-Syntax#2.3)

    见[number](#number), [symbol](#symbol)


### <span id="top level form">top level form</span>

!!! note
    .

    见[](#)


### <span id="trace output">trace output</span>

!!! note
    .

    见[](#)


### <span id="tree">tree</span>

!!! note
    .

    见[](#)


### <span id="tree structure">tree structure</span>

!!! note
    .

    见[](#)


### <span id="true">true</span>

!!! note
    .

    见[](#)


### <span id="truename">truename</span>

!!! note
    .

    见[](#)


### <span id="two-way stream">two-way stream</span>

!!! note
    .

    见[](#)


### <span id="type">type</span>

!!! note
    .

    见[](#)


### <span id="type declaration">type declaration</span>

!!! note
    .

    见[](#)


### <span id="type equivalent">type equivalent</span>

!!! note
    .

    见[](#)


### <span id="type expand">type expand</span>

!!! note
    .

    见[](#)


### <span id="type specifier">type specifier</span>

!!! note
    .

    见[](#)


### <span id="unbound">unbound</span>

!!! note
    .

    见[](#)


### <span id="unbound variable">unbound variable</span>

!!! note
    .

    见[](#)


### <span id="undefined function">undefined function</span>

!!! note
    .

    见[](#)


### <span id="unintern">unintern</span>

!!! note
    .

    见[](#)


### <span id="uninterned">uninterned</span>

!!! note
    .

    见[](#)


### <span id="universal time">universal time</span>

!!! note
    .

    见[](#)


### <span id="unqualified method">unqualified method</span>

!!! note
    .

    见[](#)


### <span id="unregistered package">unregistered package</span>

!!! note
    .

    见[](#)


### <span id="unsafe">unsafe</span>

!!! note
    .

    见[](#)


### <span id="unsafe call">unsafe call</span>

!!! note
    .

    见[](#)


### <span id="upgrade">upgrade</span>

!!! note
    .

    见[](#)


### <span id="upgraded array element type">upgraded array element type</span>

!!! note
    .

    见[](#)


### <span id="upgraded complex part type">upgraded complex part type</span>

!!! note
    .

    见[](#)


### <span id="uppercase">uppercase</span>

!!! note
    .

    见[](#)


### <span id="use">use</span>

!!! note
    .

    见[](#)


### <span id="use list">use list</span>

!!! note
    .

    见[](#)


### <span id="user">user</span>

!!! note
    .

    见[](#)


### <span id="valid array dimension">valid array dimension</span>

!!! note
    .

    见[](#)


### <span id="valid array index">valid array index</span>

!!! note
    .

    见[](#)


### <span id="valid array row-major index">valid array row-major index</span>

!!! note
    .

    见[](#)


### <span id="valid fill pointer">valid fill pointer</span>

!!! note
    .

    见[](#)


### <span id="valid logical pathname host">valid logical pathname host</span>

!!! note
    .

    见[](#)


### <span id="valid pathname device">valid pathname device</span>

!!! note
    .

    见[](#)


### <span id="valid pathname directory">valid pathname directory</span>

!!! note
    .

    见[](#)


### <span id="valid pathname host">valid pathname host</span>

!!! note
    .

    见[](#)


### <span id="valid pathname name">valid pathname name</span>

!!! note
    .

    见[](#)


### <span id="valid pathname type">valid pathname type</span>

!!! note
    .

    见[](#)


### <span id="valid pathname version">valid pathname version</span>

!!! note
    .

    见[](#)


### <span id="valid physical pathname host">valid physical pathname host</span>

!!! note
    .

    见[](#)


### <span id="valid sequence index">valid sequence index</span>

!!! note
    .

    见[](#)


### <span id="value">value</span>

!!! note
    .

    见[](#)


### <span id="value cell">value cell</span>

!!! note
    .

    见[](#)


### <span id="variable">variable</span>

!!! note
    .

    见[](#)


### <span id="vector">vector</span>

!!! note
    .

    见[](#)


### <span id="vertical-bar">vertical-bar</span>

!!! note "|"
    n.
    `|`, 称为竖线的标准字符.

    见[](#)


### <span id="whitespace">whitespace</span>

!!! note
    .

    见[](#)


### <span id="wild">wild</span>

!!! note
    .

    见[](#)


### <span id="write">write</span>

!!! note
    .

    见[](#)


### <span id="writer">writer</span>

!!! note
    .

    见[](#)


### <span id="yield">yield</span>

!!! note
    .

    见[](#)
