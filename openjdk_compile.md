# OpenJDK compile

- repository: [openjdk/jdk](https://github.com/openjdk/jdk)
- code fix: [【Java】macOS 下编译 JDK8](https://swsmile.info/2018/11/12/%E3%80%90Java%E3%80%91%E7%BC%96%E8%AF%91%E4%B8%8E%E5%8F%8D%E7%BC%96%E8%AF%91-macOS%E4%B8%8B%E7%BC%96%E8%AF%91JDK8/)

## 菜谱

- configure

```
./configure --with-debug-level=slowdebug  --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home --with-freetype-include=/usr/local/Cellar/freetype/2.9.1/include/freetype2 --with-freetype-lib=/usr/local/Cellar/freetype/2.9.1/lib --with-target-bits=64 --with-jvm-variants=server --with-jdk-variant=normal --with-milestone=internal  --with-num-cores=2 --with-jobs=4 CC=clang CXX=clang++
```

```
====================================================
A new configuration has been successfully created in
/Users/zhoujiagen/jvm/hotspot/jdk-jdk8-b120/build/macosx-x86_64-normal-server-slowdebug
using configure arguments '--with-debug-level=slowdebug --with-boot-jdk=/Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home --with-freetype-include=/usr/local/Cellar/freetype/2.9.1/include/freetype2 --with-freetype-lib=/usr/local/Cellar/freetype/2.9.1/lib --with-target-bits=64 --with-jvm-variants=server --with-jdk-variant=normal --with-milestone=internal --with-num-cores=2 --with-jobs=4 CC=clang CXX=clang++'.

Configuration summary:
* Debug level:    slowdebug
* JDK variant:    normal
* JVM variants:   server
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64

Tools summary:
* Boot JDK:       java version "1.8.0_172" Java(TM) SE Runtime Environment (build 1.8.0_172-b11) Java HotSpot(TM) 64-Bit Server VM (build 25.172-b11, mixed mode)  (at /Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home)
* C Compiler:     Apple LLVM version (clang-902.0.39.1) version 9.1.0 (clang-902.0.39.1) (at /usr/bin/clang)
* C++ Compiler:   Apple LLVM version (clang-902.0.39.1) version 9.1.0 (clang-902.0.39.1) (at /usr/bin/clang++)

Build performance summary:
* Cores to use:   4
* Memory limit:   16384 MB
* ccache status:  installed, but disabled (version older than 3.1.4)
```

- lib fix

```
sudo ln -s /Library/Developer/CommandLineTools/usr/lib/libclang.dylib /usr/local/lib/libclang.dylib
```

- make

```
make COMPILER_WARNINGS_FATAL=false LFLAGS='-Xlinker -lstdc++' CC=clang USE_CLANG=true LP64=1
```
