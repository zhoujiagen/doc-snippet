# The Go Programming Language Specification[^1]

[^1]: The Go Programming Language Specification: https://golang.org/ref/spec

|时间|内容|
|:---------|:----------------------------------------|
| 20191031 | kick off: 介绍, 标记约定, 词法元素, 常量, 变量, 类型. |
| 20191101 | add: 类型和值的属性, 块, 声明和作用域, 表达式. |
| 20191102 | add: 语句. |
| 20191103 | add: 内建函数, 包, 程序初始化和执行, 错误, 运行时panic, 系统的考虑. |
| 20191104 | add: EBNF文法汇总. |

## EBNF文法

``` EBNF
Production  = production_name "=" [ Expression ] "." .
Expression  = Alternative { "|" Alternative } .
Alternative = Term { Term } .
Term        = production_name | token [ "…" token ] | Group | Option | Repetition .
Group       = "(" Expression ")" .
Option      = "[" Expression "]" .
Repetition  = "{" Expression "}" .
```

``` EBNF
newline        = /* the Unicode code point U+000A */ .
unicode_char   = /* an arbitrary Unicode code point except newline */ .
unicode_letter = /* a Unicode code point classified as "Letter" */ .
unicode_digit  = /* a Unicode code point classified as "Number, decimal digit" */ .


letter        = unicode_letter | "_" .
decimal_digit = "0" … "9" .
binary_digit  = "0" | "1" .
octal_digit   = "0" … "7" .
hex_digit     = "0" … "9" | "A" … "F" | "a" … "f" .

identifier = letter { letter | unicode_digit } .

int_lit        = decimal_lit | binary_lit | octal_lit | hex_lit .
decimal_lit    = "0" | ( "1" … "9" ) [ [ "_" ] decimal_digits ] .
binary_lit     = "0" ( "b" | "B" ) [ "_" ] binary_digits .
octal_lit      = "0" [ "o" | "O" ] [ "_" ] octal_digits .
hex_lit        = "0" ( "x" | "X" ) [ "_" ] hex_digits .

decimal_digits = decimal_digit { [ "_" ] decimal_digit } .
binary_digits  = binary_digit { [ "_" ] binary_digit } .
octal_digits   = octal_digit { [ "_" ] octal_digit } .
hex_digits     = hex_digit { [ "_" ] hex_digit } .

float_lit         = decimal_float_lit | hex_float_lit .

decimal_float_lit = decimal_digits "." [ decimal_digits ] [ decimal_exponent ] |
                    decimal_digits decimal_exponent |
                    "." decimal_digits [ decimal_exponent ] .
decimal_exponent  = ( "e" | "E" ) [ "+" | "-" ] decimal_digits .

hex_float_lit     = "0" ( "x" | "X" ) hex_mantissa hex_exponent .
hex_mantissa      = [ "_" ] hex_digits "." [ hex_digits ] |
                    [ "_" ] hex_digits |
                    "." hex_digits .
hex_exponent      = ( "p" | "P" ) [ "+" | "-" ] decimal_digits .

imaginary_lit = (decimal_digits | int_lit | float_lit) "i" .

rune_lit         = "'" ( unicode_value | byte_value ) "'" .
unicode_value    = unicode_char | little_u_value | big_u_value | escaped_char .
byte_value       = octal_byte_value | hex_byte_value .
octal_byte_value = `\` octal_digit octal_digit octal_digit .
hex_byte_value   = `\` "x" hex_digit hex_digit .
little_u_value   = `\` "u" hex_digit hex_digit hex_digit hex_digit .
big_u_value      = `\` "U" hex_digit hex_digit hex_digit hex_digit
                           hex_digit hex_digit hex_digit hex_digit .
escaped_char     = `\` ( "a" | "b" | "f" | "n" | "r" | "t" | "v" | `\` | "'" | `"` ) .

string_lit             = raw_string_lit | interpreted_string_lit .
raw_string_lit         = "`" { unicode_char | newline } "`" .
interpreted_string_lit = `"` { unicode_value | byte_value } `"` .

Type      = TypeName | TypeLit | "(" Type ")" .
TypeName  = identifier | QualifiedIdent .
TypeLit   = ArrayType | StructType | PointerType | FunctionType | InterfaceType |
	    SliceType | MapType | ChannelType .

ArrayType   = "[" ArrayLength "]" ElementType .
ArrayLength = Expression .
ElementType = Type .

SliceType = "[" "]" ElementType .

StructType    = "struct" "{" { FieldDecl ";" } "}" .
FieldDecl     = (IdentifierList Type | EmbeddedField) [ Tag ] .
EmbeddedField = [ "*" ] TypeName .
Tag           = string_lit .

PointerType = "*" BaseType .
BaseType    = Type .

FunctionType   = "func" Signature .
Signature      = Parameters [ Result ] .
Result         = Parameters | Type .
Parameters     = "(" [ ParameterList [ "," ] ] ")" .
ParameterList  = ParameterDecl { "," ParameterDecl } .
ParameterDecl  = [ IdentifierList ] [ "..." ] Type .

InterfaceType      = "interface" "{" { MethodSpec ";" } "}" .
MethodSpec         = MethodName Signature | InterfaceTypeName .
MethodName         = identifier .
InterfaceTypeName  = TypeName .

MapType     = "map" "[" KeyType "]" ElementType .
KeyType     = Type .

ChannelType = ( "chan" | "chan" "<-" | "<-" "chan" ) ElementType .

Block = "{" StatementList "}" .
StatementList = { Statement ";" } .

Declaration   = ConstDecl | TypeDecl | VarDecl .
TopLevelDecl  = Declaration | FunctionDecl | MethodDecl .

ConstDecl      = "const" ( ConstSpec | "(" { ConstSpec ";" } ")" ) .
ConstSpec      = IdentifierList [ [ Type ] "=" ExpressionList ] .

IdentifierList = identifier { "," identifier } .
ExpressionList = Expression { "," Expression } .

TypeDecl = "type" ( TypeSpec | "(" { TypeSpec ";" } ")" ) .
TypeSpec = AliasDecl | TypeDef .

AliasDecl = identifier "=" Type .

TypeDef = identifier Type .

VarDecl     = "var" ( VarSpec | "(" { VarSpec ";" } ")" ) .
VarSpec     = IdentifierList ( Type [ "=" ExpressionList ] | "=" ExpressionList ) .

ShortVarDecl = IdentifierList ":=" ExpressionList .

FunctionDecl = "func" FunctionName Signature [ FunctionBody ] .
FunctionName = identifier .
FunctionBody = Block .

MethodDecl = "func" Receiver MethodName Signature [ FunctionBody ] .
Receiver   = Parameters .

Operand     = Literal | OperandName | "(" Expression ")" .
Literal     = BasicLit | CompositeLit | FunctionLit .
BasicLit    = int_lit | float_lit | imaginary_lit | rune_lit | string_lit .
OperandName = identifier | QualifiedIdent.

QualifiedIdent = PackageName "." identifier .

CompositeLit  = LiteralType LiteralValue .
LiteralType   = StructType | ArrayType | "[" "..." "]" ElementType |
                SliceType | MapType | TypeName .
LiteralValue  = "{" [ ElementList [ "," ] ] "}" .
ElementList   = KeyedElement { "," KeyedElement } .
KeyedElement  = [ Key ":" ] Element .
Key           = FieldName | Expression | LiteralValue .
FieldName     = identifier .
Element       = Expression | LiteralValue .

FunctionLit = "func" Signature FunctionBody .

PrimaryExpr =
	Operand |
	Conversion |
	MethodExpr |
	PrimaryExpr Selector |
	PrimaryExpr Index |
	PrimaryExpr Slice |
	PrimaryExpr TypeAssertion |
	PrimaryExpr Arguments .

Selector       = "." identifier .
Index          = "[" Expression "]" .
Slice          = "[" [ Expression ] ":" [ Expression ] "]" |
                 "[" [ Expression ] ":" Expression ":" Expression "]" .
TypeAssertion  = "." "(" Type ")" .
Arguments      = "(" [ ( ExpressionList | Type [ "," ExpressionList ] ) [ "..." ] [ "," ] ] ")" .

MethodExpr    = ReceiverType "." MethodName .
ReceiverType  = Type .

Expression = UnaryExpr | Expression binary_op Expression .
UnaryExpr  = PrimaryExpr | unary_op UnaryExpr .

binary_op  = "||" | "&&" | rel_op | add_op | mul_op .
rel_op     = "==" | "!=" | "<" | "<=" | ">" | ">=" .
add_op     = "+" | "-" | "|" | "^" .
mul_op     = "*" | "/" | "%" | "<<" | ">>" | "&" | "&^" .

unary_op   = "+" | "-" | "!" | "^" | "*" | "&" | "<-" .

Conversion = Type "(" Expression [ "," ] ")" .

Statement =
	Declaration | LabeledStmt | SimpleStmt |
	GoStmt | ReturnStmt | BreakStmt | ContinueStmt | GotoStmt |
	FallthroughStmt | Block | IfStmt | SwitchStmt | SelectStmt | ForStmt |
	DeferStmt .

SimpleStmt = EmptyStmt | ExpressionStmt | SendStmt | IncDecStmt | Assignment | ShortVarDecl .

EmptyStmt = .

LabeledStmt = Label ":" Statement .
Label       = identifier .

ExpressionStmt = Expression .

SendStmt = Channel "<-" Expression .
Channel  = Expression .

IncDecStmt = Expression ( "++" | "--" ) .

Assignment = ExpressionList assign_op ExpressionList .

assign_op = [ add_op | mul_op ] "=" .

IfStmt = "if" [ SimpleStmt ";" ] Expression Block [ "else" ( IfStmt | Block ) ] .

SwitchStmt = ExprSwitchStmt | TypeSwitchStmt .

ExprSwitchStmt = "switch" [ SimpleStmt ";" ] [ Expression ] "{" { ExprCaseClause } "}" .
ExprCaseClause = ExprSwitchCase ":" StatementList .
ExprSwitchCase = "case" ExpressionList | "default" .

TypeSwitchStmt  = "switch" [ SimpleStmt ";" ] TypeSwitchGuard "{" { TypeCaseClause } "}" .
TypeSwitchGuard = [ identifier ":=" ] PrimaryExpr "." "(" "type" ")" .
TypeCaseClause  = TypeSwitchCase ":" StatementList .
TypeSwitchCase  = "case" TypeList | "default" .
TypeList        = Type { "," Type } .

ForStmt = "for" [ Condition | ForClause | RangeClause ] Block .
Condition = Expression .

ForClause = [ InitStmt ] ";" [ Condition ] ";" [ PostStmt ] .
InitStmt = SimpleStmt .
PostStmt = SimpleStmt .

RangeClause = [ ExpressionList "=" | IdentifierList ":=" ] "range" Expression .

GoStmt = "go" Expression .

SelectStmt = "select" "{" { CommClause } "}" .
CommClause = CommCase ":" StatementList .
CommCase   = "case" ( SendStmt | RecvStmt ) | "default" .
RecvStmt   = [ ExpressionList "=" | IdentifierList ":=" ] RecvExpr .
RecvExpr   = Expression .

ReturnStmt = "return" [ ExpressionList ] .

BreakStmt = "break" [ Label ] .

ContinueStmt = "continue" [ Label ] .

GotoStmt = "goto" Label .

FallthroughStmt = "fallthrough" .

DeferStmt = "defer" Expression .

SourceFile       = PackageClause ";" { ImportDecl ";" } { TopLevelDecl ";" } .

PackageClause  = "package" PackageName .
PackageName    = identifier .

ImportDecl       = "import" ( ImportSpec | "(" { ImportSpec ";" } ")" ) .
ImportSpec       = [ "." | PackageName ] ImportPath .
ImportPath       = string_lit .
```

## 1 [介绍](https://golang.org/ref/spec#Introduction)

本文是Go编程语言的参考手册. 更多信息和其它文档参见[golang.org](https://golang.org/).

Go是设计为系统编程的通用语言. 它是强类型的、支持垃圾收集、显式支持并发编程. 程序是由包构造的, 包的属性支持高效的依赖管理.

文法是紧凑和规则的, 便于像集成开发环境的自动化工具的分析.

## 2 [标记约定](https://golang.org/ref/spec#Notation)

语法使用EBNF(Extended Backus-Naur Form)描述:

``` EBNF
Production  = production_name "=" [ Expression ] "." .
Expression  = Alternative { "|" Alternative } .
Alternative = Term { Term } .
Term        = production_name | token [ "…" token ] | Group | Option | Repetition .
Group       = "(" Expression ")" .
Option      = "[" Expression "]" .
Repetition  = "{" Expression "}" .
```

`Production`是由`Term`和下面的操作符构造的`Expression`, 按优先级升序:

```
|   备选(alternation)
()  分组(grouping)
[]  可选(option): 0或1次
{}  重复(repetition): 0到n次
```

小写的名称用于标识词法记号. 非终结符用首字母大写的驼峰格式标识(CamelCase). 词法记号用双引号(`""`)或反引号(``` `` ```)包裹.

形式`a … b`表示从`a`到`b`的字符集合作为备选. `…`在本规范中也用作表示各种枚举或不再进一步描述的代码片段. 字符`…`(不是三个字符`...`)不是Go语言的标记.

### [源代码表示](https://golang.org/ref/spec#Source_code_representation)

源代码是编码为UTF-8的Unicode文本.
文本不是规范法的, 因此单个重音码点与组合一个重音符号和一个字母构造出的相同字符是不同的; 视为两个码点.
为了方便, 本文使用未限定的术语字符(character)表示源码文本中的Unicode码点.

每个码点是不同的; 例如, 大写和小写字母是不同的字符.

实现约束: 为与其它工具兼容, 编译器不允许源码文本中出现NUL字符(`U+0000`).

### [字符](https://golang.org/ref/spec#Characters)

下面的像用于表示特定的Unicode字符类:

``` EBNF
newline        = /* the Unicode code point U+000A */ .
unicode_char   = /* an arbitrary Unicode code point except newline */ .
unicode_letter = /* a Unicode code point classified as "Letter" */ .
unicode_digit  = /* a Unicode code point classified as "Number, decimal digit" */ .
```

The Unicode Standard 8.0[^2]的4.5节 *通用类别* 定义了一组字符类别. Go将在字母类别Lu、Ll、Lt、Lm和Lo中的所有字符作为Unicode字母处理, 将数值类别Nd中的作为Unicode数字处理.

[^2]: The Unicode Standard 8.0: https://www.unicode.org/versions/Unicode8.0.0/

### [字母和数字](https://golang.org/ref/spec#Letters_and_digits)

下划线字符`_`(`U+005F`)被视为一个字母(letter):

``` EBNF
letter        = unicode_letter | "_" .
decimal_digit = "0" … "9" .
binary_digit  = "0" | "1" .
octal_digit   = "0" … "7" .
hex_digit     = "0" … "9" | "A" … "F" | "a" … "f" .
```

## 3 [词法元素](https://golang.org/ref/spec#Lexical_elements)

### [注释](https://golang.org/ref/spec#Comments)

注释做为程序的文档, 有两种形式:

1. 行注释以字符序列`//`开始, 在行尾结束.
2. 通用注释以字符序列`/*`开始, 以后续的第一个字符序列`*/`结束.

注释不能在rune、字符串字面量、注释中开始. 不包含换行的通用注释表现为空格. 其它注释表现为换行.

### [记号](https://golang.org/ref/spec#Tokens)

记号(token)构成Go语言的词汇表. 有四个类型: 标识符(identifier)、关键字(keyword)、操作符(operator)和标点符号(punctuation)、字面量(literal).
由空格(`U+0020`)、水平制表符(`U+0009`)、回车(`U+000D`)、换行(`U+000A`)构造的空白, 除了在分隔记号否则会组合成单个记号外, 被忽略.
同时, 换行或文件结束会触发插入一个分号. 在将输入拆分为记号时, 下一个记号是构成有效记号的最长的字符序列.

### [分号](https://golang.org/ref/spec#Semicolons)

形式文法使用分号(`;`)作为一组产生式的终结符. Go程序使用下面的两个规则忽略大多数分号:

1. 当输入被拆分为记号时, 如果行的最后一个记号是下面这些时, 会自动在记号流中该记号后面插入一个分号:<br>
标识符<br>
整数、浮点数、虚部、rune或字符串字面量<br>
关键字`break`、`continue`、`fallthrough`、`return`之一<br>
操作符和标点符号`++`、`--`、`)`、`]`、`}`之一<br>
2. 为支持复杂语句占据单行, 可以省略闭`)`或`}`之前的分号.

为反映习惯用法, 本文档中代码示例使用这些规则省略分号.

### [标识符](https://golang.org/ref/spec#Identifiers)

标识符命名程序实体, 例如变量和类型.
标识符是一个或多个字母和数字的序列. 标识符的第一个字符必须是字母.

``` EBNF
identifier = letter { letter | unicode_digit } .
```

``` go
a
_x9
ThisVariableIsExported
αβ
```

一些标识符是预声明的.

### [关键字](https://golang.org/ref/spec#Keywords)

下面的关键字被保留, 不能用作标识符.

``` go
break        default      func         interface    select
case         defer        go           map          struct
chan         else         goto         package      switch
const        fallthrough  if           range        type
continue     for          import       return       var
```

### [操作符和标点符号](https://golang.org/ref/spec#Operators_and_punctuation)

下面的字符序列表示操作符(包括赋值操作符)和标点符号:

``` go
+    &     +=    &=     &&    ==    !=    (    )
-    |     -=    |=     ||    <     <=    [    ]
*    ^     *=    ^=     <-    >     >=    {    }
/    <<    /=    <<=    ++    =     :=    ,    ;
%    >>    %=    >>=    --    !     ...   .    :
     &^          &^=
```

### [整数字面量](https://golang.org/ref/spec#Integer_literals)

整数字面量是表示整数常量的数字序列. 可选的前缀设置基: 二进制(`0b`或`0B`)、八进制(`0o`或`0O`)、十六进制(`0x`或`0X`). 单个`0`表示十进制的零.
在十六进制字面量中, 从`a`到`f`和`A`到`F`的字母表示值10到15.

为了可读性, 下划线字符(`_`)可以在基前缀之后或后续的数字质检出现; 这个下划线不改变字面量的值.

``` EBNF
int_lit        = decimal_lit | binary_lit | octal_lit | hex_lit .
decimal_lit    = "0" | ( "1" … "9" ) [ [ "_" ] decimal_digits ] .
binary_lit     = "0" ( "b" | "B" ) [ "_" ] binary_digits .
octal_lit      = "0" [ "o" | "O" ] [ "_" ] octal_digits .
hex_lit        = "0" ( "x" | "X" ) [ "_" ] hex_digits .

decimal_digits = decimal_digit { [ "_" ] decimal_digit } .
binary_digits  = binary_digit { [ "_" ] binary_digit } .
octal_digits   = octal_digit { [ "_" ] octal_digit } .
hex_digits     = hex_digit { [ "_" ] hex_digit } .
```

``` go
42
4_2
0600
0_600
0o600
0O600       // second character is capital letter 'O'
0xBadFace
0xBad_Face
0x_67_7a_2f_cc_40_c6
170141183460469231731687303715884105727
170_141183_460469_231731_687303_715884_105727

_42         // an identifier, not an integer literal
42_         // invalid: _ must separate successive digits
4__2        // invalid: only one _ at a time
0_xBadFace  // invalid: _ must separate successive digits
```

### [浮点数字面量](https://golang.org/ref/spec#Floating-point_literals)

浮点数字面量是浮点数常量的十进制或十六进制表示.

十进制浮点数字面量由整数部分(十进制数字)、小数点、小数部分(十进制数字)和指数部分(`e`或`E`后接一个可选的符号和十进制数字)构成.
可以省略整数部分或小数部分中的一个; 可以省略小数点或字数部分中的一个.
指数值 $exp$ 将尾数(整数和小数部分)放大 $10^{exp}$.

十六进制浮点数字面量由`0x`或`0X`前缀、整数部分(十六进制数字)、小数点、小数部分(十六进制数字)和指数部分(`p`或`P`后接一个可选的符号和是进制数字)构成.
可以省略整数部分或小数部分中的一个; 也可以省略小数点, 但要有指数部分.(语法匹配IEEE 754-2008 §5.12.3中的语法.)
指数值 $exp$ 将尾数(整数和小数部分)放大 $2^{exp}$.

为了可读性, 下划线字符(`_`)可以在基前缀之后或后续的数字质检出现; 这个下划线不改变字面量的值.

``` EBNF
float_lit         = decimal_float_lit | hex_float_lit .

decimal_float_lit = decimal_digits "." [ decimal_digits ] [ decimal_exponent ] |
                    decimal_digits decimal_exponent |
                    "." decimal_digits [ decimal_exponent ] .
decimal_exponent  = ( "e" | "E" ) [ "+" | "-" ] decimal_digits .

hex_float_lit     = "0" ( "x" | "X" ) hex_mantissa hex_exponent .
hex_mantissa      = [ "_" ] hex_digits "." [ hex_digits ] |
                    [ "_" ] hex_digits |
                    "." hex_digits .
hex_exponent      = ( "p" | "P" ) [ "+" | "-" ] decimal_digits .
```

