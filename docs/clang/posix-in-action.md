# Posix in Action



|时间|内容|
|:------|:----|
|20190309|将UNIX系统编程按并行维度(数据并行和指令并行)划分为两部分.|
|20190310-20190317|进程环境, 进程控制, 进程关系, 信号, 线程, 线程控制, 守护进程.|
|20190318|文件IO, 文件和目录; 开始标准IO库.<br>如何查询程序的系统调用? 例:<br>Linux: $ strace -c ./getcputc < getcputc.c|
|20190320|添加通信抽象: 进程间通信IPC, 网络IPC套接字|
|20190325|第一遍skimming, 除了:<br>终端IO, 伪终端, 数据库函数库, 与网络打印机通信|
|20191127|添加资源|


## 资源

- [Advanced Programming in the UNIX® Environment, Third Edition](http://www.apuebook.com/apue3e.html)
- UNIX Systems Programming: Communication, Concurrency and Threads, 2nd Edition, by Kay Robbins and Steve Robbins

### [The Open Group Base Specifications Issue 7, 2018 edition](https://pubs.opengroup.org/onlinepubs/9699919799.2018edition/)

> Base Definitions
>> Introduction<br>
>> Conformance<br>
>> Definitions<br>
>> General Concepts<br>
>> File Format Notation<br>
>> Character Set<br>
>> Locale<br>
>> Environment Variables<br>
>> Regular Expressions<br>
>> Directory Structure and Devices<br>
>> General Terminal Interface<br>
>> Utility Conventions<br>
>> Headers<br>

> System Interfaces
>> Introduction<br>
>> General Information<br>
>> System Interfaces

> Shell & Utilities
>> Introduction<br>
>> Shell Command Language<br>
>> Batch Environment Services<br>
>> Utilities

> Rationale
>> Rationale for Base Definitions<br>
>> Rationale for System Interfaces<br>
>> Rationale for Shell & Utilities<br>
>> Portability Considerations (Informative)<br>
>> Subprofiling Considerations (Informative)


## 问题
- 信号处理<br>
信号屏蔽字与可重入函数的相互作用<br>
信号处理在父子进程和多线程进程中的处理

- 线程安全<br>
有哪些线程安全的函数<br>
如何实现线程安全<br>
有哪些同步机制: 信号量, 锁, 条件变量, 屏障, 文件锁等

- IO<br>
如何判断IO函数是阻塞的还是非阻塞的


## 限制

- 编译时限制: 头文件. ISO C: `<limits.h>`
- 运行时限制: 与文件/目录无关(sysconf()), 与文件/目录有关(pathconf(), fpathconf())

## 选项

- 编译时选项: `<unistd.h>`
- 运行时选项: 与文件/目录无关(sysconf()), 与文件/目录有关(pathconf(), fpathconf())

## 系统调用

``` Shell
$ uname -a
Linux gda 4.4.0-143-generic #169~14.04.2-Ubuntu SMP Wed Feb 13 15:00:41 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

-[How to find your current set of Linux system calls](https://www.cs.fsu.edu/~langley/current-system-calls.html)

``` Shell
ls /usr/share/man/man2 | sed -e s/.2.gz//g | xargs man -s 2 -k  | sort | grep -v 'unimplemented system calls'
```

## 权限管理

> TODO(zhoujiagen) 文件访问, 进程权限; after 数据和指令

## 数据
### 文件IO
文件描述符

- 打开或创建文件: open(), openat()
- 创建新文件: creat()
- 关闭文件: close()
- 当前文件偏移量: lseek()
- 从打开文件中读数: read()
- 向打开文件写数据: write()

文件共享

- 内核文件数据结构: 进程表项, 文件表项, v节点(v-node), i节点(i-node)

原子操作

- 追加到一个文件: O_APPEND标志
- 原子性的定位并执行IO: pread(), pwrite()
- 创建文件: open()的O_CREAT, O_EXCL

复制现有的文件描述符: dup(), dup2()

保证磁盘上实际文件系统与缓冲区中内容一致性: sync(), fdatasync(), sync()

修改已打开文件的属性: fcntl()

IO操作的杂物箱: ioctl()

/dev/fd目录

### 文件和目录

文件属性: 数据结构stat

``` C
stat(), fstat(), lstat(), fstatat()
```

文件类型: 普通文件, 目录文件, 块特殊文件, 字符特殊文件, FIFO, 套接字, 符号链接.

进程关联的ID:

- 实际用户/组ID: 标识我们究竟是谁, 取自口令文件中的登录项;
- 有效用户/组ID, 附属组ID: 决定我们的文件访问权限
- 保存的设置用户/组ID: 在执行一个程序时包含了有效用户/组ID的副本

文件访问权限: st_mode, `<sys/stat.h>`

访问权限位:
- `S_IS[UID | GID]`: 设置用户/组ID
- `S_ISVTX`: 粘着位
- `S_I[R | W | X][USR | GRP | OTH]`: 读/写/执行 用户/组/其他

新文件和目录的所有权

按实际用户/组ID进行访问权限测试: access(), faccessat()

为进程设置文件模式创建屏蔽字: umask()

更改现有文件的访问权限: chmod(), fchmod(), fchmodat()

粘着位(sticky bit): 保存正文位(saved-text bit), S_ISVTX

更改文件的用户/组ID: chown(), fchown(), fchownat(), lchown()

文件长度: st_size

文件截断: truncate(), ftruncate()

文件系统: i节点数组, 目录块(目录项), 文件块, 符号链接

创建一个指向现有文件的链接: link(), linkat(), unlink(), unlinkat(), remove()

重命名文件或目录: rename(), renameat()

符号链接: 函数是否处理符号链接

- 创建: symlink(), symlinkat()
- 打开链接本身: readlink(), readlinkat()

文件的时间:

- 文件数据的最后访问时间: st_atim
- 文件数据的最后修改时间: st_mtim
- i节点状态的最后修改时间: st_ctim

修改文件的访问和修改时间: futimens(), utimensat(), utimes()

创建目录: mkdir(), mkdirat()

删除目录: rmdir()

读目录: 数据结构DIR, dirent(`<dirent.h>`)

```
opendir(), fdopendir(), readdir(), rewinddir(), closedir(), telldir(), seekdir()
```

更改当前工作目录: chdir(), fchdir()

获取当前工作目录完整的绝对路径名: getcwd()

设备特殊文件

- 设备号数据结构: dev_t
- 访问主/次设备号: 宏major/minor
- 文件系统的设备号: st_dev
- 字符特殊文件和块特殊文件: st_rdev, 实际设备的设备号


### 标准IO库

``` C
<stdio.h>
```

流(stream): 数据结构FILE

- 流的定向: fwide()
- 打开流: fopen(), freopen(), fdopen()
- 关闭流: fclose()
- 读写流: getc(), fgetc(), getchar(), ferror(), feof(), ungetc(), putc(), fputc(), putchar()
- 定位流: ftell(), fseek(), rewind(), ftello(), fseeko(), fgetpos(), fsetpos()
- 文件描述符: fileno()

缓冲: 全缓冲, 行缓冲, 不带缓冲

- setbuf(), setvbuf()

每次一行IO: fgets(), gets(), fpust(), puts()

二进制IO: fread(), fwrite()

格式化IO:

``` C
printf(), fprintf(), dprintf(), sprintf(), snprintf(),
vprintf(), vfprintf(), vdprintf(), vsprintf(), vsnprintf(),
scanf(), fscanf(), sscanf(),
vscanf(), vfscanf(), vsscanf()
```

临时文件: tmpnam(), tmpfile(), mkdtemp(), mkstemp()

内存流: fmemopen(), open_memstream(), open_wmemstream()

标准IO的替代软件: fio, sfio, ASI, uClibc, Newlib

### 系统数据文件和信息

> TODO: 口令文件, 阴影文件, 组文件, 附属组ID, 登录账户记录

系统标识: uname(), 数据结构utsname, gethostname()

时间和日期例程:
协调世界时(Coordinate Universal Time, UTC): 自19700101 00:00:00经过的秒数

`<time.h>`

日历时间: 数据结构time_t

```
time()
```

时钟时间: 数据结构clockid_t, timespec

``` C
clock_gettime(), clock_getres(), clock_settime(), gettimeofday()
```

分解的时间: 数据结构tm

``` C
gmtime(), localtime(), mktime(), strftime(), strftime_l(), strptime()
```

环境变量TZ

### 高级IO
非阻塞IO:

``` C
O_NONBLOCK: open(), fcntl()
```


记录锁: 数据结构flock

``` C
fcntl()
```

建议性锁, 强制性锁

IO多路复用(multiplexing)

``` C
select(), pselect(), poll()
```

异步IO: 数据结构aiocb

> http://man7.org/linux/man-pages/man7/aio.7.html

``` C
aio_read(), aio_write(), aio_fsync(), aio_error(), aio_return(), aio_suspend(), aio_cancel()
lio_listio()
```


散布读(scatter read)/聚集写(gather write): 一次函数调用中读/写多个非连续缓冲区

``` C
readv(), writev()
```

内存映射IO(memory-mapped IO)

``` C
mmap(), mprotect(), msync(), munmap()
```

### 终端IO

> TODO(zhoujiagen) at last

### 伪终端

> TODO(zhoujiagen) at last

## 指令

### 进程环境

main(): exec()

进程终止: 退出函数(exit(), _exit(), _Exit()), 注册终止处理程序atexit()

命令行参数

``` C
int main(int argc, char *argv[])
```

环境表

``` C
extern char **environ;
```

C程序存储空间布局: 正文段、数据段、未初始化数据段(bss段)、栈、堆.

共享库

存储空间分配: malloc(), calloc(), realloc(), free()

系统调用: sbrk

环境变量: getenv(), putenv(), setenv(), unsetenv(), clearenv()

跨函数的goto语句: setjmp(), longjmp()

进程资源限制: getrlimit(), setrlimit()

### 进程控制

获取进程相关ID: getpid, getppid, getuid, geteuid, getgid, getegid

- PID 0: 调度进程/交换进程(swapper)
- PID 1: init进程/Mac OS中launchd进程

创建新进程: fork()

写时复制技术(Copy-On-Write, COW):父子进程每个相同的打开文件描述符共享一个文件表项, 共享同一个文件偏移量

进程正常终止:

- (1) main中执行return;
- (2) 调用exit;
- (3) 调用_exit或_Exit;
- (4) 最后一个线程执行return;
- (5) 最后一个线程pthread_exit.

进程异常终止:

- (1) 调用abort, 产生SIGABRT信号;
- (2) 接收到某些信号;
- (3) 响应取消请求(cancellation)

被init进程收养/孤儿进程

僵尸进程(zombie)

wait(), waitpid(): 等待有某一个子进程终止

`<sys/wait.h>`

waitid()

wait3(), wait4()

exec函数(7个): 用磁盘上的新程序替换当前进程的正文段、数据段、堆段和栈段.

``` C
execl()
execv()
execle()
execve()
execlp()
execvp()
fexecve()
```

- l: list, 参数列表
- v: vector, 参数数组
- p: 使用PATH
- e: 使用`envp[]`数组

更改用户ID/用户组ID: setuid(), getgid(), setreuid(), seteegid(), seteuid(), setegid()

- re: real, 实际用户/组
- e: effective, 有效用户/组

getlogin(): 获取登录名


system(cmdstring): 执行命令字符串

进程审计: `<sys/acct.h>`

nice值: nice值越小, 优先级越高.

``` C
nice()
getpriority(), setpriority()
```

进程时间: 墙上时钟时间, 用户CPU时间, 系统CPU时间

```
times()
```


### 进程关系

终端登录, 网络登录

进程组
进程组ID
进程组长: 其进程组ID=其进程ID
同一进程组中的各进程接收来自同一终端的各种新型号
getpgrp(), getpgid(), setpgid()

会话
一个或多个进程组的集合
setsid(), getsid()
控制终端
控制进程
前台进程组, 后台进程组
tcgetpgrp(), tcsetpgrp(), tcgetsid()

作业控制: 允许在一个终端上启动多个作业(进程组), 控制哪个作业可以访问终端以及哪些作业在后台运行.

登录shell, 从登录shell启动的进程

### 信号

> 需要多读几遍.

`<signal.h>`

``` C
void ( *signal(int signo, void (*func)(int)) )(int);
```

signal()的语义与实现有关, 最好试用sigaction()替代signal().


信号集(signal set)

- 数据类型: sigset_t
- 函数: sigemptyset(), sigfillset(), sigaddset(), sigdelset(), sigismember()

进程的信号屏蔽字: 当前阻塞不能递送给该进程的信号集; sigprocmask()

进程中阻塞不能投递的信号集: sigpending()

sigaction()

sigsetjmp(), siglongjump()

sigsuspend()

abort()

system()

sleep(), nanosleep(), clock_nanosleep()

sigqueue()

作业控制信号

信号名和编号

### 线程

POSIX线程/pthread

```
<pthread.h>
```

- 线程标识: pthread_equal(), pthread_self()
- 线程创建: pthread_create()
- 线程终止: pthread_exit()
- 访问线程返回值: pthread_join()
- 取消线程: pthread_cancel()
- 线程清理处理程序: pthread_cleanup_push(), pthread_cleanup_pop()

#### 线程同步

##### 互斥量(mutex)

``` C
pthread_mutex_t             // 互斥锁类型
pthread_mutex_init()        // 初始化
PTHREAD_MUTEX_INITIALIZER
pthread_mutex_destroy()     // 销毁
pthread_mutex_lock()        // 申请
pthread_mutex_trylock()     // 申请, 立即返回
pthread_mutex_unlock()      // 释放
pthread_mutex_timedlock()   // 指定线程阻塞时间
```

##### 读写锁(reader-writer lock)

``` C
pthread_rwlock_t              // 读写锁类型
pthread_rwlock_init()         // 初始化
pthread_rwlock_destroy()      // 销毁
pthread_rwlock_rdlock()       // 申请读
pthread_rwlock_wrlock()       // 申请写
pthread_rwlock_unlock()       // 释放
pthread_rwlock_tryrdlock()    // 申请读, 立即返回
pthread_rwlock_trywrlock()    // 申请写, 立即返回
pthread_rwlock_timedrdlock()  // 申请读, 带时间
pthread_rwlock_timedwrlock()  // 申请写, 带时间
```

##### 条件变量(condition variable)

``` C
pthread_cond_t                  // 条件变量类型
pthread_cond_init()             // 初始化
PTHREAD_COND_INITIALIZER
pthread_cond_destroy()          // 销毁
pthread_cond_wait(cond, mutex)  // 等待
pthread_cond_timedwait()        // 带时间的等待
pthread_cond_signal()           // 唤醒至少一个
pthread_cond_broadcast()        // 唤醒所有
```

##### 自旋锁

不是通过休眠阻塞, 而是在获取锁之前一直处于忙等(自旋)阻塞状态

``` C
pthread_spin_init()
pthread_spin_destroy()
pthread_spin_lock()
pthread_spin_trylock()
pthread_spin_unlock()
```

##### 屏障(barrier)

pthread_join()是一种屏障

``` C
pthread_barrier_init()
pthread_barrier_destroy()
pthread_barrier_wait()
```

#### 线程与信号

信号处理器是进程级的, 线程级的信号屏蔽.

推荐使用专用于信号处理的线程.

```
pthread_kill()    // 产生信号
pthread_sigmask() // 屏蔽信号
```

#### 线程控制

线程属性:

``` C
pthread_attr_t
pthread_attr_init()
pthread_attr_destroy()
pthread_attr_getdetachstate() // 分离状态
pthread_attr_setdetachstate()
pthread_attr_getstack()       // 线程栈地址
pthread_attr_setstack()
pthread_attr_getstacksize()   // 线程栈大小
pthread_attr_setstacksize()
pthread_attr_getguardsize()   // 线程栈尾避免栈溢出的扩展内存大小
pthread_attr_setguardsize()
```

同步属性:

互斥量属性

``` C
pthread_mutexattr_t
pthread_mutexattr_init()
pthread_mutexattr_destroy()
pthread_metexattr_getpshared()
pthread_metexattr_setpshared()
pthread_metexattr_getrobust()
pthread_metexattr_setrobust()
pthread_metex_consistent()
pthread_metexattr_gettype()
pthread_metexattr_settype()
```

读写锁属性

``` C
pthread_rwlockattr_t
pthread_rwlockattr_init()
pthread_rwlockattr_destroy()
pthread_rwlockattr_getpshared()
pthread_rwlockattr_setpshared()
```

条件变量属性

``` C
pthread_condattr_t
pthread_condattr_init()
pthread_condattr_destroy()
pthread_condattr_getpshared()
pthread_condattr_setpshared()
pthread_condattr_getclock()
pthread_condattr_setclock()
```

屏障属性

``` C
pthread_barrierattr_t
pthread_barrierattr_init()
pthread_barrierattr_destroy()
pthread_barrierattr_getpshared()
pthread_barrierattr_setpshared()
```


重入
如果一个函数在相同的时间点可以被多个线程安全的调用: 该函数是线程安全的.
但这并不能说明对信号处理程序来说改函数也是可重入的.

以线程安全的方式管理FILE对象:

``` C
ftrylockfile(), flockfile(), funlockfile()
```

### 守护进程

编写守护进程的基本规则:

- (1) 调用umask将文件模式创建屏蔽字设置为一个已知值;
- (2) 调用fork, 然后使父进程exit;
- (3) 调用setsid创建新会话, 然后将调用进程: 成为新会话的首进程, 成为一个新进程组的组长进程, 没有控制终端;
- (4) 将当前工作目录更改为根目录;
- (5) 关闭不在需要的文件描述符;
- (6) 打开/dev/null使其具有文件描述符0、1、2.

出错记录: 一个集中的守护进程出错记录设施

产生日志消息:

- (1) 内核例程调用log函数写入/dev/klog设备;
- (2) 大多数用户守护进程调用syslog写入/dev/log;
- (3) 将日志消息发向UDP端口514(syslogd守护进程所在主机).

``` C
openlog(), syslog(), closelog(), setlogmask()
```

单实例守护进程

守护进程惯例:

- 锁文件在/var/run目录中, 例: /var/run/crond.pid;
- 配置文件在/etc目录中, 例: /etc/syslog.conf;
- 系统初始化脚本/etc/rc*或/etc/init.d/*; 终止后自动重启: /etc/inittab中respawan记录项;
- 配置文件变更: 捕获SIGHUP信号后重新读取.


## 通信
### 进程间通信
#### 管道

历史上是半双工的, 只能在具有公共祖先的两个进程之间使用

``` C
pipe()
```

PIPE_BUF: 内核的管道缓冲区大小

#### FIFO: 命名管道

通过FIFO, 不相关的进程也能交换数据.
S_ISFIFO宏: 测试文件是否是FIFO类型

``` C
mkfifo(), mkfifoat()
```

#### XSI IPC

三种XSI IPC(结构): 消息队列、信号量、共享内存
IPC结构在系统范围内起作用, 没有引用计数; 在文件系统中没有名字.

标识符, 键: 数据结构key_t, `<sys/types.h>`; IPC_PRIVATE

``` C
ftok()
```

权限结构: ipc_perm, `<sys/ipc.h>`

修改:

``` C
msgctl()
semctl()
shmctl()
```

##### 消息队列

存储在内核中的消息链接表
数据结构: 队列当前状态msqid_ds

``` C
msgget()
smgctl()
msgsnd()
msgrcv()
```

##### 信号量

是一个计数器, 用于为多个进程提供对共享数据对象的访问.
数据结构: semid_ds, 由内核维护

``` C
semget()
semctl()
semop()
```

##### 共享内存

允许两个或多个进程共享一个给定的存储区(内存的匿名段).
数据结构: shmid_ds, 由内核维护

``` C
shmget()
shmctl()
shmat()
shmdt()
```

#### POSIX信号量

两种形式: 命名的和未命名的

``` C
sem_open()
sem_close()
sem_unlink()
sem_init()
sem_destroy()
sem_getvalue()
sem_trywait()
sem_wait()
sem_timedwait()
sem_post()
```

### 高级进程间通信

UNIX域套接字: socketpair()

命名UNIX域套接字
数据结构: sockaddr_un.sun_path

### 网络IPC: 套接字

TCP/IP协议栈

套接字描述符

``` C
socket(domain, type, protocol)
```

- domain: AF_INET, AF_INET6, AF_UNIX, AF_UPSPEC
- type: SOCK_DGRAM, SOCK_RAW, SOCK_SEQPACKET, SOCK_STREAM
- protocol: IPPROTO_IP, IPPROTO_IPV6, IPPROTO_ICMP, IPPROTO_RAW, IPPROTO_TCP, IPPROTO_UDP

文件描述符函数: shutdown()

字节序

- 处理器字节序: 大端, 小端
- TCP/IP协议栈使用大端字节序.

``` C
<apra/inet.h> // internet操作的定义
htonl()       // host long
htons()       // host short
ntohl()       // network long
htohs()       // network short
```

地址格式: `<netinet/in.h>`

- 通用的地址格式: sockaddr
- AF_INET: sockaddr_in
- AF_INET6: sockaddr_in6
- 二进制地址格式与点分十进制表示间转换: inet_ntop(), inet_pton()

地址查询

- 数据结构hostent, gethostent(), sethostent(), endhostent()
- 数据结构netent, getnetbyaddr(), getnetbyname()
- 数据结构protoent, getprotobyname(), getprotobynumber(), getprotoent(), setprotoent(), endprotoent()
- 数据结构servent, getservbyname(), getserbyport(), getservent(), setservent(), endservent()
- 数据结构addrinfo, getaddrinfo(), freeaddrinfo(), gai_strerror()
- getnameinfo()

将套接字与地址关联: bind(), getsockname(), getpeername()

建立连接: connect(), listen(), accept()

数据传输

- send(), sendto(), sendmsg(), 数据结构msghdr
- recv(), recvfrom(), recvmsg()

套接字选项

三种选项: 通用选项, 在套接字层次管理的选项, 特定于某协议的选项

``` C
setsockopt(), getsockopt()
```

带外数据(out-of-band data): TCP支持的紧急数据(urgent data); sockatmark()

#### 非阻塞和异步IO

send(), recv()指定套接字非阻塞模式

启用异步IO:

- (1) 建立套接字所有权, 这样信号可以呗传递到合适的进程 <br>
fcntl(F_SETOWN), ioctl(FIOSETOWN)/ioctl(SIOCSPGRP)
- (2) 通知套接字当IO操作不会阻塞时发信号<br>
fcntl(F_SETFL, O_ASYNC), ioctl(FIOASYNC)
