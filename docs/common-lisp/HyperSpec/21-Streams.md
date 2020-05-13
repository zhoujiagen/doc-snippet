# 21. 流

[TOC]

## <span id="21.1">21.1</span> 流的概念

### <span id="21.1.1">21.1.1</span> 流的介绍

流是一个对象, 被输入或输出函数使用, 用于表示该操作的字符或字节的源或汇. 字符流是字符的源或汇. 二进制流是字节的源或汇.

一些操作可以在任何种类的流上执行; 下图列出了可在任何种类的流上可用的标准操作.

<span id="Figure21-1">图 21-1. 一些通用流操作.</span>

``` lisp
close
input-stream-p
interactive-stream-p
output-stream-p
stream-element-type
streamp
with-open-stream
```

其它的操作只在特定流类型上有意义. 例如, `read-char`只可用于字符流, `read-byte`只可用于二进制流.

#### <span id="21.1.1.1">21.1.1.1</span> 流的抽象分类 - 1

##### <span id="21.1.1.1.1">21.1.1.1.1</span> 输入、输出和双向流

流, 不管是字符流还是二进制流, 可以是输入流(数据的来源)、输出流(数据的汇), 或者两者都是(`open`中使用`:direction :probe`).

下图列出了与输入流相关的操作符:

<span id="Figure21-2">图 21-2. 与输入流相关的操作符.</span>

``` lisp
clear-input
listen
peek-char
read
read-byte
read-char
read-char-no-hang
read-delimited-list
read-from-string
read-line
read-preserving-whitespace
unread-char
```

下图列出了与输出流相关的操作符:

<span id="Figure21-3">图 21-3. 与输出流相关的操作符.</span>

``` lisp
clear-output
finish-output
force-output
format
fresh-line
pprint
prin1
prin1-to-string
princ
princ-to-string
print
terpri
write
write-byte
write-char
write-line
write-string
write-to-string
```

同时是输入流和输出流的流称为双向流. 见函数`input-stream-p`和`output-stream-p`.

上面的两个图中的操作符都可用于双向流. 此外, 下图列出了特定的与双向流相关的操作符:

<span id="Figure21-4">图 21-4. 与双向流相关的操作符.</span>

``` lisp
y-or-n-p
yes-or-no-p
```

##### <span id="21.1.1.1.2">21.1.1.1.2</span> 打开的和关闭的流

流或者是打开的, 或者是关闭的.

除非特别说明, 创建和返回流的操作返回打开的流.

关闭流的动作标记流作为数据的源或汇的使用结束, 允许实现释放内部数据结构、释放可能被流打开时锁住的外部资源.

除非特别说明, 调用流时传入关闭的流的后果是未定义的.

可以将关闭的流转换为路径名; 在一些情况中, 例如真名称计算, 在关闭的流上转换的结果可能与打开的流的结果不同.

##### <span id="21.1.1.1.3">21.1.1.1.3</span> 交互式流

交互式流是可以执行交互式查询的流.

交互式流的准确含义是由实现定义的, 可能依赖于底层操作系统. 实现选择的用于表示交互式流的特征的内容的例子:

* 流连接到人(或等价物), 程序提示信息, 期望收到不同的输入.
* 程序提示输入, 支持常见的输入编辑.
* `read-char`可能在立即返回一个字符串或文件结束符之前, 等待用户的输入.

包含交互式流的意图是将这些流与有批量(背景或命令文件)输入的流区分开. 到批量流的输出通常被忽略或者保存以便后续查看, 所以对这种流的交互式查询可能不可用.

终端IO可以是也可以不是一个交互式流.

#### <span id="21.1.1.2">21.1.1.2</span> 流的抽象分类 - 2

##### <span id="21.1.1.2.1">21.1.1.2.1</span> 文件流

一些称为文件流的流, 可用于访问文件. 类`file-stream`的对象用于表示文件流.

打开文件的基本操作是`open`, 它返回一个文件流. 关闭流的基本操作是`close`.
宏`with-open-file`用于表示打开文件并执行一个代码体、并确保退出代码体时文件的习惯用法.