``` go
0.
72.40
072.40       // == 72.40
2.71828
1.e+0
6.67428e-11
1E6
.25
.12345E+5
1_5.         // == 15.0
0.15e+0_2    // == 15.0

0x1p-2       // == 0.25
0x2.p10      // == 2048.0
0x1.Fp+0     // == 1.9375
0X.8p-0      // == 0.5
0X_1FFFP-16  // == 0.1249847412109375
0x15e-2      // == 0x15e - 2 (integer subtraction)

0x.p1        // invalid: mantissa has no digits
1p-2         // invalid: p exponent requires hexadecimal mantissa
0x1.5e-2     // invalid: hexadecimal mantissa requires p exponent
1_.5         // invalid: _ must separate successive digits
1._5         // invalid: _ must separate successive digits
1.5_e1       // invalid: _ must separate successive digits
1.5e_1       // invalid: _ must separate successive digits
1.5e1_       // invalid: _ must separate successive digits
```

### [虚部字面量](https://golang.org/ref/spec#Imaginary_literals)

虚部字面量表示复数常量的虚部. 它由整数或浮点数字面量后接小写字母`i`构成.
虚部字面量的值是相应的整数或浮点数字面量乘以虚部单元`i`的值.

``` EBNF
imaginary_lit = (decimal_digits | int_lit | float_lit) "i" .
```

为了向前兼容, 只由十进制数字(可能有下划线)的虚部字面量整数部分被视为十进制整数, 甚至它以`0`开始.

``` go
0i
0123i         // == 123i for backward-compatibility
0o123i        // == 0o123 * 1i == 83i
0xabci        // == 0xabc * 1i == 2748i
0.i
2.71828i
1.e+0i
6.67428e-11i
1E6i
.25i
.12345E+5i
0x1p-2i       // == 0x1p-2 * 1i == 0.25i
```

### [rune字面量](https://golang.org/ref/spec#Rune_literals)

rune字面量表示rune常量, 一个表示Unicode码点的整数值. rune字面量被表示为包裹在单引号内的一个或多个字符, 例如`'x'`、`'\n'`.
在单引号中, 除了换行和未转义的单引号外的字符可以出现. 包括在单引号内的单个字符表示该字符自身的Unicode值, 而以反斜线符号开始的多字符序列以多种格式编码值.

最简单的形式在但引号中表示单个字符; 因为Go源码文本是UTF-8编码的Unicode字符, 多个UTF-8编码的字节可以表示单个整数值. 例如, 字面量`'a'`持有表示字面量`a`、Unicode `U+0061`、值`0x61`的单个字节, 而`'ä'`持有表示字面量`a-dieresis`、Unicode `U+00E4`、值`0xe4`的两个字节(`0xc3` `0xa4`).

一些反斜线符号转义允许将任意值编码为ASCII文本. 有四种将整数值表示为数值常量的方式:

- `\x`后接2个十六进制数字
- `\u`后接4个十六进制数字
- `\U`后接8个十六进制数字
- `\`后接3个八进制数字

在每个情况中, 字面量的值是在相应基上的数字表示的值.

尽管这些表示的结果都是整数, 它们有不同的有效范围. 八进制转义表示0到255(包含)之间的值. 十六进制转义满足构造的条件.
转义`\u`和`\U`表示Unicode码点, 它们中的一些值是非法的, 包括`0x10FFFF`之上的值.

在反斜线符号之后, 一些单字符穿衣表示特殊的值:

```
\a   U+0007 alert or bell
\b   U+0008 backspace
\f   U+000C form feed
\n   U+000A line feed or newline
\r   U+000D carriage return
\t   U+0009 horizontal tab
\v   U+000b vertical tab
\\   U+005c backslash
\'   U+0027 single quote  (valid escape only within rune literals)
\"   U+0022 double quote  (valid escape only within string literals)
```

所有其它以反斜线符号开始的序列在rune字面量中是非法的.

``` EBNF
rune_lit         = "'" ( unicode_value | byte_value ) "'" .
unicode_value    = unicode_char | little_u_value | big_u_value | escaped_char .
byte_value       = octal_byte_value | hex_byte_value .
octal_byte_value = `\` octal_digit octal_digit octal_digit .
hex_byte_value   = `\` "x" hex_digit hex_digit .
little_u_value   = `\` "u" hex_digit hex_digit hex_digit hex_digit .
big_u_value      = `\` "U" hex_digit hex_digit hex_digit hex_digit
                           hex_digit hex_digit hex_digit hex_digit .
escaped_char     = `\` ( "a" | "b" | "f" | "n" | "r" | "t" | "v" | `\` | "'" | `"` ) .
```

``` go
'a'
'ä'
'本'
'\t'
'\000'
'\007'
'\377'
'\x07'
'\xff'
'\u12e4'
'\U00101234'
'\''         // rune literal containing single quote character
'aa'         // illegal: too many characters
'\xa'        // illegal: too few hexadecimal digits
'\0'         // illegal: too few octal digits
'\uDFFF'     // illegal: surrogate half
'\U00110000' // illegal: invalid Unicode code point
```

### [字符串字面量](https://golang.org/ref/spec#String_literals)

字符串字面量表示由串接字符的序列获得的字符串常量. 有两种形式: 原始的字符串字面量和解释的字符串字面量.

原始的字符串字面量是包裹在反引号内的字符序列, 例如``` `foo` ```. 在反引号中, 除了反引号外其它字符可以出现.
原始的字符串字面量的值是由反引号内部的未解释的(隐式的UTF-8编码的)字符构成的字符串; 特别的, 反斜线符号没有特殊的含义, 字符j中可以包含换行.
原始的字符串字面量中的回车符号(`'\r'`)被丢弃.

解释的字符串字面量是包裹在双引号内的字符序列, 例如`"bar"`. 在双引号中, 除了换行和未转义的双引号外其它字符可以出现.
双引号内的文本构成字面量的值, 反斜线转义作为rune字面量解释(除了`\'`是非法的, `\"`是合法的).
3个数字的八进制(`\nnn`)和2个数字的十六进制(`\xnn`)转义表示结果字符串的每个字节; 所有其它转义表示每个字符的(可能是多个字节的)UTF-8编码.
因此, 在字符串字面量中, `\377`、`\xFF`表示值`0xFF=255`的单个字节, 而`ÿ`、`\u00FF`、`\xc3 \xbf`表示字符`U+00FF`的UTF-8编码的两个字节`0xc3 0xbf`.

``` EBNF
string_lit             = raw_string_lit | interpreted_string_lit .
raw_string_lit         = "`" { unicode_char | newline } "`" .
interpreted_string_lit = `"` { unicode_value | byte_value } `"` .
```

``` go
`abc`                // same as "abc"
`\n
\n`                  // same as "\\n\n\\n"
"\n"
"\""                 // same as `"`
"Hello, world!\n"
"日本語"
"\u65e5本\U00008a9e"
"\xff\u00FF"
"\uD800"             // illegal: surrogate half
"\U00110000"         // illegal: invalid Unicode code point
```

下面的这些示例都表示相同的字符串:

``` go
"日本語"                                 // UTF-8 input text
`日本語`                                 // UTF-8 input text as a raw literal
"\u65e5\u672c\u8a9e"                    // the explicit Unicode code points
"\U000065e5\U0000672c\U00008a9e"        // the explicit Unicode code points
"\xe6\x97\xa5\xe6\x9c\xac\xe8\xaa\x9e"  // the explicit UTF-8 bytes
```

如果源代码将一个字符表示为两个码点, 例如组合一个重音符号和一个字母, 如果放在rune字面量中是个错误(它不是单个码点), 放在字符串字面量中会作为两个码点出现.

## 4 [常量](https://golang.org/ref/spec#Constants)

有布尔常量、rune常量、整数常量、浮点数常量、复数常量和字符串常量.
rune常量、整数常量、浮点数常量和复数常量统称为数值常量.

常量值可以表示为:

- rune、整数、浮点数、虚部或字符串字面量
- 表示常量的标识符
- 常量表达式
- 结果为常量的转换
- 一些内建函数的结果值, 例如在任意值上应用`unsafe.Sizeof`、在一些表达式上应用`cap`或`len`、在复数常量上应用`real`和`imag`、在数值常量上应用`complex`

布尔真值用预声明常量`true`和`false`表示.
预声明标识符`iota`表示整数常量.

通常, 复数常量的形式是常量表达式.

数值常量表示任意精度的精确值, 不会上溢. 没有表示IEEE-754的负零(negative zero)、无穷大(ininity)和非数值(not-a-number, NaN)值的常量.

常量可以是有类型的或无类型的(untyped). 字面量常量、`true`、`false`、`iota`和只包含无类型常量操作数的常量表达式是无类型的.

无类型的常量有一个默认类型, 这个类型是在需要有类型的值得上下文中隐式转换到的类型, 例如, 在段变量声明`i := 0`中, 没有显式的类型.
依赖于它是否是布尔、rune、整数、浮点数、复数或字符串常量, 无类型常量的默认类型分别是`bool`、`rune`、`int`、`float64`、`complex128`或`string`.

实现约束: 尽管在语言中数值常量有任意精度, 编译器可以使用受限精度的内部表示实现它们. 即, 每个实现必须:

- 表示整数常量至少用256位
- 表示浮点数常量, 包括复数常量的部分, 尾数至少用256位, 有符号的二进制指数至少16位
- 无法精确的表示整数常量时, 抛出错误
- 如果因为上溢而无法表示浮点数或复数常量时, 抛出错误
- 如果因为精度限制而无法表示浮点数或复数常量时, 舍入到最近的可表示常量.

这些要求对字面量常量和求值常量表达式的结果都适用.

## 5 [变量](https://golang.org/ref/spec#Variables)

变量是持有值得一个存储位置. 被允许的值得集合是由变量的类型确定的.

变量声明, 或函数声明或函数字面量的签名中函数参数和结果, 会为命名的变量保留存储空间.
调用内建函数`new`或取复合字面量的地址, 在运行时为变量分配存储空间. 这种匿名变量通过指针间接寻址(pointer indirection)引用.

数组、切片和结构类型的结构变量(structed variable)有可被单独寻址的元素和字段. 每个这样的元素表现为一个变量.

变量的 ==静态类型== 是在它的声明中指定的类型、在`new`调用或复合字面量中指定的类型、结构变量中元素的类型.
接口类型的变量还有一个动态类型, 它是运行时赋予变量的值的具体类型(除了值是预声明标识符`nil`, 它没有类型).
动态类型在执行时可能发生变化, 但接口变量中存储的值总是可以赋值给变量的静态类型.

``` go
var x interface{}  // x is nil and has static type interface{}
var v *T           // v has value nil, static type *T
x = 42             // x has value 42 and dynamic type int
x = v              // x has value (*T)(nil) and dynamic type *T
```

在表达式中引用变量会获取变量的值; 这个值是最近赋值给该变量的值.
如果一个变量还未被赋值, 它的值是其类型的零值.

## 6 [类型](https://golang.org/ref/spec#Types)

类型确定了一组值和特定于这些值得草果和方法. 类型可以用类型名称标记(如果有时), 或用类型字面量描述(从既有类型构成类型).

``` EBNF
Type      = TypeName | TypeLit | "(" Type ")" .
TypeName  = identifier | QualifiedIdent .
TypeLit   = ArrayType | StructType | PointerType | FunctionType | InterfaceType |
	    SliceType | MapType | ChannelType .
```

语言预声明了一些类型名称. 其它类型是用类型声明引入的. 符合类型, 包括数组、结构、指针、函数、接口、切片、映射和通道类型, 可以使用类型字面量构造.

每个类型`T`有一个底层类型(underlying type): 如果`T`是预声明的布尔、数值或字符串类型, 或类型字面量, 相应的底层类型是`T`本身.
否则, `T`的底层类型是在它的类型声明中引用的类型的底层类型.

``` go
type (
	A1 = string
	A2 = A1
)

type (
	B1 string
	B2 B1
	B3 []B1
	B4 B3
)
```

`string`、`A1`、`A2`、`B1`、`B2`的底层类型是`string`. `[]B1`、`B3`、`B4`的底层类型是`[]B1`.

### [方法集合](https://golang.org/ref/spec#Method_sets)

类型有一个与它相关的方法集合. 接口类型的方法集合是它的接口. 其它类型`T`的方法集合由所有用接受者类型`T`声明的方法构成.
指针类型`*T`的方法集合是所有用接受者类型`*T`或`T`声明的方法(即也包括`T`的方法集合).
应用在结构上的规则包含内嵌字段, 见结构类型一节.
其它类型的方法集合为空.
在方法集合中, 每个方法必须有唯一的非空的方法名称.

类型的方法集合确定了该类型实现的接口, 和使用该类型作为接受者时可以调用的方法.

### [布尔类型](https://golang.org/ref/spec#Boolean_types)

布尔类型表示布尔真值集合, 布尔真值由预声明常量`true`和`false`标记.
预声明的布尔类型是`bool`; 它是一个定义的类型(defined type).

### [数值类型](https://golang.org/ref/spec#Numeric_types)

数值类型表示整数或浮点数值得集合. 预声明的体系结构独立的数值类型:

```
uint8       the set of all unsigned  8-bit integers (0 to 255)
uint16      the set of all unsigned 16-bit integers (0 to 65535)
uint32      the set of all unsigned 32-bit integers (0 to 4294967295)
uint64      the set of all unsigned 64-bit integers (0 to 18446744073709551615)

int8        the set of all signed  8-bit integers (-128 to 127)
int16       the set of all signed 16-bit integers (-32768 to 32767)
int32       the set of all signed 32-bit integers (-2147483648 to 2147483647)
int64       the set of all signed 64-bit integers (-9223372036854775808 to 9223372036854775807)

float32     the set of all IEEE-754 32-bit floating-point numbers
float64     the set of all IEEE-754 64-bit floating-point numbers

complex64   the set of all complex numbers with float32 real and imaginary parts
complex128  the set of all complex numbers with float64 real and imaginary parts

byte        alias for uint8
rune        alias for int32
```

n位整数的值是n位宽, 使用2的补码运算表示.

有一组预声明的数值类型, 它们有特定于实现的大小:

```
uint     either 32 or 64 bits
int      same size as uint
uintptr  an unsigned integer large enough to store the uninterpreted bits of a pointer value
```

为避免移植性问题, 除`byte`和`rune`外, 所有数值类型是定义的类型.
如果在表达式或赋值中混用了不同的数值类型, 需要显式转换. 例如, `int32`与`int`是不同的类型, 尽管在特定体系结构中它们有相同的大小.

### [字符串类型](https://golang.org/ref/spec#String_types)

字符串类型标识字符串值得集合. 字符串值是(可能为空的)字节序列. 字节的数量称为字符串的长度, 从不为负.
字符串是不可变的: 一旦穿件, 不能改变字符串的内容. 预声明的字符串类型是`string`; 它是一个已定义的类型.

字符串`s`的长度说可以用内建函数`len`获得. 如果字符串是一个常量, 它的长度是编译时常量.
字符串的字节可以用整数索引0到`len(s)-1`访问. 取这种元素的地址是非法的; 如果`s[i]`是字符串的第`i`个字节, `&s[i]`是无效的.

### [数组类型](https://golang.org/ref/spec#Array_types)

数组是固定数量的单个类型的元素的序列, 这个类型称为元素类型. 元素的数量称为数组的长度, 从不为负.

``` EBNF
ArrayType   = "[" ArrayLength "]" ElementType .
ArrayLength = Expression .
ElementType = Type .
```

长度是数组类型的一部分; 它必须被求值为用类型`int`的值表示的非负常量. 数组`a`的长度可以用内建函数`len`获得.
元素可以用整数索引0到`len(a)-1`寻址. 数组类型总是一维的, 但可以用于构成多维类型.

``` go
[32]byte
[2*N] struct { x, y int32 }
[1000]*float64
[3][5]int
[2][2][2]float64  // same as [2]([2]([2]float64))
```

### [切片类型](https://golang.org/ref/spec#Slice_types)

切片是底层数组的一个连续片段的描述符, 支持从这个数组中访问固定数量的元素序列.
切片类型标识它的元素类型的数组的所有切片. 元素的数量称为切片的茶姑娘说, 从不为负.
未初始化的切片的值是`nil`.

``` EBNF
SliceType = "[" "]" ElementType .
```

切片`s`的长度可以用内建函数`len`获得; 与数组不同的是它可以在执行中改变. 元素可以用整数索引0到`len(a)-1`寻址. 给定元素的切片索引可以比底层数组中同一元素的索引小.

切片一旦被创建, 总是与持有其元素的底层数组关联. 因此, 切片总是与它的数组和同一数组的其它切片共享存储空间; 与之相反, 不同的数组总是表示不同的存储空间.

切片底层的数组可以扩展越过切片的尾部. 容量(capacity)是这个扩展的度量: 是切片的长度与数组超过切片的长度的和; 可以在原始的切片上切片生成长度为该容量的新切片. 切片的容量可以用内建函数`cap(a)`获得.

使用内建函数`make`可以创建一个新的初始化过的元素类型为`T`的切片, `make`接受切片类型、 指定长度的参数和一个可选的指定容量的参数. 使用`make`创建的切片总是分配一个新的隐藏的数组, 返回的切片值引用该数组. 即, 执行

``` go
make([]T, length, capacity)
```

分配一个数组, 在数组上切片产生这个切片, 所以下面的两个表达式是等价的:

``` go
make([]int, 50, 100)
new([100]int)[0:50]
```

与数组一样, 切片总是一维的, 但可以用于构造高维对象. 在数组的数组中, 构造的内部的数组的长度总是相同的; 然而切片的切片(或切片的数组), 内部切片的长度可以动态变化. 内部的切片必须分别初始化.

### [结构类型](https://golang.org/ref/spec#Struct_types)

结构是称为字段的命名元素的序列, 每个字段有一个名称和类型. 字段名称可以显式(`IdentifierList`)或隐式(`EmbeddedField`)指定. 在结构中, 非空字段名称必须是唯一的.

``` EBNF
StructType    = "struct" "{" { FieldDecl ";" } "}" .
FieldDecl     = (IdentifierList Type | EmbeddedField) [ Tag ] .
EmbeddedField = [ "*" ] TypeName .
Tag           = string_lit .
```

``` go
// An empty struct.
struct {}

// A struct with 6 fields.
struct {
	x, y int
	u float32
	_ float32  // padding
	A *[]int
	F func()
}
```

用类型但不带显式字段名称声明的字段称为内嵌字段(embedded field). 内嵌字段必须指定为类型名称`T`或非接口类型名称的指针`*T`(`T`本身不能是一个指针类型). 未限定的类型名称表现为字段名称.

``` go
// A struct with four embedded fields of types T1, *T2, P.T3 and *P.T4
struct {
	T1        // field name is T1
	*T2       // field name is T2
	P.T3      // field name is T3
	*P.T4     // field name is T4
	x, y int  // field names are x and y
}
```

因为字段名称必须在结构类型中唯一, 下面的声明是非法的:

``` go
struct {
	T     // conflicts with embedded field *T and *P.T
	*T    // conflicts with embedded field T and *P.T
	*P.T  // conflicts with embedded field T and *T
}
```

结构`x`中字段或内建字段的方法`f`称作 ==提升的(promoted)== , 如果`x.f`是表示字段或方法的合法选择器.

结构中提升的字段表现与常规字段相似, 除了它们不能用作结构的符合字面量的字段名称.

给定结构类型`S`和定义的类型`T`, 提升的方法在下面列出的结构的方法集合中:

- 如果`S`包含内嵌字段`T`, `S`和`*S`的方法集合同时包含接受者`T`的提升的方法. `*S`的方法集合同时包含接受者`*T`的提升的方法.
- 如果`S`包含内嵌字段`*T`, `S`和`*S`的方法集合同时包含接受者`T`或`*T`的提升的方法.

字段声明可以可选的后接一个字符串字面量标签(tag), 它成为在相应字段声明中字段的一个属性. 空的标签字符串等价于标签不存在. 标签可以通过反射接口暴露出来, 同时作为结构的类型标识的一部分, 在其它情况下被忽略.

``` go
struct {
	x, y float64 ""  // an empty tag string is like an absent tag
	name string  "any string is permitted as a tag"
	_    [4]byte "ceci n'est pas un champ de structure"
}

// A struct corresponding to a TimeStamp protocol buffer.
// The tag strings define the protocol buffer field numbers;
// they follow the convention outlined by the reflect package.
struct {
	microsec  uint64 `protobuf:"1"`
	serverIP6 uint64 `protobuf:"2"`
}
```

### [指针类型](https://golang.org/ref/spec#Pointer_types)

指针类型标识所有指向给定类型变量的指针的集合, 这个类型称为指针的基础类型(base type). 未初始化的指针的值是`nil`.

``` EBNF
PointerType = "*" BaseType .
BaseType    = Type .
```

``` go
*Point
*[4]int
```

### [函数类型](https://golang.org/ref/spec#Function_types)

函数类型标识所有有相同参数(parameter)和结果类型的函数的集合. 未初始化的函数类型的变量的值是`nil`.

``` EBNF
FunctionType   = "func" Signature .
Signature      = Parameters [ Result ] .
Result         = Parameters | Type .
Parameters     = "(" [ ParameterList [ "," ] ] ")" .
ParameterList  = ParameterDecl { "," ParameterDecl } .
ParameterDecl  = [ IdentifierList ] [ "..." ] Type .
```

在参数或结果列表中, 名称`IdentifierList`必须全部出现或全部不出现. 如果出现, 每个名称标识执行类型的一项(参数或结果), 签名中所有非空名称必须是唯一的. 如果不出现, 每个类型代表该类型的一项. 参数和结果列表总是在括号中, 除了只有一个未命名的结果时可以写为不带括号的类型.

函数签名中最后一个参数可以是带前缀`...`的类型. 有这种参数的函数称为是变长(variadic), 可以用零个或多个传递参数调用.

``` go
func()
func(x int) int
func(a, _ int, z float32) bool
func(a, b int, z float32) (bool)
func(prefix string, values ...int)
func(a, b int, z float64, opt ...interface{}) (success bool)
func(int, int, float64) (float64, *[]int)
func(n int) func(p *T)
```

### [接口类型](https://golang.org/ref/spec#Interface_types)

接口类型描述了称为它的接口的方法集合. 接口类型的变量可以存储任意方法集合是接口超集的类型的值. 这个类型称为 ==实现了这个接口==. 未初始化的接口类型的变量的值是`nil`.

``` EBNF
InterfaceType      = "interface" "{" { MethodSpec ";" } "}" .
MethodSpec         = MethodName Signature | InterfaceTypeName .
MethodName         = identifier .
InterfaceTypeName  = TypeName .
```

在接口类型中, 每个方法必须有唯一的非空名称.

``` go
// A simple File interface.
interface {
	Read([]byte) (int, error)
	Write([]byte) (int, error)
	Close() error
}
```

``` go
interface {
	String() string
	String() string  // illegal: String not unique
	_(x int)         // illegal: method must have non-blank name
}
```

可以有多个类型实现一个接口. 例如, 如果类型`S1`和`S2`有方法集合

``` go
func (p T) Read(p []byte) (n int, err error)   { return … }
func (p T) Write(p []byte) (n int, err error)  { return … }
func (p T) Close() error                       { return … }
```

这里`T`表示`S1`或`S2`, 则接口`File`同时被`S1`和`S2`实现, 不管`S1`和`S2`有或共享哪些其它方法.

一个类型实现了任意由其方法子集构成的接口, 因此一个类型可以实现多个不同的接口. 例如, 所有类型实现了空接口(empty interface):

``` go
interface{}
```

类似的, 考虑下面的接口描述, 它在定义接口`Locker`的类型声明中出现:

``` go
type Locker interface {
	Lock()
	Unlock()
}
```

如果`S1`和`S2`也实现了

``` go
func (p T) Lock() { … }
func (p T) Unlock() { … }
```

在它们在实现了接口`File`的同时也实现了接口`Locker`.

接口`T`可以在方法描述的位置使用(可能未限定的)接口类型名称`E`. 这称为`T`中内嵌接口`E`; 它将`E`中所有(导出的或非导出的)方法添加到接口`T`中.

``` go
type ReadWriter interface {
	Read(b Buffer) bool
	Write(b Buffer) bool
}

type File interface {
	ReadWriter  // same as adding the methods of ReadWriter
	Locker      // same as adding the methods of Locker
	Close()
}

type LockedFile interface {
	Locker
	File        // illegal: Lock, Unlock not unique
	Lock()      // illegal: Lock not unique
}
```

接口类型`T`不能递归的内嵌自身或任意内嵌了`T`的接口类型.

``` go
// illegal: Bad cannot embed itself
type Bad interface {
	Bad
}

// illegal: Bad1 cannot embed itself using Bad2
type Bad1 interface {
	Bad2
}
type Bad2 interface {
	Bad1
}
```

### [映射类型](https://golang.org/ref/spec#Map_types)

映射是无序的一组单个类型的元素, 这个类型称为元素类型, 用另一个称为键类型的唯一键集合索引. 未初始化的映射的值是`nil`.

``` EBNF
MapType     = "map" "[" KeyType "]" ElementType .
KeyType     = Type .
```

比较操作符`==`和`!=`必须在键类型操作符上完整定义; 因此键类型不能是函数、映射或切片. 如果键类型是接口类型, 这些比较操作符必须在动态键值上定义; 如果没有定义会产生运行时panic.

``` go
map[string]int
map[*T]struct{ x, y float64 }
map[string]interface{}
```

映射元素的数量称为它的长度. 对映射`m`, 可以使用内建函数`len`获得, 并在执行时修改. 元素可以在运行时使用赋值添加, 使用索引表达式检索出; 使用内建函数`delete`移除.

使用内建函数`make`创建新的空映射值, `make`接受映射类型和一个可选的容量值作为传递参数:

``` go
make(map[string]int)
make(map[string]int, 100)
```

这个容量值传递参数不会限制其大小: 除值为`nil`的映射外, 映射会随存储的项增加而增长. 除了不能添加元素外, 值为`nil`的映射等价于空映射.

### [通道类型](https://golang.org/ref/spec#Channel_types)

通道(channel)为并发执行的函数提供了通过发送和接收给定元素类型的值通信的机制. 未初始化的通道的值是`nil`.

``` EBNF
ChannelType = ( "chan" | "chan" "<-" | "<-" "chan" ) ElementType .
```

可选的操作符`<-`指定了通道的方向(direction): 发送(send)或接收(receive). 如果没有指定方向, 通道是双向的. 可以用赋值或显式转换将通道限制为只能发送或只能接收.

``` go
chan T          // can be used to send and receive values of type T
chan<- float64  // can only be used to send float64s
<-chan int      // can only be used to receive ints
```

操作符`<-`与最左边的`chan`关联:

``` go
chan<- chan int    // same as chan<- (chan int)
chan<- <-chan int  // same as chan<- (<-chan int)
<-chan <-chan int  // same as <-chan (<-chan int)
chan (<-chan int)
```

使用内建函数`make`创建新的初始化过的通道值, 它接受通道类型和一个可选的容量值作为传递参数:

``` go
make(chan int, 100)
```

容量, 即元素的数量, 设置了通道中缓冲区的大小. 如果容量为零或未出现, 通道是未缓冲的, 只有在一个发送者和接收者同时准备好时通信成功. 否则, 通道是缓冲的, 如果缓冲区不满(发送时)或不空(接收时), 通信会无阻塞的成功. 值为`nil`的通道从不准备好通信.

使用内建函数`close`关闭通道. 接收操作符的多值赋值形式, 报告接收到的值是否是在通道关闭之前发送的.

单个通道可以在没有进一步同步的情况下, 用在多个goroutine中的发送语句、接收操作和调用内建函数`cap`和`len`. 通道表现为先进先出的队列. 例如, 如果一个goroutine在通道上发送值, 第二个goroutine接收它们, 值按发送的顺序接收.

## 7 [类型和值的属性](https://golang.org/ref/spec#Properties_of_types_and_values)
### [类型标识](https://golang.org/ref/spec#Type_identity)

两个类型是同一的(identical)或不同的.

定义的类型总是与其它类型不同.
如果两个类型的底层类型字面量是结构上等价的, 则这两个类型是同一的; 即它们有相同的字面量结构, 相应的组件有同一的类型. 详细说明如下:

- 两个数组类型是同一的: 如果它们有同一的元素类型和相同的数组长度.
- 两个切片类型是统一的: 如果它们有相同的元素类型.
- 两个结构类型是同一的: 如果它们有相同的字段序列, 且对应的字段有相同的名称、同一的类型和同一的标签. 不同包中非导出的字段的名称总是不同的.
- 两个指针类型是同一的: 如果它们有同一的基础类型.
- 两个函数类型是同一的: 如果它们有相同数量的参数和结果值, 对应的参数和结果类型是同一的, 且它们或者都是变长的或者都不是. 参数和结果的名称不要求匹配.
- 两个接口类型是同一的: 如果它们有相同的方法集合, 这些对应的方法有相同的名称和同一的函数类型. 不同包中非导出的方法名称总是不同的. 方法的顺序是不相关的.
- 两个映射类型是同一个: 如果它们有同一的键和元素类型.
- 两个通道类型是同一的: 如果它们有同一的元素类型和相同的方向.

给定下面的声明:

``` go
type (
	A0 = []string
	A1 = A0
	A2 = struct{ a, b int }
	A3 = int
	A4 = func(A3, float64) *A0
	A5 = func(x int, _ float64) *[]string
)

type (
	B0 A0
	B1 []string
	B2 struct{ a, b int }
	B3 struct{ a, c int }
	B4 func(int, float64) *B0
	B5 func(x int, y float64) *A1
)

type	C0 = B0
```

这些类型是等价的:

``` go
A0, A1, []string
A2, struct{ a, b int }
A3, int
A4, func(int, float64) *[]string, A5

B0, C0
[]int, []int
struct{ a, b *T5 }, struct{ a, b *T5 }
func(x int, y float64) *[]string, func(int, float64) (result *[]string), A5
```

`B0`与`B1`是不同的, 因为它们是用不同的类型定义创建的新类型; `func(int, float64) *B0`与`func(x int, y float) *[]string`是不同的, 因为`B0`不同于`[]string`.

### [可赋值性](https://golang.org/ref/spec#Assignability)

如果下面条件中的一个适用时, 值`x`可以赋值给类型`T`的变量:

- `x`的类型与`T`是同一的.
- `x`的类型`V`与`T`有同一的底层类型, 且`V`和`T`中至少一个不是定义的类型.
- `T`是一个接口类型, `x`实现了`T`.
- `x`是双向通道值, `T`是通道类型, `x`的类型`V`与`T`有相同的元素类型, 且`V`和`T`中至少一个不是定义的类型.
- `x`是预声明标识符`nil`, `T`是指针、函数、切片、映射、通道或接口类型.
- `x`是类型`T`的值的无类型的常量表示.

### [可表示性](https://golang.org/ref/spec#Representability)

如果下面条件中的一个适用时, 常量`x`可以用类型`T`的值表示:

- `x`在由`T`确定的一组值中.
- `T`是浮点数类型, `x`可以在无上溢时舍入到`T`的精度. 舍入使用IEEE 754 round-to-even规则, 但将IEEE负零(nagative zero)简化为无符号的零. 注意常量值从不会产生IEEE负零、非数值或无穷大.
- `T`是负数类型, `x`的组件`real(x)`和`imag(x)`可以用`T`的组件类型(`float32`或`float64`)的值表示.


``` go
x                   T           x is representable by a value of T because

'a'                 byte        97 is in the set of byte values
97                  rune        rune is an alias for int32, and 97 is in the set of 32-bit integers
"foo"               string      "foo" is in the set of string values
1024                int16       1024 is in the set of 16-bit integers
42.0                byte        42 is in the set of unsigned 8-bit integers
1e10                uint64      10000000000 is in the set of unsigned 64-bit integers
2.718281828459045   float32     2.718281828459045 rounds to 2.7182817 which is in the set of float32 values
-1e-1000            float64     -1e-1000 rounds to IEEE -0.0 which is further simplified to 0.0
0i                  int         0 is an integer value
(42 + 0i)           float32     42.0 (with zero imaginary part) is in the set of float32 values
```

``` go
x                   T           x is not representable by a value of T because

0                   bool        0 is not in the set of boolean values
'a'                 string      'a' is a rune, it is not in the set of string values
1024                byte        1024 is not in the set of unsigned 8-bit integers
-1                  uint16      -1 is not in the set of unsigned 16-bit integers
1.1                 int         1.1 is not an integer value
42i                 float32     (0 + 42i) is not in the set of float32 values
1e1000              float64     1e1000 overflows to IEEE +Inf after rounding
```

## 8 [块](https://golang.org/ref/spec#Blocks)

块(block)是在匹配的大括号(`{}`)中声明和语句的序列(序列可能为空).

``` EBNF
Block = "{" StatementList "}" .
StatementList = { Statement ";" } .
```

在源代码中显式的块之外, 有隐式块:

1. 全局块(universe block)包含所有Go源码文本.
2. 每个包有一个包块, 包含包中所有Go源码文本.
3. 每个文件有文件块, 包含文件中所有Go源码文本.
4. 每个`if`、`for`和`switch`语句被视为有其隐式块.
5. `switch`或`select`语句中每个子句表现为一个隐式块.

块嵌套并影响作用域.

## 9 [声明和作用域](https://golang.org/ref/spec#Declarations_and_scope)

声明绑定一个非空标识符到一个常量、类型、变量、函数、标签(label)或包上.
程序中每个标识符必须声明.
在同一个块中不允许声明同一个标识符两次, 不允许在文件和包块中同时声明标识符.

空标识符(blank idenfitier)在声明中就像其它标识符一样使用, 但它不引入绑定, 因此是未被声明的.
在包块中, 标识符`init`只能用于`init`函数声明, 与空标识符一样它不引入新绑定.

``` EBNF
Declaration   = ConstDecl | TypeDecl | VarDecl .
TopLevelDecl  = Declaration | FunctionDecl | MethodDecl .
```

声明的标识符的作用域(scope)是源码文本的一段, 其中这个标识符表示指定的常量、类型、变量、函数、标签或包.

Go使用块的词法作用域:

1. 预声明标识符的作用域是全局块.
2. 表示在顶层(任意函数外)声明的常量、类型、变量、函数(不是方法)的标识符的作用域是包块.
3. 导入的包的包名称的作用域是包含这个导入声明的文件的文件块.
4. 表示方法接收者、函数参数或结果变量的标识符的作用域是函数体.
5. 在函数中声明的常量或变量标识符的作用域, 从`ConstSpec`或`VarSpec`结尾开始, 以最内层的包裹块的结尾结束.
6. 在函数中声明的类型标识符的作用域, 从`TypeSpec`中标识符开始, 以最内层的包裹块的结尾结束.

在一个块中声明的标识符, 可以在这个块中的块重新声明. 如果在内层声明的标识符的作用域中, 这个标识符表示内层声明的实体.

包子句不是一个声明; 包的名称不在任何作用域中出现. 它的用途是指出属于同一个包的文件, 和指定用于导入声明的默认包名称.

### [标签作用域](https://golang.org/ref/spec#Label_scopes)

标签(label)是用带标签的语句声明的, 用在`break`、`continue`和`goto`语句中. 定义从未被使用的标签是非法的.
与其它标识符不同, 标签不是块作用域的, 不与不是标签的标识符冲突.
标签的作用域是它的声明所在的函数体中, 不包括任意内嵌函数的体.

### [空标识符](https://golang.org/ref/spec#Blank_identifier)

空标识符(balnk identifier)用下划线字符`_`表示. 作为替代常规(非空)标识符的匿名占位符, 在声明中和赋值中有特殊的含义: 作为一个操作数(operand).

### [预声明的标识符](https://golang.org/ref/spec#Predeclared_identifiers)

下面的标识符是隐式声明在全局块中的:

```
Types:
	bool byte complex64 complex128 error float32 float64
	int int8 int16 int32 int64 rune string
	uint uint8 uint16 uint32 uint64 uintptr

Constants:
	true false iota

Zero value:
	nil

Functions:
	append cap close complex copy delete imag len
	make new panic print println real recover
```

### [导出的标识符](https://golang.org/ref/spec#Exported_identifiers)

可以导出一个标识符, 以允许在另一个包中访问它. 标识符是导出的, 如果:

1. 标识符名称的第一个字符是Unicode大写字母(Unicode类`Lu`); 且
2. 标识符是在包块中声明的, 或者他是一个字段名称或方法名称.

所有其它标识符是不导出的.

### [标识符的唯一性](https://golang.org/ref/spec#Uniqueness_of_identifiers)

给定一组标识符, 如果一个标识符与这个组中其它标识符不同, 称这个标识符是唯一的.
如果两个标识符的拼写不同, 或者它们出现在不同的包中且是非导出的, 则这两个标识符不同. 否则, 它们是相同.

### [常量声明](https://golang.org/ref/spec#Constant_declarations)

常量声明将一组标识符(常量的名称)到一组常量表达式的值上. 标识符的数量必须与表达式的数量相同, 左端的第n个标识符绑定到右端的第n个表达式的值上.

``` EBNF
ConstDecl      = "const" ( ConstSpec | "(" { ConstSpec ";" } ")" ) .
ConstSpec      = IdentifierList [ [ Type ] "=" ExpressionList ] .

IdentifierList = identifier { "," identifier } .
ExpressionList = Expression { "," Expression } .
```

如果类型出现, 所有的常量取指定的类型, 表达式必须可以赋值给这个类型.
如果类型省略了, 常量取相应的表达式的类型.
如果表达式的值是无类型的常量, 声明的常量保持无类型的, 常量标识符表示这个常量值. 例如, 如果表达式是一个浮点数字面量, 常量标识符表示一个浮点数常量, 甚至在这个字面量的小数部分为零时.

``` go
const Pi float64 = 3.14159265358979323846
const zero = 0.0         // untyped floating-point constant
const (
	size int64 = 1024
	eof        = -1  // untyped integer constant
)
const a, b, c = 3, 4, "foo"  // a = 3, b = 4, c = "foo", untyped integer and string constants
const u, v float32 = 0, 3    // u = 0.0, v = 3.0
```

在括号`()`中的`const`声明列表中, 表达式列表除了第一个`ConstSpec`外可以从任意位置忽略.
这个空列表等价于第一个前继非空表达式列表和它的类型(如果有的话)的文本替换.
因此, 省略表达式列表等价于重复前面的列表.
标识符的数量必须等于前面列表中表达式的数量.
与`iota`常量生成器机制一起使用, 支持轻量级的声明序列值:

``` go
const (
	Sunday = iota
	Monday
	Tuesday
	Wednesday
	Thursday
	Friday
	Partyday
	numberOfDays  // this constant is not exported
)
```

### [`iota`](https://golang.org/ref/spec#Iota)

在常量声明中, 预声明的标识符`iota`表示后继的无类型整数常量.
它的值是常量声明中相应的`ConstSpec`的索引, 从0开始. 可以用于构造一组相关的常量:

``` go
const (
	c0 = iota  // c0 == 0
	c1 = iota  // c1 == 1
	c2 = iota  // c2 == 2
)

const (
	a = 1 << iota  // a == 1  (iota == 0)
	b = 1 << iota  // b == 2  (iota == 1)
	c = 3          // c == 3  (iota == 2, unused)
	d = 1 << iota  // d == 8  (iota == 3)
)

const (
	u         = iota * 42  // u == 0     (untyped integer constant)
	v float64 = iota * 42  // v == 42.0  (float64 constant)
	w         = iota * 42  // w == 84    (untyped integer constant)
)

const x = iota  // x == 0
const y = iota  // y == 0
```

根据定义, 在同一个`ConstSpec`中多次使用`iota`有相同的值:

``` go
const (
	bit0, mask0 = 1 << iota, 1<<iota - 1  // bit0 == 1, mask0 == 0  (iota == 0)
	bit1, mask1                           // bit1 == 2, mask1 == 1  (iota == 1)
	_, _                                  //                        (iota == 2, unused)
	bit3, mask3                           // bit3 == 8, mask3 == 7  (iota == 3)
)
```

这个示例利用了最后一个非空表达式列表的隐式重复.

### [类型声明](https://golang.org/ref/spec#Type_declarations)

类型声明绑定类型名称标识符到类型上. 类型声明有两种形式: 别名声明和类型定义.

``` EBNF
TypeDecl = "type" ( TypeSpec | "(" { TypeSpec ";" } ")" ) .
TypeSpec = AliasDecl | TypeDef .
```

#### 别名声明

别名声明绑定一个标识符到指定类型上.

``` EBNF
AliasDecl = identifier "=" Type .
```

在标识符的作用域内, 它作为类型的别名使用.

``` go
type (
	nodeList = []*Node  // nodeList 与 []*Node 是同一的类型
	Polar    = polar    // Polar 与 polar 表示同一的类型
)
```

#### 类型定义

类型定义创建与指定类型有相同底层类型和操作的不同的新类型, 并绑定一个标识符到这个新类型上.

``` EBNF
TypeDef = identifier Type .
```

这个新类型称为 ==定义的类型(defined type)==. 它与其它类型都不同, 包括创建时依赖的类型.

``` go
type (
	Point struct{ x, y float64 }  // Point 与 struct{ x, y float64 } 是不同的类型
	polar Point                   // polar 与 Point 表示不同的类型
)

type TreeNode struct {
	left, right *TreeNode
	value *Comparable
}

type Block interface {
	BlockSize() int
	Encrypt(src, dst []byte)
	Decrypt(src, dst []byte)
}
```

定义的类型有与它关联的方法. 它不会从创建时给定的类型中继承任何方法, 但接口类型或符合类型元素的方法集保持不变:

``` go
// A Mutex is a data type with two methods, Lock and Unlock.
type Mutex struct         { /* Mutex fields */ }
func (m *Mutex) Lock()    { /* Lock implementation */ }
func (m *Mutex) Unlock()  { /* Unlock implementation */ }

// NewMutex has the same composition as Mutex but its method set is empty.
type NewMutex Mutex

// The method set of PtrMutex's underlying type *Mutex remains unchanged,
// but the method set of PtrMutex is empty.
type PtrMutex *Mutex

// The method set of *PrintableMutex contains the methods
// Lock and Unlock bound to its embedded field Mutex.
type PrintableMutex struct {
	Mutex
}

// MyBlock is an interface type that has the same method set as Block.
type MyBlock Block
```

类型定义可用于定义不同的布尔、数值或字符串类型, 并与其关联方法:

``` go
type TimeZone int

const (
	EST TimeZone = -(5 + iota)
	CST
	MST
	PST
)

func (tz TimeZone) String() string {
	return fmt.Sprintf("GMT%+dh", tz)
}
```

### [变量声明](https://golang.org/ref/spec#Variable_declarations)

变量声明创建一个或多个变量, 将相应的标识符绑定到这些变量上, 同时为这些变量指定一个类型和初始值.

``` EBNF
VarDecl     = "var" ( VarSpec | "(" { VarSpec ";" } ")" ) .
VarSpec     = IdentifierList ( Type [ "=" ExpressionList ] | "=" ExpressionList ) .
```

``` go
var i int
var U, V, W float64
var k = 0
var x, y float32 = -1, -2
var (
	i       int
	u, v, s = 2.0, 3.0, "bar"
)
var re, im = complexSqrt(-1)
var _, found = entries[name]  // map lookup; only interested in "found"
```

如果指定了一组表达式, 依据赋值规则, 用表达式初始化变量. 否则, 每个变量被初始化为其零值.

如果类型出现, 每个变量被指定这个类型. 否则, 每个变量被指定赋值中相应的初始值的类型. 如果这个值是一个无类型常量, 首先隐式的转换为它的默认类型; 如果这个值是一个无类型的布尔值, 首选隐式的转换为`bool`类型. 预声明的值`nil`不能用于初始化没有显式类型的变量.

``` go
var d = math.Sin(0.5)  // d is float64
var i = 42             // i is int
var t, ok = x.(T)      // t is T, ok is bool
var n = nil            // illegal
```

实现约束: 在函数体中声明一个从未被使用的变量, 编译器可以将这种情况视为非法的.

### [短变量声明](https://golang.org/ref/spec#Short_variable_declarations)

短变量声明的语法:

``` EBNF
ShortVarDecl = IdentifierList ":=" ExpressionList .
```

它是有初始化表达式但类型的无常规变量声明的简写:

```
"var" IdentifierList = ExpressionList .
```

``` go
i, j := 0, 10
f := func() int { return 7 }
ch := make(chan int)
r, w, _ := os.Pipe()  // os.Pipe() returns a connected pair of Files and an error, if any
_, y, _ := coord(p)   // coord() returns three values; only interested in y coordinate
```

与常规变量声明不同, 短变量声明可以重新声明在同一块中之前(或者块是函数体时的参数列表)声明的有相同类型的变量, 且必须至少有一个新的非空变量.
因此, 重声明只可以在多变量短声明中出现.
重声明不引入新的变量; 它只是给原变量赋予新值.

``` go
field1, offset := nextField(str, 0)
field2, offset := nextField(str, offset)  // redeclares offset
a, a := 1, 2                              // illegal: double declaration of a or no new variable if a was declared elsewhere
```

短变量声明可以只在函数中出现. 在诸如`if`、`for`、`switch`语句的初始化部分的上下文中, 它们可用于声明本地临时变量.

### [函数声明](https://golang.org/ref/spec#Function_declarations)

函数声明绑定函数名称标识符到一个函数.

``` EBNF
FunctionDecl = "func" FunctionName Signature [ FunctionBody ] .
FunctionName = identifier .
FunctionBody = Block .
```

如果函数的签名中声明了结果参数, 函数体中语句列表必须以终止语句(terminating statement)结束.

``` go
func IndexRune(s string, r rune) int {
	for i, c := range s {
		if c == r {
			return i
		}
	}
	// invalid: missing return statement
}
```

函数声明可以省略体. 这种声明提供了由Go外部实现的函数的签名, 例如汇编程序.

``` go
func min(x int, y int) int {
	if x < y {
		return x
	}
	return y
}

func flushICache(begin, end uintptr)  // implemented externally
```

### [方法声明](https://golang.org/ref/spec#Method_declarations)

方法是有接收者的函数. 方法声明绑定方法名称标识符到一个方法, 并将方法与接收者的基础类型关联.

``` EBNF
MethodDecl = "func" Receiver MethodName Signature [ FunctionBody ] .
Receiver   = Parameters .
```

接收者是用一个方法名称之前的额外参数段(parameter section)描述的. 这个参数段不许声明单个非变长的参数: 接收者.
这个参数的类型必须是已定义的类型`T`或者指向已定义类型`T`的指针. `T`称为接收者的基础类型(base type).
接收者的基础类型不能是一个指针或接口类型, 它必须定义在方法所在的包中.
称方法被限制(bound)在它的接收者类型上, 方法的名称只在类型`T`或`*T`的选择器中可见.

非空接收者标识符必须在方法的签名中唯一. 如果接收者的值没有在方法体中引用, 则它的标识符可以在生产能中省略. 这适用于函数和方法的参数.

对基础类型, 被限制在它上的方法的非空名称必须是唯一的. 如果基础类型是一个结构类型, 非空的方法名称必须与字段名称不同.

给定顶一个类型`Point`, 这些声明:

``` go
func (p *Point) Length() float64 {
	return math.Sqrt(p.x * p.x + p.y * p.y)
}

func (p *Point) Scale(factor float64) {
	p.x *= factor
	p.y *= factor
}
```

将方法`Length`和`Scale`与接收者类型`*Point`绑定到基础类型`Point`上.

方法的类型是以接收者作为第一个传递参数的函数的类型. 例如, 方法`Scale`有类型:

``` go
func(p *Point, factor float64)
```

然而, 这样声明的函数不是方法.


## 10 [表达式](https://golang.org/ref/spec#Expressions)

表达式(expression)描述了通过在操作数上应用操作符和函数的值的计算.

### [操作数](https://golang.org/ref/spec#Operands)

操作数(operand)表示表达式中基本值. 操作数可以是一个字面量、一个表示常量的(可能未限定的)非空表达式、变量、函数、括号表达式.

空标识符只可以在赋值的左端作为操作符出现.

``` EBNF
Operand     = Literal | OperandName | "(" Expression ")" .
Literal     = BasicLit | CompositeLit | FunctionLit .
BasicLit    = int_lit | float_lit | imaginary_lit | rune_lit | string_lit .
OperandName = identifier | QualifiedIdent.
```

### [限定的标识符](https://golang.org/ref/spec#Qualified_identifiers)

限定的标识符(qualified identifier)是用包名称前缀限定的标识符. 包名称和标识符必须不能为空.

``` EBNF
QualifiedIdent = PackageName "." identifier .
```

限定的标识符访问另一个包中的标识符, 后者必须是导出的. 标识符必须在包的包块中导出和声明.

``` go
math.Sin	// denotes the Sin function in package math
```

### [复合字面量](https://golang.org/ref/spec#Composite_literals)

复合字面量构造结构、数组、切片和映射的值, 每次被求值时创建一个新的值.
复合字面量由字面量的类型, 后接大括号限制的元素列表构成. 每个元素可选的前继相应的键.

``` EBNF
CompositeLit  = LiteralType LiteralValue .
LiteralType   = StructType | ArrayType | "[" "..." "]" ElementType |
                SliceType | MapType | TypeName .
LiteralValue  = "{" [ ElementList [ "," ] ] "}" .
ElementList   = KeyedElement { "," KeyedElement } .
KeyedElement  = [ Key ":" ] Element .
Key           = FieldName | Expression | LiteralValue .
FieldName     = identifier .
Element       = Expression | LiteralValue .
```

`LiteralType`的底层类型必须是结构、数组、切片或映射类型(除了指定了`TypeName`外, 语法确保了这个约束).
元素和键的类型必须可以赋值给字面量类型中的字段、元素、键类型; 没有额外的转换.
键被解释为结构字面量的字段名称、数组和切片字面量的索引、映射字面量的键.
对映射字面量, 所有元素都必须有键. 指定有相同字段名称或常量键值的多个元素是错误的.
对非常量的映射的键, 见求值顺序一节.

对结构字面量, 有下面的规则:

- 键必须是结构类型中声明的字段名称.
- 不包含任何键的元素列表, 必须按字段被声明的顺序给每个结构字段一个元素.
- 如果任意元素有键, 则所有元素必须都有键.
- 如果包含键的元素列表, 不需要给每个结构字段一个元素. 被省略的字段获得该字段的零值.
- 字面量可以省略元素列表; 这个字面量求值为它的类型的零值.
- 为另一个包中的结构中非导出字段指定一个元素, 是错误的.

给定下面的声明:

``` go
type Point3D struct { x, y, z float64 }
type Line struct { p, q Point3D }
```

可以写:

``` go
origin := Point3D{}                            // zero value for Point3D
line := Line{origin, Point3D{y: -4, z: 12.3}}  // zero value for line.q.x
```

对数组和切片字面量, 有下面的规则:

- 每个元素有一个相关的整数索引, 比较它在数组中的位置.
- 带键的元素将键用作它的索引. 键必须是一个非负常量, 可以用`int`类型标识; 如果键是有类型的, 必须是整数类型.
- 不带键的元素使用前面元素的索引加1作为索引. 如果第一个元素没有键, 它的索引是0.

取复合字面量的地址生成指向用字面量的值初始化的唯一变量的指针.

``` go
var pointer *Point3D = &Point3D{y: 1000}
```

注意切片或映射类型的零值与相同类型初始化过的空值不同. 因此, 取空切片或映射字面量的地址与使用`new`分配一个新切片或映射值的效果不同.

``` go
p1 := &[]int{}    // p1 points to an initialized, empty slice with value []int{} and length 0
p2 := new([]int)  // p2 points to an uninitialized slice with value nil and length 0
```

数组字面量的长度是在字面量类型中指定的长度. 如果在字面量中提供的元素数量比长度小, 缺失的元素被设置为数组元素类型的零值.
提供索引值在数组范围外的元素是错误的. 标记`...`指定了数组的长度等于最大元素的索引加1.

``` go
buffer := [10]string{}             // len(buffer) == 10
intSet := [6]int{1, 2, 3, 5}       // len(intSet) == 6
days := [...]string{"Sat", "Sun"}  // len(days) == 2
```

切片字面量描述了整个底层数组字面量. 因此, 切片字面量的长度和容量是最大元素的索引加1. 有下面形式的切片字面量:

``` go
[]T{x1, x2, … xn}
```

是在数组上应用切片操作的简写:

``` go
tmp := [n]T{x1, x2, … xn}
tmp[0 : n]
```

在数组、切片或映射类型`T`的复合字面量中, 本身是复合字面量元素或映射的键, 如果其字面量类型与`T`的元素或键类型同一的, 可以省略相应的字面量类型.
类似的, 是复合字面量地址的元素或键, 当元素或键的类型是`*T`时, 可以省略`&T`.

``` go
[...]Point{{1.5, -3.5}, {0, 0}}     // same as [...]Point{Point{1.5, -3.5}, Point{0, 0}}
[][]int{{1, 2, 3}, {4, 5}}          // same as [][]int{[]int{1, 2, 3}, []int{4, 5}}
[][]Point{{{0, 1}, {1, 2}}}         // same as [][]Point{[]Point{Point{0, 1}, Point{1, 2}}}
map[string]Point{"orig": {0, 0}}    // same as map[string]Point{"orig": Point{0, 0}}
map[Point]string{{0, 0}: "orig"}    // same as map[Point]string{Point{0, 0}: "orig"}

type PPoint *Point
[2]*Point{{1.5, -3.5}, {}}          // same as [2]*Point{&Point{1.5, -3.5}, &Point{}}
[2]PPoint{{1.5, -3.5}, {}}          // same as [2]PPoint{PPoint(&Point{1.5, -3.5}), PPoint(&Point{})}
```

当使用`LiterialType`的`TypeName`形式的复合字面量, 作为操作数出现在关键字与`if`、`for`或`switch`语句的块的开括号`{`之间, 且复合字面量没有包裹在`()`、`[]`、`{}`中时, 会出现解析歧义.
极少情况下, 字面量的开括号`{`被错误的解析为引入语句块. 为解决这个歧义问题, 复合字面量必须出现在括号`()`中.

``` go
if x == (T{a,b,c}[i]) { … }
if (x == T{a,b,c}[i]) { … }
```

有效的数组、切片和映射字面量的示例:

``` go
// list of prime numbers
primes := []int{2, 3, 5, 7, 9, 2147483647}

// vowels[ch] is true if ch is a vowel
vowels := [128]bool{'a': true, 'e': true, 'i': true, 'o': true, 'u': true, 'y': true}

// the array [10]float32{-1, 0, 0, 0, -0.1, -0.1, 0, 0, 0, -1}
filter := [10]float32{-1, 4: -0.1, -0.1, 9: -1}

// frequencies in Hz for equal-tempered scale (A4 = 440Hz)
noteFrequency := map[string]float32{
	"C0": 16.35, "D0": 18.35, "E0": 20.60, "F0": 21.83,
	"G0": 24.50, "A0": 27.50, "B0": 30.87,
}
```

### [函数字面量](https://golang.org/ref/spec#Function_literals)

函数字面量表示一个匿名函数.

``` EBNF
FunctionLit = "func" Signature FunctionBody .
```

``` go
func(a, b int, z float64) bool { return a*b < int(z) }
```

函数字面量可以赋值给一个变量或被直接调用.

``` go
f := func(x, y int) int { return x + y }
func(ch chan int) { ch <- ACK }(replyChan)
```

函数字面量是闭包(closure): 可以引用外围函数中定义的变量. 这些变量在外围函数和函数字面量之间共享, 只要它们可被访问就一直存活.

### [主表达式](https://golang.org/ref/spec#Primary_expressions)

主表达式(primary expression)是一元和二元表达式的操作数.

``` EBNF
PrimaryExpr =
	Operand |
	Conversion |
	MethodExpr |
	PrimaryExpr Selector |
	PrimaryExpr Index |
	PrimaryExpr Slice |
	PrimaryExpr TypeAssertion |
	PrimaryExpr Arguments .

Selector       = "." identifier .
Index          = "[" Expression "]" .
Slice          = "[" [ Expression ] ":" [ Expression ] "]" |
                 "[" [ Expression ] ":" Expression ":" Expression "]" .
TypeAssertion  = "." "(" Type ")" .
Arguments      = "(" [ ( ExpressionList | Type [ "," ExpressionList ] ) [ "..." ] [ "," ] ] ")" .
```

``` go
x
2
(s + ".txt")
f(3.1415, true)
Point{1, 2}
m["foo"]
s[i : j + 1]
obj.color
f.p[i].x()
```

### [选择器](https://golang.org/ref/spec#Selectors)

对不是包名称的主表达式`x`, 选择器表达式(selector expression)

``` go
x.f
```

表示值`x`(或`*x`)的字段或方法. 标识符`f`称为(字段或方法的)选择器(selector); 不能是空标识符.
选择器表达式的类型是`f`的类型. 如果`x`是包名称, 见限定的标识符一节.

选择器`f`可以表示类型`T`的字段或方法`f`, 或者引用`T`的嵌套的内嵌字段的字段或方法`f`.
遍历到达`f`的内嵌字段的数量称为`f`在`T`中的深度(depth). 在`T`中声明的字段或方法`f`的深度为0.
在`T`的内嵌字段`A`中声明的字段或方法`f`的深度等于`f`在`A`中的深度加1.

下面的规则应用于选择器:

1. 对类型`T`或`*T`(`T`不是指针或接口类型)的值`x`, `x.f`表示在`T`中有`f`的最浅深度的字段或方法. 如果在最浅深度有多个`f`, 选择器表达式是非法的.
2. 对接口类型`I`的值`x`, `x.f`表示`x`的动态值的名称为`f`的实际方法. 如果`I`的方法集中没有名称为`f`的方法, 选择器表达式是非法的.
3. 作为例外, 如果`x`的类型是定义的指针类型, `(*x).f`是有效的表示字段(不是方法)的选择器表达式, `x.f`是`(*x).f`的简写.
4. 在所有其它情况中, `x.f`是非法的.
5. 如果`x`是指针类型, 有值`nil`, `x.f`表示结构字段, 给`x.f`赋值或求值产生运行时panic.
6. 如果`x`是接口类型, 有值`nil`, 调用或求值方法`x.f`会产生运行时panic.

例如, 给定如下声明:

``` go
type T0 struct {
	x int
}

func (*T0) M0()

type T1 struct {
	y int
}

func (T1) M1()

type T2 struct {
	z int
	T1
	*T0
}

func (*T2) M2()

type Q *T2

var t T2     // with t.T0 != nil
var p *T2    // with p != nil and (*p).T0 != nil
var q Q = p
```

可以有:

``` go
t.z          // t.z
t.y          // t.T1.y
t.x          // (*t.T0).x

p.z          // (*p).z
p.y          // (*p).T1.y
p.x          // (*(*p).T0).x

q.x          // (*(*q).T0).x        (*q).x is a valid field selector

p.M0()       // ((*p).T0).M0()      M0 expects *T0 receiver
p.M1()       // ((*p).T1).M1()      M1 expects T1 receiver
p.M2()       // p.M2()              M2 expects *T2 receiver
t.M2()       // (&t).M2()           M2 expects *T2 receiver, see section on Calls
```

但这是无效的:

``` go
q.M0()       // (*q).M0 is valid but not a field selector
```

### [方法表达式](https://golang.org/ref/spec#Method_expressions)

如果`M`在类型`T`的方法集中, `T.M`是一个函数, 第一个传递参数是方法的接收者, 其它传递参数与`M`的参数相同.

``` EBNF
MethodExpr    = ReceiverType "." MethodName .
ReceiverType  = Type .
```

考虑结构类型`T`有两个方法, `Mv`的接收者是类型`T`, `Mp`的接收者是`*T`.

``` go
type T struct {
	a int
}
func (tv  T) Mv(a int) int         { return 0 }  // value receiver
func (tp *T) Mp(f float32) float32 { return 1 }  // pointer receiver

var t T
```

表达式

``` go
T.Mv
```

生成除第一传递参数是显式的接收者的与`Mv`等价的函数; 它有签名

``` go
func(tv T, a int) int
```

这个函数可以用显式的接收者调用, 所以下面的五个调用是等价的:

``` go
t.Mv(7)
T.Mv(t, 7)
(T).Mv(t, 7)
f1 := T.Mv; f1(t, 7)
f2 := (T).Mv; f2(t, 7)
```

类似的, 表达式

``` go
(*T).Mp
```

生成一个如下签名的函数值(function value):

``` go
func(tp *T, f float32) float32
```

对有值接收者的方法, 可以用显式的指针接收者导出一个函数, 所以

``` go
(*T).Mv
```

生成一个如下签名的函数值:

``` go
func(tv *T, a int) int
```

这种函数间接的通过接收者创建作为底层方法的接收者传递的值; 方法不会覆盖其地址在函数调用中传递的值.

最后一种情况, 指针接收者(pointer-receiver)方法的值接收者(value-receiver)函数, 是非法的, 因为指针接收者方法不在值类型的方法集中.

从方法中获得的函数值, 使用函数调用语法调用; 接收者作为第一个传递参数. 即, 给定`f := T.Mv`, `f(t, 7)`是`f`的调用, `t.f(7)`不是.
构造绑定接收者的函数, 使用函数字面量或方法值.

从接口类型的方法中获得函数值是合法的. 返回的函数接受这个接口类型的显式接收者传递参数.

### [方法值](https://golang.org/ref/spec#Method_values)

如果表达式有静态类型`T`, `M`在`T`的方法集中, `x.M`称为 ==方法值(method value)==.
方法值`x.M`是一个函数值, 可以用于`x.M`的方法调用时相同的传递参数调用.
表达式`x`在方法值的求值过程中被求值和保存; 被保存的副本在任意后续调用中用作接收者.

类型`T`可以是接口或非接口类型.

在上面的方法表达式的讨论中, 考虑结构类型`T`有两个方法, `Mv`的接收者是类型`T`, `Mp`的接收者是类型`*T`.

``` go
type T struct {
	a int
}
func (tv  T) Mv(a int) int         { return 0 }  // value receiver
func (tp *T) Mp(f float32) float32 { return 1 }  // pointer receiver

var t T
var pt *T
func makeT() T
```

表达式

``` go
t.Mv
```

生成如下类型的函数值

``` go
func(int) int
```

这两个调用是等价的:

``` go
t.Mv(7)
f := t.Mv; f(7)
```

类似的, 表达式

``` go
pt.Mp
```

生成如下类型的函数值


``` go
func(float32) float32
```

与选择器一致, 使用指针引用值接收者的非接口方法时, 自动解引用(dereference)这个指针: `pt.Mv`等价于`(*pt).Mv`.

与方法调用一致, 使用可寻址的值引用指针接收者的非接口方法时, 自动取这个值得地址: `t.Mp`等价于`(&t).Mp`.

``` go
f := t.Mv; f(7)   // like t.Mv(7)
f := pt.Mp; f(7)  // like pt.Mp(7)
f := pt.Mv; f(7)  // like (*pt).Mv(7)
f := t.Mp; f(7)   // like (&t).Mp(7)
f := makeT().Mp   // invalid: result of makeT() is not addressable
```

尽管上面的示例使用非接口类型, 从接口类型值上创建方法值也是合法的.

``` go
var i interface { M(int) } = myVal
f := i.M; f(7)  // like i.M(7)
```

### [索引表达式](https://golang.org/ref/spec#Index_expressions)

下面形式的主表达式

``` go
a[x]
```

表示数组的元素、指向由`x`索引的数组、切片或映射的指针. 值`x`分别被称为索引(index)或映射键(map key). 有下述规则:

如果`a`不是映射:

- 索引`x`必须是整数类型或无类型常量.
- 常量索引必须是非负的, 且可用`int`类型的值表示.
- 无类型的常量索引被指定类型`int`.
- 如果`0 <= x < len(a)`, 索引`x`在范围内, 否则在范围外.

对数组类型`A`的`a`:

- 常量索引必须在范围内.
- 如果运行时`x`在范围外, 发生运行时panic.
- `a[x]`是在索引`x`处的数组元素, `a[x]`的类型是`A`的元素类型.

对数组类型指针的`a`:

- `a[x]`是`(*a)[x]`的简写.

对切片类型`S`的`a`:

- 如果运行时`x`在范围外, 发生运行时panic.
- `a[x]`是在索引`x`处的切片元素, `a[x]`的类型是`A`的元素类型.

对字符类型的`a`:

- 如果字符串`a`也是常量时, 常量索引必须在范围内.
- 如果运行时`x`在范围外, 发生运行时panic.
- `a[x]`是在索引`x`处的非常量字节, `a[x]`的类型是`byte`.
- `a[x]`不可以被赋值.

对映射类型`M`的`a`:

- `x`的类型必须可以赋值给`M`的键类型.
- 如果映射包含键`x`的项, `a[x]`是有键`x`的映射元素, `a[x]`的类型是`M`的元素类型.
- 如果映射是`nil`或者不包含这样的项, `a[x]`是`M`的元素类型的零值.

否则, `a[x]`是非法的.

在用于赋值或初始化形式的类型`map[K]V`的映射`a`上的索引表达式

``` go
v, ok = a[x]
v, ok := a[x]
var v, ok = a[x]
```

产生一个额外的无类型布尔值. 如果键`x`在映射中出现, `ok`的值是`ture`, 否则是`false`.

给`nil`映射的元素赋值, 产生运行时panic.

### [切片表达式](https://golang.org/ref/spec#Slice_expressions)

切片表达式从字符串、数组、数组的指针、切片中构造子字符串或切片.
有两个变种: 指定上下界的简单形式, 同时指定容量的完整形式.

#### 简单切片表达式

对字符串、数组、数组的指针、切片`a`, 主表达式

``` go
a[low : high]
```

构造出一个子字符串或切片. 索引`low`和`high`在操作数`a`中选择在结果中出现的元素.
结果的索引从0开始, 长度等于`high-low`. 在数组`a`上切片

``` go
a := [5]int{1, 2, 3, 4, 5}
s := a[1:4]
```

切片`s`有类型`[]int`、长度3、容量4和元素

``` go
s[0] == 2
s[1] == 3
s[2] == 4
```

为了方便, 可以忽略任意索引. 缺失的`low`索引默认为0; 缺失的`high`索引默认为被切片的操作数的长度.

``` go
a[2:]  // same as a[2 : len(a)]
a[:3]  // same as a[0 : 3]
a[:]   // same as a[0 : len(a)]
```

如果`a`是指向数组的指针, `a[low:high]`是`(*a)[low:high]`的简写.

对数组或字符串, 如果`0 <= low <= high <= len(a)`, 索引是在范围内的, 否则索引是在范围外的.
对切片, 索引上边界是切片容量`cap(a)`而不是其长度.
常量索引必须是非负的, 可以用`int`类型的值表示; 对数组或常量字符串, 常量索引必须在范围内.
如果两个索引都是常量, 它们必须满足`low <= high`. 如果运行时索引在范围外, 发生运行时panic.

除了无类型的字符串, 如果被切片的操作数是字符串或切片, 切片操作的结果是与操作数同类型的非常量值.
对无类型的字符串操作数, 结果是`string`类型的非常量值. 如果被切片的操作数是数组, 它必须是可寻址的, 切片操作的结果时与数组同元素类型的切片.

如果有效的切片表达式中被切片的操作数是`nil`切片, 结果是`nil`切片. 否则, 如果结果是切片, 它与操作数共享底层的数组.

``` go
var a [10]int
s1 := a[3:7]   // underlying array of s1 is array a; &s1[2] == &a[5]
s2 := s1[1:4]  // underlying array of s2 is underlying array of s1 which is array a; &s2[1] == &a[5]
s2[1] = 42     // s2[1] == s1[2] == a[5] == 42; they all refer to the same underlying array element
```

#### 完整切片表达式

对数组、数组指针或切片`a`(不是字符串), 主表达式

``` go
a[low : high : max]
```

构造出于简单切片表达式`a[low:high]`有相同类型、相同长度和元素的切片. 此外, 它控制结果切片的容量, 将其设置为`max-low`.
只可以省略第一个索引; 它默认为0. 在数组`a`上切片

``` go
a := [5]int{1, 2, 3, 4, 5}
t := a[1:3:5]
```

切片`t`有类型`[]int`、长度2、容量4和元素

``` go
t[0] == 2
t[1] == 3
```

与简单切片表达式一致, 如果`a`是数组指针, `a[low:high:max]`是`(*a)[low:high:max]`的简写. 如果被切片的操作数是数组, 它必须是可寻址的.

如果`0 <= low <= high <= max <= cap(a)`, 索引是在范围内的, 否则索引是在范围外的.
常量索引必须是非负的, 可以用`int`类型的值表示; 对数组, 常量索引必须在范围内.
如果多个索引都是常量, 它们必须满足相对的在范围中. 如果运行时索引在范围外, 发生运行时panic.

### [类型断言](https://golang.org/ref/spec#Type_assertions)

对接口类型的表达式`x`和类型`T`, 主表达式

``` go
x.(T)
```

断言`x`不是`nil`, 且存储在`x`中的值属于类型`T`. 记法`x.(T)`称为 ==类型断言(type assertion)==.

更精确的说,

- 如果`T`不是接口类型, `x.(T)`断言`x`的动态类型是与`T`同一的. 在这种情况下, `T`必须实现`x`的接口类型; 否则, 因为不可能在`x`中存储类型`T`的值, 这个类型断言是无效的.
- 如果`T`是接口类型, `x.(T)`断言`x`的动态类型实现接口`T`.

如果类型断言为真, (类型断言)表达式的值是存储在`x`中的值, 它的类型是`T`.
如果类型断言为假, 发生运行时panic. 即, 尽管`x`的动态类型只在运行时可知, 在正确的程序中`x.(T)`的类型已知为`T`.

``` go
var x interface{} = 7          // x has dynamic type int and value 7
i := x.(int)                   // i has type int and value 7

type I interface { m() }

func f(y I) {
	s := y.(string)        // illegal: string does not implement I (missing method m)
	r := y.(io.Reader)     // r has type io.Reader and the dynamic type of y must implement both I and io.Reader
	…
}
```

在赋值或初始化形式中使用的类型断言

``` go
v, ok = x.(T)
v, ok := x.(T)
var v, ok = x.(T)
var v, ok T1 = x.(T)
```

产生一个额外的无类型的布尔值. 如果断言为真, `ok`的值是`true`. 否则, 它的值是`false`, `v`的值是`T`类型的零值. 这种情况下不会发生运行时panic.

### [调用](https://golang.org/ref/spec#Calls)

给定函数类型`F`的表达式`f`,

``` go
f(a1, a2, … an)
```

使用传递参数`a1, a2, … an`调用`f`. 除了一个特殊情况外, 传递参数必须是可以赋予`F`的参数类型的单值表达式, 并在函数被调用之前求值.
这个表达式的类型是`F`的结果类型.
方法调用是类似的, 但方法本身被描述为其接收者类型值上的选择器.

``` go
math.Atan2(x, y)  // function call
var pt *Point
pt.Scale(3.5)     // method call with receiver pt
```

在函数调用中, 函数值和传递参数按常规顺序求值. 在它们求值后, 调用的参数按值传递给函数, 被调用的函数开始执行.
当函数返回时, 函数的返回参数按值传回给调用函数.

调用`nil`函数值产生运行时panic.

作为一个特殊情况, 如果函数或方法`g`的返回值与另一函数或方法`f`的参数, 数量上相同且可分别赋值, 则调用`f(g(parameters_of_g))`会在按序将`g`的返回值绑定到`f`的参数上后调用`f`. 对`f`的调用不能有除调用`g`外的参数, `g`必须只要有一个返回值. 如果`f`最后有一个`...`参数, 它被赋予`g`的返回值中赋予常规参数之后的返回值.

``` go
func Split(s string, pos int) (string, string) {
	return s[0:pos], s[pos:]
}

func Join(s, t string) string {
	return s + t
}

if Join(Split(value, len(value)/2)) != value {
	log.Panic("test fails")
}
```

如果`x`的类型的方法集中有`m`, 传递参数列表可以赋予`m`的参数列表, 方法调用`x.m()`是有效的. 如果`x`是可寻址的, `&x`的方法集合中有`m`, `x.m()`是`(&x).m()`的简写:

``` go
var p Point
p.Scale(3.5)
```

没有不同的方法类型, 没有方法字面量.

### [传递参数给`...`参数](https://golang.org/ref/spec#Passing_arguments_to_..._parameters)

如果`f`是变长的, 有最有一个类型为`...T`的参数`p`, 则在`f`中, `p`的类型等价于类型`[]T`.
如果没有指定`p`对应的实际传递参数调用`f`, 传递给`p`的值是`nil`. 否则, 被传递的值是类型为`[]T`的新切片, 有新的底层数组, 数组中后继元素是实际传递参数, 它们必须可以赋予`T`. 因此, 切片的长度和容量是限制在`p`上的传递参数的数量, 每个调用点可能不同.

给定函数和调用

``` go
func Greeting(prefix string, who ...string)
Greeting("nobody")
Greeting("hello:", "Joe", "Anna", "Eileen")
```

在`Greeting`中, 在第一个调用中`who`有值`nil`, 在第二个调用中有值`[]string{"Joe", "Anna", "Eileen"}`.

如果最后一个传递参数可以赋予切片类型`[]T`, 如果这个传递参数后接`...`, 它被作为类型`...T`的值传递. 在这种情况下, 不会创建新的切片.

给定切片和调用

``` go
s := []string{"James", "Jasmine"}
Greeting("goodbye:", s...)
```

在`Greeting`中, `who`有与`s`相同的值, 共享底层数组.

### [操作符](https://golang.org/ref/spec#Operators)

操作符(operator)将操作数组合到表达式中.

``` EBNF
Expression = UnaryExpr | Expression binary_op Expression .
UnaryExpr  = PrimaryExpr | unary_op UnaryExpr .

binary_op  = "||" | "&&" | rel_op | add_op | mul_op .
rel_op     = "==" | "!=" | "<" | "<=" | ">" | ">=" .
add_op     = "+" | "-" | "|" | "^" .
mul_op     = "*" | "/" | "%" | "<<" | ">>" | "&" | "&^" .

unary_op   = "+" | "-" | "!" | "^" | "*" | "&" | "<-" .
```

比较操作符在其它地方讨论. 对其它二元操作符, 操作数的类型必须是同一的, 除非操作中包含移位或无类型的常量.
对只包含常量的操作, 见常量表达式一节.

除了移位操作外, 如果一个操作数是无类型的常量, 另一个操作数不是, 常量会隐式的转换为另一个操作数的类型.

移位表达式中右侧的操作数必须有整数类型或者是可用`unit`类型值表示的无类型常量.
如果非常量移位表达式的左侧的操作符是无类型常量, 它会首先被隐式转换为假设移位表达式被左侧的操作数替代时的类型.

``` go
var s uint = 33
var i = 1<<s                  // 1 has type int
var j int32 = 1<<s            // 1 has type int32; j == 0
var k = uint64(1<<s)          // 1 has type uint64; k == 1<<33
var m int = 1.0<<s            // 1.0 has type int; m == 0 if ints are 32bits in size
var n = 1.0<<s == j           // 1.0 has type int32; n == true
var o = 1<<s == 2<<s          // 1 and 2 have type int; o == true if ints are 32bits in size
var p = 1<<s == 1<<33         // illegal if ints are 32bits in size: 1 has type int, but 1<<33 overflows int
var u = 1.0<<s                // illegal: 1.0 has type float64, cannot shift
var u1 = 1.0<<s != 0          // illegal: 1.0 has type float64, cannot shift
var u2 = 1<<s != 1.0          // illegal: 1 has type float64, cannot shift
var v float32 = 1<<s          // illegal: 1 has type float32, cannot shift
var w int64 = 1.0<<33         // 1.0<<33 is a constant shift expression
var x = a[1.0<<s]             // 1.0 has type int; x == a[0] if ints are 32bits in size
var a = make([]byte, 1.0<<s)  // 1.0 has type int; len(a) == 0 if ints are 32bits in size
```

#### 操作符优先级

一元操作符有最高的优先级. 因为`++`和`--`操作符构成语句而不是表达式, 它们被放在操作符层次外.
因此, 语句`*p++`等价于`(*p)++`.

二元操作符有五个优先级层次. 乘法操作符最强, 后面是加法操作符、比较操作符、`&&`(逻辑AND), 最后是`||`(逻辑OR):

```
Precedence    Operator
    5             *  /  %  <<  >>  &  &^
    4             +  -  |  ^
    3             ==  !=  <  <=  >  >=
    2             &&
    1             ||
```

同一优先级的二元操作符是从左至右结合的. 例如, `x / y * z`与`(x / y) * z`是相同的.

``` go
+x
23 + 3*x[i]
x <= f()
^a >> b
f() || g()
x == y+1 && <-chanPtr > 0
```

### [算术操作符](https://golang.org/ref/spec#Arithmetic_operators)

算术操作符应用在数值值上, 产生与第一个操作数类型向量的结果.
4个标准的算术操作符(`+`、`-`、`*`、`/`)应用在整数、浮点数和复数类型上; `+`同时也应用在字符串上.
位级的逻辑和移位操作符值应用于整数.

```
+    sum                    integers, floats, complex values, strings
-    difference             integers, floats, complex values
*    product                integers, floats, complex values
/    quotient               integers, floats, complex values
%    remainder              integers

&    bitwise AND            integers
|    bitwise OR             integers
^    bitwise XOR            integers
&^   bit clear (AND NOT)    integers

<<   left shift             integer << unsigned integer
>>   right shift            integer >> unsigned integer
```

#### 整数操作符

对两个整数值`x`和`y`, 整数商(quotient) `q = x / y`和余数(remainder) `r = x % y`满足下面的关系:

```
x = q*y + r  and  |r| < |y|
```

这里`x / y`向0截断.


```
x     y     x / y     x % y
5     3       1         2
-5     3      -1        -2
5    -3      -1         2
-5    -3       1        -2
```

这个规则的一个例外是, 因为2的补码整数上溢, 如果被除数`x`是`x`的`int`类型的最小复数, 商`q = x / -1`等于`x`(这是`r`=0).

```
                         x, q
int8                     -128
int16                  -32768
int32             -2147483648
int64    -9223372036854775808
```

如果除数是个常量, 它不许不为0. 如果运行时除数为0, 发生运行时panic.
如果被除数非负, 除数是2的常量幂, 除法被右移位替换, 余数的计算被位级AND操作替换:

```
x     x / 4     x % 4     x >> 2     x & 3
11      2         3         2          3
-11     -2        -3        -3          1
```

移位操作符将左侧操作数移动右侧操作数指定的数量, 右侧操作数必须是正的.
如果运行时移位数量为负, 发生运行时panic.
如果左侧到作数是有符号的整数, 移位操作符实现算术移位(arithemetic shift); 如果是无符号的整数, 则实现逻辑移位(logical shift).
移位数量没有上限. 对移位数量为n, 移位表现为左侧操作符每次移动1位的移动n次.
因此, `x << 1`与`x*2`是相同, `x >> 1`与`x/2`是相同的, 除了向负无穷截断.

对整数操作数, 一元操作符`+`、`-`和`^`定义如下:

```
+x                          is 0 + x
-x    negation              is 0 - x
^x    bitwise complement    is m ^ x  with m = "all bits set to 1" for unsigned x
                                      and  m = -1 for signed x
```

#### 整数上溢

对无符号的整数值, 操作`+`、`-`、`*`和`<<`按模 $2^{n}$ 计算, $n$ 是无符号整数的类型的位宽度. 粗糙的讲, 这些无符号整数操作在上溢时忽略高位.

对有符号的整数, 操作`+`、`-`、`*`、`/`和`<<`可以合法的上溢, 结果值存在, 并被有符号的整数表示、操作和其操作数确定性的定义. 上溢不会产生运行时panic. 编译器不可以在上溢不会发生的假设下优化代码. 例如, 不可以假设`x < x + 1`总为真.

#### 浮点数操作符

对浮点数和复数, `+x`与`x`相同, `-x`是`x`相应的负数. 浮点数或复数除以0的结果未在IEEE-754中描述; 是否发生运行时panic是实现特定的.

实现可以组合多个浮点数操作(可能跨语句)到单个融合的操作(fused operation)中, 生成与分别执行和舍入指令获得值不同的结果.
显式的浮点数类型转换舍入到目标类型的精度, 阻止会忽视舍入的融合(fusion).

例如, 一些体系结构中提供了FMA(fused multiply and add)指令, 计算`x*y + z`时不会舍入中间结果`x*y`.
下面的示例展示了Go实现何时可以使用这个指令:

``` go
// FMA allowed for computing r, because x*y is not explicitly rounded:
r  = x*y + z
r  = z;   r += x*y
t  = x*y; r = t + z
*p = x*y; r = *p + z
r  = x*y + float64(z)

// FMA disallowed for computing r, because it would omit rounding of x*y:
r  = float64(x*y) + z
r  = z; r += float64(x*y)
t  = float64(x*y); r = t + z
```

#### 字符串串接

字符串可以使用`+`操作符或`+=`赋值操作符进行串接.

``` go
s := "hi" + string(c)
s += " and good bye"
```

字符串加法通过串接操作数创建一个新的字符串.

### [比较操作符](https://golang.org/ref/spec#Comparison_operators)

比较操作符比较两个操作数, 产生一个无类型的布尔值.

```
==    equal
!=    not equal
<     less
<=    less or equal
>     greater
>=    greater or equal
```

在任何比较中, 第一个操作数必须可以赋值给第二个操作数的类型, 反之亦然.

相等操作符`==`和`!=`应用在 ==可比较的(comparable)== 操作数上.
排序操作符`<`、`<=`、`>`、`>=`应用在 ==可排序的(ordered)== 操作数上.
这些术语和比较结果定义如下:

- 布尔值是可比较的. 如果两个布尔值同时是`true`或`false`时, 它们相等.
- 整数值是可比较和可排序的.
- 浮点数值是可比较和可排序的, 按IEEE-754中的定义.
- 复数值是可比较的. 两个复数值`u`和`v`, 如果`real(u) == real(v)`且`imag(u) == imag(v)`时, 它们相等.
- 字符串值是可比较和可排序的, 按字节级.
- 指针值是可比较的. 如果两个指针指向同一个变量或者都有值`nil`, 它们相等. 指向不同的零大小的变量的指针相等或不相等.
- 通道值是可比较的的. 如果两个通道值是用同一个`make`调用创建的或者都有值`nil`, 它们相等.
- 接口值是可比较的. 如果两个接口值有同一的动态类型且动态值相等, 或者都有值`nil`, 它们相等.
- 非接口类型`X`的值`x`与接口类型`T`的值`t`是可比较的, 当类型`X`的值是可比较的且`X`实现了`T`. 如果`t`的动态类型与`X`是同一的, 且`t`的动态值与`x`相等, `x`与`t`是相等的.
- 如果结构中所有字段是可比较的, 结构值是可比较的. 如果两个结构值对应的非空字段相等, 它们相等.
- 如果数组的数组元素类型是可比较的, 数组值是可比较的. 如果两个数组值对应的元素相等, 它们相等.

如果两个接口值由同一的动态类型, 但这个类型的值是不可比较的, 比较这两个接口值会产生运行时panic.
这个行为不仅适用于两个接口值的直接比较, 也适用于比较接口值的数组或有接口值字段的结构.

切片、映射和函数值是不可比较的. 然而, 作为特殊情况, 切片、映射或函数值可以与预声明的标识符`nil`进行比较.
将接口、通道和接口值与`nil`进行比较也是允许的, 并遵循上面的规则.

``` go
const c = 3 < 4            // c is the untyped boolean constant true

type MyBool bool
var x, y int
var (
	// The result of a comparison is an untyped boolean.
	// The usual assignment rules apply.
	b3        = x == y // b3 has type bool
	b4 bool   = x == y // b4 has type bool
	b5 MyBool = x == y // b5 has type MyBool
)
```

### [逻辑操作符](https://golang.org/ref/spec#Logical_operators)

逻辑操作符应用在布尔值上, 产生与操作数相同类型的结果. 右侧操作数被有条件的求值.

```
&&    conditional AND    p && q  is  "if p then q else false"
||    conditional OR     p || q  is  "if p then true else q"
!     NOT                !p      is  "not p"
```

### [地址操作符](https://golang.org/ref/spec#Address_operators)

对类型`T`的操作数`x`, 地址操作`&x`产生类型为`*T`的指向`x`的指针.
操作数必须是可寻址的, 即是变量、指针间接寻址、切片索引操作、可寻址的结构操作数的字段选择器、可寻址的数组的数组索引操作.
作为可寻址要求的一个例外, `x`可以是(可能带括弧的)复合字面量. 如果求值`x`产生运行时panic, 求值`&x`也会发生.

对指针类型`*T`的操作数`x`, ==指针间接寻址(pointer indirection)== `*x`表示`x`指向的类型`T`变量.
如果`x`是`nil`, 尝试求值`*x`产生运行时panic.

``` go
&x
&a[f(2)]
&Point{2, 3}
*p
*pf(x)

var x *int = nil
*x   // causes a run-time panic
&*x  // causes a run-time panic
```

### [接收操作符](https://golang.org/ref/spec#Receive_operator)

对通道类型的操作数`ch`, 接收操作`<-ch`的值是从通道`ch`接收到的值.
通道的方向必须允许接收操作, 结构操作的类型是通道的元素类型.
这个表达式会阻塞直到有值可用时. 从`nil`通道中接收会永远阻塞.
在已关闭的通道上的接收操作总是立即执行, 在任何之前发送的值已被处理后, 产生元素类型的零值.

``` go
v1 := <-ch
v2 = <-ch
f(<-ch)
<-strobe  // wait until clock pulse and discard received value
```

在赋值或初始化形式中使用的接收表达式

``` go
x, ok = <-ch
x, ok := <-ch
var x, ok = <-ch
var x, ok T = <-ch
```

产生一个额外的无类型布尔结果, 报告通信是否成功. 如果接收到的值是通道上成功的发送操作递送的, `ok`的值为`true`; 如果因为通道关闭或为空, 接收到的值为零值时, `ok`的值为`false`.

### [转换](https://golang.org/ref/spec#Conversions)

转换(conversion)将表达式的类型修改为转换指定的类型. 转换可以字面的在代码中出现, 或者蕴含在表达式出现的上下文中.

显式转换是形式为`T(x)`的表达式, 这里`T`是一个类型, `x`是一个可被转换为`T`的表达式.

``` EBNF
Conversion = Type "(" Expression [ "," ] ")" .
```

如果类型以操作符`*`或`<-`开始, 或者类型以关键字`func`开始且没有结果列表, 当需要避免歧义是需要加上括号:

``` go
*Point(p)        // same as *(Point(p))
(*Point)(p)      // p is converted to *Point
<-chan int(c)    // same as <-(chan int(c))
(<-chan int)(c)  // c is converted to <-chan int
func()(x)        // function signature func() x
(func())(x)      // x is converted to func()
(func() int)(x)  // x is converted to func() int
func() int(x)    // x is converted to func() int (unambiguous)
```

如果常量值`x`可用类型`T`的值表示时, `x`可以转换为类型`T`. 作为特殊情况, 与将非常量转换为字符串类型一样, 常量`x`可以显式的转换为字符串类型.

转换常量产生有类型的常量结果.

``` go
uint(iota)               // iota value of type uint
float32(2.718281828)     // 2.718281828 of type float32
complex128(1)            // 1.0 + 0.0i of type complex128
float32(0.49999999)      // 0.5 of type float32
float64(-1e-1000)        // 0.0 of type float64
string('x')              // "x" of type string
string(0x266c)           // "♬" of type string
MyString("foo" + "bar")  // "foobar" of type MyString
string([]byte{'a'})      // not a constant: []byte{'a'} is not a constant
(*int)(nil)              // not a constant: nil is not a constant, *int is not a boolean, numeric, or string type
int(1.2)                 // illegal: 1.2 cannot be represented as an int
string(65.0)             // illegal: 65.0 is not an integer constant
```

在下面的情况中, 非常量值`x`可以转换为类型`T`:

- `x`可以赋值给`T`.
- 忽略结构标签(见下文), `x`的类型与`T`有同一的底层类型.
- 忽略结构标签(见下文), `x`的类型和`T`是指针类型(不是定义的类型), 它们的指针基础类型有同一的底层类型.
- `x`的类型和`T`都是整数或浮点数类型.
- `x`的类型和`T`都是复数类型.
- `x`是整数、字节切片、rune, `T`是字符串类型.
- `x`是字符串, `T`是字节切片或rune.

当转换时比较结构类型的同一性, 会忽略结构标签.

``` go
type Person struct {
	Name    string
	Address *struct {
		Street string
		City   string
	}
}

var data *struct {
	Name    string `json:"name"`
	Address *struct {
		Street string `json:"street"`
		City   string `json:"city"`
	} `json:"address"`
}

var person = (*Person)(data)  // ignoring tags, the underlying types are identical
```

特定的规则应用在数值类型和字符串类型之间的相互(非常量)转换. 这些转换会修改`x`的表示, 并增加运行时费用.
所有其它的转换只会改变`x`的类型, 不会改变其表示.

没有语言机制用于在指针和整数之间转换. `unsafe`包在受限场景下实现了这个功能.

#### 数值类型之间的转换

对非常量数值值转换, 下面的规则适用:

1. 当在整数类型之间转换时, 如果值是有符号的整数, 它被符号扩展到隐式的无限精度; 否则用零扩展. 然后被截断到匹配结果类型的大小. 例如, 如果`v := uint16(0x10F0)`, 则`uint32(int8(v)) == 0xFFFFFFF0`. 转换总是产生一个有效的值; 没有上溢的标示.
2. 当将浮点数转换为整数时, 小数部分被求其(向零截断).
3. 当将整数或浮点数转换为浮点数类型, 或者将复数转换到另一个复数类型时, 结果值舍入到目标类型指定的精度. 例如, `float32`类型的变量的值可以使用超过IEEE-754 32位数的精度存储, 但`float32(x)`表示舍入`x`的值到32位精度的结果. 类似的, `x + 0.1`可以使用超过32位的精度, 但`float32(x + 0.1)`不会.

在包含浮点值或复数值的所有非常量转换中, 如果结果类型不能表示值, 转换成功, 但结果值是依赖于实现的.

#### 字符串类型转换

1 将有符号或无符号整数值转换为字符串类型, 产生一个包含整数的UTF-8表示的字符串. 在有效Unicode码点之外的值被转换为`"\uFFFD"`.

``` go
string('a')       // "a"
string(-1)        // "\ufffd" == "\xef\xbf\xbd"
string(0xf8)      // "\u00f8" == "ø" == "\xc3\xb8"
type MyString string
MyString(0x65e5)  // "\u65e5" == "日" == "\xe6\x97\xa5"
```

2 将字节切片转换为字符串类型, 产生连续的字节是切片的元素的字符串.

``` go
string([]byte{'h', 'e', 'l', 'l', '\xc3', '\xb8'})   // "hellø"
string([]byte{})                                     // ""
string([]byte(nil))                                  // ""

type MyBytes []byte
string(MyBytes{'h', 'e', 'l', 'l', '\xc3', '\xb8'})  // "hellø"
```

3 将rune切片转换为字符串类型, 产生各rune值转换为字符串的拼接而成的字符串.

``` go
string([]rune{0x767d, 0x9d6c, 0x7fd4})   // "\u767d\u9d6c\u7fd4" == "白鵬翔"
string([]rune{})                         // ""
string([]rune(nil))                      // ""

type MyRunes []rune
string(MyRunes{0x767d, 0x9d6c, 0x7fd4})  // "\u767d\u9d6c\u7fd4" == "白鵬翔"
```

4 将字符串类型值转换为字节类型切片, 产生连续的元素是字符串的字节的切片.

``` go
[]byte("hellø")   // []byte{'h', 'e', 'l', 'l', '\xc3', '\xb8'}
[]byte("")        // []byte{}

MyBytes("hellø")  // []byte{'h', 'e', 'l', 'l', '\xc3', '\xb8'}
```

5 将字符串类型值换换为rune类型切片, 产生包含字符串中各Unicode码点的切片.

``` go
[]rune(MyString("白鵬翔"))  // []rune{0x767d, 0x9d6c, 0x7fd4}
[]rune("")                 // []rune{}

MyRunes("白鵬翔")           // []rune{0x767d, 0x9d6c, 0x7fd4}
```

### [常量表达式](https://golang.org/ref/spec#Constant_expressions)

常量表达式只包含常量操作数, 并在编译时求值.

每当使用布尔、数值和字符串类型的操作数是合法的时, 无类型的布尔、数值和字符串常量可以用作操作数.

常量比较总是产生无类型的布尔常量. 如果常量移位表达式的左操作数是无类型的常量, 结果是整数常量; 否则是与左侧操作数相同的类型(必须是整数类型)的常量.

其它在无类型的常量上的操作产生相同种类的无类型常量; 即布尔、整数、浮点数、复数或字符串常量. 如果二元操作(除移位操作外)的无类型操作数是不同种类的, 结果是后面出现的操作数的种类: 整数、rune、浮点数、复数. 例如, 无类型的整数常量除以无类型的复数常量, 产生一个无类型的复数常量.

``` go
const a = 2 + 3.0          // a == 5.0   (untyped floating-point constant)
const b = 15 / 4           // b == 3     (untyped integer constant)
const c = 15 / 4.0         // c == 3.75  (untyped floating-point constant)
const Θ float64 = 3/2      // Θ == 1.0   (type float64, 3/2 is integer division)
const Π float64 = 3/2.     // Π == 1.5   (type float64, 3/2. is float division)
const d = 1 << 3.0         // d == 8     (untyped integer constant)
const e = 1.0 << 3         // e == 8     (untyped integer constant)
const f = int32(1) << 33   // illegal    (constant 8589934592 overflows int32)
const g = float64(2) >> 1  // illegal    (float64(2) is a typed floating-point constant)
const h = "foo" > "bar"    // h == true  (untyped boolean constant)
const j = true             // j == true  (untyped boolean constant)
const k = 'w' + 1          // k == 'x'   (untyped rune constant)
const l = "hi"             // l == "hi"  (untyped string constant)
const m = string(k)        // m == "x"   (type string)
const Σ = 1 - 0.707i       //            (untyped complex constant)
const Δ = Σ + 2.0e-4       //            (untyped complex constant)
const Φ = iota*1i - 1/1i   //            (untyped complex constant)
```

在无类型整数、rune、浮点数常量上应用内建函数`complex`, 生成无类型的复数常量.

``` go
const ic = complex(0, c)   // ic == 3.75i  (untyped complex constant)
const iΘ = complex(0, Θ)   // iΘ == 1i     (type complex128)
```

常量表达式总是被准确求值; 中间值和常量本身可能要求比语言中任意预声明的类型支持的精度明显大的精度. 下面是合法的声明:

``` go
const Huge = 1 << 100         // Huge == 1267650600228229401496703205376  (untyped integer constant)
const Four int8 = Huge >> 98  // Four == 4                                (type int8)
```

常量除法或取余操作的除数必须不是零:

``` go
3.14 / 0.0   // illegal: division by zero
```

有类型的常量的值必须总是可以用常量类型的值正确的表示. 下面的常量表达式是非法的:

``` go
uint(-1)     // -1 cannot be represented as a uint
int(3.14)    // 3.14 cannot be represented as an int
int64(Huge)  // 1267650600228229401496703205376 cannot be represented as an int64
Four * 300   // operand 300 cannot be represented as an int8 (type of Four)
Four * 100   // product 400 cannot be represented as an int8 (type of Four)
```

一元补操作符`^`中使用的掩码(mask)匹配非常量的规则: 对无符号常量, 掩码全是`1`; 对有符号的或无类型的常量, 掩码全是`-1`.

``` go
^1         // untyped integer constant, equal to -2
uint8(^1)  // illegal: same as uint8(-2), -2 cannot be represented as a uint8
^uint8(1)  // typed uint8 constant, same as 0xFF ^ uint8(1) = uint8(0xFE)
int8(^1)   // same as int8(-2)
^int8(1)   // same as -1 ^ int8(1) = -2
```

实现约束: 编辑器在计算无类型的浮点数或复数常量表达式时, 可以使用舍入; 见常量一节中的实现约束. 舍入可能会导致浮点数常量表达式在整数上下文中无效的, 甚至在使用无限精度计算时它是整的(integral)时, 反之亦然.

### [求值的顺序](https://golang.org/ref/spec#Order_of_evaluation)

在包级别, 初始化依赖确定了在变量声明中各初始化表达式的求值顺序.
此外, 求值表达式的操作数时, 赋值或返回语句、所有函数调用和方法调用、通信操作是按词法从左向右的顺序求值.

例如, 在(函数本地)赋值中

``` go
y[f()], ok = g(h(), i()+x[j()], <-c), k()
```

函数调用和通信的顺序是: `f()`、`h()`、`i()`、`j()`、`<-c`、`k()`.
然而, 这些顺序相对于`x`的求值和索引以及`y`的求值的顺序是未描述的.

``` go
a := 1
f := func() int { a++; return a }
x := []int{a, f()}            // x may be [1, 2] or [2, 2]: evaluation order between a and f() is not specified
m := map[int]int{a: 1, a: 2}  // m may be {2: 1} or {2: 2}: evaluation order between the two map assignments is not specified
n := map[int]int{a: f()}      // n may be {2: 3} or {3: 3}: evaluation order between the key and the value is not specified
```

在包级别, 初始化依赖覆盖了各初始化表达式的从左向右的规则, 但不会覆盖各表达式中的操作数的从左向右的规则:

``` go
var a, b, c = f() + v(), g(), sqr(u()) + v()

func f() int        { return c }
func g() int        { return a }
func sqr(x int) int { return x*x }

// functions u and v are independent of all other variables and functions
```

函数调用的顺序是: `u()`、`sqr()`、`v()`、`f()`、`v()`、`g()`.

在单个表达式中的浮点数操作, 按操作符的结合性求值. 显式的括号通过覆盖默认结合性影响求值. 在表达式`x + (y + z)`中, 加法`y + z`在加`x`前执行.

## 11 [语句](https://golang.org/ref/spec#Statements)

语句(statement)控制执行.

``` EBNF
Statement =
	Declaration | LabeledStmt | SimpleStmt |
	GoStmt | ReturnStmt | BreakStmt | ContinueStmt | GotoStmt |
	FallthroughStmt | Block | IfStmt | SwitchStmt | SelectStmt | ForStmt |
	DeferStmt .

SimpleStmt = EmptyStmt | ExpressionStmt | SendStmt | IncDecStmt | Assignment | ShortVarDecl .
```

### [终止语句](https://golang.org/ref/spec#Terminating_statements)

终止语句(terminating statement)阻止同一个块中词法的在它之后出现的所有语句的执行. 下面的语句是终止语句:

1. `return`语句, `goto`语句.
2. 对内建函数`panic`的调用.
3. 语句列表以终止语句结束的块.
4. `if`语句:<br> 有else分支, 且<br> 两个分支都是终止语句.
5. `for`语句:<br> 没有引用这个`for`语句的`break`语句, 且<br> 没有循环条件.
6. `switch`语句:<br> 没有引用这个`switch`语句的`break`语句, <br> 有默认情况, 且<br> 每个情况(包括默认情况)中的语句列表, 以终止语句结束, 或者以带标签`fallthrough`语句结束.
7. `select`语句:<br> 没有引用这个`select`语句的`break`语句, 且<br> 每个情况(如果有默认情况也包括进来)中的语句列表, 以终止语句结束.
8. 标记了终止语句的带标签的语句.

### [空语句](https://golang.org/ref/spec#Empty_statements)

空语句不做什么.

``` EBNF
EmptyStmt = .
```

### [带标签的语句](https://golang.org/ref/spec#Labeled_statements)

带标签的语句可以是`goto`、`break`或`continue`语句的目标.

``` EBNF
LabeledStmt = Label ":" Statement .
Label       = identifier .
```

``` go
Error: log.Panic("error encountered")
```

### [表达式语句](https://golang.org/ref/spec#Expression_statements)

除特定内建函数外, 函数和方法调用、接收操作可以在语句上下文中出现. 这些语句可以有括号包裹.

``` EBNF
ExpressionStmt = Expression .
```

下面的内建函数不允许在语句上下文中出现:

``` go
append cap complex imag len make new real
unsafe.Alignof unsafe.Offsetof unsafe.Sizeof
```

``` go
h(x+y)
f.Close()
<-ch
(<-ch)
len("foo")  // illegal if len is the built-in function
```

### [发送语句](https://golang.org/ref/spec#Send_statements)

发送语句在通常上发送值. 通道表达式必须有通道类型, 通道的方向必须允许发送操作, 被发送的值的类型必须可以赋予通道的元素类型.

``` EBNF
SendStmt = Channel "<-" Expression .
Channel  = Expression .
```

通道和值表达式在通信开始之前求值. 通信被阻塞直到发送可以执行.
在无缓冲的通道上的发送, 在一个接收者准备好时, 可以执行.
在有缓冲的通道上的发送, 在缓冲区中有空间时, 可以执行.
在已关闭的通道上的发送, 会导致运行时panic.
在`nil`通道上的发送被永远阻塞.

``` go
ch <- 3  // send value 3 to channel ch
```

### [递增递减语句](https://golang.org/ref/spec#IncDec_statements)

`++`和`--`语句将它们的操作数递增或递减无类型的常量1. 在赋值中, 操作数必须是可寻址的或者是映射索引表达式.

``` EBNF
IncDecStmt = Expression ( "++" | "--" ) .
```

下面的赋值语句时语义等价的:

```
IncDec statement    Assignment
x++                 x += 1
x--                 x -= 1
```

### [赋值](https://golang.org/ref/spec#Assignments)

``` EBNF
Assignment = ExpressionList assign_op ExpressionList .

assign_op = [ add_op | mul_op ] "=" .
```

每个左端操作数必须是可寻址的、映射索引表达式或(只对`=`赋值)空标识符. 操作数可以包裹在括号内.

``` go
x = 1
*p = f()
a[i] = 23
(k) = <-ch  // same as: k = <-ch
```

赋值操作`x op= y`, `op`是一个二元算术操作符, 它等价于`x = x op (y)`, 但只会求值`x`一次. 构造`op=`是单个记号.
在赋值操作中, 左侧和右侧表达式列表臧洪必须有一个单值的表达式, 左侧的表达式不能是空标识符.

``` go
a[i] <<= 2
i &^= 1<<n
```

元组赋值(tuple assignment)将多值的操作的每个元素赋予一组变量.
有两种形式.
第一种, 右侧操作数是单个多值的表达式, 例如函数调用、通道或映射操作、类型断言. 左侧操作数的数量必须匹配这些值的数量. 例如, 如果`f`是一个返回两个值的函数,

``` go
x, y = f()
```

将第一个值赋予`x`, 将第二个赋予`y`.
在第二种形式中, 左侧操作数的数量必须等于右侧表达式的数量, 每个都必须是单值的, 右侧第n个表达式赋予左侧的第n个操作数:

``` go
one, two, three = '一', '二', '三'
```

空标识符提供了在赋值中忽略右侧值的方法:

``` go
_ = x       // evaluate x but ignore it
x, _ = f()  // evaluate f() but ignore second result value
```

赋值分两个阶段处理.
首先, 左侧的索引表达式和指针间接寻址(包括选择器中的隐式指针间接寻址)的操作数 与 右侧的表达式被按通常的顺序求值.
然后, 按从左到右的顺序执行赋值.

``` go
a, b = b, a  // exchange a and b

x := []int{1, 2, 3}
i := 0
i, x[i] = 1, 2  // set i = 1, x[0] = 2

i = 0
x[i], i = 2, 1  // set x[0] = 2, i = 1

x[0], x[0] = 1, 2  // set x[0] = 1, then x[0] = 2 (so x[0] == 2 at end)

x[1], x[3] = 4, 5  // set x[1] = 4, then panic setting x[3] = 5.

type Point struct { x, y int }
var p *Point
x[2], p.x = 6, 7  // set x[2] = 6, then panic setting p.x = 7

i = 2
x = []int{3, 5, 7}
for i, x[i] = range x {  // set i, x[2] = 0, x[0]
	break
}
// after this loop, i == 0 and x == []int{3, 5, 3}
```

在赋值中, 每个值必须可以赋予它被赋予的操作数的类型, 有一些特殊情况:

1. 任意有类型的值可以赋予空标识符.
2. 如果无类型的常量被赋予接口类型变量或空标识符, 常量被首先隐式转换为它的默认类型.
3. 如果无类型的布尔值被赋予接口类型变量或空标识符, 它被首先隐式转换为`bool`类型.


### [`if`语句](https://golang.org/ref/spec#If_statements)

`if`语句根据布尔表达式的值, 描述两个分支的条件执行. 如果表达式求值为`true`, 则`if`分支被执行, 否则如果有`else`分支的话它被执行.

``` EBNF
IfStmt = "if" [ SimpleStmt ";" ] Expression Block [ "else" ( IfStmt | Block ) ] .
```

``` go
if x > max {
	x = max
}
```

表达式之前可以有一个简单语句, 它在求值表达式之前执行.

``` go
if x := f(); x < y {
	return x
} else if x > z {
	return z
} else {
	return y
}
```

### [`switch`语句](https://golang.org/ref/spec#Switch_statements)

`switch`语句提供了多路执行. 将表达式或类型描述符与`switch`中的case进行比较, 确定执行哪个分支.

``` EBNF
SwitchStmt = ExprSwitchStmt | TypeSwitchStmt .
```

有两种形式: 表达式`switch`和类型`switch`. 在表达式`switch`中, 各case有用于与`switch`表达式进行比较的表达式. 在类型`switch`中, 各case有用于与特别标注的`switch`表达式的类型进行比较的类型. `switch`表达式只在`switch`语句中求值一次.

#### 表达式`switch`

在表达式`switch`中, `switch`表达式被求值, case表达式不需要是常量, 按从左到右和从上往下的顺序求值; 第一个与`switch`表达式相等的触发相应的语句执行; 其它的case被跳过. 如果没有case匹配, 且有一个默认case, 则它的语句被执行. 最多有一个默认case, 它可以在`switch`语句的任意位置出现. 缺失的`switch`表达式与布尔值`true`等价.

``` EBNF
ExprSwitchStmt = "switch" [ SimpleStmt ";" ] [ Expression ] "{" { ExprCaseClause } "}" .
ExprCaseClause = ExprSwitchCase ":" StatementList .
ExprSwitchCase = "case" ExpressionList | "default" .
```

如果`switch`表达式求值为无类型的常量, 它将首先被隐式的转换到它的默认类型; 如果它是一个无类型的布尔值, 它将首先被隐式的转换为`bool`类型. 预声明的无类型值`nil`不能用作`switch`表达式.

如果一个case表达式是无类型的, 它将首先被隐式的转换到`switch`表达式的类型. 对每个(可能是被转换后的)case表达式`x`, `switch`表达式的值`t`, `x == t`必须是有效的比较.

换句话说, `switch`表达式被视为用于声明和初始化没有显式类型的临时变量`t`; 它是每个case表达式`x`测试相等性的值.

在一个case或默认子句中, 最后一个非空语句可以是(可能带标签的)`fallthrough`语句, 用于表明控制应该从当前子句流转到下一个子句的第一个语句. 否则, 控制流转到`switch`语句的尾部. `fallthrough`语句可以在表达式`switch`中除最后一个子句外的所有子句中作为最后一个语句出现.

`switch`表达式之前可以有一个简单语句, 它在求值表达式前执行.

``` go
switch tag {
default: s3()
case 0, 1, 2, 3: s1()
case 4, 5, 6, 7: s2()
}

switch x := f(); {  // missing switch expression means "true"
case x < 0: return -x
default: return x
}

switch {
case x < y: f1()
case x < z: f2()
case x == 4: f3()
}
```

实现约束: 编译器可以不允许多个表达式求值为同一个常量. 例如, 当前编译器不允许case表达式中有重复的整数、浮点数或字符串常量.

#### 类型`switch`

类型`switch`比较类型而不是值. 除此之外与表达式`switch`类似. 它被特殊的`switch`表达式标记, 这个表达式有类型断言的形式, 使用了保留字`type`而不是实际的类型:

``` go
switch x.(type) {
// cases
}
```

然后case将实际类型`T`与表达式`x`的动态类型进行匹配. 在类型断言中, `x`必须是接口类型, 每个case中非接口类型`T`必须实现`x`的类型. 类型`switch`中各case列出的类型必须都不相同.

``` EBNF
TypeSwitchStmt  = "switch" [ SimpleStmt ";" ] TypeSwitchGuard "{" { TypeCaseClause } "}" .
TypeSwitchGuard = [ identifier ":=" ] PrimaryExpr "." "(" "type" ")" .
TypeCaseClause  = TypeSwitchCase ":" StatementList .
TypeSwitchCase  = "case" TypeList | "default" .
TypeList        = Type { "," Type } .
```

`TypeSwitchGuard`可以包含一个短变量声明. 当使用了这黑暗能形式时, 变量在每个子句的隐式块中`TypeSwitchCase`的末尾声明. 在只有一个类型的case的子句中, 变量有这个类型; 否则, 变量有`TypeSwitchGuard`中表达式的类型.

除类型外, case可以使用预声明标识符`nil`; 当`TypeSwitchGuard`中表达式是`nil`接口值时, 这个case被选择. 最多只有一个`nil`case.

给定类型`interface{}`的表达式`x`, 下面的类型`switch`

``` go
switch i := x.(type) {
case nil:
	printString("x is nil")                // type of i is type of x (interface{})
case int:
	printInt(i)                            // type of i is int
case float64:
	printFloat64(i)                        // type of i is float64
case func(int) float64:
	printFunction(i)                       // type of i is func(int) float64
case bool, string:
	printString("type is bool or string")  // type of i is type of x (interface{})
default:
	printString("don't know the type")     // type of i is type of x (interface{})
}
```

可以重写为:

``` go
v := x  // x is evaluated exactly once
if v == nil {
	i := v                                 // type of i is type of x (interface{})
	printString("x is nil")
} else if i, isInt := v.(int); isInt {
	printInt(i)                            // type of i is int
} else if i, isFloat64 := v.(float64); isFloat64 {
	printFloat64(i)                        // type of i is float64
} else if i, isFunc := v.(func(int) float64); isFunc {
	printFunction(i)                       // type of i is func(int) float64
} else {
	_, isBool := v.(bool)
	_, isString := v.(string)
	if isBool || isString {
		i := v                         // type of i is type of x (interface{})
		printString("type is bool or string")
	} else {
		i := v                         // type of i is type of x (interface{})
		printString("don't know the type")
	}
}
```

`TypeSwitchGuard`之前可以有一个简单语句, 他在求值这个守卫之前执行.

类型`switch`中不允许有`fallthrough`语句.

### [`for`语句](https://golang.org/ref/spec#For_statements)

`for`语句描述重复执行的块. 有三种形式: 迭代可以被单个条件、一个`for`子句或一个`range`子句控制.

``` EBNF
ForStmt = "for" [ Condition | ForClause | RangeClause ] Block .
Condition = Expression .
```

#### 单条件的`for`语句

在其最简单的形式中, `for`语句描述了只要布尔条件求值为`true`时重复执行块. 条件(condition)在每次迭代前求值. 如果条件不存在, 它等价于布尔值`true`.

``` go
for a < b {
	a *= 2
}
```

#### 有`for`子句的`for`语句

有`ForClause`的`for`语句也由它的条件控制, 但它可以额外的指定初始化和迭代后语句, 例如赋值语句、递增递减语句. 初始化语句可以一个短变量声明, 但迭代后语句不可以. 在初始化语句中声明的变量在每次迭代中重用.

``` EBNF
ForClause = [ InitStmt ] ";" [ Condition ] ";" [ PostStmt ] .
InitStmt = SimpleStmt .
PostStmt = SimpleStmt .
```

``` go
for i := 0; i < 10; i++ {
	f(i)
}
```

如果初始化语句非空, 它在求值第一次迭代的条件之前执行一次; 如果迭代后语句非空, 它在每次块执行后执行(只在块被执行了时). `ForClause`中每个元素可以为空, 但除了只有一个条件外, 必须要有分号. 如果条件不存在, 它等价于布尔值`ture`.

``` go
for cond { S() }    is the same as    for ; cond ; { S() }
for      { S() }    is the same as    for true     { S() }
```

#### 有`range`子句的`for`语句

有`range`子句的`for`语句迭代数组、切片、字符串、映射或从通道接收到的值中每个项. 对每个项, 它将迭代值赋予相应的迭代变量, 然后执行块.

``` EBNF
RangeClause = [ ExpressionList "=" | IdentifierList ":=" ] "range" Expression .
```

`range`子句中右侧的表达式称为`range`表达式, 它可以是一个数组、数组指针、切片、字符串、映射或允许接收操作的通道. 就像在赋值中, 如果左边的操作数存在, 它必须是可寻址的或映射的索引表达式; 它们表示迭代变量. 如果`range`表达式是一个通道, 最多只允许一个迭代变量, 否则可以是两个. 如果最后一个迭代变量是空标识符, `range`子句等价于没有这个标识符的子句.

`range`表达式`x`在开始循环之前求值一次, 有一个例外情况: 如果最多只有一个迭代变量存在, 且`len(x)`是常量, `range`表达式不被求值.

左侧的函数调用在每次迭代中求值一次. 对每次迭代, 如果相应的迭代变量粗鸟在, 迭代值按如下方式生成:

```
Range expression                          1st value          2nd value

array or slice  a  [n]E, *[n]E, or []E    index    i  int    a[i]       E
string          s  string type            index    i  int    see below  rune
map             m  map[K]V                key      k  K      m[k]       V
channel         c  chan E, <-chan E       element  e  E
```

1. 对数组、数值指针、切片值`a`, 索引迭代值按升序升恒, 从元素索引0开始. 如果最多有一个迭代变量存在, `range`循环产生从0到`len(a)-1`的迭代值, 不会索引入数组或切片自身. 对`nil`欺骗, 迭代的数量是0.
2. 对字符串值, `range`子句从字节索引0开始迭代字符串中的Unicode码点. 在一次成功的迭代后, 索引值称为字符串中连续的UTF-8编码的码点的第一个字节的索引, 第二个值的类型是`rune`, 是相应码点的值. 如果迭代遇到无效的UTF-8序列, 第二个值会是Unicode替换字符`0xFFFD`, 下一个迭代在字符串中移进单个字节.
3. 在映射上的迭代顺序是未描述的, 不保证在每次循环中相同. 如果在迭代中移除还没有访问到的项, 相应的迭代值不会产生. 如果在迭代中创建映射项, 这个项可能在迭代中产生, 也可能被跳过. 对每个创建的项和不同循环之间, 选择不同. 如果映射是`nil`, 迭代的次数是0.
4. 对通道, 产生的迭代值是直到通道被关闭之前在通道上发送的连续的值. 如果通道是`nil`, `range`表达式永远阻塞.

像在赋值语句中一样, 迭代值被赋予相应的迭代变量.

`range`子句中声明的迭代变量可以使用短变量声明(`:=`)形式. 在这种情况下, 它们的类型被设置为相应的迭代值的类型, 它们的作用域是`for`语句块; 它们在每次迭代中重用. 如果迭代变量在`for`语句外声明, 在执行后, 它们的值是最后一个迭代中的值.

``` go
var testdata *struct {
	a *[7]int
}
for i, _ := range testdata.a {
	// testdata.a is never evaluated; len(testdata.a) is constant
	// i ranges from 0 to 6
	f(i)
}

var a [10]string
for i, s := range a {
	// type of i is int
	// type of s is string
	// s == a[i]
	g(i, s)
}

var key string
var val interface {}  // element type of m is assignable to val
m := map[string]int{"mon":0, "tue":1, "wed":2, "thu":3, "fri":4, "sat":5, "sun":6}
for key, val = range m {
	h(key, val)
}
// key == last map key encountered in iteration
// val == map[key]

var ch chan Work = producer()
for w := range ch {
	doWork(w)
}

// empty a channel
for range ch {}
```

### [`go`语句](https://golang.org/ref/spec#Go_statements)

`go`语句就像在同一地址空间中使用独立的并发线程(或goroutine)中开始函数调用的执行.

``` EBNF
GoStmt = "go" Expression .
```

其中的表达式`Expression`必须是函数或方法调用; 不能包裹在括号内. 对内建函数的调用的限制与表达式语句中相同.

在调用方goroutine中, 函数值和参数按常规方式求值, 但与常规调用不同的是, 程序执行不会等待被调用的函数完成. 而是, 函数在新的goroutine中立即开始执行. 当函数终止时, 它的goroutine也终止. 如果函数有任何返回值, 在函数结束时被丢弃.

``` go
go Server()
go func(ch chan<- bool) { for { sleep(10); ch <- true }} (c)
```

### [`select`语句](https://golang.org/ref/spec#Select_statements)

`select`语句从一组可能的发送或接收操作中选择一个执行. 它看上去与`switch`语句相似, 但case均引用通信操作.

``` EBNF
SelectStmt = "select" "{" { CommClause } "}" .
CommClause = CommCase ":" StatementList .
CommCase   = "case" ( SendStmt | RecvStmt ) | "default" .
RecvStmt   = [ ExpressionList "=" | IdentifierList ":=" ] RecvExpr .
RecvExpr   = Expression .
```

有`RecvStmt`的case可以将`RecvExpr`的结果赋予一个或两个变量, 它们可以使用短变量声明. `RecvExpr`必须是一个(被括号包裹的)接收操作. 最多有一个默认case, 可以在case列表中任意位置出现.

`select`语句的执行有几个步骤:

1. 在进入`select`语句时, 对语句中的所有case, 接收操作和通道的通道操作数、发送语句的右侧表达式按源码顺序精确执行一次. 结果是一组要发送或接收的通道, 以及相应的要发送的值. 求值的副作用会发生, 不管选择了哪个通信操作(如果有的话)执行. `RecvStmt`左侧有短变量声明或赋值的表达式暂未被求值.
2. 如果可以执行一个或多个通信, 使用一种伪随机的方式选择一个. 否则, 如果有一个默认case, 选择它. 如果没有默认case, `select`被阻塞直到至少有一个通信可以执行.
3. 除非被选择的case是默认case, 其通信操作被执行.
4. 如果被选择的case是由短变量声明或赋值的`RecvStmt`, 其左侧的表达式被求值, 使用接收的值赋值.
5. 被选择的case的语句列表被执行.

因为在`nil`通常上的通信永远不会执行, 只有`nil`通道且没有默认case的`select`语句被永远阻塞.

``` go
var a []int
var c, c1, c2, c3, c4 chan int
var i1, i2 int
select {
case i1 = <-c1:
	print("received ", i1, " from c1\n")
case c2 <- i2:
	print("sent ", i2, " to c2\n")
case i3, ok := (<-c3):  // same as: i3, ok := <-c3
	if ok {
		print("received ", i3, " from c3\n")
	} else {
		print("c3 is closed\n")
	}
case a[f()] = <-c4:
	// same as:
	// case t := <-c4
	//	a[f()] = t
default:
	print("no communication\n")
}

for {  // send random sequence of bits to c
	select {
	case c <- 0:  // note: no statement, no fallthrough, no folding of cases
	case c <- 1:
	}
}

select {}  // block forever
```

### [`return`语句](https://golang.org/ref/spec#Return_statements)

在函数`F`中的`return`终止`F`的执行, 可选的提供一个或多个结果值. 任何被`F`延迟的(deferred)函数在`F`返回它的调用者之前执行.

``` EBNF
ReturnStmt = "return" [ ExpressionList ] .
```

在没有结果类型的函数中, `return`语句必须不能指定任何结果值.

``` go
func noResult() {
	return
}
```

有三种方式从有结果类型的函数中返回值:

1 返回的值(一个或多个)可以显式的列在`return`语句中. 每个表达式必须是单值的, 且可以赋予函数结果类型中相应的元素.

``` go
func simpleF() int {
	return 2
}

func complexF1() (re float64, im float64) {
	return -7.0, -4.0
}
```

2 `return`语句中的表达式列表可以是对一个多值函数的单个调用. 其效果与将该函数返回的值分别赋给临时变量(有相应值的类型)后, 使用列出这些变量的`return`语句一样.

``` go
func complexF2() (re float64, im float64) {
	return complexF1()
}
```

3 如果函数的结果类型指定了结果参数的名称, 表达式列表可以为空. 结果参数表现的与常规本地函数一样, 函数可以在必要时赋予它们值. `return`语句返回这些变量的值.

``` go
func complexF3() (re float64, im float64) {
	re = 7.0
	im = 4.0
	return
}

func (devnull) Write(p []byte) (n int, _ error) {
	n = len(p)
	return
}
```

不管结果值是如何声明的, 在进入函数时它们被初始化为其类型的零值.
指定结果集的`return`语句在任何被延迟的函数执行之前设置结果参数.

实现约束: 如果在返回出现的作用域中有与结果参数相同名称的不同的实体(常量、类型或变量), 编译器不允许`return`语句中有空表达式列表.

``` go
func f(n int) (res int, err error) {
	if _, err := f(n-1); err != nil {
		return  // invalid return statement: err is shadowed
	}
	return
}
```

### [`break`语句](https://golang.org/ref/spec#Break_statements)

`break`语句终止在同一个函数中最内层的`for`、`switch`或`select`语句的执行.

``` EBNF
BreakStmt = "break" [ Label ] .
```

如果有标签, 它必须包裹了`for`、`switch`或`select`语句, 是执行被终止的位置.

``` go
OuterLoop:
	for i = 0; i < n; i++ {
		for j = 0; j < m; j++ {
			switch a[i][j] {
			case nil:
				state = Error
				break OuterLoop
			case item:
				state = Found
				break OuterLoop
			}
		}
	}
```

### [`continue`语句](https://golang.org/ref/spec#Continue_statements)

`continue`语句在其所在的最内层`for`循环中开始下一个迭代. `for`循环必须在同一个函数中.

``` EBNF
ContinueStmt = "continue" [ Label ] .
```

如果有标签, 它必须包裹了`for`语句, 是执行继续的位置.

``` go
RowLoop:
	for y, row := range rows {
		for x, data := range row {
			if data == endOfRow {
				continue RowLoop
			}
			row[x] = data + bias(x, y)
		}
	}
```

### [`goto`语句](https://golang.org/ref/spec#Goto_statements)

`goto`语句将控制转移到在同一个函数中有相应的标签的语句中.

``` EBNF
GotoStmt = "goto" Label .
```

``` go
goto Error
```

执行`goto`语句必须不能导致任何在`goto`点时未在作用域中的变量进入作用域. 例如,

``` go
goto L  // BAD
v := 3
L:
```

是错误的, 因为调到标签`L`跳过了创建`c`.

块外的`goto`语句不能调到块内的标签处. 例如:

``` go
if n%2 == 1 {
	goto L1
}
for n > 0 {
	f()
	n--
L1:
	f()
	n--
}
```

是错误的, 因为标签`L`在`for`语句块中, 但`goto`不在.

### [`fallthrough`语句](https://golang.org/ref/spec#Fallthrough_statements)

`fallthrough`语句在表达式`switch`语句中, 将控制转移到下一个case子句的第一个语句.
可以只用作这样的子句的最后一个非空语句.

``` EBNF
FallthroughStmt = "fallthrough" .
```

### [`defer`语句](https://golang.org/ref/spec#Defer_statements)

`defer`语句调用一个函数, 这个函数的执行被延迟到包裹它的函数返回时, 不管包裹它的函数执行了`return`语句、到达函数体的末尾、相应的goroutine panic了.

``` EBNF
DeferStmt = "defer" Expression .
```

表达式`Expression`必须是一个函数或方法调用; 它不能使用括号包裹. 可以调用的内建函数限制为与表达式语句中一样.

每次`defer`语句执行时, 调用的函数值和参数按常规方式求值并被保存, 但未调用实际函数. 被延迟的函数在包裹它的函数返回前立即调用, 按与被延迟相反的顺序调用. 即, 如果包裹它的函数通过显式的`return`语句返回, 被延迟的函数在`return`语句设置结果参数之后、但在函数返回到它的调用者之前执行. 如果被延迟的函数值是`nil`, 当这个函数被调用时发生panic, 而不是`defer`语句执行时.

例如, 如果被延迟的函数是一个函数字面量, 包裹它的函数有在这个字面量内作用域中命名的结果参数, 被延迟的函数可以在结果参数被返回之前修改它们. 如果被延迟的函数有任何返回值, 它们在函数完成是被丢弃(参见处理panic一节).

``` go
lock(l)
defer unlock(l)  // unlocking happens before surrounding function returns

// prints 3 2 1 0 before surrounding function returns
for i := 0; i <= 3; i++ {
	defer fmt.Print(i)
}

// f returns 42
func f() (result int) {
	defer func() {
		// result is accessed after it was set to 6 by the return statement
		result *= 7
	}()
	return 6
}
```

## 12 [内建函数](https://golang.org/ref/spec#Built-in_functions)

内建函数(built-in function, BIF)是预声明的. 它们与其它函数一样调用, 但一些接受一个类型而不是表达式作为第一个传递参数.

内建函数没有标准的Go类型, 所以它们只能在调用表达式中出现, 不能被作用做函数值.

### [`close`: 关闭](https://golang.org/ref/spec#Close)

对通道`c`, 内建函数`close(c)`几乎不会有只在通道上发送. 如果`c`是一个只能接收的通道, 关闭它是个错误. 在关闭的通道上发送或关闭导致运行时panuc. 关闭`nil`通道也导致运行时panic. 在调用`close`后, 在任何之前发送的值被接收后, 接收操作会无阻塞的返回通道类型的零值. 多值接收操作返回接收到的值和一个通道是否已关闭的指示值.

### [`len`和`cap`: 长度和容量](https://golang.org/ref/spec#Length_and_capacity)

内建函数`len`和`cap`接受多种类型的传递参数, 返回类型`int`的结果. 实现保证返回的值适配`int`.

```
Call      Argument type    Result

len(s)    string type      string length in bytes
          [n]T, *[n]T      array length (== n)
          []T              slice length
          map[K]T          map length (number of defined keys)
          chan T           number of elements queued in channel buffer

cap(s)    [n]T, *[n]T      array length (== n)
          []T              slice capacity
          chan T           channel buffer capacity
```

切片的容量是底层数组中有空间分配的元素的数量. 任何时候均满足关系:

```
0 <= len(s) <= cap(s)
```

`nil`切片、映射或通道的长度是0. `nil`切片或通道的容量是0.

如果`s`是字符串常量, 表达式`len(s)`是常量. 如果`s`的类型是数组或数组指针, 且表达式不包含通道接收或(非常量的)函数调用, 表达式`len(s)`和`cap(s)`是常量; 这种情况下, `s`不被求值. 否则, 调用`len`和`cap`不是常量, `s`被求值.

``` go
const (
	c1 = imag(2i)                    // imag(2i) = 2.0 is a constant
	c2 = len([10]float64{2})         // [10]float64{2} contains no function calls
	c3 = len([10]float64{c1})        // [10]float64{c1} contains no function calls
	c4 = len([10]float64{imag(2i)})  // imag(2i) is a constant and no function call is issued
	c5 = len([10]float64{imag(z)})   // invalid: imag(z) is a (non-constant) function call
)
var z complex128
```

### [`new`: 分配](https://golang.org/ref/spec#Allocation)

内建函数`new`接受类型`T`, 运行时分配这个类型的变量的存储空间, 返回指向它的`*T`类型的值. 变量按初始值一节中描述的方式初始化.

``` go
new(T)
```

例如

``` go
type S struct { a int; b float64 }
new(S)
```

为类型`S`的变量分配存储空间, 并初始化(`a=0`, `b=0.0`), 返回包含位置地址的`*S`类型的值.

### [`make`: 创建切片、映射和通道](https://golang.org/ref/spec#Making_slices_maps_and_channels)

内建函数`make`接受类型`T`, 它必须是切片、映射或通道类型, 可选的后接类型特定的一组表达式. 返回`T`类型的值(不是`*T`类型的值). 内存按初始值一节中描述的方式初始化.

```
Call             Type T     Result

make(T, n)       slice      slice of type T with length n and capacity n
make(T, n, m)    slice      slice of type T with length n and capacity m

make(T)          map        map of type T
make(T, n)       map        map of type T with initial space for approximately n elements

make(T)          channel    unbuffered channel of type T
make(T, n)       channel    buffered channel of type T, buffer size n
```

每个大小床底参数`n`和`m`必须是整数类型或无类型的常量. 常量大小传递参数必须非负, 且可用类型`int`的值表示; 如果是无类型的常量, 被给定类型`int`. 如果提供的`n`和`m`是常量, `n`必须不能大于`m`. 如果运行时`n`是负数或比`m`大, 发生运行时panic.

``` go
s := make([]int, 10, 100)       // slice with len(s) == 10, cap(s) == 100
s := make([]int, 1e3)           // slice with len(s) == cap(s) == 1000
s := make([]int, 1<<63)         // illegal: len(s) is not representable by a value of type int
s := make([]int, 10, 0)         // illegal: len(s) > cap(s)
c := make(chan int, 10)         // channel with a buffer size of 10
m := make(map[string]int, 100)  // map with initial space for approximately 100 elements
```

在映射类型上用大小参数`n`调用`make`会创建初始有`n`个元素的映射. 具体的行为是依赖于实现的.

### [`append`、`copy`: 追加和拷贝切片](https://golang.org/ref/spec#Appending_and_copying_slices)

内建函数`append`和`copy`辅助常见的切片操作. 它们的结果与传递参数引用的内存是否重叠无关.

变长函数`append`追加零个或多个值`x`到类型`S`的值`s`, `S`必须是切片类型, 返回结果切片, 它也是类型`S`的. 值`x`传递给类型`...T`参数, `T`是`S`的元素类型, 相应的参数传递规则适用. 作为一个特殊情况, `append`也接受第一个传递参数可以赋给类型`[]byte`, 第二个传递参数是类型`string`后接`...`. 这是追加字节到字符串的形式.

``` go
append(s S, x ...T) S  // T is the element type of S
```

如果`s`的容量不够接受额外的值, `append`分配一个新的足够大的底层数组来接受已有切片元素和额外的值. 否则, `append`重用底层数组.

``` go
s0 := []int{0, 0}
s1 := append(s0, 2)                // append a single element     s1 == []int{0, 0, 2}
s2 := append(s1, 3, 5, 7)          // append multiple elements    s2 == []int{0, 0, 2, 3, 5, 7}
s3 := append(s2, s0...)            // append a slice              s3 == []int{0, 0, 2, 3, 5, 7, 0, 0}
s4 := append(s3[3:6], s3[2:]...)   // append overlapping slice    s4 == []int{3, 5, 7, 2, 3, 5, 7, 0, 0}

var t []interface{}
t = append(t, 42, 3.1415, "foo")   //                             t == []interface{}{42, 3.1415, "foo"}

var b []byte
b = append(b, "bar"...)            // append string contents      b == []byte{'b', 'a', 'r' }
```

函数`copy`从源`src`中拷贝切片元素到目标`det`, 返回拷贝的元素的数量. 两个传递参数必须有同一的元素类型`T`, 且必须可以赋给类型`[]T`的切片. 拷贝的元素的数量是`len(src)`与`len(dts)`的较小值. 作为一个特殊情况, `copy`也接受目标传递参数可赋给类型`[]type`, 源传递参数是类型`string`的. 这种形式从字符串中拷贝字节到字节切片.

``` go
copy(dst, src []T) int
copy(dst []byte, src string) int
```

示例:

``` go
var a = [...]int{0, 1, 2, 3, 4, 5, 6, 7}
var s = make([]int, 6)
var b = make([]byte, 5)
n1 := copy(s, a[0:])            // n1 == 6, s == []int{0, 1, 2, 3, 4, 5}
n2 := copy(s, s[2:])            // n2 == 4, s == []int{2, 3, 4, 5, 4, 5}
n3 := copy(b, "Hello, World!")  // n3 == 5, b == []byte("Hello")
```

### [`delete`: 删除映射元素](https://golang.org/ref/spec#Deletion_of_map_elements)

内建函数`delete`从映射`m`中删除键`k`的元素. `k`的类型必须可以赋给`m`的键类型.

``` go
delete(m, k)  // remove element m[k] from map m
```

如果映射`m`是`nil`或元素`m[k]`不存在, `delete`不执行任何操作.

### [操作复数](https://golang.org/ref/spec#Complex_numbers)

有三个函数组装和拆解复数. 内建函数`complex`从浮点数的实部和虚部构造复数值, `real`和`imag`从复数值中提取实部和虚部.

``` go
complex(realPart, imaginaryPart floatT) complexT
real(complexT) floatT
imag(complexT) floatT
```

传递参数与返回值的类型是对应的. 对`complex`, 两个传递参数必须是同一个浮点数类型, 返回类型是有相应浮点数部件的复数类型: 对于`float32`传递参数是`complex64`, 对`float64`是`complex128`. 如果只有一个传递参数求值为无类型的常量, 它首先被隐式的转换为另一个传递参数的类型. 如果两个传递参数求值为无类型的常量, 它们必须不是复数值或者虚部为0, 返回值是无类型的复数常量.

对`real`和`imag`, 传递参数必须是复数类型, 返回类型是相应的浮点数类型: 对`complex64`传递参数是`float32`, 对`complex128`是`float64`. 如果传递参数求值为无类型的常量, 它必须是一个数值, 返回值是无类型的浮点数常量.

`real`和`imag`函数构成了`complex`的逆向操作, 所以对复数类型`Z`的值`z`, 有`z == Z(complex(real(z), imag(z))`.

如果这些函数的操作数都是常量, 返回值是常量.

``` go
var a = complex(2, -2)             // complex128
const b = complex(1.0, -1.4)       // untyped complex constant 1 - 1.4i
x := float32(math.Cos(math.Pi/2))  // float32
var c64 = complex(5, -x)           // complex64
var s int = complex(1, 0)          // untyped complex constant 1 + 0i can be converted to int
_ = complex(1, 2<<s)               // illegal: 2 assumes floating-point type, cannot shift
var rl = real(c64)                 // float32
var im = imag(a)                   // float64
const c = imag(b)                  // untyped constant -1.4
_ = imag(3 << s)                   // illegal: 3 assumes complex type, cannot shift
```

### [处理panic](https://golang.org/ref/spec#Handling_panics)

两个内建函数`panic`和`recover`, 辅助报告和处理运行时panic和程序定义的错误状况.

``` go
func panic(interface{})
func recover() interface{}
```

执行函数`F`时, 显式调用`panic`或运行时panic终止`F`的执行. 任何被`F`延迟的函数按常规方式被执行. 然后, `F`的调用者中延迟的函数被执行, 直到执行的goroutine中顶层函数中延迟的函数被执行. 在那一点, 程序被终止, 错误状况被报告, 包括作为传递参数传递给panic的值. 这个终止序列称为 ==panicking==.

``` go
panic(42)
panic("unreachable")
panic(Error("cannot parse"))
```

`recover`函数允许程序管理panicking的goroutine的行为. 假设函数`G`延迟了函数`D`, `D`中调用了`recover`, 在`G`正执行的同一个goroutine中的一个函数中发生了panic. 当被延迟的函数的运行到达`D`时, `D`中调用`recover`的返回值将会是调用`panic`时传入的值. 如果`D`正常返回, 没有调用新的`panic`, panicking序列结束, 恢复正常的执行. 任何被`G`在`D`之前延迟的函数开始执行, `G`的执行通过返回其调用者而终止.

`recover`的返回值是`nil`, 如果下面的任一条件满足:

- `panic`的传递参数是`nil`;
- 这个goroutine不处于panicking状态;
- `recover`没有被延迟的函数直接调用.

下面的示例中`protect`调用和函数传递参数`g`, 保护调用者不受`g`产生的运行时panic影响:

``` go
func protect(g func()) {
	defer func() {
		log.Println("done")  // Println executes normally even if there is a panic
		if x := recover(); x != nil {
			log.Printf("run time panic: %v", x)
		}
	}()
	log.Println("start")
	g()
}
```

### [自举时BIF](https://golang.org/ref/spec#Bootstrapping)

当前的实现中提供了几个自举时有用的内建函数. 这些函数是出于完整性而记录的, 不保证在语言中保留. 它们会返回结果.

```
Function   Behavior

print      prints all arguments; formatting of arguments is implementation-specific
println    like print but prints spaces between arguments and a newline at the end
```

实现约束: `print`和`println`不需要接受任意传递参数类型, 但必须支持打印布尔、数值和字符串类型.

## 13 [包](https://golang.org/ref/spec#Packages)

Go程序是通过链接包而构造的. 包是由一个或多个源文件构造的, 其中声明了包中常量、类型、变量和函数, 以及可以在同一个包中所有文件中可访问的实体. 这些元素可被导出, 并在另一个包中使用.

### [源文件组织](https://golang.org/ref/spec#Source_file_organization)

每个源文件有定义它所属包的包子句, 后接可能为空的一组导入声明, 声明希望使用的其内容的包, 后接一组可能为空的函数、类型、变量和常量声明.

``` EBNF
SourceFile       = PackageClause ";" { ImportDecl ";" } { TopLevelDecl ";" } .
```

### [包子句](https://golang.org/ref/spec#Package_clause)

包子句开始每个源文件, 定义了这个文件所属的包.

``` EBNF
PackageClause  = "package" PackageName .
PackageName    = identifier .
```

`PackageName`必须不能是空标识符.

``` go
package math
```

一组共享相同的`PackageName`的文件构成包的实现. 实现要求一个包的所有源文件在同一个目录下.

### [导入声明](https://golang.org/ref/spec#Import_declarations)

导入声明描述了包含它的源文件依赖于被导入的包中的功能, 允许访问这些包中的导出的标识符. 导入命名了一个用于访问导入包的标识符(`PackageName`)和描述导入包的`ImportPath`.

``` EBNF
ImportDecl       = "import" ( ImportSpec | "(" { ImportSpec ";" } ")" ) .
ImportSpec       = [ "." | PackageName ] ImportPath .
ImportPath       = string_lit .
```

`PackageName`用于限定的标识符中访问被导入包中导出的标识符. 它在文件块中声明. 如果省略了`PackageName`, 它默认为被导入包的包子句中指定的标识符. 如果在名称位置处出现一个显式的点号(`.`), 则被导入包中在包块中声明的所有导出的标识符在源文件的文件块中声明, 必须不带限定的访问.

`ImportPath`的解释是依赖于实现的, 但通常是已编译包的完整文件名称的子字符串, 是相对于已安装包的仓库的.

实现约束: 编译器限制`ImportPaths`只能使用属于Unicode L/M/N/P/S通用类别的字符(不带空白的图形字符)的非空字符串, 不包括字符``` !"#$%&'()*,:;<=>?[\]^`{|} ```和`U+FFFD`.

假设已编译了包含包子句`package math`的包, 它导出了函数`Sin`, 并安装在由`"lib/math"`标识的文件中. 下面展示了如何在导入这个包的文件中访问`Sin`:

``` go
Import declaration          Local name of Sin

import   "lib/math"         math.Sin
import m "lib/math"         m.Sin
import . "lib/math"         Sin
```

导入声明声明了导入包与被导入包之间的依赖关系. 一个包直接或间接的导入自身是非法的, 导入包但没有引用它导出的标识符是非法的. 当只为使用包的副作用(初始化)而导入包时, 使用空标识符作为显式的包名称:

``` go
import _ "lib/math"
```

### [一个示例包](https://golang.org/ref/spec#An_example_package)

这是一个实现了并发素筛(prime sieve)的完整的Go包.

``` go
package main

import "fmt"

// Send the sequence 2, 3, 4, … to channel 'ch'.
func generate(ch chan<- int) {
	for i := 2; ; i++ {
		ch <- i  // Send 'i' to channel 'ch'.
	}
}

// Copy the values from channel 'src' to channel 'dst',
// removing those divisible by 'prime'.
func filter(src <-chan int, dst chan<- int, prime int) {
	for i := range src {  // Loop over values received from 'src'.
		if i%prime != 0 {
			dst <- i  // Send 'i' to channel 'dst'.
		}
	}
}

// The prime sieve: Daisy-chain filter processes together.
func sieve() {
	ch := make(chan int)  // Create a new channel.
	go generate(ch)       // Start generate() as a subprocess.
	for {
		prime := <-ch
		fmt.Print(prime, "\n")
		ch1 := make(chan int)
		go filter(ch, ch1, prime)
		ch = ch1
	}
}

func main() {
	sieve()
}
```

## 14 [程序初始化和执行](https://golang.org/ref/spec#Program_initialization_and_execution)

### [零值](https://golang.org/ref/spec#The_zero_value)

通过声明或调用`call`给变量分配存储空间, 或者通过复合字面量或调用`make`创建新的值, 没有提供显式的初始化, 变量或值被给予一个默认值. 每个这样的标量或值的元素被设置为其类型的零值(zero value): 对布尔是`false`, 对数值类型是0, 对字符串是`""`, 对指针、函数、接口、切片、通道和映射是`nil`. 初始化是递归的, 所以对一个结构的数组中没有元素, 如果没有指定值时, 字段均有零值.

下面的声明式等价的:

``` go
var i int
var i int = 0
```

``` go
type T struct { i int; f float64; next *T }
t := new(T)
```

有:

``` go
t.i == 0
t.f == 0.0
t.next == nil
```

下面的声明也一样:

``` go
var t T
```

### [包初始化](https://golang.org/ref/spec#Package_initialization)

在包中, 包级的变量初始化按步骤处理, 在每个步骤中选择在声明顺序中最早, 且没有对未初始化的变量的依赖的变量.

更准确的将, 包级变量视为准备好初始化的, 如果它还没有被初始化, 没有初始化表达式或者它的初始化表达式没有对未初始化变量的依赖. 重复的处理下一个在声明顺序中最早的且准备好初始化的包级变量, 直到没有变量准备好初始化.

如果这个过程结束时仍有变量未初始化, 这些变量是一个或多个初始化环的一部分, 程序不是有效的.

左侧有多个变量的变量声明, 用右侧单个(多值的)表达式初始化, 这些变量被一起初始化: 如果左侧的任何变量被初始化, 所有这些变量在同一步骤中被初始化.

``` go
var x = a
var a, b = f() // a and b are initialized together, before x is initialized
```

在包初始化中, 空变量被视为任意其它声明中的变量.

在多个文件中声明的变量的声明顺序是由这些文件被呈现给编译器的顺序确定的. 在第一个文件中声明的变量是在第二个文件中声明的变量之前声明的, 以此类推.

依赖性分析不依赖于变量的实际值, 只依赖于它们在源码中的传递性的词法引用. 例如, 变量`x`的初始化表达式引用了一个函数, 其函数体引用了变量`y`, 则`x`依赖于`y`. 详细的说:

- 对变量或函数的引用是表示该变量或函数的标识符.
- 对方法`m`的引用时方法值或形式为`t.m`的方法表达式, 这里`t`的(静态)类型不是借口类型, 方法`m`在`t`的方法集中. 结果函数值`t.m`是否被调用是无关的.
- 如果变量、函数或方法`x`的初始化表达式或体(对函数和方法而言)包含对变量`y`的引用或对依赖于变量`y`的函数或方法的引用, 则`x`依赖于变量`y`.

例如, 给定声明

``` go
var (
	a = c + b  // == 9
	b = f()    // == 4
	c = f()    // == 5
	d = 3      // == 5 after initialization has finished
)

func f() int {
	d++
	return d
}
```

初始化顺序是`d`、`b`、`c`、`a`. 注意初始化表达式中的子表达式的顺序是无关的: `a = c + b`与`a = b + c`在这个例子中产生相同的初始化顺序.

依赖分析在每个包中进行; 只考虑在当前保重声明的变量、函数和(非接口)方法. 如果变量之间存在其它隐藏的数据依赖, 这些变量之间的初始化顺序是未描述的.

例如, 给定声明

``` go
var x = I(T{}).ab()   // x has an undetected, hidden dependency on a and b
var _ = sideEffect()  // unrelated to x, a, or b
var a = b
var b = 42

type I interface      { ab() []int }
type T struct{}
func (T) ab() []int   { return []int{a, b} }
```

变量`a`将在`b`之后初始化, 但`x`是否在`b`之前、在`b`和`a`之间或在`a`之后、`sideEffect()`被调用时初始化, 是未描述的.

变量也可以用在包块中声明的`init`函数初始化, 这个函数没有传递参数和返回参数.

``` go
func init() { … }
```

可以在每个包中、单个源文件中定义多个`init`函数. 在包块中, 标识符`init`只能被用于声明`init`函数, 这个标识符本身不是声明的. 因此`init`函数不能在程序中其它位置引用.

没有导入(声明)的包的初始化, 首先是赋予所有包级变量以初始化, 然后按在源文件中的顺序调用`init`函数, 如果多个文件中有`init`函数, 依据文件呈现给编译器的顺序. 如果包中有导入(声明), 被导入的包在初始化这个包之前被初始化. 如果多个包导入了同一个包, 被导入的包只会被初始化一次. 包的导入, 通过构造保证不存在循环初始化依赖.

包初始化, 包括变量初始化和对`init`函数的调用, 在单个goroutine中一次一个包的顺序进行. `init`函数可以加载其它goroutine, 它可以与初始化代码并发运行. 然而, 初始化总是顺序的调用`init`函数: 在前一个返回之前不会调用下一个.

为确保初始化行为的可重复性, 推荐构建系统将属于同一个包的多个文件按文件名的词法顺序呈现给编译器.

### [程序执行](https://golang.org/ref/spec#Program_execution)

完整的程序是通过链接一个称为`main`包的单个未被导入的包和其它它导入的包创建的(导入是传递性的). `main`包必须有包名`main`, 且声明没有传递参数、不返回值的函数`main`.

``` go
func main() { … }
```

程序执行从初始化`main`包开始, 然后调用函数`main`. 当函数`main`的调用返回时, 程序结束. 不等待其它(非`main`)goroutine完成.

## 15 [错误](https://golang.org/ref/spec#Errors)

预声明的类型`error`定义为

``` go
type error interface {
	Error() string
}
```

它是表示错误状况的接口, `nil`值表示没有错误. 例如, 从文件中读取数据的函数可以定义为:

``` go
func Read(f *File, b []byte) (n int, err error)
```

## 16 [运行时panic](https://golang.org/ref/spec#Run_time_panics)

执行错误, 例如尝试在数组边界外的索引操作, 会触发运行时panic, 这等价于以实现定义的接口类型`runtime.Error`的值作为传递参数调用内建函数`panic`. 这个类型满足预声明的接口类型`error`. 表示不同运行时错误状况的具体的错误值是未描述的.

``` go
package runtime

type Error interface {
	error
	// and perhaps other methods
}
```

## 17 [系统的考虑](https://golang.org/ref/spec#System_considerations)

### [包`unsafe`](https://golang.org/ref/spec#Package_unsafe)

内建包`unsafe`, 被编译器感知, 可通过导入路径`"unsafe"`访问, 提供了包括违背类型系统的操作的底层编程工具. 使用`unsafe`的包必须被仔细检查类型安全性, 不是可移植的. 保重提供了这些接口:

``` go
package unsafe

type ArbitraryType int  // shorthand for an arbitrary Go type; it is not a real type
type Pointer *ArbitraryType

func Alignof(variable ArbitraryType) uintptr
func Offsetof(selector ArbitraryType) uintptr
func Sizeof(variable ArbitraryType) uintptr
```

`Pointer`是指针类型, 但`Pointer`值不能被解引用. 底层类型`uintptr`的指针或值可以被转换为具有底层类型`Pointer`的类型, 反之亦然. 在`Pointer`与`uintptr`之间转换的效果是由实现定义的.

``` go
var f float64
bits = *(*uint64)(unsafe.Pointer(&f))

type ptr unsafe.Pointer
bits = *(*uint64)(ptr(&f))

var p ptr = nil
```

函数`Alignof`和`Sizeof`接受任意类型的表达式`x`, 分别返回对齐和大小, 就像有一个假想的用`var v = x`声明的变量`v`.

函数`Offsetof`接受(可能包裹在括号中的)选择器`s.f`, 表示用`s`或`*s`表示的结构的字段`f`, 返回这个字段相对于结构的地址的偏移量. 如果`f`是内嵌字段, 它必须可以不用指针间接寻址的通过结构的字段可达. 对有字段`f`的结构`s`:

``` go
uintptr(unsafe.Pointer(&s)) + unsafe.Offsetof(s.f) == uintptr(unsafe.Pointer(&s.f))
```

计算机体系结构可能要求内存地址是对齐的(alogned); 即, 变量的地址是变量的类型的对齐长度(alignment)的倍数. 函数`Alignof`接受表示任意类型的变量的表达式, 返回变量的类型的字节对齐长度. 对变量`x`:

``` go
uintptr(unsafe.Pointer(&x)) % unsafe.Alignof(x) == 0
```

对`Alignof`、`Offsetof`和`Sizeof`的调用是编译时类型`uintptr`的常量表达式.

### [大小和对齐保证](https://golang.org/ref/spec#Size_and_alignment_guarantees)

对数值类型, 保证有下面的大小:

``` go
type                                 size in bytes

byte, uint8, int8                     1
uint16, int16                         2
uint32, int32, float32                4
uint64, int64, float64, complex64     8
complex128                           16
```

保证有下面最小对齐属性:

1. 对任何类型的标量`x`: ` unsafe.Alignof(x)`至少为1.
2. 对结构类型的变量`x`: `unsafe.Alignof(x)`是`x`的字段`f`上`unsafe.Alignof(x.f)`最大的值, 但至少为1.
3. 对数组类型的变量`x`: `unsafe.Alignof(x)`与对齐数组的元素类型的变量一样.

如果结构或数组类型没有大小大于0的字段或元素, 它的大小为0. 两个不同的大小为0的变量在内存中可以有相同的地址.
