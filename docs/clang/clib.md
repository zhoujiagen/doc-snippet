# C Library

## C99

|Section|header|description|
|:---|:---|:---|
|B.1|`<assert.h>`|验证程序断言 Diagnostics|
|B.2|`<complex.h>`|复数算数运算支持 Complex|
|B.3|`<ctype.h>`|字符分类和映射支持 Character handling|
|B.4|`<errno.h>`|出错码 Errors|
|B.5|`<fenv.h>`|浮点环境 Floating-point environment|
|B.6|`<float.h>`|浮点常量和特性 Characteristics of floating types|
|B.7|`<inttypes.h>`|整形格式变换 Format conversion of integer types|
|B.8|`<iso646.h>`|赋值、关系和一元操作符宏 Alternative spellings|
|B.9|`<limits.h>`|实现常量 Sizes of integer types|
|B.10|`<locale.h>`|本地化类别和相关定义 Localization|
|B.11|`<math.h>`|数学函数、类型声明和常量 Mathematics|
|B.12|`<setjmp.h>`|非局部goto Nonlocal jumps|
|B.13|`<signal.h>`|信号 Signal handling|
|B.14|`<stdarg.h>`|可变长度参数表 Variable arguments|
|B.15|`<stdbool.h>`|布尔类型和值 Boolean type and values|
|B.16|`<stddef.h>`|标准定义 Common definitions|
|B.17|`<stdint.h>`|整形 Integer types|
|B.18|`<stdio.h>`|标准IO库 Input/output|
|B.19|`<stdlib.h>`|试用函数 General utilities|
|B.20|`<string.h>`|字符串操作 String handling|
|B.21|`<tgmath.h>`|通用类型数学宏 Type-generic math|
|B.22|`<time.h>`|时间和日期 Date and time|
|B.23|`<wchar.h>`|扩充的多字符和宽字符支持 Extended multibyte/wide character utilities|
|B.24|`<wctype.h>`|宽字符分类和映射支持 Wide character classification and mapping utilities|


## POSIX.1

|header|description|source|
|:---|:---|:---|
|`<aio.h>`|异步IO||
|`<arpa/inet.h>`|因特网定义||
|`<assert.h>`||C标准库|
|`<complex.h>`||C标准库|
|`<cpio.h>`|cpio归档值[^1]||
|`<ctype.h>`||C标准库|
|`<dirent.h>`|目录项||
|`<dlfcn.h>`|动态链接||
|`<errno.h>`||C标准库|
|`<fcntl.h>`|||
|`<fenv.h>`||C标准库|
|`<float.h>`||C标准库|
|`<fmtmsg.h>`|消息显示结构|XSI可选|
|`<fnmatch.h>`|文件名匹配类型||
|`<ftw.h>`|文件树漫游|XSI可选|
|`<glob.h>`|路径名模式匹配与生成||
|`<grp.h>`|组文件||
|`<iconv.h>`|代码集变换实用程序||
|`<inttypes.h>`||C标准库|
|`<iso646.h>`||C标准库|
|`<langinfo.h>`|语言信息常量||
|`<libgen.h>`|路径名管理函数|XSI可选|
|`<limits.h>`||C标准库|
|`<locale.h>`||C标准库|
|`<math.h>`||C标准库|
|`<monetary.h>`|货币类型与函数||
|`<mqueue.h>`|消息队列|可选|
|`<ndbm.h>`|数据库操作|XSI可选|
|`<net/if.h>`|套接字本地接口||
|`<netdb.h>`|网络数据库操作||
|`<netinet/in.h>`|因特网地址族||
|`<netinet/tcp.h>`|传输控制协议定义||
|`<nl_types.h>`|消息类||
|`<poll.h>`|投票函数||
|`<pthread.h>`|线程||
|`<pwd.h>`|口令文件||
|`<regex.h>`|正则表达式||
|`<sched.h>`|执行调度||
|`<search.h>`|搜索表|XSI可选|
|`<semaphore.h>`|信号量||
|`<setjmp.h>`||C标准库|
|`<signal.h>`||C标准库|
|`<spawn.h>`|实时spawn接口|可选|
|`<stdarg.h>`||C标准库|
|`<stdbool.h>`||C标准库|
|`<stddef.h>`||C标准库|
|`<stdint.h>`||C标准库|
|`<stdio.h>`||C标准库|
|`<stdlib.h>`||C标准库|
|`<string.h>`||C标准库|
|`<strings.h>`|字符串操作||
|`<stropts.h>`|||
|`<sys/ipc.h>`|IPC|XSI可选|
|`<sys/mman.h>`|存储管理声明||
|`<sys/msg.h>`|XSI消息队列|XSI可选|
|`<sys/resource.h>`|资源操作|XSI可选|
|`<sys/select.h>`|select函数||
|`<sys/sem.h>`|XSI信号量|XSI可选|
|`<sys/shm.h>`|XSI共享存储|XSI可选|
|`<sys/socket.h>`|套接字接口||
|`<sys/stat.h>`|文件状态||
|`<sys/statvfs.h>`|文件系统信息||
|`<sys/time.h>`|时间类型|XSI可选|
|`<sys/times.h>`|进程时间||
|`<sys/types.h>`|基本系统数据类型||
|`<sys/uio.h>`|矢量IO操作|XSI可选|
|`<sys/un.h>`|UNIX域套接字定义||
|`<sys/utsname.h>`|系统名||
|`<sys/wait.h>`|进程控制||
|`<syslog.h>`|系统出错日志记录|XSI可选|
|`<tar.h>`|tar归档值||
|`<termios.h>`|终端IO||
|`<tgmath.h>`||C标准库|
|`<time.h>`||C标准库|
|`<trace.h>`|||
|`<ulimit.h>`|||
|`<unistd.h>`|符号常量||
|`<utime.h>`|||
|`<utmpx.h>`|用户账户数据库|XSI可选|
|`<wchar.h>`||C标准库|
|`<wctype.h>`||C标准库|
|`<wordexp.h>`|字扩充类型||

