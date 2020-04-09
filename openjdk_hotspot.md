# HotSpot

- 术语: [HotSpot Glossary of Terms](http://openjdk.java.net/groups/hotspot/docs/HotSpotGlossary.html)
- 存储管理: [Storage Management](http://openjdk.java.net/groups/hotspot/docs/StorageManagement.html)
- Johannes Kepler University (JKU): [Compiler and JVM Research at JKU](http://ssw.jku.at/Research/Projects/JVM/)

## 主要的组件

1 运行时(Runtime)

- 解释器(Interpreter)
- 线程管理(Thead Management)
- 同步(Synchronization)
- 类加载(Class loading)

2 堆管理(Heap management)

- 垃圾收集器(Garbage collectors)

3 编译系统(Compilation system)


## 运行时

> [Runtime](https://wiki.openjdk.java.net/display/HotSpot/Runtime)

- 运行时概览
- 应用类数据共享: AppCDS
- 异步监视器平坦化
- 调用序列
- 压缩的通用对象指针: Compressed ordinary object pointer
- 命令行标志
- Java控制栈
- 方法处理和动态调用
- 原生监视器设计
- 同步


### HotSpot运行时概览

> [HotSpot Runtime Overview](http://openjdk.java.net/groups/hotspot/docs/RuntimeOverview.html)

- 命令行参数解析
- VM生命周期
- VM类加载
- 字节码校验器和格式检查器
- 类数据共享
- 解释器
- Java异常处理
- 同步
- 线程管理
- C++堆管理
- JNI: Java Native Interface
- VM致命错误处理
