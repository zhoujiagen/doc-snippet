# 符号

> There are 978 symbols in the COMMON-LISP package.


## <span id="&allow-other-keys">&allow-other-keys</span>
## <span id="&aux">&aux</span>
## <span id="&body">&body</span>
## <span id="&environment">&environment</span>
## <span id="&key">&key</span>
## <span id="&optional">&optional</span>
## <span id="&rest">&rest</span>
## <span id="&whole">&whole</span>
## <span id="*">`*`</span>
## <span id="**">`**`</span>
## <span id="***">`***`</span>
## <span id="*break-on-signals*">`*break-on-signals*`</span>
## <span id="*compile-file-pathname*">`*compile-file-pathname*`</span>
## <span id="*compile-file-truename*">`*compile-file-truename*`</span>
## <span id="*compile-print*">`*compile-print*`</span>
## <span id="*compile-verbose*">`*compile-verbose*`</span>
## <span id="*debug-io*">`*debug-io*`</span>
## <span id="*debugger-hook*">`*debugger-hook*`</span>
## <span id="*default-pathname-defaults*">`*default-pathname-defaults*`</span>
## <span id="*error-output*">`*error-output*`</span>
## <span id="*features*">`*features*`</span>
## <span id="*gensym-counter*">`*gensym-counter*`</span>
## <span id="*load-pathname*">`*load-pathname*`</span>
## <span id="*load-print*">`*load-print*`</span>
## <span id="*load-truename*">`*load-truename*`</span>
## <span id="*load-verbose*">`*load-verbose*`</span>
## <span id="*macroexpand-hook*">`*macroexpand-hook*`</span>
## <span id="*modules*">`*modules*`</span>
## <span id="*package*">`*package*`</span>
## <span id="*print-array*">`*print-array*`</span>
## <span id="*print-base*">`*print-base*`</span>
## <span id="*print-case*">`*print-case*`</span>
## <span id="*print-circle*">`*print-circle*`</span>
## <span id="*print-escape*">`*print-escape*`</span>
## <span id="*print-gensym*">`*print-gensym*`</span>
## <span id="*print-length*">`*print-length*`</span>
## <span id="*print-level*">`*print-level*`</span>
## <span id="*print-lines*">`*print-lines*`</span>
## <span id="*print-miser-width*">`*print-miser-width*`</span>
## <span id="*print-pprint-dispatch*">`*print-pprint-dispatch*`</span>
## <span id="*print-pretty*">`*print-pretty*`</span>
## <span id="*print-radix*">`*print-radix*`</span>
## <span id="*print-readably*">`*print-readably*`</span>
## <span id="*print-right-margin*">`*print-right-margin*`</span>
## <span id="*query-io*">`*query-io*`</span>
## <span id="*random-state*">`*random-state*`</span>
## <span id="*read-base*">`*read-base*`</span>
## <span id="*read-default-float-format*">`*read-default-float-format*`</span>
## <span id="*read-eval*">`*read-eval*`</span>
## <span id="*read-suppress*">`*read-suppress*`</span>
## <span id="*readtable*">`*readtable*`</span>
## <span id="*standard-input*">`*standard-input*`</span>
## <span id="*standard-output*">`*standard-output*`</span>
## <span id="*terminal-io*">`*terminal-io*`</span>
## <span id="*trace-output*">`*trace-output*`</span>
## <span id="+">`+`</span>
## <span id="++">`++`</span>
## <span id="+++">`+++`</span>
## <span id="-">`-`</span>
## <span id="/">`/`</span>
## <span id="//">`//`</span>
## <span id="///">`///`</span>
## <span id="/=">`/=`</span>
## <span id="1+">`1+`</span>
## <span id="1-">`1-`</span>
## <span id="<">`<`</span>
## <span id="<=">`<=`</span>
## <span id="=">`=`</span>
## <span id=">">`>`</span>
## <span id=">=">`>=`</span>
## <span id="abort">abort</span>
## <span id="abs">abs</span>
## <span id="acons">acons</span>
## <span id="acos">acos</span>
## <span id="acosh">acosh</span>
## <span id="add-method">add-method</span>
## <span id="adjoin">adjoin</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    adjoin item list &key key test test-not => new-list
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="adjust-array">adjust-array</span>
## <span id="adjustable-array-p">adjustable-array-p</span>
## <span id="allocate-instance">allocate-instance</span>
## <span id="alpha-char-p">alpha-char-p</span>
## <span id="alphanumericp">alphanumericp</span>
## <span id="and">and</span>

!!! tip "Macro"
	**Syntax**:

	``` lisp
	and form* => result*
	```

**Arguments and Values**:

- `form`: 一个形式
- `results`: 最后一个`form`的求值结果, 或符号`nil`, 或`t`

**Description**:

宏`and`按 **从左至右** 的顺序一次求值一个`form`.
一旦任意形式求值为`nil`, `and`不再对剩下的`form`求值直接返回`nil`.
如果除最后一个外所有的`form`求值为true, `and`返回求值最后一个`form`的结果.

如果没有提供`form`, `(and)`返回`t`.

`and`可以返回最后一个子形式的多值.

**Examples**:

``` lisp
(if (and (>= n 0)
				 (< n (length a-simple-vector))
				 (eq (elt a-simple-vector n) 'foo))
		(princ "Foo!"))
```

如果`a-simple-vector`的`n`位置元素是符号`foo`, `n`对`a-simple-vector`是有效的索引, 则上述表达式输出`Foo!`. 如果`n`越界, 因为`and`保证从左至右的顺序测试其内容部分, `elt`不被调用.