[^1]: https://www.systutorials.com/docs/linux/man/1-cpio/


### Signal

```
<signal.h>
```

- T: Abnormal termination of the process.
- A: Abnormal termination of the process with additional actions.
- I: Ignore the signal.
- S: Stop the process.
- C: Continue the process, if it is stopped; otherwise, ignore the signal.

|Name|Signal|Default Action|Description|
|:---|:---|:---|:---|
|异常终止|SIGABRT|A|Process abort signal.|
|定时器超时|SIGALRM|T|Alarm clock.|
|访问内存对象未定义部分|SIGBUS|A|Access to an undefined portion of a memory object.|
|子进程终止, 暂停或继续|SIGCHLD|I|Child process terminated, stopped, or continued.|
|使暂停进程继续执行|SIGCONT|C|Continue executing, if stopped.|
|算术操作错误|SIGFPE|A|Erroneous arithmetic operation.|
|连接断开|SIGHUP|T|Hangup.|
|非法指令|SIGILL|A|Illegal instruction.|
|终端中断信号|SIGINT|T|Terminal interrupt signal.|
|杀死|SIGKILL|T|Kill (cannot be caught or ignored).|
|写到无进程读的管道|SIGPIPE|T|Write on a pipe with no one to read it.|
|终端退出信号|SIGQUIT|A|Terminal quit signal.|
|无效内存引用|SIGSEGV|A|Invalid memory reference.|
|停止执行|SIGSTOP|S|Stop executing (cannot be caught or ignored).|
|终止|SIGTERM|T|Termination signal.|
|终端停止信号|SIGTSTP|S|Terminal stop signal.|
|后台进程尝试读|SIGTTIN|S|Background process attempting read.|
|后台进程尝试写|SIGTTOU|S|Background process attempting write.|
|用户定义信号1|SIGUSR1|T|User-defined signal 1.|
|用户定义信号2|SIGUSR2|T|User-defined signal 2.|
|可轮询事件|SIGPOLL|T|Pollable event.|
|梗概计时器超时|SIGPROF|T|Profiling timer expired.|
|无效系统调用|SIGSYS|A|Bad system call.|
|跟踪/断点陷阱|SIGTRAP|A|Trace/breakpoint trap.|
|socket上高带宽数据可用|SIGURG|I|High bandwidth data is available at a socket.|
|虚拟定时器超时|SIGVTALRM|T|Virtual timer expired.|
|超出CPU时间限制|SIGXCPU|A|CPU time limit exceeded.|
|超出文件大小限制|SIGXFSZ|A|File size limit exceeded.|

### async-signal-safe函数

> The Open Group Base Specifications Issue 7, 2018 edition
>> System Interfaces
>>> 2.4.3 Signal Actions

