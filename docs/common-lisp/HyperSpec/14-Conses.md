# 14. Cons

[TOC]

## <span id="14.1">14.1</span> Cons概念

Cons是复合数据对象, 有两个组件: car和cdr.

<span id="Figure14-1">图 14-1. Cons相关的一些已定义名称.</span>

``` lisp
car
cdr
cons
rplaca
rplacd
```

依赖于上下文, 一组连接的Cons可以有不同的处理视角. 不同的处理视角有多种操作支持.

### <span id="14.1.1">14.1.1</span> Cons作为树

==树== 是由Cons和原子构成的二元递归数据结构: Cons也可以是树(有时称为子树或分支), 原子是终结符号(有时称为叶子).
通常, 叶子表示数据, 而分支建立数据之间的关系.

<span id="Figure14-2">图 14-2. 树相关的一些已定义名称.</span>

``` lisp
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
copy-tree
nsublis
nsubst
nsubst-if
nsubst-if-not
nthcdr
sublis
subst
subst-if
subst-if-not
tree-equal
```

#### <span id="14.1.1.1">14.1.1.1</span> 树作为参数的常见约束

除非特别指出, 对任意接收树作为参数的标准函数, 如果树是循环的, 后果未定义.

### <span id="14.1.2">14.1.2</span> Conse作为列表

==列表== 是Cons链, 每个Cons的car是列表的一个元素, 每个Cons的cdr或者指向是链中下一个Cons或者是终结原子.

==合式列表(proper list)== 是以空列表结束的列表. 空列表是合式列表, 但不是Cons.

==非合式列表== 是不是合式列表的列表, 即是循环列表或点列表.

==点列表== 是终结原子不是空列表的列表. 非`nil`的原子自身不被是被一个列表, 更不是点列表.

==循环列表== 是没有终结的Cons链, 因为链中一些Cons是后续某个Cons的cdr.

<span id="Figure14-3">图 14-3. 列表相关的一些已定义名称.</span>

``` lisp
append
butlast
copy-alist
copy-list
eighth
endp
fifth
first
fourth
last
ldiff
list
list*
list-length
make-list
member
member-if
member-if-not
nbutlast
nconc
ninth
nreconc
nth
nthcdr
pop
push
pushnew
rest
revappend
second
seventh
sixth
tailp
tenth
third
```

#### <span id="14.1.2.1">14.1.2.1</span> 列表作为关联列表

==关联列表== 是表示关联键和值的Cons的列表, 每个Cons的car是键, cdr是与该键关联的值.

<span id="Figure14-4">图 14-4. 关联列表相关的一些已定义名称.</span>

``` lisp
acons
assoc
assoc-if
assoc-if-not
pairlis
rassoc
rassoc-if
rassoc-if-not
```

#### <span id="14.1.2.2">14.1.2.2</span> 列表作为集

列表有时被看作集, 将其元素视为无序的, 同时假设不存在重复元素.

<span id="Figure14-5">图 14-5. 集相关的一些已定义名称.</span>

``` lisp
adjoin
intersection
nintersection
nset-difference
nset-exclusive-or
nunion
set-difference
set-exclusive-or
subsetp
union
```

#### <span id="14.1.2.3">14.1.2.3</span> 列表作为参数的常见约束

除非特别说明, 对任意接收列表作为参数的标准函数, 在值是点列表时, 应该准备发出类型为`type-error`的错误信号.

除非特别说明, 对任意接收列表作为参数的标准函数, 如果列表是循环的, 后果未定义.

## <span id="14.2">14.2</span> Cons字典

见[Cons的字典](../Dictionary#14.2).
