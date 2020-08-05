# BPF工具

## 资源

- [Linux kernel source tree](https://github.com/torvalds/linux): Linux Kerbel on Github
- [Documentation/trace](https://github.com/torvalds/linux/tree/master/Documentation/trace): Linux trace documets

## BCC

### BCC工具汇总

- bpftool: tool for inspection and simple manipulation of eBPF programs and maps

|#|工具|说明|描述|
|-|:---|:---|:---|
|1 | [argdist](#argdist)    | 函数参数的频率或直方图. | Trace a function and display a histogram or frequency count of its parameter values. Uses Linux eBPF/bcc.|
|2 | bashreadline    | | |
|3 | biolatency    | | |
|4 | biosnoop    | | |
|5 | biotop    | | |
|6 | bitesize    | | |
|7 | bpflist    | | Display processes currently using BPF programs and maps.|
|8 | btrfsdist    | | |
|9 | btrfsslower    | | |
|10 | cachestat    | | |
|11 | cachetop    | | |
|12 | capable    | | |
|13 | cobjnew    | | |
|14 | cpudist    | | |
|15 | cpuunclaimed    | | |
|16 | criticalstat    | | |
|17 | dbslower    | | |
|18 | dbstat    | | |
|19 | dcsnoop    | | |
|20 | dcstat    | | |
|21 | deadlock    | | |
|22 | deadlock.c    | | |
|23 | drsnoop    | | |
|24 | execsnoop    | | |
|25 | exitsnoop    | | |
|26 | ext4dist    | | |
|27 | ext4slower    | | |
|28 | filelife    | | |
|29 | fileslower    | | |
|30 | filetop    | | |
|31 | [funccount](#funccount)    | 函数调用事件计数. | Count function, tracepoint, and USDT probe calls matching a pattern. Uses Linux eBPF/bcc.|
|32 | funclatency    | | |
|33 | funcslower    | | |
|34 | gethostlatency    | | |
|35 | hardirqs    | | |
|36 | inject    | | |
|37 | javacalls    | | |
|38 | javaflow    | | |
|39 | javagc    | | |
|40 | javaobjnew    | | |
|41 | javastat    | | |
|42 | javathreads    | | |
|43 | killsnoop    | | |
|44 | klockstat    | | |
|45 | llcstat    | | |
|46 | mdflush    | | |
|47 | memleak    | | |
|48 | mountsnoop    | | |
|49 | mysqld_qslower    | | |
|50 | nfsdist    | | |
|51 | nfsslower    | | |
|52 | nodegc    | | |
|53 | nodestat    | | |
|54 | offcputime    | | |
|55 | offwaketime    | | |
|56 | oomkill    | | |
|57 | [opensnoop](#opensnoop)    |跟踪`open()`系统调用. |Trace open() syscalls. Uses Linux eBPF/bcc. |
|58 | perlcalls    | | |
|59 | perlflow    | | |
|60 | perlstat    | | |
|61 | phpcalls    | | |
|62 | phpflow    | | |
|63 | phpstat    | | |
|64 | pidpersec    | | |
|65 | profile    | | |
|66 | pythoncalls    | | |
|67 | pythonflow    | | |
|68 | pythongc    | | |
|69 | pythonstat    | | |
|70 | reset-trace    | | |
|71 | rubycalls    | | |
|72 | rubyflow    | | |
|73 | rubygc    | | |
|74 | rubyobjnew    | | |
|75 | rubystat    | | |
|76 | runqlat    | | |
|77 | runqlen    | | |
|78 | runqslower    | | |
|79 | shmsnoop    | | |
|80 | slabratetop    | | |
|81 | sofdsnoop    | | |
|82 | softirqs    | | |
|83 | solisten    | | |
|84 | sslsniff    | | |
|85 | [stackcount](#stackcount)    | 产生事件的调用栈计数. | Count function calls and their stack traces. Uses Linux eBPF/bcc.|
|86 | statsnoop    | | |
|87 | syncsnoop    | | |
|88 | syscount    | | |
|89 | tclcalls    | | |
|90 | tclflow    | | |
|91 | tclobjnew    | | |
|92 | tclstat    | | |
|93 | tcpaccept    | | |
|94 | tcpconnect    | | |
|95 | tcpconnlat    | | |
|96 | tcpdrop    | | |
|97 | tcplife    | | |
|98 | tcpretrans    | | |
|99 | tcpstates    | | |
|100 | tcpsubnet    | | |
|101 | tcptop    | | |
|102 | tcptracer    | | |
|103 | tplist    | | |
|104 | [trace](#trace)    | 多种源(kprobes, uprobes, tracepoints, USDT probes)的每事件跟踪. | Trace a function and print its arguments or return value, optionally evaluating a filter. Uses Linux eBPF/bcc. |
|105 | ttysnoop    | | |
|106 | vfscount    | | |
|107 | vfsstat    | | |
|108 | wakeuptime    | | |
|109 | xfsdist    | | |
|110 | xfsslower    | | |
|111 | zfsdist    | | |
|112 | zfsslower     | | |

#### 1 ALL

##### trace

```
trace [options] probe [probe ...]
```

`probe`:

```
eventname(signature) (boolean filter) "format string", arguments
```

`eventname`:

- `name`, `p:name`: 探查内核函数`name()`
- `r::name`: 探查内核函数`name()`的返回值
- `lib:name`, `p:lib:name`: 探查库`lib`中用户层的函数`name()`
- `r:lib:name`: 探查库`lib`中用户层的函数`name()`的返回值
- `path:name`: 探查路径`path`下用户层函数`name()`
- `r:path:name`: 探查路径`path`下用户层函数`name()`的返回值
- `t:system:name`: 探查跟踪点`system:name`
- `u:lib:name`: 探查库`lib`中名称为`name`的USDT探针
- `*`: 通配符. 选项`-r`允许使用正则表达式.


The `format string` is based on `printf()`.

```
## fs/open.c
trace 'do_sys_open "%s", arg2'
trace 'r::do_sys_open "ret: %d", retval'
## kernel/time/hrtimer.c
trace -U 'do_nanosleep "mode: %d", arg2'
trace 'do_nanosleep(struct hrtimer_sleeper *t) "task: %x", t->task'
## pam lib
trace 'pam:pam_start "%s: %s", arg1, arg2'
```


##### argdist

```
argdist {-C|-H} [options] probe
```

- `-C`: frequency count
- `-H`: power-of-two histogram

`probe`:

```
eventname(signature)[:type[,type...]:expr[,expr...][:filter][#label]]
```

- `eventname`, `signature`: 同[eventname](#trace), 但不支持内核函数的简写
- `type`: 汇总的值的类型
- `expr`: 汇总的表达式; 特殊的变量: `$retval`、`$latency`、`$entry(param)`
- `filter`: 可选的过滤事件的布尔表达式
- `label`: 可选的添加到输出的标记性文本

```
## 内核函数vfs_read()的返回值的直方图
argdist -H 'r::vfs_read()'
## 进程1005中用户级libc中read()的返回值的直方图
argdist -p 1005 -H 'r:c:read()' # PID=1005
## 按系统调用ID计数
argdist -C 't:raw_syscalls:sys_enter():int:args->id'
## tcp_sendmsg(...)中size参数的计数
argdist -C 'p::tcp_sendmsg(struct sock *sk, struct msghdr *msg, size_t size):u32:size'
argdist -H 'p::tcp_sendmsg(struct sock *sk, struct msghdr *msg, size_t size):u32:size'
## 进程181中libc的write(...)中fd参数的计数
argdist -p 181 -C 'p:c:write(int fd):int:fd'
## 延迟>0.1ms的进程的频率
argdist -C 'r::__vfs_read():u32:$PID:$lantency > 100000'
```

##### funccount

```
funccount [options] eventname
```

`eventname`:

- `name`, `p:name`: 探查内核函数`name()`
- `lib:name`, `p:lib:name`: 探查库`lib`中用户层的函数`name()`
- `path:name`: 探查路径`path`下用户层函数`name()`
- `t:system:name`: 探查跟踪点`system:name`
- `u:lib:name`: 探查库`lib`中名称为`name`的USDT探针
- `*`: 通配符. 选项`-r`允许使用正则表达式.

```
funccount 'tcp_*'
funccount -i 1 'tcp_send*'
funccount -i 1 't:block:*'
funccount -i 1 t:sched:sched_process_fork
funccount -i 1 c:getaddrinfo
funccount 'go:os.*'
```

##### funcslower
##### funclatency
##### stackcount

```
stackcount [options] eventname
```

`eventname` see [eventname](#funccount)


```
stackcount t:block:block_rq_insert
stackcount ip_output
statckcount t:sched:sched_switch
stackcount t:syscalls:sys_enter_read
```

##### profile



#### 2 Applications

传统工具: 系统调试器.

##### ucalls
##### uflow
##### uobjnew
##### ustat
##### uthreads
##### ugc
##### mysqld_qslower
##### dbstat
##### dbslower
##### bashreadline
##### mysqld_clat
##### bashfunc
##### bashfunclat

#### 3 Runtimes

传统工具: 运行时调试器.

##### javathreads
##### jnistacks

#### 4 System Libraries

传统工具: ltrace(1).

##### gethostlatency
##### memleak
##### sslsniff
##### threadssnoop
##### pmlock
##### pmheld

#### 5 System Call Interface

传统工具: strace(1), perf(1).

##### scread
##### opensnoop

```
## 跟踪所有open()系统调用
opensnoop
## 跟踪10内的所有open()系统调用
opensnoop -d 10
```

字段:

- TIME(s): Time of the call, in seconds.
- UID: User ID
- PID: Process ID
- TID: Thread ID
- COMM: Process name
- FD: File descriptor (if success), or -1 (if failed)
- ERR: Error number (see the system's errno.h)
- FLAGS: Flags passed to open(2), in octal
- PATH: Open path

##### statsnoop
##### syncsnoop
##### ioprofile
##### syscount
##### killsnoop
##### shellsnoop
##### signals
##### naptime
##### eperm
##### setuids
##### elfsnoop
##### modsnoop
##### execsnoop

works by tracing the execve(2) system call


##### exitsnoop
##### pidpersec

#### 6 内核

传统工具: Ftrace, perf(1).


##### 6.1 VFS

###### filetop
###### filelife
###### fileslower
###### vfscount
###### vfsstat
###### filetype
###### fsrwstat
###### vfssize
###### mmapfiles
###### writesync
###### cacheestat
###### cachetop
###### dcstat
###### dcsnoop
###### mountsnoop
###### icstat
###### bufgrow
###### readahead
###### writeback

##### 6.2 File Systems

###### btrfsslower, btrfsdist
###### ext4slower, ext4dist
###### nfsslower, nfsdist
###### xfsslower, xfsdist
###### zfsslower, zfsdist
###### overlayfs

##### 6.3 Volume Manager

###### mdflush

##### 6.4 Block Device

###### biotop
###### biosnoop
###### biolatency

summarizes block device I/O (disk I/O) as a latency histogram


###### bitesize
###### seeksize
###### biopattern
###### biostacks
###### bioerr
###### issched
###### blkthrot

##### 6.5 Sockets

###### sofdsnoop
###### sockstat
###### sofamily
###### soprotocol
###### sormem
###### soconnect
###### soaccept
###### socketio
###### socksize
###### soconnlat
###### so1stbyte
###### skbdrop
###### skblife

##### 6.6 TCP/UDP

###### tcptop
###### tcplife
###### tcptracer
###### tcpconnect
###### tcpaccept
###### tcpconnlat
###### tcpretrans
###### tcpsubnet
###### tcpdrop
###### tcpstates
###### tcpsynbl
###### tcpwin
###### tcpnagle
###### tcpreset
###### updconnect

##### 6.7 IP

###### ipecn
###### superping
###### qdisc-fq

##### 6.8 Scheduler

###### cpudist
###### cpuwalk
###### runqlat
###### runqlen
###### runqslower
###### cppunclaimed
###### deadlock
###### offcputime
###### wakeuptime
###### offwaketime
###### softirqs
###### offcpuhist
###### threaded
###### pidnss
###### mlock
###### mheld
###### smpcalls
###### workq

##### 6.9 Virtual Memory

###### slabratetop
###### oomkill
###### memleak
###### shmsnoop
###### drssnoop
###### kmem
###### kpages
###### numamove
###### mmapsnoop
###### brkstack
###### faults
###### ffaults
###### fmapfault
###### hfaults
###### vmscan
###### swapin

#### 7 硬件

传统工具: perf, sar, /proc计数器.

##### 7.1 Net Device

###### ieee80211scan

##### 7.2 Device Drivers

###### hardirqs
###### criticalstat
###### ttysnoop
###### scsilatency
###### scsiresult
###### nvmelatency

##### 7.3 CPU

###### llcstat
###### cpufreq

#### 8 Other

###### capable
###### xenhyper
###### kvmexits

## bpfrace

### bpftrace编程

usage:

```
bpftrace -e program
bpfrace file.bt
```

program structure:

```
program:
  // regular probes
  probes { actions }
  probes { actions }
  /*
  * probes with fitler
  */
  probes /filter/ { actions }
  // probes to execute same actions
  probe1,probe2,... { actions }

probes:
  type:identifier1[:identifier2[:...]]
  kprobe:vfs_read
  kprobe:vfs_*
  uprobe:/bin/bash:readline
  // 查看通配符适配的探针: bpftrace -l 'kprobe:vfs_*'

filter:
  /pid = 123/
  /pid/
  /pid > 100 && pid < 1000/

actions:
  { action one; action two; action three;}
  { $x = 42; printf("$x is %d", $x); }

functions:
  printf()
  exit()        // exit bpftrace
  str(char *)   // return a string from a pointer
  system(format[, arguments ...]) // run a command at the shell

variables:
  // built-in
  pid           // process id
  comm          // process name
  nsecs         // timestamp in nanoseconds
  curtask       // address of the current thread's task_struct
  tid           // current thread ID
  // scratch
  $1 = 1;
  $y = "hello";
  $z = (struct task_struct *) curtask;
  // map: global storage
  probe1 { @a = 1; }
  probe2 { $x = @a; }
  @start[tid] = nsecs;
  @path[pid, $fd] = str(arg0);  // multi-key map

map-functions:
  @x = count();   // a per-CPU map
  @x++;           // a global CPU map
  @y = sum($x);
  @z = hist($x);
  print(@x);
  delete(@start[tid]);
```

#### bpftrace Probe Types:

- tracepoint(t): 内核静态探查点

```
tracepoint:tracepoint_name

tracepoint:syscalls:sys_enter_read
tracepoint:syscalls:sys_exit_read

// ssize_t read(int fd, void *buf, size_t count);
$ bpftrace -lv tracepoint:syscalls:sys_enter_read
tracepoint:syscalls:sys_enter_read
    int __syscall_nr;
    unsigned int fd;
    char * buf;
    size_t count;
// 引用参数: args->fd, args->buf, args->count, args->ret

$ bpftrace -e 'tracepoint:syscalls:sys_enter_read {
printf("-> clone() by %s PID %d\n", comm, pid); }
tracepoint:syscalls:sys_exit_read {
printf("<- clone() return %d, %s PID %d\n", args->ret, comm, pid); }'
$ bpftrace -e 't:syscalls:sys_*_execve { printf("%s %s PID %d\n", probe, comm, pid); }'
```

- usdt(U): 用户级静态定义的跟踪

```
usdt:binary_path:probe_name
usdt:libraty_path:probe_name
usdt:binary_path:probe_namespace:probe_name
usdt:library_path:probe_namespace:probe_name

bpftrace -l 'usdt:/usr/bin/python3'
```

- kprobe(k), kretprobe(kr): 内核动态函数(返回)探查

```
kprobe:function_name
kretprobe:function_name

// 参数arg0, arg1, ..., argN
// 返回值retval
```

- uprobe(u), uretprobe(ur): 用户层动态函数(返回)探查

```
uprobe:binary_path:function_name
uprobe:library_path:function_name
uretprobe:binary_path:function_name
uretprobe:library_path:function_name

// 参数arg0, arg1, ..., argN
// 返回值retval
```

- software(s),  hardware(h): 内核基于软件的事件, 硬件基于计数器的探查

```
software:event_name:count
software:event_name:
hardware:event_name:count
hardware:event_name:
```

`event_name`:

```
// software
cpu-clock
task-clock
page-faults
context-switches
cpu-migrations
minor-faults
major-faults
alignment-faults
emulation-faults
dummy
bpf-output

// hardware
cpu-cycles
instructions
cache-references
cache-misses
branch-instructions
bus-cycles
frontend-stalls
backend-stalls
ref-cycles
```

- profile(p), interval(i): 跨所有CPU的基于时间的采样, (单个CPU)的基于时间的报告

```
profile:hz:rate     // Hertz(events per second)
profile:s:rate      // seconds
profile:ms:rate     // milliseconds
profile:us:rate     // microseconds
interval:s:rate
interval:ms:rate
```

- BEGIN: bpftrace的开始
- END: bpftrace的结束

#### Flow Control

- tests: fitler, ternary operator, if statement
- tests: `==`, `!=`, `>`, `<`, `>=`, `<=`, `&&`, `||`
- 表达式可以使用括号括起来
- 有限的支持循环
- ternary operator:

```
test ? true_statement : false_statement

$abs = $x >= 0 ? $x : -$x;
```

- if statement:

```
if (test) { true_statements }
if (test) { true_statements } else { false_statements }
```

- unrolles loop

```
unroll (count) { statements }
// count最大值为20
```

#### Operators

- `=`: 赋值
- `+`, `-`, `*`, `/`: 加减乘除
- `++`, `--`: 自增, 自减
- `&`, `|`, `^`: 二进制与/或/异或
- `!`: 逻辑非
- `<<`, `>>`: 左移, 右移
- `+=`, `-=`, `*=`, `%=`, `&=`, `^=`, `<<=`, `>>=`: 复合操作符

#### Variables

内建变量:

- pid: 进程ID
- tid: 线程ID
- uid: 用户ID
- username: 用户名称
- nsecs: 时间戳, 纳秒
- elapsed: 自bpftrace初始化开始的时间戳, 纳秒
- cpu: 处理器ID
- comm: 进程名称
- kstatck: 内核调用栈
- ustack: 用户层调用栈
- arg0, ..., argN: 一些探针类型的参数
- args: 一些探针类型的参数
- retval: 一些探针类型的范沪指
- func: 被跟踪的函数的名称
- probe: 当前探针的全名
- curtask: 内核`task_struct`
- cgroup: Cgroup ID
- $1, ..., $N: bpftrace程序的位置参数

临时变量:

```
$name
```

map变量:

```
@name
@name[key]
@name[key1, key2[, ...]]

@start = nsecs;           // integer type
@last[tid] = nsecs;       // key: integer, value: integer
@bytes = hist(retval);    // power-of-two histogram type
@who[pid, comm] = count();  // key: integer+string, value: count() map function

```

#### Functions

```
printf(char *fmt, [, ...])    // 格式化输出
time(char *fmt)               // 输出格式化的时间
join(char *arr[]);            // 输出字符串数组, 按空格拼接
str(char *s [, int len])      // 返回指针s处的字符串
kstack(int limit)             // 返回内核栈, 有深度限制
kstack(mode [, limit])        // mode: bpftrace, perf
ustack(int limit)             // 返回用户栈, 有深度限制
ustack(mod [, limit])
ksym(void *p)                 // 接卸内核地址, 返回字符串符号
usym(void *p)                 // 解析用户控件地址, 返回字符串符号
kaddr(char *name)             // 解析内核符号名称到地址
uaddr(char *name)             // 解析用户空间符号名称到地址
reg(char *name)               // 返回寄存器中存储的值
ntop([int af, ] int addr)     // 返回IP地址的字符串表示
system(char *fmt, [, ...])    // 执行shell命令
cat(char *filename)           // 输出文件内容
exit()                        // 退出bpftrace
```

##### Map Functions

```
count()                     // 出现次数计数
sum(int n)                  // 和
avg(int n)                  // 平均值
min(int n)                  // 最小值
max(int n)                  // 最大值
stats(int n)                // 次数, 平均值, 和
hist(int n)                 // 值的power-of-two之防护
lhist(int n, int min, int max, int step) // 值的线性直方图
delete(@m[key])             // 删除键值对
print(@m[, top [, div]])    // 输出map
clean(@m)                   // 清空map
zero(@m)                    // 将所有值设置为0
```

#### Debugging

### bpftrace One-Liners

```
bpftrace -e 'tracepoint:syscalls:sys_enter_execve { printf("%s -> %s\n", comm, str(args->filename)); }'
bpftrace -e 'tracepoint:syscalls:sys_enter_execve { join(args->argv); }'
bpftrace -e 'tracepoint:syscalls:sys_enter_openat { printf("%s %s\n", comm, str(args->filename)); }'
bpftrace -e 'tracepoint:raw_syscalls:sys_enter { @[comm] = count(); }'
bpftrace -e 'tracepoint:syscalls:sys_enter_* { @[probe] = count(); }'
bpftrace -e 'tracepoint:raw_syscalls:sys_enter { @[pid, comm] = count(); }'
bpftrace -e 'tracepoint:syscalls:sys_exit_read /args->ret/ { @[comm] = sum(args->ret); }'
bpftrace -e 'tracepoint:syscalls:sys_exit_read { @[comm] = hist(args->ret); }'
bpftrace -e 'tracepoint:block:block_rq_issue { printf("%d %s %d\n", pid, comm, args->bytes); }'
bpftrace -e 'software:major-faults:1 { @[comm] = count(); }'
bpftrace -e 'software:faults:1 { @[comm] = count(); }'
bpftrace -e 'profile:hz:49 /pid == 189/ { @[ustack] = count(); }'
```


### bpftrace工具汇总

```
# list all probes
bpfrace -l '*'
```


#### bashreadline.bt
#### biolatency.bt
#### biosnoop.bt
#### biostacks.bt
#### bitesize.bt
#### capable.bt
#### cpuwalk.bt
#### dcsnoop.bt
#### execsnoop.bt
#### gethostlatency.bt
#### killsnoop.bt
#### loads.bt
#### mdflush.bt
#### naptime.bt
#### oomkill.bt
#### opensnoop.bt
#### pidpersec.bt
#### runqlat.bt
#### runqlen.bt
#### setuids.bt
#### statsnoop.bt
#### swapin.bt
#### syncsnoop.bt
#### syscount.bt
#### tcpaccept.bt
#### tcpconnect.bt
#### tcpdrop.bt
#### tcplife.bt
#### tcpretrans.bt
#### tcpsynbl.bt
#### threadsnoop.bt
#### vfscount.bt
#### vfsstat.bt
#### writeback.bt
#### xfsdist.bt
