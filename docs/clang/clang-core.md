# Clang Core



|时间|内容|
|:------|:----|
|20190305-20190314|语法和语义部分; GNU Make. 移至[GNU Make](../build-tools/make.md)|
|20190314|准备阅读Autotools工具集文档. 移至[GNU Autotools](../build-tools/autotools.md)|
|20190319|添加字节对齐topic.|

## 资源

- [awesome-c](https://github.com/jobbole/awesome-c-cn)
- [C syntax](https://en.wikipedia.org/wiki/C_syntax), [The syntax of C in Backus-Naur form](http://www.cs.man.ac.uk/~pjj/bnf/c_syntax.bnf)

## 语法和语义

自动变量(auto), 全局变量, 寄存器变量(register), 易失变量(volatile), 静态变量(static)

### 声明

#### extern

在一个程序的所有源文件中, 一个外部变量只能在某个文件中定义一次, 其他文件可以通过extern声明访问它.

外部变量的定义中必须指定数组的长度, extern声明中则不一定要指定数组的长度.

例子:

``` c
// file1
// 变量声明
extern int sp;
extern double val[];

void push(double f);
void pop(void);

// file 2
// 变量定义
int sp = 0;
double valp[MAXVAL];
```

#### static

限定外部变量与函数, 将其声明的的对象的作用域先定位被编译源文件的剩余部分;

也可以用于声明局部变量: 与自动变量不同,不管其所在函数是否被调用它一直存在.

例子:

``` c
static char buf[BUFSIZE];
static int bufp = 0;
```

#### register
寄存器变量, 其地址不可访问;
只适用于自动变量和函数的形式参数.


例子:

``` c
void f(register unsinged m, register long n) {
    register in i;
}
```

#### 初始化

在不进行显式初始化的情况下, 外部变量和静态变量都将被初始化为0; 初始化表达式必须时常量表达式.

自动变量和寄存器变量的初值没有定义; 初始表达式中可以半酣任意在此表达式之前已经定义的值(包括函数调用).

### 预处理指令

- 指令都以`#`开始, `#`不需要在行首, 只要它之前只有空白字符.
- 指令的符号之间可以插入任意数量的空格或水平制表符.

``` c
#   define      N         100
```

- 指令总是在第一个换行符处结束, 除非使用`\`字符指明要延续.

``` c
#define DISK_CAPACITY (SIDES * \
                      TRACKS_PER_SIDE * \
                      SECTORS_PER_TRACK * \
                      BYTES_PER_SECTOR)
```

- 注释可以与指令放在同一行.

``` c
#define FREEZING_PT 32.0f /* freezing point of water */
```


#### 文件包含

- `#include`

在源文件中形如的行都将被替换为文件名指定的文件的内容:

``` c
#include "文件名"
#include <文件名>
```

被包含的文件本身也可以包含`#include`指令.

#### 宏定义

``` c
// 形式: define 名字 替换文本
#define forever for(;;)

#define max(A, B) ((A) > (B) ? (A) : (B))
// x = max(p+q, r+s);
// x = ((p+q) > (r+s) ? (p+q) : (r+s));
```

取消名字的宏定义

``` c
#undef getchar
```

预处理器运算符

``` c
// 引用实际参数的带引号的字符串
#define dprint(expr) printf(#expr "=%g\n", expr)
// 连接实际参数
#define paste(front, back) front ## back
```

#### 条件编译

``` c
#if, #elif, #else #endif
#ifdef, #ifndef
```

例子:

``` c
#if SYSTEM == SYSV
    #define HDR "sysv.h"
#elif SYSTEM == BSD
    #define HDR "bsd.h"
#elif SYSTEM == MSDOS
    #define HDR "msdos.h"
#else
    #define HDR "default.h"
#endif
#include HDR

#if !defined(HDR)
#define HDR
// hdr.h文件的内容
#endif

#ifndef HDR
#define HDR
// hdr.h文件的内容
#endif
```

#### 其它指令

- `#error`
- `#line`
- `#pragma`


## 运算符


|优先级|名称|符号|结合性|
|:---|:---|:---|:---|
|1|数组取下标|`[]`|左|
|1|函数调用|`()`|左|
|1|取结构和联合的成员|`.` `->`|左|
|1|自增(后缀)|`++`|左|
|1|自减(后缀)|`--`|左|
|2|自增(前缀)|`++`|右|
|2|自减(前缀)|`--`|右|
|2|取地址|`&`|右|
|2|间接寻址|`*`|右|
|2|一元正号|`+`|右|
|2|一元负号|`-`|右|
|2|按位求反|`~`|右|
|2|逻辑非|`!`|右|
|2|计算所需空间|`sizeof`|右|
|3|强制类型转换|`()`|右|
|4|乘法类运算符|`*` `/` `%`|左|
|5|加法类运算符|`+` `-`|左|
|6|移位|`<<` `>>`|左|
|7|关系|`<` `>` `<=` `>=`|左|
|8|判等|`==` `!=`|左|
|9|按位与|`&`|左|
|10|按位异或|`^`|左|
|11|按位或|`|`|左|
|12|逻辑与|`&&`|左|
|13|逻辑或|`||`|左|
|14|条件|`?:`|左|
|15|赋值|`=` `*=` `/=` `%=`<br> `+=` `-=` `<<=` `>>=` <br> `&=` `^=` `|=`|右|
|16|逗号|`,`|左|




## 字节对齐




## 标准库
