# OpenJDK HotSpot RTFSC

## 项目结构

- [jdk8u252-b08](https://github.com/AdoptOpenJDK/openjdk-jdk8u)

```
├── cpu
├── os
├── os_cpu
└── share
    ├── tools
    └── vm
        ├── adlc                        // Architecture Description Language Compiler
        ├── asm                         // platform-independent assembler
        ├── c1                          // C1 Compiler
        ├── ci                          // 动态编译器???
        ├── classfile                   // 类加载器和类文件
        ├── code                        // 机器码生成???
        ├── compiler                    // 调用动态编译器的接口???
        ├── gc_implementation           // GC实现
        │   ├── concurrentMarkSweep
        │   ├── g1
        │   ├── parNew
        │   ├── parallelScavenge
        │   └── shared
        ├── gc_interface                // GC接口
        ├── interpreter                 // 解释器
        ├── libadt                      // 抽象数据结构
        ├── memory                      // 内存管理
        ├── oops                        // VM内部对象表示
        ├── opto                        // C2 Compiler
        ├── precompiled
        ├── prims                       // 对外接口???
        ├── runtime                     // 运行时
        ├── services                    // Serviceability
        ├── shark                       // LLVM based Compiler
        ├── trace
        └── utilities
```


