# 工具

[TOC]

## Eclipse CDT

- [Effective Eclipse CDT](https://www.jianshu.com/p/dafcdce1f9cb)
- [Eclipse CDT代码自动提示](https://blog.csdn.net/virus026/article/details/17226371)
- [Mac配置Eclipse CDT的Debug出现的问题](https://blog.csdn.net/lllkey/article/details/79433166)
- [eclipse 中使用autotools plugins](https://www.cnblogs.com/respawn/archive/2012/07/16/2593030.html)
- [How to setup gdb and Eclipse to debug C++ files on macOS Sierra](https://www.thomasvitale.com/how-to-setup-gdb-and-eclipse-to-debug-c-files-on-macos-sierra/)

- [降级安装gdb 8.0.1](https://juejin.im/post/5acdc6bef265da239a6029f4)

```
brew unlink gdb
brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/9ec9fb27a33698fc7636afce5c1c16787e9ce3f3/Formula/gdb.rb
brew pin gdb
```

## Ubuntu

- 设置环境变量: `/etc/profile.d`
- [Apt](https://help.ubuntu.com/lts/serverguide/apt.html): `/etc/apt/sources.list`, `/etc/apt/sources.list.d`
- [Ubuntu Backports](https://help.ubuntu.com/community/UbuntuBackports)

example: [libseccomp-dev](https://launchpad.net/ubuntu/trusty/+package/libseccomp-dev)

```
# 在/etc/apt/sources.list中修改或添加
deb http://archive.ubuntu.com/ubuntu trusty-backports main restricted universe multiverse
```

```
# 添加文件/etc/apt/preferences.d/libseccomp.pref
Package: *
Pin: release a=trusty-backports
Pin-Priority: 100
```

```
# 安装
$ sudo apt-get install libseccomp-dev/trusty-backports
```

## GNU

- [GNU Software](https://www.gnu.org/software/)
- [GNU coding standards](https://www.gnu.org/prep/standards/)
- Autotools: 见[GNU Autotools](/build-tools/autotools).


Error:

```
$ ./autogen.sh
Makefile.am: error: required file './NEWS' not found
Makefile.am: error: required file './README' not found
Makefile.am: error: required file './AUTHORS' not found
Makefile.am: error: required file './ChangeLog' not found
src/Makefile.am:3: warning: source file 'threads/example_posix_thread_attr.c' is in a subdirectory,
src/Makefile.am:3: but option 'subdir-objects' is disabled
automake: warning: possible forward-incompatibility.
automake: At least a source file is in a subdirectory, but the 'subdir-objects'
automake: automake option hasn't been enabled.  For now, the corresponding output
automake: object file(s) will be placed in the top-level directory.  However,
automake: this behaviour will change in future Automake versions: they will
automake: unconditionally cause object files to be placed in the same subdirectory
automake: of the corresponding sources.
automake: You are advised to start using 'subdir-objects' option throughout your
automake: project, to avoid future incompatibilities.
autoreconf: automake failed with exit status: 1

```

Solution:

```
$ touch NEWS README AUTHORS ChangeLog

# 添加AUTOMAKE_OPTIONS
$ cat src/Makefile.am
AUTOMAKE_OPTIONS = subdir-objects
bin_PROGRAMS=a.out example_posix_thread_attr.out
a_out_SOURCES=posix-in-action.c
example_posix_thread_attr_out_SOURCES=threads/example_posix_thread_attr.c
```
