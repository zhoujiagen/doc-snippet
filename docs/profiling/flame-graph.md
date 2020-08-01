# Flame Graph

## 资源

- [The Flame Graph](https://queue.acm.org/detail.cfm?id=2927301): an ACMQ article.
- [Flame Graphs visualize profiled code](https://github.com/brendangregg/FlameGraph): on Github.


## Flame Graph Explained and Interpretation

火焰图有如下特征:

- 调用栈用一列矩形框表示, 每个矩形框表示一个函数(栈帧).
- y轴展示栈深度, 根栈帧在底部, 叶栈帧在顶部. 顶部的矩形框表示收集调用栈时on-CPU的函数, 在其下的矩形框表示它的祖先.
- x轴横跨调用栈集合, 不是按时间先后展示的, 从左到右的顺序没有特殊的含义. 从左到右将函数名称按字典序排列. 当水平相邻的函数是相同的时合并矩形框.
- 每个函数矩形框的宽度展示它在调用栈中或在其祖先函数中出现的频次 .
- 如果矩形框的宽度足够大, 会展示函数的名称.
- 每个矩形框的背景色不是重要的, 按暖色调随机选取.
- 可能跨越单个线程、多个线程、多个应用或多个主机.
- 调用栈可以从多个探查目标中收集, 其宽度可以表示度量值而不是抽样数量. 例如, 一个探查器可以度量线程被阻塞的时间和它的调用栈. 这可以可视化为火焰图, x轴横跨整个被阻塞时间, 火焰图展示了阻塞代码路径.


按如下方式解读火焰图:

- 顶层的边展示了收集调用栈时在CPU上运行的函数.
- 沿着顶层边查找大的高原, 它展示了在这次探查中运行频率较高的调用栈.
- 从上往下读函数的祖先.
- 函数的矩形框的宽度可以直接用来比较: 宽的表示在探查中出现频率较高, 值得首先考察.
- 按时间采样调用栈探查CPU时, 如果一个函数的矩形框交款, 可能是因为它每个函数调用消耗更多CPU或者这个函数被更频繁的调用.
- 火焰图中的分支, 表现为单个函数上两个或多个大的塔, 对分析很有用. 它们可以指出隶属于一个逻辑组的代码, 也可能是由条件语句产生的.



解读示例:

[Interpretation Example](https://dl.acm.org/cms/attachment/da59940d-0241-44f3-a32e-1e8c6fbbf0c4/gregg6.png)

```
     +----+----+----+
     |      g()     |
+----+----+----+----+
| e()|      f()     |
+----+----+----+----+----+----+
|           d()               |
+----+----+----+----+----+----+----+----+
|           c()               |   i()   |
+----+----+----+----+----+----+----+----+
|           b()               |   h()   |
+----+----+----+----+----+----+----+----+
|                 a()                   |
+----+----+----+----+----+----+----+----+
```

- 顶层边表示`g()`最常on-CPU.
- `d()`较宽, 但它暴露出的顶层边on-CPU最少.
- `b()`和`c()`在采样中没有直接on-CPU.
- `g()`之下的函数表示它的祖先: `g()`被`f()`调用, `f()`被`d()`调用, 等等.
- 视觉上`b()`与`h()`的宽度表示on-CPU的`b()`代码路径几乎是`h()`代码路径的四倍. 实际on-CPU的函数是它们调用的函数.
- 代码中主要的分支在`a()`调用`b()`和`h()`时.


## Play in the Wild World

### MySQL示例

[MySQL Profiler Output as a Flame Graph](https://queue.acm.org/downloads/2016/Gregg4.svg)



```
# 1 Capture stack with Linux perf_events
# PID 181, 60 second, 99 Hertz
$ perf record -F 99 -p 181 -a -g -- sleep 60
$ perf script > out.perf

# fold stack for Linux perf_event "perf script" output
$ ./stackcollapse-perf.pl ../mysql-out.perf > ../mysql-out.folded

# render a SVG
$ ./flamegraph.pl ../mysql-out.folded > ../mysql.svg
```