#### <span id="21.1.1.3">21.1.1.3</span> 流的其他子类

类`stream`有一些子类. 下图列出了关于这些子类的信息:

<span id="Figure21-5">图 21-5. 与特殊的流相关的已定义名称.</span>

|类              |相关操作符                         |
|:--------------|:--------------------------------|
|broadcast-stream |make-broadcast-stream <br> broadcast-stream-streams |
|concatenated-stream|make-concatenated-stream <br> concatenated-stream-streams|
|echo-stream  |make-echo-stream <br> echo-stream-input-stream <br> echo-stream-output-stream|
|string-stream |make-string-input-stream <br> with-input-from-string <br> make-string-output-stream <br> with-output-to-string <br> get-output-stream-string |
|synonym-stream |make-synonym-stream <br> synonym-stream-symbol  |
|two-way-stream|make-two-way-stream <br> two-way-stream-input-stream <br> two-way-stream-output-stream |


### <span id="21.1.2">21.1.2</span> 流变量

值必须是流的变量被称为流变量.

该规范中为多种需要输入或输出时但没有提供特定的流的情况, 定义了一些流变量. 下图列出了这些标准流变量. 如果任意时刻这些变量的值不是打开的流, 后果是未定义的.

<span id="Figure21-6">图 21-6. 标准的流变量.</span>

|术语        |变量名称            |
|:----------|:------------------|
|调试IO|`*debug-io* `|
|错误输出|`*error-output* `|
|查询IO|`*query-io* `|
|标准输入|`*standard-input* `|
|标准输出|`*standard-output*`|
|终端IO|`*terminal-io* `|
|trace输出|`*trace-output* `|

注意, 通常标准的流变量, 如果是输入流则名称以`-input*`结束, 如果是输出流则名称已`-output*`结束, 如果是双向流则以`-io*`结束.

用户程序可以指派或绑定除`*terminal-io*`之外的任意标准的流变量.

### <span id="21.1.3">21.1.3</span> 标准函数的流传递参数

下图中的操作符接受打开的或关闭的流的流传递参数:

<span id="Figure21-7">图 21-7. 接受打开的或关闭的流的操作符.</span>

``` lisp
broadcast-stream-streams
close
compile-file
compile-file-pathname
concatenated-stream-streams
delete-file
directory
directory-namestring
dribble
echo-stream-input-stream
echo-stream-ouput-stream
ed
enough-namestring
file-author
file-namestring
file-write-date
host-namestring
load
logical-pathname
merge-pathnames
namestring
open
open-stream-p
parse-namestring
pathname
pathname-match-p
pathnamep
probe-file
rename-file
streamp
synonym-stream-symbol
translate-logical-pathname
translate-pathname
truename
two-way-stream-input-stream
two-way-stream-output-stream
wild-pathname-p
with-open-file
```


下图中的操作符只接受打开的流的流传递参数:

<span id="Figure21-8">图 21-8. 只接受打开的流的操作符.</span>

``` lisp
clear-input
clear-output
file-length
file-position
file-string-length
finish-output
force-output
format
fresh-line
get-output-stream-string
input-stream-p
interactive-stream-p
listen
make-broadcast-stream
make-concatenated-stream
make-echo-stream
make-synonym-stream
make-two-way-stream
output-stream-p
peek-char
pprint
pprint-fill
pprint-indent
pprint-linear
pprint-logical-block
pprint-newline
pprint-tab
pprint-tabular
prin1
princ
print
print-object
print-unreadable-object
read
read-byte
read-char
read-char-no-hang
read-delimited-list
read-line
read-preserving-whitespace
stream-element-type
stream-external-format
terpri
unread-char
with-open-stream
write
write-byte
write-char
write-line
write-string
y-or-n-p
yes-or-no-p
```

### <span id="21.1.4">21.1.4</span> 聚合流的限制

如果聚合流中的任意组件在聚合流关闭之前是关闭的, 后果是未定义的.

如果从同义流的创建到关闭期间, 同义流符号没有绑定到打开的流, 后果是未定义的.

## <span id="21.2">21.2</span> 流的字典

见[流的字典](../Dictionary#21.2).
