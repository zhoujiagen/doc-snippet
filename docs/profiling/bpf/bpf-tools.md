# BPF工具

## 资源

- [Linux kernel source tree](https://github.com/torvalds/linux): Linux Kerbel on Github
- [Documentation/trace](https://github.com/torvalds/linux/tree/master/Documentation/trace): Linux trace documets

## 工具汇总

|#|工具|说明|描述|
|-|:---|:---|:---|
|1 | [argdist](#argdist)    | 函数参数的频率或直方图. | Trace a function and display a histogram or frequency count of its parameter values. Uses Linux eBPF/bcc.|
|2 | bashreadline    | | |
|3 | biolatency    | | |
|4 | biosnoop    | | |
|5 | biotop    | | |
|6 | bitesize    | | |
|7 | bpflist    | | |
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

## 1 ALL

### trace

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
# fs/open.c
trace 'do_sys_open "%s", arg2'
trace 'r::do_sys_open "ret: %d", retval'
# kernel/time/hrtimer.c
trace -U 'do_nanosleep "mode: %d", arg2'
trace 'do_nanosleep(struct hrtimer_sleeper *t) "task: %x", t->task'
# pam lib
trace 'pam:pam_start "%s: %s", arg1, arg2'
```


### argdist

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
# 内核函数vfs_read()的返回值的直方图
argdist -H 'r::vfs_read()'
# 进程1005中用户级libc中read()的返回值的直方图
argdist -p 1005 -H 'r:c:read()' # PID=1005
# 按系统调用ID计数
argdist -C 't:raw_syscalls:sys_enter():int:args->id'
# tcp_sendmsg(...)中size参数的计数
argdist -C 'p::tcp_sendmsg(struct sock *sk, struct msghdr *msg, size_t size):u32:size'
argdist -H 'p::tcp_sendmsg(struct sock *sk, struct msghdr *msg, size_t size):u32:size'
# 进程181中libc的write(...)中fd参数的计数
argdist -p 181 -C 'p:c:write(int fd):int:fd'
# 延迟>0.1ms的进程的频率
argdist -C 'r::__vfs_read():u32:$PID:$lantency > 100000'
```

### funccount

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

### funcslower
### funclatency
### stackcount

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

### profile



## 2 Applications

传统工具: 系统调试器.

### ucalls
### uflow
### uobjnew
### ustat
### uthreads
### ugc
### mysqld_qslower
### dbstat
### dbslower
### bashreadline
### mysqld_clat
### bashfunc
### bashfunclat

## 3 Runtimes

传统工具: 运行时调试器.

### javathreads
### jnistacks

## 4 System Libraries

传统工具: ltrace(1).

### gethostlatency
### memleak
### sslsniff
### threadssnoop
### pmlock
### pmheld

## 5 System Call Interface

传统工具: strace(1), perf(1).

### scread
### opensnoop

```
# 跟踪所有open()系统调用
opensnoop
# 跟踪10内的所有open()系统调用
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

### statsnoop
### syncsnoop
### ioprofile
### syscount
### killsnoop
### shellsnoop
### signals
### naptime
### eperm
### setuids
### elfsnoop
### modsnoop
### execsnoop

works by tracing the execve(2) system call


### exitsnoop
### pidpersec

## 6 内核

传统工具: Ftrace, perf(1).


### 6.1 VFS

#### filetop
#### filelife
#### fileslower
#### vfscount
#### vfsstat
#### filetype
#### fsrwstat
#### vfssize
#### mmapfiles
#### writesync
#### cacheestat
#### cachetop
#### dcstat
#### dcsnoop
#### mountsnoop
#### icstat
#### bufgrow
#### readahead
#### writeback

### 6.2 File Systems

#### btrfsslower, btrfsdist
#### ext4slower, ext4dist
#### nfsslower, nfsdist
#### xfsslower, xfsdist
#### zfsslower, zfsdist
#### overlayfs

### 6.3 Volume Manager

#### mdflush

### 6.4 Block Device

#### biotop
#### biosnoop
#### biolatency

summarizes block device I/O (disk I/O) as a latency histogram


#### bitesize
#### seeksize
#### biopattern
#### biostacks
#### bioerr
#### issched
#### blkthrot

### 6.5 Sockets

#### sofdsnoop
#### sockstat
#### sofamily
#### soprotocol
#### sormem
#### soconnect
#### soaccept
#### socketio
#### socksize
#### soconnlat
#### so1stbyte
#### skbdrop
#### skblife

### 6.6 TCP/UDP

#### tcptop
#### tcplife
#### tcptracer
#### tcpconnect
#### tcpaccept
#### tcpconnlat
#### tcpretrans
#### tcpsubnet
#### tcpdrop
#### tcpstates
#### tcpsynbl
#### tcpwin
#### tcpnagle
#### tcpreset
#### updconnect

### 6.7 IP

#### ipecn
#### superping
#### qdisc-fq

### 6.8 Scheduler

#### cpudist
#### cpuwalk
#### runqlat
#### runqlen
#### runqslower
#### cppunclaimed
#### deadlock
#### offcputime
#### wakeuptime
#### offwaketime
#### softirqs
#### offcpuhist
#### threaded
#### pidnss
#### mlock
#### mheld
#### smpcalls
#### workq

### 6.9 Virtual Memory

#### slabratetop
#### oomkill
#### memleak
#### shmsnoop
#### drssnoop
#### kmem
#### kpages
#### numamove
#### mmapsnoop
#### brkstack
#### faults
#### ffaults
#### fmapfault
#### hfaults
#### vmscan
#### swapin

## 7 硬件

传统工具: perf, sar, /proc计数器.

### 7.1 Net Device

#### ieee80211scan

### 7.2 Device Drivers

#### hardirqs
#### criticalstat
#### ttysnoop
#### scsilatency
#### scsiresult
#### nvmelatency

### 7.3 CPU

#### llcstat
#### cpufreq

## 8 Other

#### capable
#### xenhyper
#### kvmexits
