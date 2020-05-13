# 23. 读取器

[TOC]

## <span id="23.1">23.1</span> 读取器的概念

### <span id="23.1.1">23.1.1</span> Lisp读取器的动态控制

可以动态控制Lisp读取器的多个当面. 见[2.1.1 `readtable`](../02-Syntax#2.1.1)和[2.1.2 影响Lisp读取器的变量](../02-Syntax#2.1.2).

### <span id="23.1.2">23.1.2</span>Lisp读取器中readtable大小写的作用

当前readtable的到小写按下面的方式影响Lisp读取器:

* `:upcase`<br>
当readtable的大小写是`:upcase`时, 未转义的成分字符被转换为大写, 见[2.2 读取器算法](../02-Syntax#2.2).
* `:downcase`<br>
当readtable的大小写是`:downcase`时, 未转义的成分字符被转换为小写.
* `:preserve`<br>
当readtable的大小写是`:preserve`时, 所有字符的大小写保持不变.
* `:invert`<br>
当readtable的大小写是`:invert`时, 如果在扩展记号中的所有未转义字母有同样的大小写, 则这些(未转义)的字母被转换为相反的大小写.

#### <span id="23.1.2.1">23.1.2.1</span> 示例: Lisp读取器中readtable大小写的作用

``` lisp
(defun test-readtable-case-reading ()
  (let ((*readtable* (copy-readtable nil)))
    (format t "READTABLE-CASE  Input   Symbol-name~
             ~%-----------------------------------~
             ~%")
    (dolist (readtable-case '(:upcase :downcase :preserve :invert))
      (setf (readtable-case *readtable*) readtable-case)
      (dolist (input '("ZEBRA" "Zebra" "zebra"))
        (format t "~&:~A~16T~A~24T~A"
                (string-upcase readtable-case)
                input
                (symbol-name (read-from-string input)))))))
```

`(test-readtable-case-reading)`的输出是:

``` lisp
READTABLE-CASE     Input Symbol-name
-------------------------------------
   :UPCASE         ZEBRA   ZEBRA
   :UPCASE         Zebra   ZEBRA
   :UPCASE         zebra   ZEBRA
   :DOWNCASE       ZEBRA   zebra
   :DOWNCASE       Zebra   zebra
   :DOWNCASE       zebra   zebra
   :PRESERVE       ZEBRA   ZEBRA
   :PRESERVE       Zebra   Zebra
   :PRESERVE       zebra   zebra
   :INVERT         ZEBRA   zebra
   :INVERT         Zebra   Zebra
   :INVERT         zebra   ZEBRA
```

### <span id="23.1.3">23.1.3</span> 一些读取器函数的传递参数转换

#### <span id="23.1.3.1">23.1.3.1</span> EOF-ERROR-P传递参数

在输入函数调用中的`eof-error-p`, 控制当是从文件的输入(或其它有确定尾部的输入源)且达到文件尾时会发生什么. 如果`eof-error-p`是true(默认情况), 在文件尾发出类型为`end-of-file`的错误信号. 如果是false, 不会发出错误信号, 函数会返回`eof-value`.

诸如`read`的读取对象表示而不是单个字符的函数, 不限于`eof-error-p`, 在文件在对象表示中间结束时总是会发出错误信号. 例如, 如果文件没有包含足够的平衡左括号的右括号, `read`发出错误信号. 如果文件在一个符号或数值后立即以文件结束符结束, `read`会成功读取符号或数值, 当继续调用时动作会与`eof-error-p`一样. 类似的, 函数`read-line`会成功的读取文件的最后一行, 甚至这一行中包含文件结束符而不是换行符时也是如此. 可被忽略的文本, 例如只包含空白或注释的文本, 不被视为对象的开始; 如果`read`开始读取一个表达式, 但值看到这种可被忽略的文本, 它不会认为文件在对象中间结束. 因此, 传递参数`eof-error-p`控制文件在对象中间结束时发生什么.

#### <span id="23.1.3.2">23.1.3.2</span> RECURSIVE-P传递参数

如果提供了非`nil`的`recursive-p`, 它表明这个函数调用不是罪外层的`read`调用, 而是内嵌的调用, 通常是从读取器宏函数中的调用. 提出这种递归调用有三个原因.

1. 最外层的调用建立了`#n=`和`#n#`语法作用的上下文. 考虑下面的表达式:

``` lisp
(cons '#3=(p q r) '(x y . #3#))
```

如果单引用读取器宏按如下方式定义:

``` lisp
(set-macro-character #\'       ;incorrect
   #'(lambda (stream char)
        (declare (ignore char))
        (list 'quote (read stream))))
```

则每次对单引用读取器宏函数的调用都会建立独立的`read`信息作用域的上下文, 这个作用域包括像`#3=`和`#3#`标记之间的标识的作用域. 然而, 对这个表达式, 作用域明显是想用外层括号组确定的, 所以这个定义是不正确的. 正确的方式是用`recursive-p`定义单引用读取器宏:

``` lisp
(set-macro-character #\'       ;correct
   #'(lambda (stream char)
        (declare (ignore char))
        (list 'quote (read stream t nil t))))
```


2. 递归调用不会改变读取过程是否保留是空白(由最外层调用是否是`read`或`read-preserving-whitespace`确定). 假设单引用是按上面不正确的方式定义的, 则读取表达式`'foo<Space>`的`read-preserving-whitespace`调用不会保留符号`foo`之后的空格符号, 因为这个单引用读取器宏函数调用了`read`而不是`read-preserving-whitespace`来读取后面的表达式(这种情况下的`foo`). 正确的定义, 给`read`传递值为true的`recursive-p`, 允许最外层调用确定是否保留空白.

3. 当遇到文件结束符且传递参数`eof-error-p`非`nil`时, 发出的错误信号种类可能依赖于recursive-p的值. 如果`recursive-p`是true, 则文件结束符被视为已在打印的表示中间出现; 如果为false, 则文件结束符被视为已在对象之间而不是在一个对象中出现.

## <span id="23.2">23.2</span> 读取器的字典

见[读取器的字典](../Dictionary#23.2).
