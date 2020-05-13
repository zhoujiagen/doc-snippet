# 17. 序列

[TOC]

## <span id="17.1">17.1</span> 序列的概念

序列是有序的元素集合, 通过向量或列表实现.

序列可以用函数`make-sequence`创建, 也可以使用创建类型`sequence`的子类型的对象的函数创建(例如`list`、`make-list`、`mapcar`和`vector`).

序列函数是该规范定义的或被实现扩展添加的函数, 操作一个或多个序列.
每当序列函数必须构造并返回一个新向量, 它总是返回一个简单向量.
类似的, 构造的字符串是简单字符串.

<span id="Figure17-1">图 17-1. 标准的序列函数.</span>

``` lisp
concatenate
copy-seq
count
count-if
count-if-not
delete
delete-duplicates
delete-if
delete-if-not
elt
every
fill
find
find-if
find-if-not
length
map
map-into
merge
mismatch
notany
notevery
nreverse
nsubstitute
nsubstitute-if
nsubstitute-if-not
position
position-if
position-if-not
reduce
remove
remove-duplicates
remove-if
remove-if-not
replace
reverse
search
some
sort
stable-sort
subseq
substitute
substitute-if
substitute-if-not
```

### <span id="17.1.1">17.1.1</span> 序列参数的限制

通常, 被视为序列的列表(包括关联列表和属性列表), 必须是合式列表.

## <span id="17.2">17.2</span> 测试函数的规则

### <span id="17.2.1">17.2.1</span> 满足两个传递参数的测试

当用下图列出的操作符F组合处理对象O与序列S中每个元素Ei时, 有时需要控制F测试O是否在S中出现的方式.
这个控制是由带`:test`或`:test-not`传递参数的函数提供的.

<span id="Figure17-2">图 17-2. 有两个传递参数测试的操作符.</span>

``` lisp
adjoin
assoc
count
delete
find
intersection
member
mismatch
nintersection
nset-difference
nset-exclusive-or
nsublis
nsubst
nsubstitute
nunion
position
pushnew
rassoc
remove
remove-duplicates
search
set-difference
set-exclusive-or
sublis
subsetp
subst
substitute
tree-equal
union
```

可能并不直接比较对象O和Ei. 如果提供了`:key`传递参数, 它是一个将每个Ei作为传递参数的单传递函数的指示器, 生成用于比较的对象Zi. (如果没有`:key`传递参数, Zi是Ei).

由`:key`传递参数指示的函数从不在O上调用. 然而, 如果这个函数在多个队列上操作(例如`set-difference`), O是在其它序列的一个元素上调用`:key`函数的结果.

F的传递参数`:test`, 是一个有两个传递参数(O和Zi)的函数的指示器. 如果`:test`函数返回表示true的广义布尔值, 称Ei满足测试(或O与Ei满足测试).

F的传递参数`:test-not`, 是一个有两个传递参数(O和Zi)的函数的指示器. 如果`:test`函数返回表示false的广义布尔值, 称Ei满足测试(或O与Ei满足测试).

如果没有提供`:test`或`:test-not`传递参数, 则`:test`传递参数默认为`#'eql`.

如果在对F的同一个调用中同时有`:test`和`:test-not`传递参数, 后果是未定义的.

#### <span id="17.2.1.1">17.2.1.1</span> 实例: 满足两个传递参数的测试

``` lisp
;; 使用:test
(remove "FOO" '(foo bar "FOO" "BAR" "foo" "bar") :test #'equal)
=>  (foo bar "BAR" "foo" "bar")
(remove "FOO" '(foo bar "FOO" "BAR" "foo" "bar") :test #'equalp)
=>  (foo bar "BAR" "bar")
(remove "FOO" '(foo bar "FOO" "BAR" "foo" "bar") :test #'string-equal)
=>  (bar "BAR" "bar")
(remove "FOO" '(foo bar "FOO" "BAR" "foo" "bar") :test #'string=)
=>  (BAR "BAR" "foo" "bar")

;; 使用:test-not
(remove 1 '(1 1.0 #C(1.0 0.0) 2 2.0 #C(2.0 0.0)) :test-not #'eql)
=>  (1)
(remove 1 '(1 1.0 #C(1.0 0.0) 2 2.0 #C(2.0 0.0)) :test-not #'=)
=>  (1 1.0 #C(1.0 0.0))
(remove 1 '(1 1.0 #C(1.0 0.0) 2 2.0 #C(2.0 0.0)) :test (complement #'=))
=>  (1 1.0 #C(1.0 0.0))

;; 使用:key
(count 1 '((one 1) (uno 1) (two 2) (dos 2)) :key #'cadr) =>  2

(count 2.0 '(1 2 3) :test #'eql :key #'float) =>  1

;; 使用:key和:test
(count "FOO" (list (make-pathname :name "FOO" :type "X")
                   (make-pathname :name "FOO" :type "Y"))
       :key #'pathname-name
       :test #'equal)
=>  2
```

### <span id="17.2.2">17.2.2</span> 满足一个传递参数的测试

当使用下图中的函数时, 筛选出序列S中元素E, 不是基于两传递参数谓词和[17.2.1 满足两个传递参数的测试](#17.2.1)中描述的函数下对象O出现与否, 而是基于单传递参数谓词.

<span id="Figure17-3">图 17-3. 有单个传递参数测试的操作符.</span>

``` lisp
assoc-if
assoc-if-not
count-if
count-if-not
delete-if
delete-if-not
find-if
find-if-not
member-if
member-if-not
nsubst-if
nsubst-if-not
nsubstitute-if
nsubstitute-if-not
position-if
position-if-not
rassoc-if
rassoc-if-not
remove-if
remove-if-not
subst-if
subst-if-not
substitute-if
substitute-if-not
```

可能并不直接使用Ei. 如果有`:key`传递参数, 它是将Ei作为传递参数的单传递参数函数的指示器, 生成用于比较的对象Zi(如果没有`:key`传递参数, Zi是Ei).

该规范中定义的以`-if`结尾的函数接受的第一个传递参数是单传递参数Zi的函数的指示器.
如果`:test`函数返回表示true的广义布尔值, 称Ei满足测试.

该规范中定义的以`-if-not`结尾的函数接受的第一个传递参数是单传递参数Zi的函数的指示器.
如果`:test`函数返回表示false的广义布尔值, 称Ei满足测试.

#### <span id="17.2.2.1">17.2.2.1</span> 示例: 满足一个传递参数的测试

``` lisp
;; 使用-if
(count-if #'zerop '(1 #C(0.0 0.0) 0 0.0d0 0.0s0 3)) =>  4

;; 比较使用-if和-if-not
(remove-if-not #'symbolp '(0 1 2 3 4 5 6 7 8 9 A B C D E F))
=>  (A B C D E F)
(remove-if (complement #'symbolp) '(0 1 2 3 4 5 6 7 8 9 A B C D E F))
=>  (A B C D E F)

;; 使用:key
(count-if #'zerop '("foo" "" "bar" "" "" "baz" "quux") :key #'length)
=>  3
```

## <span id="17.3">17.3</span> 序列的字典

见[序列的字典](../Dictionary#17.3).