|||||
|:---|:---|:---|:---|
|_Exit()|getppid()|sendmsg()|tcgetpgrp()|
|_exit()|getsockname()|sendto()|tcsendbreak()|
|abort()|getsockopt()|setgid()|tcsetattr()|
|accept()|getuid()|setpgid()|tcsetpgrp()|
|access()|htonl()|setsid()|time()|
|aio_error()|htons()|setsockopt()|timer_getoverrun()|
|aio_return()|kill()|setuid()|timer_gettime()|
|aio_suspend()|link()|shutdown()|timer_settime()|
|alarm()|linkat()|sigaction()|times()|
|bind()|listen()|sigaddset()|umask()|
|cfgetispeed()|longjmp()|sigdelset()|uname()|
|cfgetospeed()|lseek()|sigemptyset()|unlink()|
|cfsetispeed()|lstat()|sigfillset()|unlinkat()|
|cfsetospeed()|memccpy()|sigismember()|utime()|
|chdir()|memchr()|siglongjmp()|utimensat()|
|chmod()|memcmp()|signal()|utimes()|
|chown()|memcpy()|sigpause()|wait()|
|clock_gettime()|memmove()|sigpending()|waitpid()|
|close()|memset()|sigprocmask()|wcpcpy()|
|connect()|mkdir()|sigqueue()|wcpncpy()|
|creat()|mkdirat()|sigset()|wcscat()|
|dup()|mkfifo()|sigsuspend()|wcschr()|
|dup2()|mkfifoat()|sleep()|wcscmp()|
|execl()|mknod()|sockatmark()|wcscpy()|
|execle()|mknodat()|socket()|wcscspn()|
|execv()|ntohl()|socketpair()|wcslen()|
|execve()|ntohs()|stat()|wcsncat()|
|faccessat()|open()|stpcpy()|wcsncmp()|
|fchdir()|openat()|stpncpy()|wcsncpy()|
|fchmod()|pause()|strcat()|wcsnlen()|
|fchmodat()|pipe()|strchr()|wcspbrk()|
|fchown()|poll()|strcmp()|wcsrchr()|
|fchownat()|posix_trace_event()|strcpy()|wcsspn()|
|fcntl()|pselect()|strcspn()|wcsstr()|
|fdatasync()|pthread_kill()|strlen()|wcstok()|
|fexecve()|pthread_self()|strncat()|wmemchr()|
|ffs()|pthread_sigmask()|strncmp()|wmemcmp()|
|fork()|raise()|strncpy()|wmemcpy()|
|fstat()|read()|strnlen()|wmemmove()|
|fstatat()|readlink()|strpbrk()|wmemset()|
|fsync()|readlinkat()|strrchr()|write()|
|ftruncate()|recv()|strspn()||
|futimens()|recvfrom()|strstr()||
|getegid()|recvmsg()|strtok_r()||
|geteuid()|rename()|symlink()||
|getgid()|renameat()|symlinkat()||
|getgroups()|rmdir()|tcdrain()||
|getpeername()|select()|tcflow()||
|getpgrp()|sem_post()|tcflush()||
|getpid()|send()|tcgetattr()||

### Thread Safe函数

> The Open Group Base Specifications Issue 7, 2018 edition
>> System Interfaces
>>> 2.9.1 Thread-Safety

All functions defined by this volume of POSIX.1-2017 shall be thread-safe, except that the following functions1 need **not** be thread-safe.

|||||
|:---|:---|:---|:---|
|asctime()|ftw()|getutxent()|putenv()|
|basename()|getdate()|getutxid()|pututxline()|
|catgets()|getenv()|getutxline()|rand()|
|crypt()|getgrent()|gmtime()|readdir()|
|ctime()|getgrgid()|hcreate()|setenv()|
|dbm_clearerr()|getgrnam()|hdestroy()|setgrent()|
|dbm_close()|gethostent()|hsearch()|setkey()|
|dbm_delete()|getlogin()|inet_ntoa()|setlocale()|
|dbm_error()|getnetbyaddr()|l64a()|setpwent()|
|dbm_fetch()|getnetbyname()|lgamma()|setutxent()|
|dbm_firstkey()|getnetent()|lgammaf()|strerror()|
|dbm_nextkey()|getopt()|lgammal()|strsignal()|
|dbm_open()|getprotobyname()|localeconv()|strtok()|
|dbm_store()|getprotobynumber()|localtime()|system()|
|dirname()|getprotoent()|lrand48()|ttyname()|
|dlerror()|getpwent()|mblen()|unsetenv()|
|drand48()|getpwnam()|mbtowc()|wctomb()|
|encrypt()|getpwuid()|mrand48()||
|endgrent()|getservbyname()|nftw()||
|endpwent()|getservbyport()|nl_langinfo()||
|endutxent()|getservent()|ptsname()||
