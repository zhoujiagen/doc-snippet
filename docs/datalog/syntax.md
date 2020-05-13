# Datalog的语法

Datalog is a declarative logic programming language that syntactically is a subset of Prolog. It is often used as a query language for deductive databases. In recent years, Datalog has found new application in data integration, information extraction, networking, program analysis, security, and cloud computing.[^1]


Racket的Datalog Module Language语法如下[^2]:

``` EBNF
(* 程序, 语句, 断言, 收回, 查询 *)
‹program› ::= ‹statement›*
‹statement› ::= ‹assertion› |  ‹retraction› |  ‹query›
‹assertion› ::= ‹clause› .
‹retraction› ::= ‹clause› ~
‹query› ::= ‹literal› ?

(* 从句 *)
‹clause› ::= ‹literal› :- ‹body› | ‹literal›
(* 体 *)
‹body› ::= ‹literal› , ‹body› | ‹literal›
(* 字面量 *)
‹literal› ::= ‹predicate-sym› ( )
            | ‹predicate-sym› ( ‹terms› )
            | ‹predicate-sym›
            | ‹term› = ‹term›
            | ‹term› != ‹term›
(* 谓词符号 *)
‹predicate-sym› ::= ‹IDENTIFIER› | ‹STRING›
(* 项 *)
‹terms› ::= ‹term› | ‹term› , ‹terms›
‹term› ::=  ‹VARIABLE› | ‹constant›
(* 常量 *)
‹constant› ::= ‹IDENTIFIER› | ‹STRING›
```

- 以`%`开始行注释.
- `‹VARIABLE›`是大写和小写的Unicode字母、数字和下划线构成的序列. 必须以大写Unicode字母开始.
- `‹IDENTIFIER›`是可打印字符的序列, 不包含: `(`、``` ` ``` 、`'`、`)`、`=`、`:`、`.`、`~`、`?`、`"`、`%`和空格.
- `‹STRING›`是被双引号包裹的字符序列. 需要使用转义字符`\`转义`"`、换行和`\`.
- `‹literal›`是一个谓词符号`‹predicate-sym›`, 后接括号以及可选的逗号分隔的项`‹term›`列表. 特别的, 由`=`(`!=`)分隔的两个`‹term›`是相等(不相等)谓词的字面量`‹literal›`.
- `‹term›`是变量`‹VARIABLE›`或常量`‹constant›`.
- `‹predicate-sym›`是标识符`‹IDENTIFIER›`或字符串`‹STRING›`.
- `‹constant›`是标识符`‹IDENTIFIER›`或字符串`‹STRING›`.
- `‹clause›`是头部`‹literal›`, 后接可选的体`‹body›`. `‹body›`是逗号分隔的字面量`‹literal›`. 没有体的`‹clause›`是 **事实(fact)**, 有体时是 **规则(rule)**. `:-`将规则中头部和体分开. 如果`‹clause›`头部出现的所有变量均在其体中出现, 称它是 **安全的**.
- `‹program›`由语句`‹statement›`构成.
- `‹statement›`是断言`‹assertion›`, 或者收回`‹retraction›`, 或者查询`‹query›`.
- `‹assertion›`是以`.`结尾的`‹clause›`.
- `‹retraction›`是以`~`结尾的`‹clause›`, 从数据库中移除`‹clause›`.
- `‹query›`是以`?`结尾的`‹literal›`.

示例: `‹literal›`

``` prolog
parent(john, douglas)
zero-arity-literal
"="(3,3)
""(-0-0-0,&&&,***,"\00")
```

示例: 安全的`‹clause›`

``` prolog
parent(john, douglas)

ancestor(A, B) :-
  parent(A, B)
ancestor(A, B) :-
  parent(A, C),
  ancestor(C, B)
```


示例: `‹program›`

``` prolog
#lang datalog

% Example from https://docs.racket-lang.org/datalog/datalog.html

edge(a, b).
edge(b, c).
edge(c, d).
edge(d, a).

path(X, Y) :- edge(X, Y).
path(X, Y) :- edge(X, Z), path(Z, Y).

path(X, Y)?
```


[^1]: https://en.wikipedia.org/wiki/Datalog
[^2]: https://docs.racket-lang.org/datalog/datalog.html
