# GNU Make

## 资料

- [Managing Projects with GNU Make 3rd Edition by Robert Mecklenburg](https://resources.oreilly.com/examples/9780596006105)
- [What is the difference between the GNU Makefile variable assignments =, ?=, := and +=?](https://stackoverflow.com/questions/448910/what-is-the-difference-between-the-gnu-makefile-variable-assignments-a)


## 规则

``` Makefile
target1 target2 target3 : prerequisite1 prerequisite2
    command1
    command2
    command3
```

显示规则(explicit rules)
模式规则(pattern rules): 使用通配符, 而不是明确的文件名
静态模式规则: 模式规则, 但只应用于特定的目标文件列表
后缀规则(suffix rules): obsolete, 编写通用规则的方法
隐式规则(implicit rules): 模式规则, 或内建的后缀规则


``` Makefile
# 独立的两个规则
vpath.o: make.h config.h getopt.h gettext.h dep.h
variable.o: make.h config.h getopt.h gettext.h dep.h
```

``` Makefile
# 同一目标的两条规则
vpath.o: vpath.c make.h config.h getopt.h gettext.h dep.h
vpath.o: filedef.h hash.h job.h commands.h variable.h vpath.h
```

通配符(wildcard/globbing)

```
~, *, ?, [...], [^...]
```

``` Makefile
prog: *.c
    $(CC) -o $@ $^
```

伪目标(phony target): 不表示文件的目标; 总是out of date
标准的伪目标: all, install, clean, distclean, TAGS, info, check

``` Makefile
.PHONY: clean
clean:
    rm -f *.o lexer.c
```

空目标(empty target): 空文件

``` Makefile
prog: size prog.o
    $(CC) $(LDFLAGS) -o $@ $^


size: prog.o
    size $^
    touch size # 空文件
```

变量: `$(variable-name)`, `${variable-name}`, `$single-character-variable-name`

自动变量(automatic variables): 规则匹配时由make设置.

- `$@` <br>The filename representing the target.<br>
目标文件名
- `$*` <br>The stem of the target filename. A stem is typically a filename without its suffix. Its use outside of pattern rules is discouraged.<br>
目标文件名的无后缀名称
- `$%` <br>The filename element of an archive member specification.<br>
目标是归档成员时的成员名, 例目标是foo.a(bar.o), `$%`是bar.o, `$@`是foo.a; 目标不是归档成员时`$%`为空.
- `$<` <br>The filename of the first prerequisite.<br>
第一个前提文件名
- `$?` <br>The names of all prerequisites that are newer than the target, separated by spaces.<br>
比目标新的所有前提文件名
- `$^` <br>The filenames of all the prerequisites, separated by spaces. This list has duplicate filenames removed since for most uses, such as compiling, copying, etc.,duplicates are not wanted.<br>
所有前提文件名
- `$+` <br>Similar to `$^`, this is the names of all the prerequisites separated by spaces, except that $+ includes duplicates. This variable was created for specific situations such as arguments to linkers where duplicate values have meaning.<br>
可能重复的所有前提文件名


返回值的目录部分: ```$(@D), $(<D), …```
返回值的文件部分: ```$(@F), $(<F), ...```

``` Makefile
count_words: count_words.o counter.o lexer.o -lfl
    gcc $^ -o $@
count_words.o: count_words.c
    gcc -c $<
counter.o: counter.c
    gcc -c $<
lexer.o: lexer.c
    gcc -c $<
lexer.c: lexer.l
    flex -t $< > $@
```

VPATH变量和vpath指令

``` Makefile
# 指定源文件目录
VPATH = src
```

``` Makefile
# 指令格式: vpath pattern directory list
vpath %.c src
vpath %.l src
vpath %.h include
```

模式规则

%表示任意数量的字符, 在模式中只能出现一次.

``` Makefile
# 由xxx.c生成xxx.o文件
%.o: %.c
    $(COMPILE.c) $(OUTPUT_OPTION) $<

# 由xxx.o生成xxx文件
%: %.o
    $(LINK.o) $^ $(LOADLIBES) $(LDLIBS) -o $@
```

静态模式规则: 只能应用于特定的目标

``` Makefile
# OBJECTS变量中指定的文件
$(OBJECTS): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@
```

后缀规则: obsolete, 兼容性目的支持

``` Makefile
.c.o:
    $(COMPILE.c) $(OUTPUT_OPTION) $<
# 等价于
%.o: %.c
    $(COMPILE.c) $(OUTPUT_OPTION) $<
```

``` Makefile
.p:
    $(LINK.p) $^ $(LOADLIBES) $(LDLIBS) -o $@
# 等价于
%: %.p
    $(LINK.p) $^ $(LOADLIBES) $(LDLIBS) -o $@
```

特殊的目标

``` Makefile
.SUFFIXES: .out .a .ln .o .c .cc .C .cpp .p .f .F .r .y .l
```

隐式规则数据库

- --print-data-base/-p: 查看模式规则
无命令脚本的模式规则将规则从数据库中移除.
- --just-print/-n: 查看构建动作
- --no-builtin-rules/-r: 不使用内建规则
- --no-builtin-variables/-R: 不使用内建变量

``` Makefile
# 示例隐式规则
%.o: %.c
$(COMPILE.c) $(OUTPUT_OPTION) $<
# 变量
COMPILE.c = $(CC) $(CFLAGS) $(CPPFLAGS) $(TARGET_ARCH) -c
CC = gcc
OUTPUT_OPTION = -o $@
# 在Makefile中覆盖
CPPFLAGS = -I project/include
# 在命令中覆盖, 自定义变量INCLUDES
$ make CPPFLAGS=-DDEBUG
COMPILE.c = $(CC) $(CFLAGS) $(INCLUDES) $(CPPFLAGS) $(TARGET_ARCH) -c
INCLUDES = -I project/include
```

特殊的目标(12个):

- .PHONY
- .INTERMEDIATE
- .SECONDARY
- .PRECIOUS
- .DELETE_ON_ERROR
- .EXPORT_ALL_VARIABLES


自动生成依赖

``` Makefile
VPATH = src include
CPPFLAGS = -I include
SOURCES = count_words.c \
    lexer.c \
    counter.c
count_words: counter.o lexer.o -lfl
count_words.o: counter.h
counter.o: counter.h lexer.h
lexer.o: lexer.h

include $(subst .c,.d,$(SOURCES))
# 使用gcc -M生成依赖xxx.d文件
%.d: %.c
    $(CC) -M $(CPPFLAGS) $< > $@.$$$$; \
    sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@; \
    rm -f $@.$$$$
```

生成库归档: ar

``` Makefile
# ar -- create and maintain library archives
# 添加
ar rv libcounter.a counter.o lexer.o
# 更新
ar rv libcounter.a counter.o
```

创建和更新库

``` Makefile
libcounter.a: counter.o lexer.o
    $(AR) $(ARFLAGS) $@ $?

# 使用members in archive
libcounter.a: libcounter.a(lexer.o) libcounter.a(counter.o)

libcounter.a(lexer.o): lexer.o
    $(AR) $(ARFLAGS) $@ $<

libcounter.a(counter.o): counter.o
    $(AR) $(ARFLAGS) $@ $<

# 更新index in archive
libcounter.a: counter.o lexer.o
    $(RM) $@
    $(AR) $(ARFLGS) $@ $^
    $(RANLIB) $@
```

使用库作为前提

``` Makefile
xpong: $(OBJECTS) /lib/X11/libX11.a /lib/X11/libXaw.a
    $(LINK) $^ -o $@
# 或者使用-l语法
xpong: $(OBJECTS) -lX11 -lXaw
    $(LINK) $^ -o $@

# 处理库中循环依赖: -lA -lB -lA
xpong: xpong.o libui.a libdynamics.a libui.a -lX11
    $(CC) $+ -o $@
```

双冒号规则: 根据不同的条件更新构建目标

``` Makefile
file-list:: generate-list-script
chmod +x $<
generate-list-script $(files) > file-list
file-list:: $(files)
generate-list-script $(files) > file-list
```

## 变量和宏

简单变量(:=)

``` Makefile
MAKE_DEPEND := $(CC) -M
```

递归(展开)变量(=)

``` Makefile
MAKE_DEPEND = $(CC) -M
# Some time later
CC = gcc
```

条件赋值(?=): 变量没有值时才执行赋值

``` Makefile
# Put all generated files in the directory $(PROJECT_DIR)/out.
OUTPUT_DIR ?= $(PROJECT_DIR)/out
```

追加(+=)


宏: 包含内嵌换行的变量

``` Makefile
define sample-macro
	@echo "I can do this"
	@echo "I can also do this"
endef

.POHNY: test
test-macro:
	$(sample-macro)
```

### 变量何时展开?

make工作机制的两个阶段:

- (1) 读取makefile和导入的makefile, 将变量和规则加载到内部数据库, 创建依赖图;
- (2) 分析依赖图, 确定需要更新的目标, 执行更新目标所需的命令脚本.

make元素展开规则:

- For variable assignments, the lefthand side of the assignment is always expanded immediately when make  reads the line during its first phase.
- The righthand side of =  and ?=  are deferred until they are used in the second phase.
- The righthand side of :=  is expanded immediately.
- The righthand side of +=  is expanded immediately if the lefthand side was originally defined as a simple variable. Otherwise, its evaluation is deferred.
- For macro definitions (those using define ), the macro variable name is immediately expanded and the body of the macro is deferred until used.
- For rules, the targets and prerequisites are always immediately expanded while the commands are always deferred.

特定于目标/模式的变量

``` Makefile
# 使用特殊编译选项
gui.o: gui.h
    $(COMPILE.c) -DUSE_NEW_MALLOC=1 $(OUTPUT_OPTION) $<
# 使用特定于目标的变量
gui.o: CPPFLAGS += -DUSE_NEW_MALLOC=1
gui.o: gui.h
    $(COMPILE.c) $(OUTPUT_OPTION) $<
```

``` Makefile
target...: variable = value
target...: variable := value
target...: variable += value
target...: variable ?= value
```

变量的来源: makefile文件, 命令行, 环境变量, 自动变量

条件和导入处理

``` Makefile
if-condition
    text if the condition is true
endif

if-condition
    text if the condition is true
else
    text if the condition is false
endif
# if-condition
# 变量名variable-name不能使用$()
ifdef variable-name
ifndef variable-name
ifeq test
ifneq test
# test的两种表达方式
"a" "b"
(a,b)
```

``` Makefile
# 文件: Makefile
# Simple makefile including a generated file.
include foo.mk
# 未找到导入文件时不报错:
#-include foo.mk
$(warning Finished include)
foo.mk: bar.mk
    m4 --define=FILENAME=$@ bar.mk > $@

# 文件: bar.mk - Report when I am being read.
$(warning Reading FILENAME)
```

make将makefile文件视为目标

make查找导入文件的顺序: include指令, --include-dir命令行参数, 编译搜索路径.

在makefile第一个目标之前导入文件可能会修改默认目标, 解决方法:

``` Makefile
# Ensure all is the default goal.
all:
include support.mk
# Now that we have our variables defined, complete the all target.
all: $(programs)
```

标准make变量

- MAKE_VERSION
- CURDIR
- MAKEFILE_LIST
- MAKECMDGOALS
- .VARIABLES

## 函数

内建和用户自定义函数
用户自定义函数保存在变量或宏中

``` Makefile
define simple-function1
	@echo hello
endef

define simple-function2
	@echo $1
endef

# NOT WORK: $(simple-function1 'there2')
test-udf:
	$(simple-function1)
	$(call simple-function2, 'there')
```

内建函数(Built-in Functions)

``` Makefile
$(function-name arg1[, argn])
```

可以使用模式作为参数, 与模式规则中语法相同.

- 字符串函数: filter, filter-out, findstring, subst, patsubst, words, word, firstword, wordlist
- 文件名函数: wildcard, dir, notdir, suffix, basename, addsuffix, addprefix, join
- 流程控制函数: if, error, foreach
- 杂项函数: sort, shell, strip, origin, warning

## 命令

命令脚本处理顺序:

- (1) 读取代码;
- (2) 展开变量;
- (3) 求值make表达式;
- (4) 执行命令.

``` Makefile
# $(call strip-program, file)
define strip-program
    strip $1
endef
complex_script:
    $(CC) $^ -o $@
ifdef STRIP
    $(call strip-program, $@)
endif
    $(if $(PACK), upx --best $@)
    $(warning Final size: $(shell ls -s $@))
```

ifdef指令在出现时立即被处理

warning在执行命令前被求值, 即在complex_script修改前执行.
