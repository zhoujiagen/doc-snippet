# 15. 数组

[TOC]

## <span id="15.1">15.1</span> 数组的概念

### <span id="15.1.1">15.1.1</span> 数组元素

数组包含一组被称为元素的对象, 可以通过线性坐标系统访问每个元素.

#### <span id="15.1.1.1">15.1.1.1</span> 数组索引

用(可能为空的)一系列索引引用数组元素. 这个系列的长度必须等于数组的维秩.
每个索引必须是非负的小于相应数组维度有限数. 数组索引从0开始.

#### <span id="15.1.1.2">15.1.1.2</span> 数组的维

数组的一个轴(aixs)称为 ==维==.

每个维是一个非负的有限数; 如果数组的任一维是零, 这个数组没有元素.
允许一个维为零, 在这种情况下数组没有元素, 尝试访问元素是一个错误. 可以使用数组的其他属性, 例如维.

##### <span id="15.1.1.2.1">15.1.1.2.1</span> 数组维度上的实现限制

实现可以在数组的维上添加限制, 但对该限制有一个最小需求. 见变量`array-dimension-limit`.

#### <span id="15.1.1.3">15.1.1.3</span> 数组的维秩

数组可以有任意数量的维(包括零). 维的数量称为 ==维秩==.

如果数组的维秩是零, 则称该数组没有维, 维的积(见`array-total-size`)是1; 维秩为零的数组有一个元素.

##### <span id="15.1.1.3.1">15.1.1.3.1</span> 向量

维秩为1的数组(一维数组), 称为 ==向量==.

###### <span id="15.1.1.3.1.1">15.1.1.3.1.1</span> 填充指针

填充指针是不比向量中元素总数大的非负整数.
不是所有的向量都有填充指针. 见函数`make-array`和`adjust-array`.

如果向量中元素有大于等于零、小于填充指针(如果存在的话)的索引, 则称该元素是 **活跃的**.
没有填充指针的数组, 所有元素都是活跃的.

只有向量有填充指针, 多维数组没有. 可以创建代替有填充指针的向量的多维数组.

##### <span id="15.1.1.3.2">15.1.1.3.2</span> 多维数组

###### <span id="15.1.1.3.2.1">15.1.1.3.2.1</span> 多维数组的存储布局

多维数组按行主序的顺序存储元素, 即内部将多维数组保存为一个一维数组, 多维索引按字典序排列, 最后一个索引变化最快.

###### <span id="15.1.1.3.2.2">15.1.1.3.2.2</span> 数组的维秩上实现限制

实现中可以在数组的维秩上添加显式, 但对该显式有一个最小需求. 见变量`array-rank-limit`.

### <span id="15.1.2">15.1.2</span> 特殊的数组

数组可以是 ==常规数组==, 即每个元素可以是任何对象; 也可以是 ==特殊的数组==, 即每个元素必须属于受限的类型.

短语 **特化为类型`<<type>>`的数组** 有时用于强调数组的元素类型. 当`<<type>>`是`t`时, 这个短语也使用, 虽然了特化为类型`t`的数组是常规数组, 不是特殊的数组.

下图列出了可用于数组创建、访问和信息操作的已定义名称.

<span id="Figure15-1">图 15-1. 通用的数组相关的已定义名称.</span>

``` lisp
adjust-array
adjustable-array-p
aref
array-dimension
array-dimension-limit
array-dimensions
array-displacement
array-element-type
array-has-fill-pointer-p
array-in-bounds-p
array-rank
array-rank-limit
array-row-major-index
array-total-size
array-total-size-limit
fill-pointer
make-array
svref
upgraded-array-element-type
upgraded-complex-part-type
vector
vector-pop
vector-push
vector-push-extend
```

#### <span id="15.1.2.1">15.1.2.1</span> 数组升级

类型T1的 **升级的数组元素类型** 是类型T2, T2是T1的超类型, 每当在对象创建或区分类型中T1被用作数组元素类型时, 替代T1.

在创建对象时, 被要求的元素类型被称为 **表达的数组元素类型**. 表达的数组元素类型的升级的数组元素类型, 称为被创建数组的 **实际数组元素类型**.

类型升级意味着在类型层次格中向上移动.
一个类型总是它的升级的数组元素类型的子类型.
同时, 如果类型Tx是另一个类型Ty的子类型, 则Tx的升级的数组元素类型必须是Ty的升级的数组元素类型的子类型.
两个不相交的类型可以升级为同一个类型.

类型T1的升级的数组元素类型T2是T1自身的一个函数, 即与使用T2的数组的任意其他属性不相关, 例如维秩、可调整性、填充指针或替代.
符合标准的实现可以使用函数`upgraded-array-element-type`预测实现如何升级指定的类型.


#### <span id="15.1.2.2">15.1.2.2</span> 特殊数组的必备种类

元素的类型被限制为类型`character`或`chatacter`的子类型的向量, 被称为字符串. 字符串属于类型`string`.
下图列出了与字符串相关的已定义名称.

字符串是特殊的数组, 逻辑上被这一章包含. 然而, 关于字符串的更多信息见[16. 字符串](../16-Strings).

<span id="Figure15-2">图 15-2. 操作字符串的操作符.</span>

``` lisp
char
make-string
nstring-capitalize
nstring-downcase
nstring-upcase
schar
string
string-capitalize
string-downcase
string-equal
string-greaterp
string-left-trim
string-lessp
string-not-equal
string-not-greaterp
string-not-lessp
string-right-trim
string-trim
string-upcase
string/=
string<
string<=
string=
string>
string>=
```

元素的类型被限制为类型`bit`的向量, 称为位向量. 位向量属于类型`bit-vector`.
下图列出了位数组的操作的已定义名称.

<span id="Figure15-3">图 15-3. 操作位数组的操作符.</span>

``` lisp
bit
bit-and
bit-andc1
bit-andc2
bit-eqv
bit-ior
bit-nand
bit-nor
bit-not
bit-orc1
bit-orc2
bit-xor
sbit
```

## <span id="15.2">15.2</span> 数组的字典

见[数组的字典](../Dictionary#15.2).