``` lisp
(setq temp1 1 temp2 1 temp3 1) =>  1
(and (incf temp1) (incf temp2) (incf temp3)) =>  2
(and (eql 2 temp1) (eql 2 temp2) (eql 2 temp3)) =>  true
(decf temp3) =>  1

; (eq temp3 'nil)求值为false, (decf temp3)不被执行
(and (decf temp1) (decf temp2) (eq temp3 'nil) (decf temp3)) =>  NIL

(and (eql temp1 temp2) (eql temp2 temp3)) =>  true
(and) =>  T
```

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[cond](#cond), [every](#every), [if](#if), [or](#or), [when](#when)

**Notes**:

``` lisp
(and form) ==  (let () form)
(and form1 form2 ...) ==  (when form1 (and form2 ...))
```


## <span id="append">append</span>
## <span id="apply">apply</span>
## <span id="apropos">apropos</span>
## <span id="apropos-list">apropos-list</span>
## <span id="aref">aref</span>
## <span id="arithmetic-error">arithmetic-error</span>
## <span id="arithmetic-error-operands">arithmetic-error-operands</span>
## <span id="arithmetic-error-operation">arithmetic-error-operation</span>
## <span id="array">array</span>
## <span id="array-dimension">array-dimension</span>
## <span id="array-dimension-limit">array-dimension-limit</span>
## <span id="array-dimensions">array-dimensions</span>
## <span id="array-displacement">array-displacement</span>
## <span id="array-element-type">array-element-type</span>
## <span id="array-has-fill-pointer-p">array-has-fill-pointer-p</span>
## <span id="array-in-bounds-p">array-in-bounds-p</span>
## <span id="array-rank">array-rank</span>
## <span id="array-rank-limit">array-rank-limit</span>
## <span id="array-row-major-index">array-row-major-index</span>
## <span id="array-total-size">array-total-size</span>
## <span id="array-total-size-limit">array-total-size-limit</span>
## <span id="arrayp">arrayp</span>
## <span id="ash">ash</span>
## <span id="asin">asin</span>
## <span id="asinh">asinh</span>
## <span id="assert">assert</span>
## <span id="assoc">assoc</span>
## <span id="assoc-if">assoc-if</span>
## <span id="assoc-if-not">assoc-if-not</span>
## <span id="atan">atan</span>
## <span id="atanh">atanh</span>
## <span id="atom">atom</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    atom object => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="base-char">base-char</span>
## <span id="base-string">base-string</span>
## <span id="bignum">bignum</span>
## <span id="bit">bit</span>
## <span id="bit-and">bit-and</span>
## <span id="bit-andc1">bit-andc1</span>
## <span id="bit-andc2">bit-andc2</span>
## <span id="bit-eqv">bit-eqv</span>
## <span id="bit-ior">bit-ior</span>
## <span id="bit-nand">bit-nand</span>
## <span id="bit-nor">bit-nor</span>
## <span id="bit-not">bit-not</span>
## <span id="bit-orc1">bit-orc1</span>
## <span id="bit-orc2">bit-orc2</span>
## <span id="bit-vector">bit-vector</span>
## <span id="bit-vector-p">bit-vector-p</span>
## <span id="bit-xor">bit-xor</span>
## <span id="block">block</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    block name form* => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="boole">boole</span>
## <span id="boole-1">boole-1</span>
## <span id="boole-2">boole-2</span>
## <span id="boole-and">boole-and</span>
## <span id="boole-andc1">boole-andc1</span>
## <span id="boole-andc2">boole-andc2</span>
## <span id="boole-c1">boole-c1</span>
## <span id="boole-c2">boole-c2</span>
## <span id="boole-clr">boole-clr</span>
## <span id="boole-eqv">boole-eqv</span>
## <span id="boole-ior">boole-ior</span>
## <span id="boole-nand">boole-nand</span>
## <span id="boole-nor">boole-nor</span>
## <span id="boole-orc1">boole-orc1</span>
## <span id="boole-orc2">boole-orc2</span>
## <span id="boole-set">boole-set</span>
## <span id="boole-xor">boole-xor</span>
## <span id="boolean">boolean</span>
## <span id="both-case-p">both-case-p</span>
## <span id="boundp">boundp</span>
## <span id="break">break</span>
## <span id="broadcast-stream">broadcast-stream</span>
## <span id="broadcast-stream-streams">broadcast-stream-streams</span>
## <span id="built-in-class">built-in-class</span>
## <span id="butlast">butlast</span>
## <span id="byte">byte</span>
## <span id="byte-position">byte-position</span>
## <span id="byte-size">byte-size</span>

## <span id="call-arguments-limit">call-arguments-limit</span>
## <span id="call-method">call-method</span>
## <span id="call-next-method">call-next-method</span>
## <span id="car">car</span>, <span id="caaaar">caaaar</span>, <span id="caaadr">caaadr</span>, <span id="caaar">caaar</span>, <span id="caadar">caadar</span>, <span id="caaddr">caaddr</span>, <span id="caadr">caadr</span>, <span id="caar">caar</span>, <span id="cadaar">cadaar</span>, <span id="cadadr">cadadr</span>, <span id="cadar">cadar</span>, <span id="caddar">caddar</span>, <span id="cadddr">cadddr</span>, <span id="caddr">caddr</span>, <span id="cadr">cadr</span>, <span id="cdaaar">cdaaar</span>, <span id="cdaadr">cdaadr</span>, <span id="cdaar">cdaar</span>, <span id="cdadar">cdadar</span>, <span id="cdaddr">cdaddr</span>, <span id="cdadr">cdadr</span>, <span id="cdar">cdar</span>, <span id="cddaar">cddaar</span>, <span id="cddadr">cddadr</span>, <span id="cddar">cddar</span>, <span id="cdddar">cdddar</span>, <span id="cddddr">cddddr</span>, <span id="cdddr">cdddr</span>, <span id="cddr">cddr</span>, <span id="cdr">cdr</span>

!!! tip "Accessor"
	**Syntax**:

    ``` lisp
    car x => object
    cdr x => object
    caar x => object
    cadr x => object
    cdar x => object
    cddr x => object
    caaar x => object
    caadr x => object
    cadar x => object
    caddr x => object
    cdaar x => object
    cdadr x => object
    cddar x => object
    cdddr x => object
    caaaar x => object
    caaadr x => object
    caadar x => object
    caaddr x => object
    cadaar x => object
    cadadr x => object
    caddar x => object
    cadddr x => object
    cdaaar x => object
    cdaadr x => object
    cdadar x => object
    cdaddr x => object
    cddaar x => object
    cddadr x => object
    cdddar x => object
    cddddr x => object
    (setf (car x) new-object)
    (setf (cdr x) new-object)
    (setf (caar x) new-object)
    (setf (cadr x) new-object)
    (setf (cdar x) new-object)
    (setf (cddr x) new-object)
    (setf (caaar x) new-object)
    (setf (caadr x) new-object)
    (setf (cadar x) new-object)
    (setf (caddr x) new-object)
    (setf (cdaar x) new-object)
    (setf (cdadr x) new-object)
    (setf (cddar x) new-object)
    (setf (cdddr x) new-object)
    (setf (caaaar x) new-object)
    (setf (caaadr x) new-object)
    (setf (caadar x) new-object)
    (setf (caaddr x) new-object)
    (setf (cadaar x) new-object)
    (setf (cadadr x) new-object)
    (setf (caddar x) new-object)
    (setf (cadddr x) new-object)
    (setf (cdaaar x) new-object)
    (setf (cdaadr x) new-object)
    (setf (cdadar x) new-object)
    (setf (cdaddr x) new-object)
    (setf (cddaar x) new-object)
    (setf (cddadr x) new-object)
    (setf (cdddar x) new-object)
    (setf (cddddr x) new-object)
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="case">case</span>
## <span id="catch">catch</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    catch tag form* => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="ccase">ccase</span>
## <span id="ceiling">ceiling</span>, <span id="fceiling">fceiling</span>, <span id="floor">floor</span>, <span id="ffloor">ffloor</span>, <span id="round">round</span>, <span id="fround">fround</span>, <span id="truncate">truncate</span>, <span id="ftruncate">ftruncate</span>

!!! note "Function"
	**Syntax**:

	``` lisp
	floor number &optional divisor => quotient, remainder
	ffloor number &optional divisor => quotient, remainder
	ceiling number &optional divisor => quotient, remainder
	fceiling number &optional divisor => quotient, remainder
	truncate number &optional divisor => quotient, remainder
	ftruncate number &optional divisor => quotient, remainder
	round number &optional divisor => quotient, remainder
	fround number &optional divisor => quotient, remainder
	```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="cell-error">cell-error</span>
## <span id="cell-error-name">cell-error-name</span>
## <span id="cerror">cerror</span>
## <span id="change-class">change-class</span>
## <span id="char">char</span>
## <span id="char-code">char-code</span>
## <span id="char-code-limit">char-code-limit</span>
## <span id="char-downcase">char-downcase</span>
## <span id="char-equal">char-equal</span>
## <span id="char-greaterp">char-greaterp</span>
## <span id="char-int">char-int</span>
## <span id="char-lessp">char-lessp</span>
## <span id="char-name">char-name</span>
## <span id="char-not-equal">char-not-equal</span>
## <span id="char-not-greaterp">char-not-greaterp</span>
## <span id="char-not-lessp">char-not-lessp</span>
## <span id="char-upcase">char-upcase</span>
## <span id="char/=">char/=</span>
## <span id="char<">char<</span>
## <span id="char<=">char<=</span>
## <span id="char=">char=</span>
## <span id="char>">char></span>
## <span id="char>=">char>=</span>
## <span id="character">character</span>
## <span id="characterp">characterp</span>
## <span id="check-type">check-type</span>
## <span id="cis">cis</span>
## <span id="class">class</span>
## <span id="class-name">class-name</span>
## <span id="class-of">class-of</span>
## <span id="clear-input">clear-input</span>
## <span id="clear-output">clear-output</span>
## <span id="close">close</span>
## <span id="clrhash">clrhash</span>
## <span id="code-char">code-char</span>
## <span id="coerce">coerce</span>
## <span id="compilation-speed">compilation-speed</span>
## <span id="compile">compile</span>
## <span id="compile-file">compile-file</span>
## <span id="compile-file-pathname">compile-file-pathname</span>
## <span id="compiled-function">compiled-function</span>
## <span id="compiled-function-p">compiled-function-p</span>
## <span id="compiler-macro">compiler-macro</span>
## <span id="compiler-macro-function">compiler-macro-function</span>
## <span id="complement">complement</span>
## <span id="complex">complex</span>
## <span id="complexp">complexp</span>
## <span id="compute-applicable-methods">compute-applicable-methods</span>
## <span id="compute-restarts">compute-restarts</span>
## <span id="concatenate">concatenate</span>
## <span id="concatenated-stream">concatenated-stream</span>
## <span id="concatenated-stream-streams">concatenated-stream-streams</span>
## <span id="cond">cond</span>

!!! tip "Macro"
	**Syntax**:

	``` lisp
	cond {clause}* => result*

	clause::= (test-form form*)
	```

**Arguments and Values**:

- `test-form`: 一个形式
- `forms`: 隐式的`progn`
- `results`: (1) 首个`test-form`为true的`clause`的`forms`的值, 或者(2) 该`clause`中没有`forms`时`test-form`的主值, 或者(3) 没有`test-form`为true时为`nil`

**Description**:

`cond`允许依赖于`test-form`执行`forms`.

`test-form`按参数列表中顺序依次求值, 直到有一个求值为true. 如果在这个子句中没有`forms`, 则`test-form`的 **主值** 作为`cond`形式的结果返回; 否则与该`test-form`相应的`forms`从左至右依次求值, 像在一个隐式的`progn`中一样, 最后一个形式的值作为`cond`形式的返回值.

一旦有一个`test-form`求值为true, 后续的`test-from`不再求值. 如果没有`test-form`求值为true, 则返回`nil`.

**Examples**:

``` lisp
(defun select-options ()
	(cond ((= a 1) (setq a 2))
				((= a 2) (setq a 3))
				((and (= a 3) (floor a 2)))
				(t (floor a 3)))) =>  SELECT-OPTIONS

(setq a 1) =>  1

(select-options) =>  2 ; ((= a 1) (setq a 2))
a =>  2

(select-options) =>  3 ; ((= a 2) (setq a 3))
a =>  3

(select-options) =>  1 ; ((and (= a 3) (floor a 2)))

(setq a 5) =>  5
(select-options) =>  1, 2 ; (t (floor a 3))
```

**Side Effects**: 无.


**Affected By**: 无.


**Exceptional Situations**: 无.


**See Also**:

[if](#if), [case](#case)

**Notes**: 无.




## <span id="condition">condition</span>
## <span id="conjugate">conjugate</span>
## <span id="cons">cons</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    cons object-1 object-2 => cons
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="consp">consp</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    consp object => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:

## <span id="constantly">constantly</span>
## <span id="constantp">constantp</span>
## <span id="continue">continue</span>
## <span id="control-error">control-error</span>
## <span id="copy-alist">copy-alist</span>
## <span id="copy-list">copy-list</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    copy-list list => copy
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="copy-pprint-dispatch">copy-pprint-dispatch</span>
## <span id="copy-readtable">copy-readtable</span>
## <span id="copy-seq">copy-seq</span>
## <span id="copy-structure">copy-structure</span>
## <span id="copy-symbol">copy-symbol</span>
## <span id="copy-tree">copy-tree</span>
## <span id="cos">cos</span>
## <span id="cosh">cosh</span>
## <span id="count">count</span>
## <span id="count-if">count-if</span>
## <span id="count-if-not">count-if-not</span>
## <span id="ctypecase">ctypecase</span>
## <span id="debug">debug</span>
## <span id="decf">decf</span>
## <span id="declaim">declaim</span>
## <span id="declaration">declaration</span>
## <span id="declare">declare</span>
## <span id="decode-float">decode-float</span>
## <span id="decode-universal-time">decode-universal-time</span>
## <span id="defclass">defclass</span>
## <span id="defconstant">defconstant</span>
## <span id="defgeneric">defgeneric</span>
## <span id="define-compiler-macro">define-compiler-macro</span>
## <span id="define-condition">define-condition</span>
## <span id="define-method-combination">define-method-combination</span>
## <span id="define-modify-macro">define-modify-macro</span>
## <span id="define-setf-expander">define-setf-expander</span>
## <span id="define-symbol-macro">define-symbol-macro</span>
## <span id="defmacro">defmacro</span>
## <span id="defmethod">defmethod</span>
## <span id="defpackage">defpackage</span>
## <span id="defparameter">defparameter</span>
## <span id="defsetf">defsetf</span>
## <span id="defstruct">defstruct</span>
## <span id="deftype">deftype</span>
## <span id="defun">defun</span>


!!! tip "Macro"
	**Syntax**:

    ``` lisp
    defun function-name lambda-list [[declaration* | documentation]] form*

    => function-name
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="defvar">defvar</span>
## <span id="delete">delete</span>
## <span id="delete-duplicates">delete-duplicates</span>
## <span id="delete-file">delete-file</span>
## <span id="delete-if">delete-if</span>
## <span id="delete-if-not">delete-if-not</span>
## <span id="delete-package">delete-package</span>
## <span id="denominator">denominator</span>
## <span id="deposit-field">deposit-field</span>
## <span id="describe">describe</span>
## <span id="describe-object">describe-object</span>
## <span id="destructuring-bind">destructuring-bind</span>
## <span id="digit-char">digit-char</span>
## <span id="digit-char-p">digit-char-p</span>
## <span id="directory">directory</span>
## <span id="directory-namestring">directory-namestring</span>
## <span id="disassemble">disassemble</span>
## <span id="division-by-zero">division-by-zero</span>
## <span id="do">do, do*</span>

!!! tip "Macro"
    **Syntax**:

    ``` lisp
    do ({var | (var [init-form [step-form]])}*)
      (end-test-form result-form*)
      declaration*
      {tag | statement}*
    => result*

    do* ({var | (var [init-form [step-form]])}*)
      (end-test-form result-form*)
      declaration*
      {tag | statement}*
    => result*
    ```


**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:


## <span id="do-all-symbols">do-all-symbols</span>
## <span id="do-external-symbols">do-external-symbols</span>
## <span id="do-symbols">do-symbols</span>
## <span id="documentation">documentation</span>
## <span id="dolist">dolist</span>
## <span id="dotimes">dotimes</span>

!!! tip "Macro"
	**Syntax**:

    ``` lisp
    dotimes (var count-form [result-form]) declaration* {tag | statement}*

    => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="double-float">double-float</span>
## <span id="double-float-epsilon">double-float-epsilon</span>
## <span id="double-float-negative-epsilon">double-float-negative-epsilon</span>
## <span id="dpb">dpb</span>
## <span id="dribble">dribble</span>
## <span id="dynamic-extent">dynamic-extent</span>
## <span id="ecase">ecase</span>
## <span id="echo-stream">echo-stream</span>
## <span id="echo-stream-input-stream">echo-stream-input-stream</span>
## <span id="echo-stream-output-stream">echo-stream-output-stream</span>
## <span id="ed">ed</span>
## <span id="elt">elt</span>
## <span id="encode-universal-time">encode-universal-time</span>
## <span id="end-of-file">end-of-file</span>
## <span id="endp">endp</span>
## <span id="enough-namestring">enough-namestring</span>
## <span id="ensure-directories-exist">ensure-directories-exist</span>
## <span id="ensure-generic-function">ensure-generic-function</span>
## <span id="eq">eq</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    eq x y => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="eql">eql</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    eql x y => generalized-boolean
    ```


**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="equal">equal</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    equal x y => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:






## <span id="equalp">equalp</span>
## <span id="error">error</span>
## <span id="etypecase">etypecase</span>
## <span id="eval">eval</span>
## <span id="eval-when">eval-when</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    eval-when (situation*) form* => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="evenp">evenp</span>, <span id="oddp">oddp</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    evenp integer => generalized-boolean
    oddp integer => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="every">every</span>
## <span id="exp">exp</span>
## <span id="export">export</span>
## <span id="expt">expt</span>
## <span id="extended-char">extended-char</span>
## <span id="fboundp">fboundp</span>
## <span id="fdefinition">fdefinition</span>
## <span id="file-author">file-author</span>
## <span id="file-error">file-error</span>
## <span id="file-error-pathname">file-error-pathname</span>
## <span id="file-length">file-length</span>
## <span id="file-namestring">file-namestring</span>
## <span id="file-position">file-position</span>
## <span id="file-stream">file-stream</span>
## <span id="file-string-length">file-string-length</span>
## <span id="file-write-date">file-write-date</span>
## <span id="fill">fill</span>
## <span id="fill-pointer">fill-pointer</span>
## <span id="find">find</span>
## <span id="find-all-symbols">find-all-symbols</span>
## <span id="find-class">find-class</span>
## <span id="find-if">find-if</span>
## <span id="find-if-not">find-if-not</span>
## <span id="find-method">find-method</span>
## <span id="find-package">find-package</span>
## <span id="find-restart">find-restart</span>
## <span id="find-symbol">find-symbol</span>
## <span id="finish-output">finish-output</span>
## <span id="first">first</span>, <span id="second">second</span>, <span id="third">third</span>, <span id="fourth">fourth</span>, <span id="fifth">fifth</span>, <span id="sixth">sixth</span>, <span id="seventh">seventh</span>, <span id="eighth">eighth</span>, <span id="ninth">ninth</span>, <span id="tenth">tenth</span>

!!! tip "Accessor"
	**Syntax**:

    ``` lisp
    first list => object
    second list => object
    third list => object
    fourth list => object
    fifth list => object
    sixth list => object
    seventh list => object
    eighth list => object
    ninth list => object
    tenth list => object
    (setf (first list) new-object)
    (setf (second list) new-object)
    (setf (third list) new-object)
    (setf (fourth list) new-object)
    (setf (fifth list) new-object)
    (setf (sixth list) new-object)
    (setf (seventh list) new-object)
    (setf (eighth list) new-object)
    (setf (ninth list) new-object)
    (setf (tenth list) new-object)
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:


## <span id="fixnum">fixnum</span>
## <span id="flet">flet</span>, <span id="labels">labels</span>, <span id="macrolet">macrolet</span>

!!! attention "Special Operator"
	**Syntax**:

``` lisp
flet ((function-name lambda-list [[local-declaration* | local-documentation]] local-form*)*) declaration* form*
=> result*

labels ((function-name lambda-list [[local-declaration* | local-documentation]] local-form*)*) declaration* form*
=> result*

macrolet ((name lambda-list [[local-declaration* | local-documentation]] local-form*)*) declaration* form*
=> result*
```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="float">float</span>
## <span id="float-digits">float-digits</span>
## <span id="float-precision">float-precision</span>
## <span id="float-radix">float-radix</span>
## <span id="float-sign">float-sign</span>
## <span id="floating-point-inexact">floating-point-inexact</span>
## <span id="floating-point-invalid-operation">floating-point-invalid-operation</span>
## <span id="floating-point-overflow">floating-point-overflow</span>
## <span id="floating-point-underflow">floating-point-underflow</span>
## <span id="floatp">floatp</span>

## <span id="fmakunbound">fmakunbound</span>
## <span id="force-output">force-output</span>
## <span id="format">format</span>
## <span id="formatter">formatter</span>
## <span id="fresh-line">fresh-line</span>
## <span id="ftype">ftype</span>
## <span id="funcall">funcall</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    funcall function &rest args => result*
    ```

**Arguments and Values**:

- function: 函数指示器
- args: 函数的参数
- results: 函数的返回值

**Description**:

`funcall`在使用参数`args`调用函数`function`.
如果`function`是个符号, 通过在全局环境中找到其函数值将其转换为一个函数.

**Examples**:

``` lisp
(funcall #'+ 1 2 3) =>  6
(funcall 'car '(1 2 3)) =>  1
(funcall 'position 1 '(1 2 3 2 1) :start 1) =>  4
(cons 1 2) =>  (1 . 2)
(flet ((cons (x y) `(kons ,x ,y)))
  (let ((cons (symbol-function '+)))
    (funcall #'cons
             (funcall 'cons 1 2)
             (funcall cons 1 2))))
=>  (KONS (1 . 2) 3)
```

**Affected By**: 无.

**Exceptional Situations**:

如果`function`是个符号, 且没有全局定义的同名函数或者是全局定义的同名宏或特殊操作符时, 发出类型为`undefined-function`的错误信号.

**See Also**:

[apply](#apply), [function](#function), [3.1 求值](../03-Evaluation-and-Compilation#3.1).

**Notes**:

``` lisp
(funcall function arg1 arg2 ...)
==  (apply function arg1 arg2 ... nil)
==  (apply function (list arg1 arg2 ...))
```

`funcall`和普通函数调用之前的区别是, 前者是通过求值一个形式获得的函数, 而后者是在函数通常出现的位置做特殊解释获得的.


## <span id="function">function</span>


!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    function name => function
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="function-keywords">function-keywords</span>
## <span id="function-lambda-expression">function-lambda-expression</span>
## <span id="functionp">functionp</span>
## <span id="gcd">gcd</span>
## <span id="generic-function">generic-function</span>
## <span id="gensym">gensym</span>
## <span id="gentemp">gentemp</span>
## <span id="get">get</span>
## <span id="get-decoded-time">get-decoded-time</span>
## <span id="get-dispatch-macro-character">get-dispatch-macro-character</span>
## <span id="get-internal-real-time">get-internal-real-time</span>
## <span id="get-internal-run-time">get-internal-run-time</span>
## <span id="get-macro-character">get-macro-character</span>
## <span id="get-output-stream-string">get-output-stream-string</span>
## <span id="get-properties">get-properties</span>
## <span id="get-setf-expansion">get-setf-expansion</span>
## <span id="get-universal-time">get-universal-time</span>
## <span id="getf">getf</span>
## <span id="gethash">gethash</span>
## <span id="go">go</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    go tag =>|
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="graphic-char-p">graphic-char-p</span>
## <span id="handler-bind">handler-bind</span>
## <span id="handler-case">handler-case</span>
## <span id="hash-table">hash-table</span>
## <span id="hash-table-count">hash-table-count</span>
## <span id="hash-table-p">hash-table-p</span>
## <span id="hash-table-rehash-size">hash-table-rehash-size</span>
## <span id="hash-table-rehash-threshold">hash-table-rehash-threshold</span>
## <span id="hash-table-size">hash-table-size</span>
## <span id="hash-table-test">hash-table-test</span>
## <span id="host-namestring">host-namestring</span>
## <span id="identity">identity</span>
## <span id="if">if</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    if test-form then-form [else-form] => result*
    ```

**Arguments and Values**:

- `test-form`: 一个形式
- `then-form`: 一个形式
- `else-form`:一个形式, 默认为`nil`
- `results`: 如果`test-form`为true, `then-form`返回的结果; 否则是`else-form`返回的结果

**Description**:

`if`允许依赖于单个`test-form`执行形式.

首先求值`test-form`, 如果其结果为true, 则选择`then-form`, 否则选择`else-form`. 对选择的形式求值.

**Examples**:

``` lisp
(if t 1) =>  1
(if nil 1 2) =>  2

(defun test ()
	(dolist (truth-value '(t nil 1 (a b c)))
		(if truth-value (print 'true) (print 'false))
		(prin1 truth-value))) =>  TEST
(test)
>>  TRUE T
>>  FALSE NIL
>>  TRUE 1
>>  TRUE (A B C)
=>  NIL
```

**Affected By**: 无.


**Exceptional Situations**: 无.


**See Also**:

[cond](#cond), [unless](#unless), [when](#when)

**Notes**:

``` lisp
 (if test-form then-form else-form)
 ==
 (cond (test-form then-form)
       (t else-form))
```




## <span id="ignorable">ignorable</span>
## <span id="ignore">ignore</span>
## <span id="ignore-errors">ignore-errors</span>
## <span id="imagpart">imagpart</span>
## <span id="import">import</span>
## <span id="in-package">in-package</span>
## <span id="incf">incf</span>
## <span id="initialize-instance">initialize-instance</span>
## <span id="inline">inline</span>
## <span id="input-stream-p">input-stream-p</span>
## <span id="inspect">inspect</span>
## <span id="integer">integer</span>
## <span id="integer-decode-float">integer-decode-float</span>
## <span id="integer-length">integer-length</span>
## <span id="integerp">integerp</span>


!!! note "Function"
	**Syntax**:

    ``` lisp
    integerp object => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="interactive-stream-p">interactive-stream-p</span>
## <span id="intern">intern</span>
## <span id="internal-time-units-per-second">internal-time-units-per-second</span>
## <span id="intersection">intersection</span>, <span id="nintersection">nintersection</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    intersection list-1 list-2 &key key test test-not => result-list
    nintersection list-1 list-2 &key key test test-not => result-list
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="invalid-method-error">invalid-method-error</span>
## <span id="invoke-debugger">invoke-debugger</span>
## <span id="invoke-restart">invoke-restart</span>
## <span id="invoke-restart-interactively">invoke-restart-interactively</span>
## <span id="isqrt">isqrt</span>
## <span id="keyword">keyword</span>
## <span id="keywordp">keywordp</span>
## <span id="lambda">lambda</span>
## <span id="lambda-list-keywords">lambda-list-keywords</span>
## <span id="lambda-parameters-limit">lambda-parameters-limit</span>
## <span id="last">last</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    last list &optional n => tail
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="lcm">lcm</span>
## <span id="ldb">ldb</span>
## <span id="ldb-test">ldb-test</span>
## <span id="ldiff">ldiff</span>
## <span id="least-negative-double-float">least-negative-double-float</span>
## <span id="least-negative-long-float">least-negative-long-float</span>
## <span id="least-negative-normalized-double-float">least-negative-normalized-double-float</span>
## <span id="least-negative-normalized-long-float">least-negative-normalized-long-float</span>
## <span id="least-negative-normalized-short-float">least-negative-normalized-short-float</span>
## <span id="least-negative-normalized-single-float">least-negative-normalized-single-float</span>
## <span id="least-negative-short-float">least-negative-short-float</span>
## <span id="least-negative-single-float">least-negative-single-float</span>
## <span id="least-positive-double-float">least-positive-double-float</span>
## <span id="least-positive-long-float">least-positive-long-float</span>
## <span id="least-positive-normalized-double-float">least-positive-normalized-double-float</span>
## <span id="least-positive-normalized-long-float">least-positive-normalized-long-float</span>
## <span id="least-positive-normalized-short-float">least-positive-normalized-short-float</span>
## <span id="least-positive-normalized-single-float">least-positive-normalized-single-float</span>
## <span id="least-positive-short-float">least-positive-short-float</span>
## <span id="least-positive-single-float">least-positive-single-float</span>
## <span id="length">length</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    length sequence => n
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="let">let</span>, <span id="let*">let*</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    let ( { var | (var [init-form]) }* ) declaration* form* => result*
    let* ( { var | (var [init-form]) }* ) declaration* form* => result*
    ```

**Arguments and Values**:

- `var`: 一个符号
- `init-form`: 一个形式(称为初始值形式)
- `declaration`: 一个`declare`表达式, 不被求值
- `form`: 一个形式
- `results`: `forms`返回的值

**Description**:

`let`和`let*` **创建新的变量绑定**, 并使用这些绑定执行一组形式.
`let`按并行方式执行绑定, `let*`按串行方式执行绑定.

形式

``` lisp
(let ((var1 init-form-1)
			(var2 init-form-2)
			...
			(varm init-form-m))
	declaration1
	declaration2
	...
	declarationp
	form1
	form2
	...
	formn)
```

首先按顺序求值表达式`init-form-1`、`init-form-2`等, 保存结果值.
然后所有变量`varj`绑定到对应的值; 每个绑定是 **词法的**, 除非存在相反的`special`声明.
接着按顺序求值`formk`; 除最后一个值外丢弃所有值(即, `let`的体是一个隐式的`progn`).

`let*`与`let`相似, 但按串行方式执行变量绑定. 一个`var`的`init-form`表达式可以引用前面绑定的`var`.

形式

``` lisp
(let* ((var1 init-form-1)
			 (var2 init-form-2)
			 ...
			 (varm init-form-m))
	declaration1
	declaration2
	...
	declarationp
	form1
	form2
	...
	formn)
```

首先求值表达式`init-form-1`, 将结果绑定到`var1`; 然后求值表达式`init-form-2`, 将结果绑定到`var2`, 等等. 接着, 表达式`formj`按序求值; 除最后一个值外丢弃所有值(即, `let*`的体是一个隐式的`progn`).

在`let`和`let*`中, 如果一个`var`没有对应的`init-form`, 则`var`初始化为`nil`.

特殊形式`let`有属性: 名称绑定的作用域不包含任意初始值形式.
对于`let*`, 变量的作用域也包含之后的变量绑定中剩余的初始值形式.

**Examples**:

``` lisp
(setq a 'top) =>  TOP
(defun dummy-function () a) =>  DUMMY-FUNCTION

(let ((a 'inside) (b a)) ;(b a)中a是指toplevel的a
	 (format nil "~S ~S ~S" a b (dummy-function))) =>  "INSIDE TOP TOP"

(let* ((a 'inside) (b a));(b a)中a是指前一个绑定的变量
	 (format nil "~S ~S ~S" a b (dummy-function))) =>  "INSIDE INSIDE TOP"

(let ((a 'inside) (b a))
	 (declare (special a)) ;将a改为非词法的
	 (format nil "~S ~S ~S" a b (dummy-function))) =>  "INSIDE TOP INSIDE"
```

下面的代码是不正确的.

``` lisp
(let (x)
	(declare (integer x))
	(setq x (gcd y z))
	...)
```

`x`的初始值为`nil`, 违背了类型声明`(declare (integer x))`.


**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[progv](#progv)

**Notes**: 无.




## <span id="lisp-implementation-type">lisp-implementation-type</span>
## <span id="lisp-implementation-version">lisp-implementation-version</span>
## <span id="list">list</span>, <span id="list*">list*</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    list &rest objects => list
    list* &rest objects+ => result
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="list-all-packages">list-all-packages</span>
## <span id="list-length">list-length</span>
## <span id="listen">listen</span>
## <span id="listp">listp</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    listp object => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="load">load</span>
## <span id="load-logical-pathname-translations">load-logical-pathname-translations</span>
## <span id="load-time-value">load-time-value</span>


!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    load-time-value form &optional read-only-p => object
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="locally">locally</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    locally declaration* form* => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="log">log</span>
## <span id="logand">logand</span>
## <span id="logandc1">logandc1</span>
## <span id="logandc2">logandc2</span>
## <span id="logbitp">logbitp</span>
## <span id="logcount">logcount</span>
## <span id="logeqv">logeqv</span>
## <span id="logical-pathname">logical-pathname</span>
## <span id="logical-pathname-translations">logical-pathname-translations</span>
## <span id="logior">logior</span>
## <span id="lognand">lognand</span>
## <span id="lognor">lognor</span>
## <span id="lognot">lognot</span>
## <span id="logorc1">logorc1</span>
## <span id="logorc2">logorc2</span>
## <span id="logtest">logtest</span>
## <span id="logxor">logxor</span>
## <span id="long-float">long-float</span>
## <span id="long-float-epsilon">long-float-epsilon</span>
## <span id="long-float-negative-epsilon">long-float-negative-epsilon</span>
## <span id="long-site-name">long-site-name</span>

## <span id="loop">loop</span>

!!! tip "Macro"
	**Syntax**:

	``` lisp
	; 简单的循环形式
	loop compound-form* => result*
	; 扩展的循环形式
	loop [name-clause] { variable-clause }* { main-clause }* => result*
	```
	``` EBNF
	(* 1 名称从句 *)
	name-clause::= named name

	(* 2 变量从句 *)
	variable-clause::= with-clause | initial-final | for-as-clause
	(* 2.1 WITH从句 *)
	with-clause::= with var1 [type-spec] [= form1] { and var2 [type-spec] [= form2] }*

	(* 3 主从句 *)
	main-clause::= unconditional | accumulation | conditional | termination-test | initial-final

	(* 2.2/3.5 序言和后继从句 *)
	initial-final::= initially compound-form+ | finally compound-form+

	(* 3.1 无条件执行从句 *)
	unconditional::= { do | doing } compound-form+ | return { form | it }
	(* 3.2 累积从句 *)
	accumulation::= list-accumulation | numeric-accumulation
	list-accumulation::= { collect | collecting | append | appending | nconc | nconcing } { form | it }
	                     [into simple-var]
	numeric-accumulation::= { count | counting | sum | summing | }
	                         maximize | maximizing | minimize | minimizing { form | it }
	                        [into simple-var] [type-spec]
	(* 3.3 条件执行从句 *)
	conditional::= { if | when | unless } form selectable-clause { and selectable-clause }*
	               [else selectable-clause { and selectable-clause }*]
	               [end]
	selectable-clause::= unconditional | accumulation | conditional
	(* 3.4 终止测试从句 *)
	termination-test::= while form | until form | repeat form | always form | never form | thereis form

	(* 2.3 for-as从句 *)
	for-as-clause::= { for | as } for-as-subclause { and for-as-subclause }*
	for-as-subclause::= for-as-arithmetic | for-as-in-list | for-as-on-list | for-as-equals-then |
	                    for-as-across | for-as-hash | for-as-package
	for-as-arithmetic::= var [type-spec] for-as-arithmetic-subclause
	for-as-arithmetic-subclause::= arithmetic-up | arithmetic-downto | arithmetic-downfrom
	arithmetic-up::= [ [ { from | upfrom } form1
                        | { to | upto | below } form2
                        | by form3 ] ]+
	arithmetic-downto::= [ [ {{ from form1 }}1
                            | {{ { downto | above } form2 }}1
                            | by form3 ] ]
	arithmetic-downfrom::= [ [ {{ downfrom form1 }}1
                              | { to | downto | above } form2
                              | by form3 ] ]
	for-as-in-list::= var [type-spec] in form1 [by step-fun]
	for-as-on-list::= var [type-spec] on form1 [by step-fun]
	for-as-equals-then::= var [type-spec] = form1 [then form2]
	for-as-across::= var [type-spec] across vector
	for-as-hash::= var [type-spec] being { each | the }
	               { { hash-key | hash-keys } { in | of } hash-table
	                 [using (hash-value other-var)]
                   | { hash-value | hash-values } { in | of } hash-table
	                 [using (hash-key other-var)]}
	for-as-package::= var [type-spec] being { each | the }
	                  { symbol | symbols
                      | present-symbol | present-symbols
                      | external-symbol | external-symbols }
	                  [{ in | of } package]
	(* 2.1.1 类型描述和变量 *)
	type-spec::= simple-type-spec | destructured-type-spec
	simple-type-spec::= fixnum | float | t | nil
	destructured-type-spec::= of-type d-type-spec
	d-type-spec::= type-specifier | (d-type-spec . d-type-spec)
	var::= d-var-spec
	var1::= d-var-spec
	var2::= d-var-spec
	other-var::= d-var-spec
	d-var-spec::= simple-var | nil | (d-var-spec . d-var-spec)
	```

**Arguments and Values**:

- `compound-form`: 一个复合形式
- `name`: 一个符号
- `simple-var`: 一个符号(变量名称)
- `form, form1, form2, form3`: 一个形式
- `step-fun`: 求值为单个传递参数的函数肚饿形式
- `vector`: 求值为向量的形式
- `hash-table`: 求值为哈希表的形式
- `package`: 求值为包指示器的形式
- `type-specifier`: 一个类型描述符. 可以是原子类型描述符或复合类型描述符, 在解构中恰当解析方面引入了额外的复杂性; 更多信息见[6.1.1.7 解构](../06-Iteration##6.1.1.7).
- `result`: 一个对象

**Description**:

见[6.1 循环功能](../06-Iteration#6.1).

**Examples**:

``` lisp
;; An example of the simple form of LOOP.
 (defun sqrt-advisor ()
   (loop (format t "~&Number: ")
         (let ((n (parse-integer (read-line) :junk-allowed t)))
           (when (not n) (return))
           (format t "~&The square root of ~D is ~D.~%" n (sqrt n)))))
=>  SQRT-ADVISOR
 (sqrt-advisor)
>>  Number: 5<NEWLINE>
>>  The square root of 5 is 2.236068.
>>  Number: 4<NEWLINE>
>>  The square root of 4 is 2.
>>  Number: done<NEWLINE>
=>  NIL

;; An example of the extended form of LOOP.
 (defun square-advisor ()
   (loop as n = (progn (format t "~&Number: ")
                       (parse-integer (read-line) :junk-allowed t))
         while n
         do (format t "~&The square of ~D is ~D.~%" n (* n n))))
=>  SQUARE-ADVISOR
 (square-advisor)
>>  Number: 4<NEWLINE>
>>  The square of 4 is 16.
>>  Number: 23<NEWLINE>
>>  The square of 23 is 529.
>>  Number: done<NEWLINE>
=>  NIL

;; Another example of the extended form of LOOP.
 (loop for n from 1 to 10
       when (oddp n)
         collect n)
=>  (1 3 5 7 9)
```

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[do](#do), [dolist](#dolist), [dotimes](#dotimes), [return](#return), [go](#go), [throw](#throw), [6.1.1.7 解构](../06-Iteration##6.1.1.7).

**Notes**:

除了简单的循环形式中不能用本地宏`loop-finish`, 简单的循环形式与扩展的循环形式按如下方式关联:

``` lisp
(loop compound-form*) ==  (loop do compound-form*)
```



## <span id="loop-finish">loop-finish</span>
## <span id="lower-case-p">lower-case-p</span>
## <span id="machine-instance">machine-instance</span>
## <span id="machine-type">machine-type</span>
## <span id="machine-version">machine-version</span>
## <span id="macro-function">macro-function</span>
## <span id="macroexpand">macroexpand</span>
## <span id="macroexpand-1">macroexpand-1</span>

## <span id="make-array">make-array</span>
## <span id="make-broadcast-stream">make-broadcast-stream</span>
## <span id="make-concatenated-stream">make-concatenated-stream</span>
## <span id="make-condition">make-condition</span>
## <span id="make-dispatch-macro-character">make-dispatch-macro-character</span>
## <span id="make-echo-stream">make-echo-stream</span>
## <span id="make-hash-table">make-hash-table</span>
## <span id="make-instance">make-instance</span>
## <span id="make-instances-obsolete">make-instances-obsolete</span>
## <span id="make-list">make-list</span>
## <span id="make-load-form">make-load-form</span>
## <span id="make-load-form-saving-slots">make-load-form-saving-slots</span>
## <span id="make-method">make-method</span>
## <span id="make-package">make-package</span>
## <span id="make-pathname">make-pathname</span>
## <span id="make-random-state">make-random-state</span>
## <span id="make-sequence">make-sequence</span>
## <span id="make-string">make-string</span>
## <span id="make-string-input-stream">make-string-input-stream</span>
## <span id="make-string-output-stream">make-string-output-stream</span>
## <span id="make-symbol">make-symbol</span>
## <span id="make-synonym-stream">make-synonym-stream</span>
## <span id="make-two-way-stream">make-two-way-stream</span>
## <span id="makunbound">makunbound</span>
## <span id="map">map</span>
## <span id="map-into">map-into</span>
## <span id="mapc">mapc</span>, <span id="mapcar">mapcar</span>, <span id="mapcan">mapcan</span>, <span id="mapl">mapl</span>, <span id="maplist">maplist</span>, <span id="mapcon">mapcon</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    mapc function &rest lists+ => list-1
    mapcar function &rest lists+ => result-list
    mapcan function &rest lists+ => concatenated-results
    mapl function &rest lists+ => list-1
    maplist function &rest lists+ => result-list
    mapcon function &rest lists+ => concatenated-results
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="maphash">maphash</span>
## <span id="mask-field">mask-field</span>
## <span id="max">max</span>
## <span id="member">member</span>, <span id="member-if">member-if</span>, <span id="member-if-not">member-if-not</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    member item list &key key test test-not => tail
    member-if predicate list &key key => tail
    member-if-not predicate list &key key => tail
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="merge">merge</span>
## <span id="merge-pathnames">merge-pathnames</span>
## <span id="method">method</span>
## <span id="method-combination">method-combination</span>
## <span id="method-combination-error">method-combination-error</span>
## <span id="method-qualifiers">method-qualifiers</span>
## <span id="min">min</span>
## <span id="minusp">minusp</span>
## <span id="mismatch">mismatch</span>
## <span id="mod">mod</span>, <span id="rem">rem</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    mod number divisor => modulus
    rem number divisor => remainder
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="most-negative-double-float">most-negative-double-float</span>
## <span id="most-negative-fixnum">most-negative-fixnum</span>
## <span id="most-negative-long-float">most-negative-long-float</span>
## <span id="most-negative-short-float">most-negative-short-float</span>
## <span id="most-negative-single-float">most-negative-single-float</span>
## <span id="most-positive-double-float">most-positive-double-float</span>
## <span id="most-positive-fixnum">most-positive-fixnum</span>
## <span id="most-positive-long-float">most-positive-long-float</span>
## <span id="most-positive-short-float">most-positive-short-float</span>
## <span id="most-positive-single-float">most-positive-single-float</span>
## <span id="muffle-warning">muffle-warning</span>
## <span id="multiple-value-bind">multiple-value-bind</span>
## <span id="multiple-value-call">multiple-value-call</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    multiple-value-call function-form form* => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="multiple-value-list">multiple-value-list</span>
## <span id="multiple-value-prog1">multiple-value-prog1</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    multiple-value-prog1 first-form form* => first-form-results
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="multiple-value-setq">multiple-value-setq</span>
## <span id="multiple-values-limit">multiple-values-limit</span>
## <span id="name-char">name-char</span>
## <span id="namestring">namestring</span>
## <span id="nbutlast">nbutlast</span>
## <span id="nconc">nconc</span>
## <span id="next-method-p">next-method-p</span>
## <span id="nil">nil</span>

!!! tip "Constant Variable"
	**Constant Value**:

	``` lisp
	nil
	```


**Description**:


**Examples**:


**See Also**:


**Notes**:




## <span id="no-applicable-method">no-applicable-method</span>
## <span id="no-next-method">no-next-method</span>
## <span id="not">not</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    not x => boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="notany">notany</span>
## <span id="notevery">notevery</span>
## <span id="notinline">notinline</span>
## <span id="nreconc">nreconc</span>
## <span id="nreverse">nreverse</span>
## <span id="nset-exclusive-or">nset-exclusive-or</span>
## <span id="nstring-capitalize">nstring-capitalize</span>
## <span id="nstring-downcase">nstring-downcase</span>
## <span id="nstring-upcase">nstring-upcase</span>
## <span id="nsublis">nsublis</span>
## <span id="nth">nth</span>

!!! tip "Accessor"
	**Syntax**:

    ``` lisp
    nth n list => object
    (setf (nth n list) new-object)
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="nth-value">nth-value</span>
## <span id="nthcdr">nthcdr</span>

!!! note "Function"
	**Syntax**:

	``` lisp
	nthcdr n list => tail
	```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="null">null</span>

!!! note "Function"
    **Syntax**:

    ``` lisp
    null object => boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="number">number</span>
## <span id="numberp">numberp</span>
## <span id="numerator">numerator</span>
## <span id="open">open</span>
## <span id="open-stream-p">open-stream-p</span>
## <span id="optimize">optimize</span>
## <span id="or">or</span>

!!! tip "Macro"
    **Syntax**:

    ``` lisp
    or form* => results*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="otherwise">otherwise</span>
## <span id="output-stream-p">output-stream-p</span>
## <span id="package">package</span>
## <span id="package-error">package-error</span>
## <span id="package-error-package">package-error-package</span>
## <span id="package-name">package-name</span>
## <span id="package-nicknames">package-nicknames</span>
## <span id="package-shadowing-symbols">package-shadowing-symbols</span>
## <span id="package-use-list">package-use-list</span>
## <span id="package-used-by-list">package-used-by-list</span>
## <span id="packagep">packagep</span>
## <span id="pairlis">pairlis</span>
## <span id="parse-error">parse-error</span>
## <span id="parse-integer">parse-integer</span>
## <span id="parse-namestring">parse-namestring</span>
## <span id="pathname">pathname</span>
## <span id="pathname-device">pathname-device</span>
## <span id="pathname-directory">pathname-directory</span>
## <span id="pathname-host">pathname-host</span>
## <span id="pathname-match-p">pathname-match-p</span>
## <span id="pathname-name">pathname-name</span>
## <span id="pathname-type">pathname-type</span>
## <span id="pathname-version">pathname-version</span>
## <span id="pathnamep">pathnamep</span>
## <span id="peek-char">peek-char</span>
## <span id="phase">phase</span>
## <span id="pi">pi</span>
## <span id="plusp">plusp</span>
## <span id="pop">pop</span>

!!! tip "Macro"
	**Syntax**:

	``` lisp
	pop place => element
	```

**Arguments and Values**:

- `place`: 一个位置, 它的值是一个列表(可以是点列表、循环列表)
- `element`: 一个对象(`place`的内容的car)

**Description**:

`pop`读取`place`的值, 记住检索的列表的car, 将列表的cdr写入`place`, 最终返回原始检索的列表car.

有关`place`的子形式的求值, 见[5.1.1.1 子形式到位置的求值](../05-Data-and-Control-Flow#5.1.1.1).

**Examples**:

``` lisp
(setq stack '(a b c)) =>  (A B C)
(pop stack) =>  A
stack =>  (B C)

(setq llst '((1 2 3 4))) =>  ((1 2 3 4))
(pop (car llst)) =>  1
llst =>  ((2 3 4))
```

**Side Effects**: `place`的内容被修改.

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[push](#push), [pushnew](#pushnew), [5.1 通用的引用](../05-Data-and-Control-Flow#5.1)

**Notes**:

`(pop place)`的作用基本上等价于:

``` lisp
(prog1 (car place) (setf place (cdr place)))
```

除了后者会求值`place`的子形式三次, 而`pop`只求值一次.

## <span id="position">position</span>
## <span id="position-if">position-if</span>
## <span id="position-if-not">position-if-not</span>
## <span id="pprint">pprint</span>
## <span id="pprint-dispatch">pprint-dispatch</span>
## <span id="pprint-exit-if-list-exhausted">pprint-exit-if-list-exhausted</span>
## <span id="pprint-fill">pprint-fill</span>
## <span id="pprint-indent">pprint-indent</span>
## <span id="pprint-linear">pprint-linear</span>
## <span id="pprint-logical-block">pprint-logical-block</span>
## <span id="pprint-newline">pprint-newline</span>
## <span id="pprint-pop">pprint-pop</span>
## <span id="pprint-tab">pprint-tab</span>
## <span id="pprint-tabular">pprint-tabular</span>
## <span id="prin1">prin1</span>
## <span id="prin1-to-string">prin1-to-string</span>
## <span id="princ">princ</span>
## <span id="princ-to-string">princ-to-string</span>
## <span id="print">print</span>
## <span id="print-not-readable">print-not-readable</span>
## <span id="print-not-readable-object">print-not-readable-object</span>
## <span id="print-object">print-object</span>
## <span id="print-unreadable-object">print-unreadable-object</span>
## <span id="probe-file">probe-file</span>
## <span id="proclaim">proclaim</span>
## <span id="prog">prog</span>
## <span id="prog*">prog*</span>
## <span id="prog1">prog1</span>
## <span id="prog2">prog2</span>
## <span id="progn">progn</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    progn form* => result*
    ```

**Arguments and Values**:

- `forms`: 一个隐式的`progn`
- `results`: `forms`的值

**Description**:

`progn`按给定的顺序求值`forms`. 丢弃除最后一个形式外的值.

如果`progn`作为顶级形式出现, 则`progn`中的`forms`被编译器视为顶级形式.

**Examples**:

``` lisp
(progn) =>  NIL
(progn 1 2 3) =>  3
(progn (values 1 2 3)) =>  1, 2, 3

(setq a 1) =>  1
(if a
		 (progn (setq a nil) 'here)
		 (progn (setq a t) 'there)) =>  HERE
a =>  NIL
```

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[prog1](#prog1), [prog2](#prog2), [3.1 求值](../03-Evaluation-and-Compilation#3.1)

**Notes**:

Common Lisp中很多地方包含使用隐式`progn`的语法. 即, 作为其语法的一部分,
允许写多个被串行求值的形式, 忽略除最后一个形式的结果, 返回最后一个形式的结果.
这些地方包括但不限于: lambda表达式的体, 多个控制和条件形式的体(`case`、`catch`、`progn`、`when`等).

## <span id="program-error">program-error</span>
## <span id="progv">progv</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    progv symbols values form* => result*
    ```

**Arguments and Values**:

- `symbols`: 符号列表; 被求值
- `values`: 对象列表, 被求值
- `forms`: 隐式`progn`
- `results`: `forms`返回的值

**Description**:

`progv`创建新的 **动态变量** 绑定, 并使用这些绑定执行每个`form`. 按顺序求值每个`form`.

`progv`允许绑定一个或多个 **运行时确定名称的** 动态变量. 每个`form`按顺序求值, 使用名称在`symbols`中的绑定到相应的`values`的动态变量.
如果提供了过少的`values`, 则剩下的符号被绑定为没有值.
如果提供了过多的`values`, 则多余的被忽略.
在从`progv`中退出时, 动态变量的绑定被撤销.

**Examples**:

``` lisp
(setq *x* 1) =>  1

(progv '(*x*) '(2) *x*) =>  2
*x* =>  1 ;动态变量绑定被撤销
```

假设`*x*`不是全局特殊的,

``` lisp
(let ((*x* 3))				; 变量*x*
	 (progv '(*x*) '(4)	; 符号*x*
		 (list *x* (symbol-value '*x*)))) =>  (3 4)
```

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[let](#let), [3.1 求值(Evaluation)](../03-Evaluation-and-Compilation#3.1)

**Notes**:

`progv`在编写内嵌在Lisp中的语言时很有用, 提供了绑定动态变量的机制.

## <span id="provide">provide</span>
## <span id="psetq">psetq</span>
## <span id="push">push</span>
## <span id="pushnew">pushnew</span>
## <span id="quote">quote</span>

!!! attention "Special Operator"
    **Syntax**:

    ``` lisp
    quote object => object
    ```

**Arguments and Values**:

- `object`: 一个对象, 不被求值

**Description**:

特殊操作符`quote`直接返回`object`.

如果字面量对象(包括被引述的对象)被破坏性修改时, 后果未定义.

**Examples**:

``` lisp
(setq a 1) =>  1

(quote (setq a 3)) =>  (SETQ A 3)
a =>  1 ;object未被求值

'a =>  A
''a =>  (QUOTE A)
'''a =>  (QUOTE (QUOTE A))

(setq a 43) =>  43
(list a (cons a 3)) =>  (43 (43 . 3))
(list (quote a) (quote (cons a 3))) =>  (A (CONS A 3))

1 =>  1
'1 =>  1

"foo" =>  "foo"
'"foo" =>  "foo"

(car '(a b)) =>  A
'(car '(a b)) =>  (CAR (QUOTE (A B)))

#(car '(a b)) =>  #(CAR (QUOTE (A B)))
'#(car '(a b)) =>  #(CAR (QUOTE (A B)))
```

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[3.1 求值(Evaluation)](../03-Evaluation-and-Compilation#3.1), [2.4.3 `'`(single-quote)](../02-Syntax#2.4.3), [3.2.1 编译器术语](../03-Evaluation-and-Compilation#3.2.1)

**Notes**:

文本记法`'object`等价于`(quote object)`, 见[3.2.1 编译器术语](../03-Evaluation-and-Compilation#3.2.1).

自求值对象不需要被`quote`引述. 符号和列表用于标识程序的部分, 不使用`quote`不能作为常值数据使用.
因为`quote`抑制了这些对象的求值, 它们称为数据而不是程序.

## <span id="random">random</span>
## <span id="random-state">random-state</span>
## <span id="random-state-p">random-state-p</span>
## <span id="rassoc">rassoc</span>
## <span id="rassoc-if">rassoc-if</span>
## <span id="rassoc-if-not">rassoc-if-not</span>
## <span id="ratio">ratio</span>
## <span id="rational">rational</span>
## <span id="rationalize">rationalize</span>
## <span id="rationalp">rationalp</span>
## <span id="read">read</span>
## <span id="read-byte">read-byte</span>
## <span id="read-char">read-char</span>
## <span id="read-char-no-hang">read-char-no-hang</span>
## <span id="read-delimited-list">read-delimited-list</span>
## <span id="read-from-string">read-from-string</span>
## <span id="read-line">read-line</span>
## <span id="read-preserving-whitespace">read-preserving-whitespace</span>
## <span id="read-sequence">read-sequence</span>
## <span id="reader-error">reader-error</span>
## <span id="readtable">readtable</span>
## <span id="readtable-case">readtable-case</span>
## <span id="readtablep">readtablep</span>
## <span id="real">real</span>
## <span id="realp">realp</span>
## <span id="realpart">realpart</span>
## <span id="reduce">reduce</span>
## <span id="reinitialize-instance">reinitialize-instance</span>
## <span id="remf">remf</span>
## <span id="remhash">remhash</span>
## <span id="remove">remove</span>
## <span id="remove-duplicates">remove-duplicates</span>
## <span id="remove-if">remove-if</span>
## <span id="remove-if-not">remove-if-not</span>
## <span id="remove-method">remove-method</span>
## <span id="remprop">remprop</span>
## <span id="rename-file">rename-file</span>
## <span id="rename-package">rename-package</span>
## <span id="replace">replace</span>
## <span id="require">require</span>
## <span id="rest">rest</span>
## <span id="restart">restart</span>
## <span id="restart-bind">restart-bind</span>
## <span id="restart-case">restart-case</span>
## <span id="restart-name">restart-name</span>
## <span id="return">return</span>
## <span id="return-from">return-from</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    return-from name [result] =>|
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="revappend">revappend</span>
## <span id="reverse">reverse</span>
## <span id="room">room</span>
## <span id="rotatef">rotatef</span>
## <span id="row-major-aref">row-major-aref</span>
## <span id="rplaca">rplaca</span>
## <span id="rplacd">rplacd</span>
## <span id="safety">safety</span>
## <span id="satisfies">satisfies</span>
## <span id="sbit">sbit</span>
## <span id="scale-float">scale-float</span>
## <span id="schar">schar</span>
## <span id="search">search</span>
## <span id="sequence">sequence</span>
## <span id="serious-condition">serious-condition</span>
## <span id="set">set</span>
## <span id="set-difference">set-difference</span>, <span id="nset-difference">nset-difference</span>

!!! note "Function"
	**Syntax**:

``` lisp
set-difference list-1 list-2 &key key test test-not => result-list
nset-difference list-1 list-2 &key key test test-not => result-list
```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="set-dispatch-macro-character">set-dispatch-macro-character</span>
## <span id="set-exclusive-or">set-exclusive-or</span>
## <span id="set-macro-character">set-macro-character</span>
## <span id="set-pprint-dispatch">set-pprint-dispatch</span>
## <span id="set-syntax-from-char">set-syntax-from-char</span>
## <span id="setf">setf</span>, <span id="psetf">psetf</span>

!!! tip "Macro"
    **Syntax**:

    ``` lisp
    setf {pair}* => result*
    psetf {pair}* => nil

    pair::= place newvalue
    ```

**Arguments and Values**:

- `place`: 一个位置
- `newvalue`: 一个形式
- `results`: 为最后一个位置存储形式放回的多值, 或在没有`pairs`时返回`nil`

**Description**:

`setf`将`place`的值修改为`newvalue`.

`(setf place newvalue)`展开为一个更新形式, 出处求值`newvalue`的结果到被`place`引用的位置.
一些`place`形式使用带可选传递参数的访问器. 这些可选传递参数是否被`setf`允许, 或它们使用的效果是什么, 是由`setf`展开器函数决定的, 不受`setf`的控制. 任何接受`&optional`、`&rest`或`&key`传递参数且这些传递参数声明为可用于`setf`的函数的文档必须描述如何处理这些传递参数.

如果提供了多个`pair`, `pairs`按顺序处理, 即:

``` lisp
(setf place-1 newvalue-1
			place-2 newvalue-2
			...
			place-N newvalue-N)
```

等价于:

``` lisp
(progn (setf place-1 newvalue-1)
			 (setf place-2 newvalue-2)
			 ...
			 (setf place-N newvalue-N))
```

对于`psetf`, 如果提供了多个`pair`, 则将新值指派给位置是并行执行的.
待求值的所有子形式(`place`和`newvalue`的形式), 按从左到右的顺序求值; 在所有求值完成后, 所有指派动作按不可预计的顺序执行.

`setf`和`psetf`展开的详情, 见[5.1.2 位置的种类](../05-Data-and-Control-Flow#5.1.2).

**Examples**:

``` lisp
(setq x (cons 'a 'b) y (list 1 2 3)) =>  (1 2 3)
(setf (car x) 'x (cadr y) (car x) (cdr x) y) =>  (1 X 3)
x =>  (X 1 X 3)
y =>  (1 X 3)

(setq x (cons 'a 'b) y (list 1 2 3)) =>  (1 2 3)
(psetf (car x) 'x (cadr y) (car x) (cdr x) y) =>  NIL
x =>  (X 1 A 3)
y =>  (1 A 3)
```

**Affected By**:

[define-setf-expander](#define-setf-expander), [defsetf](#defsetf), [`*macroexpand-hook*`](#*macroexpand-hook*)

**Exceptional Situations**: 无.

**See Also**:

[define-setf-expander](#define-setf-expander), [defsetf](#defsetf), [macroexpand-1](#macroexpand-1), [rotatef](#rotatef), [shiftf](#shiftf), [5.1 通用的引用](../05-Data-and-Control-Flow#5.1)

**Notes**: 无.


## <span id="setq">setq</span>

!!! attention "Special Form"
	**Syntax**:

	``` lisp
	setq { pair }* => result

	pair ::= var form
	```

	**Pronunciation**: ['set,kyoo]


**Arguments and Values**:

- `var`: 一个符号, 命名了一个不是常值变量的变量
- `form`: 一个形式
- `result`: 最后一个`form`的主值, 或者在没有提供`pair`时为`nil`

**Description**:

赋值给变量. `setq var1 form1 var2 form2 ...`是Lisp中简单的变量赋值语句.
首先`form1`被求值, 其求值结果存放在变量`var1`中, 接着`form2`被求值, 其求值结果存放在变量`var2`中, 等等.
`setq`可被用于 **词法** 或 **动态** 变量的赋值.

如果任意一个变量`var`引用了`symbol-macrolet`生成的绑定, 则该变量被视为使用了`setf`(而不是`setq`).

**Examples**:

``` lisp
;; A simple use of SETQ to establish values for variables.
(setq a 1 b 2 c 3) =>  3
a =>  1
b =>  2
c =>  3

;; Use of SETQ to update values by sequential assignment.
;; 串行赋值
(setq a (1+ b) b (1+ a) c (+ a b)) =>  7
a =>  3
b =>  4
c =>  7

;; This illustrates the use of SETQ on a symbol macro.
(let ((x (list 10 20 30))) ;x=(10 20 30)
	(symbol-macrolet ((y (car x)) (z (cadr x))) ; y=10, z=20
		(setq y (1+ z) z (1+ y)) ;y=21, z=22 -- setf x=(21 22 30)
		(list x y z)))
=>  ((21 22 30) 21 22)
```

**Side Effects**:

每个`form`的主值被赋值给相应的`var`.

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[psetq](#psetq), [set](#set). [setf](#setf), [symbol-macrolet](#symbol-macrolet)

**Notes**: 无.



## <span id="shadow">shadow</span>
## <span id="shadowing-import">shadowing-import</span>
## <span id="shared-initialize">shared-initialize</span>
## <span id="shiftf">shiftf</span>
## <span id="short-float">short-float</span>
## <span id="short-float-epsilon">short-float-epsilon</span>
## <span id="short-float-negative-epsilon">short-float-negative-epsilon</span>
## <span id="short-site-name">short-site-name</span>
## <span id="signal">signal</span>
## <span id="signed-byte">signed-byte</span>
## <span id="signum">signum</span>
## <span id="simple-array">simple-array</span>
## <span id="simple-base-string">simple-base-string</span>
## <span id="simple-bit-vector">simple-bit-vector</span>
## <span id="simple-bit-vector-p">simple-bit-vector-p</span>
## <span id="simple-condition">simple-condition</span>
## <span id="simple-condition-format-arguments">simple-condition-format-arguments</span>
## <span id="simple-condition-format-control">simple-condition-format-control</span>
## <span id="simple-error">simple-error</span>
## <span id="simple-string">simple-string</span>
## <span id="simple-string-p">simple-string-p</span>
## <span id="simple-type-error">simple-type-error</span>
## <span id="simple-vector">simple-vector</span>
## <span id="simple-vector-p">simple-vector-p</span>
## <span id="simple-warning">simple-warning</span>
## <span id="sin">sin</span>
## <span id="single-float">single-float</span>
## <span id="single-float-epsilon">single-float-epsilon</span>
## <span id="single-float-negative-epsilon">single-float-negative-epsilon</span>
## <span id="sinh">sinh</span>
## <span id="sleep">sleep</span>
## <span id="slot-boundp">slot-boundp</span>
## <span id="slot-exists-p">slot-exists-p</span>
## <span id="slot-makunbound">slot-makunbound</span>
## <span id="slot-missing">slot-missing</span>
## <span id="slot-unbound">slot-unbound</span>
## <span id="slot-value">slot-value</span>
## <span id="software-type">software-type</span>
## <span id="software-version">software-version</span>
## <span id="some">some</span>
## <span id="sort">sort</span>
## <span id="space">space</span>
## <span id="special">special</span>
## <span id="special-operator-p">special-operator-p</span>
## <span id="speed">speed</span>
## <span id="sqrt">sqrt</span>
## <span id="stable-sort">stable-sort</span>
## <span id="standard">standard</span>
## <span id="standard-char">standard-char</span>
## <span id="standard-char-p">standard-char-p</span>
## <span id="standard-class">standard-class</span>
## <span id="standard-generic-function">standard-generic-function</span>
## <span id="standard-method">standard-method</span>
## <span id="standard-object">standard-object</span>
## <span id="step">step</span>
## <span id="storage-condition">storage-condition</span>
## <span id="store-value">store-value</span>
## <span id="stream">stream</span>
## <span id="stream-element-type">stream-element-type</span>
## <span id="stream-error">stream-error</span>
## <span id="stream-error-stream">stream-error-stream</span>
## <span id="stream-external-format">stream-external-format</span>
## <span id="streamp">streamp</span>
## <span id="string">string</span>
## <span id="string-capitalize">string-capitalize</span>
## <span id="string-downcase">string-downcase</span>
## <span id="string-equal">string-equal</span>
## <span id="string-greaterp">string-greaterp</span>
## <span id="string-left-trim">string-left-trim</span>
## <span id="string-lessp">string-lessp</span>
## <span id="string-not-equal">string-not-equal</span>
## <span id="string-not-greaterp">string-not-greaterp</span>
## <span id="string-not-lessp">string-not-lessp</span>
## <span id="string-right-trim">string-right-trim</span>
## <span id="string-stream">string-stream</span>
## <span id="string-trim">string-trim</span>
## <span id="string-upcase">string-upcase</span>
## <span id="string/=">string/=</span>
## <span id="string<">string<</span>
## <span id="string<=">string<=</span>
## <span id="string=">string=</span>
## <span id="string>">string></span>
## <span id="string>=">string>=</span>
## <span id="stringp">stringp</span>
## <span id="structure">structure</span>
## <span id="structure-class">structure-class</span>
## <span id="structure-object">structure-object</span>
## <span id="style-warning">style-warning</span>
## <span id="sublis">sublis</span>
## <span id="subseq">subseq</span>
## <span id="subsetp">subsetp</span>
## <span id="subst">subst</span>, <span id="subst-if">subst-if</span>, <span id="subst-if-not">subst-if-not</span>, <span id="nsubst">nsubst</span>, <span id="nsubst-if">nsubst-if</span>, <span id="nsubst-if-not">nsubst-if-not</span>

!!! note "Function"
	**Syntax**:

``` lisp
subst new old tree &key key test test-not => new-tree
subst-if new predicate tree &key key => new-tree
subst-if-not new predicate tree &key key => new-tree
nsubst new old tree &key key test test-not => new-tree
nsubst-if new predicate tree &key key => new-tree
nsubst-if-not new predicate tree &key key => new-tree
```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="substitute">substitute</span>, <span id="substitute-if">substitute-if</span>, <span id="substitute-if-not">substitute-if-not</span>, <span id="nsubstitute">nsubstitute</span>, <span id="nsubstitute-if">nsubstitute-if</span>, <span id="nsubstitute-if-not">nsubstitute-if-not</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    substitute newitem olditem sequence &key from-end test test-not start end count key
    => result-sequence

    substitute-if newitem predicate sequence &key from-end start end count key
    => result-sequence

    substitute-if-not newitem predicate sequence &key from-end start end count key
    => result-sequence

    nsubstitute newitem olditem sequence &key from-end test test-not start end count key
    => sequence

    nsubstitute-if newitem predicate sequence &key from-end start end count key
    => sequence

    nsubstitute-if-not newitem predicate sequence &key from-end start end count key
    => sequence
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="subtypep">subtypep</span>
## <span id="svref">svref</span>
## <span id="sxhash">sxhash</span>
## <span id="symbol">symbol</span>
## <span id="symbol-function">symbol-function</span>
## <span id="symbol-macrolet">symbol-macrolet</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    symbol-macrolet ( (symbol expansion)* ) declaration* form*
    => result*
    ```

**Arguments and Values**:

- `symbol`: 一个符号
- `expansion`: 一个形式
- `declaration`: 一个`decalre`表达式, 不被求值
- `forms`: 一个隐式的`progn`
- `results`: `forms`返回的值

**Description**:

`symbol-macrolet`提供了影响 **符号的宏扩展环境** 的机制.

`symbol-macrolet`在词法上, 为每个由`symbol`命名的符号宏建立展开函数.
符号宏的展开函数唯一可以保证的属性是, 当被应用在形式和环境时, 返回正确的展开(展开在概念上存储在展开函数中还是环境中是依赖于实现的).

`symbol-macrolet`的词法作用域中对`symbol`作为变量的引用, 被使用正常的宏展开过程处理, 见[3.1.2.1.1 符号作为形式](../03-Evaluation-and-Compilation#3.1.2.1.1).
符号宏的展开受与调用符号宏相同的词法环境中进一步的宏展开影响, 跟常规宏一样.

`let`中的`declaration`在这里也可使用, 有一个例外: 如果用`special`声明一个已用`symbol-macrolet`定义的符号时, `symbol-macrolet`发出错误信号.

当`symbol-macrolet`形式中的`forms`被展开时, 使用`setq`设置指定变量的值时被视为是`setf`.
`psetq`一个定义为符号宏的符号时, 被视为是`psetf`, `multiple-value-setq`被视为是`values`上调用`setf`.

`symbol-macrolet`的使用可以被`let`遮盖. 换种方式说, `symbol-macrolet`只在`forms`周围的`symbol`词法绑定作用域中替换`symbol`的出现.

**Examples**:

``` lisp
;;; The following is equivalent to
;;;   (list 'foo (let ((x 'bar)) x)),
;;; not
;;;   (list 'foo (let (('foo 'bar)) 'foo))
 (symbol-macrolet ((x 'foo))
   (list x (let ((x 'bar)) x))) ;被let遮盖
=>  (foo bar)
NOT=>  (foo foo)

 (symbol-macrolet ((x '(foo x)))
   (list x))
=>  ((FOO X))
```

**Affected By**: 无.

**Exceptional Situations**:

如果尝试绑定定义为 **全局变量** 的符号, 发出类型为`program-error`的错误信号.

如果`declaration`中包含`special`声明, 使用了被`symbol-macrolet`绑定的符号, 发出类型为`program-error`的错误信号.

**See Also**:

[with-slots](#with-slots), [macroexpand](#macroexpand)

**Notes**:

特殊形式`symbol-macrolet`是实现`macroexpand`的基本机制.

如果一个`symbol-macrolet`形式是顶级形式, `forms`也按顶级形式处理. 见[3.2.3 文件编译](../03-Evaluation-and-Compilation#3.2.3).

## <span id="symbol-name">symbol-name</span>
## <span id="symbol-package">symbol-package</span>
## <span id="symbol-plist">symbol-plist</span>
## <span id="symbol-value">symbol-value</span>
## <span id="symbolp">symbolp</span>
## <span id="synonym-stream">synonym-stream</span>
## <span id="synonym-stream-symbol">synonym-stream-symbol</span>
## <span id="t">t</span>
## <span id="tagbody">tagbody</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    tagbody {tag | statement}* => nil
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="tailp">tailp</span>
## <span id="tan">tan</span>
## <span id="tanh">tanh</span>
## <span id="terpri">terpri</span>
## <span id="the">the</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    the value-type form => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="throw">throw</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    throw tag result-form =>|
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:




## <span id="time">time</span>
## <span id="trace">trace</span>
## <span id="translate-logical-pathname">translate-logical-pathname</span>
## <span id="translate-pathname">translate-pathname</span>
## <span id="tree-equal">tree-equal</span>
## <span id="truename">truename</span>
## <span id="two-way-stream">two-way-stream</span>
## <span id="two-way-stream-input-stream">two-way-stream-input-stream</span>
## <span id="two-way-stream-output-stream">two-way-stream-output-stream</span>
## <span id="type">type</span>
## <span id="type-error">type-error</span>
## <span id="type-error-datum">type-error-datum</span>
## <span id="type-error-expected-type">type-error-expected-type</span>
## <span id="type-of">type-of</span>
## <span id="typecase">typecase</span>
## <span id="typep">typep</span>
## <span id="unbound-slot">unbound-slot</span>
## <span id="unbound-slot-instance">unbound-slot-instance</span>
## <span id="unbound-variable">unbound-variable</span>
## <span id="undefined-function">undefined-function</span>
## <span id="unexport">unexport</span>
## <span id="unintern">unintern</span>
## <span id="union">union</span>, <span id="nunion">nunion</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    union list-1 list-2 &key key test test-not => result-list
    nunion list-1 list-2 &key key test test-not => result-list
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:



## <span id="unless">unless</span>, <span id="when">when</span>

!!! tip "Macro"
	**Syntax**:

	``` lisp
	when test-form form* => result*
	unless test-form form* => result*
	```

**Arguments and Values**:

- `test-form`: 一个形式
- `forms`: 一个隐式的`progn`
- `result`: 在`when`形式中, 如果`test-form`求值为true, 或在`unless`形式中`test-form`求值为false, 返回`forms`的求值结果; 其他情况返回`nil`

**Description**:

`when`和`unless`允许依赖于单个`test-form`执行`forms`.

在`when`形式中, 如果`test-form`求值为true, `forms`按 **从左至右** 的顺序求值, `forms`返回的结果作为`when`形式的返回结果. 否则, 如果`test-form`求值为false, `forms`不被求值, `when`形式返回`nil`.

在`unless`形式中, 如果`test-form`求值为false, `forms`按 **从左至右** 的顺序求值, `forms`返回的结果作为`unless`形式的返回结果. 否则, 如果`test-form`求值为true, `forms`不被求值, `unless`形式返回`nil`.

**Examples**:

``` lisp
(when t 'hello) =>  HELLO
(unless t 'hello) =>  NIL

(when nil 'hello) =>  NIL
(unless nil 'hello) =>  HELLO

(when t) =>  NIL
(unless nil) =>  NIL

(when t (prin1 1) (prin1 2) (prin1 3))
>>  123
=>  3

(unless t (prin1 1) (prin1 2) (prin1 3)) =>  NIL
(when nil (prin1 1) (prin1 2) (prin1 3)) =>  NIL
(unless nil (prin1 1) (prin1 2) (prin1 3))
>>  123
=>  3

(let ((x 3))
	(list
				;;1: (4)
				(when (oddp x) (incf x) (list x))
				;;2: NIL - 不执行(incf x) (list x)
				(when (oddp x) (incf x) (list x))
				;;3: (5)
				(unless (oddp x) (incf x) (list x))
				;;4: NIL - 不执行(incf x) (list x)
				(unless (oddp x) (incf x) (list x))

				;;5: 6
				(if (oddp x) (incf x) (list x))
				;;6: (6)
				(if (oddp x) (incf x) (list x))
				;;7: 7
				(if (not (oddp x)) (incf x) (list x))
				;;8: (7)
				(if (not (oddp x)) (incf x) (list x))))
=>  ((4) NIL (5) NIL 6 (6) 7 (7))
```

**Affected By**: 无.

**Exceptional Situations**: 无.

**See Also**:

[and](#and), [cond](#cond), [if](#if), [or](#or)

**Notes**:

``` lisp
(when test {form}+) ==  (and test (progn {form}+))
(when test {form}+) ==  (cond (test {form}+))
(when test {form}+) ==  (if test (progn {form}+) nil)
(when test {form}+) ==  (unless (not test) {form}+)

(unless test {form}+) ==  (cond ((not test) {form}+))
(unless test {form}+) ==  (if test nil (progn {form}+))
(unless test {form}+) ==  (when (not test) {form}+)
```



## <span id="unread-char">unread-char</span>
## <span id="unsigned-byte">unsigned-byte</span>
## <span id="untrace">untrace</span>
## <span id="unuse-package">unuse-package</span>
## <span id="unwind-protect">unwind-protect</span>

!!! attention "Special Operator"
	**Syntax**:

    ``` lisp
    unwind-protect protected-form cleanup-form* => result*
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:





## <span id="update-instance-for-different-class">update-instance-for-different-class</span>
## <span id="update-instance-for-redefined-class">update-instance-for-redefined-class</span>
## <span id="upgraded-array-element-type">upgraded-array-element-type</span>
## <span id="upgraded-complex-part-type">upgraded-complex-part-type</span>
## <span id="upper-case-p">upper-case-p</span>
## <span id="use-package">use-package</span>
## <span id="use-value">use-value</span>
## <span id="user-homedir-pathname">user-homedir-pathname</span>
## <span id="values">values</span>
## <span id="values-list">values-list</span>
## <span id="variable">variable</span>
## <span id="vector">vector</span>
## <span id="vector-pop">vector-pop</span>
## <span id="vector-push">vector-push</span>
## <span id="vector-push-extend">vector-push-extend</span>
## <span id="vectorp">vectorp</span>
## <span id="warn">warn</span>
## <span id="warning">warning</span>
## <span id="wild-pathname-p">wild-pathname-p</span>
## <span id="with-accessors">with-accessors</span>
## <span id="with-compilation-unit">with-compilation-unit</span>
## <span id="with-condition-restarts">with-condition-restarts</span>
## <span id="with-hash-table-iterator">with-hash-table-iterator</span>
## <span id="with-input-from-string">with-input-from-string</span>
## <span id="with-open-file">with-open-file</span>
## <span id="with-open-stream">with-open-stream</span>
## <span id="with-output-to-string">with-output-to-string</span>
## <span id="with-package-iterator">with-package-iterator</span>
## <span id="with-simple-restart">with-simple-restart</span>
## <span id="with-slots">with-slots</span>
## <span id="with-standard-io-syntax">with-standard-io-syntax</span>
## <span id="write">write</span>
## <span id="write-byte">write-byte</span>
## <span id="write-char">write-char</span>
## <span id="write-line">write-line</span>
## <span id="write-sequence">write-sequence</span>
## <span id="write-string">write-string</span>
## <span id="write-to-string">write-to-string</span>
## <span id="y-or-n-p">y-or-n-p</span>
## <span id="yes-or-no-p">yes-or-no-p</span>
## <span id="zerop">zerop</span>

!!! note "Function"
	**Syntax**:

    ``` lisp
    zerop number => generalized-boolean
    ```

**Arguments and Values**:


**Description**:


**Examples**:


**Affected By**:


**Exceptional Situations**:


**See Also**:


**Notes**:
