# Notes of JavaCC

## 资源

- Tom Copeland. Generating Parsers with JavaCC. Centennial Books, 2009.
- [javacc](https://github.com/javacc/javacc): JavaCC - a parser generator for building parsers from grammars. It can generate code in Java, C++ and C#.
- [ph-javacc-maven-plugin](https://github.com/phax/ph-javacc-maven-plugin): An updated version of the javacc-maven-plugin using ParserGeneratorCC

### Table of Contents for the Book

```
Perface
1 Introduction to JavaCC
2 Tokenizing
3 Parsing
4 JJTree
5 JJDoc
6 JavaCC and Unicode
7 Error Handling
8 Case Study: The JavaCC Grammar
9 Testing JavaCC Parsers
10 JavaCC and eclipse
11 Little Grammars
References
A. JavaCC Directory Layout and Building JavaCC
B. Options
Index
```

## Example


``` java
// 选项
options
{
  JDK_VERSION = "1.8";
}

// 解析器类定义
PARSER_BEGIN(SimpleParser)
import java.io.*;

public class SimpleParser
{
  public static void main(String [] args) throws ParseException
  {
    String someInput = "a";
    StringReader sr = new StringReader(someInput);
    Reader r = new BufferedReader(sr);
    SimpleParser parser = new SimpleParser(r);
    parser.A();
  }
}

PARSER_END(SimpleParser)

// 词法描述
TOKEN :
{
  < A : "a" >
}

// 语法描述
void A() :
{}
{
  < A >
  {
    System.out.println("Found a 'a'!");
  }
}
```

## Tokenizing


词法描述的构成:

- 正则表达式生成(regular expression productions): 正则表达式定义构成特定`Token`的字符序列.
- 词法状态(lexical state): 正则表达式生成的容器, 可以执行状态间切换.

四个特殊的正则表达式生成:

- `TOKEN`: 识别正则表达式生成`Token`. 使用`TOKEN_MGR_DECLS`编写处理识别出的token的代码.
- `SKIP`: 用于丢弃不需要的输入.
- `MORE`: 识别当前输入但不消费, 追加到下一个创建的`Token`中.
- `SPECIAL_TOKEN`: 识别当前输入但不消费, 追加到下一个创建的`Token`的`specialToken`字段中.


JavaCC中正则表达式:

|名称|示例|说明|
|:---|:---|:---|
|字面量|`"a"`||
|字符类|`["a", "b", "c"]`||
|范围字符类|`["a"-"z"]`||
|取反|`~["a"]`||
|重复|`("a"){4}`||
|范围重复|`("a"){2,4}`||
|一个或多个项|`("a")+`||
|零个或一个项|`("a")?`||
|零个或多个项|`("a")*`||
|或| `["a"-"z"] | ["x"-"z"]`||

处理多个匹配的两个规则:

- the maximal munch rule
- the first matching token definition rule

词法动作: 在词法描述中插入Java代码, 当遇到特定token定义时执行.

## Parsing
