# Notes of BPF

## 术语

- tracing: event-based recording.
- snooping: snooping, event dumping, and tracing usually refer to the same thing.
- sampling: take a subset of measurements to paint a coarse picture of the target.
- observability: refers to understanding a system through observation, and classifies the tools that accomplish this. These tools includes tracing tools, sampling tools, and tools based on fixed counters. It does not include benchmark tools.
- dynamic instrumentation: dynamic tracing, insert instrumentation points into live software, in production. kprobes for kernel functions, uprobes for user-level functions.
- static instrumentation: stable event names are coded into the software and manintained by the developers. tracepoints for kernel, user-level statically defined tracing(USDT) for user-level.

## 前端

### BCC

[BCC (BPF Compiler Collection)](https://github.com/iovisor/bcc) was the first higher-level tracing framework developed for BPF.
It provides a C programming environment for writing kernel BPF code and other languages for the user-level interface: Python, Lua, and C++.

### bpftrace

[bpftrace](https://github.com/iovisor/bpftrace) is a newer front end that provides a special-purpose, high-level language for developing BPF tools.

### ply

It is designed to be lightweight and require minimal dependencies, which makes it a good fit for embedded Linux environments.


## instrumentation

### dynamic instrumentation

- kprobes
- uprobes

### static instrumentation

- tracepoints
- user-level statically defined tracing(USDT)


## 工具

### 汇总

|组件|工具|说明|
|:---|:---|:---|
|ALL|trace||
|ALL|argdist||
|ALL|funccount||
|ALL|funcslower||
|ALL|funclatency||
|ALL|stackcount||
|ALL|profile||
|Applications|c\*, java\*, node\*, php\*, python\*, ruby\*|应用进程.|
|Applications|ucalls||
|Applications|uflow||
|Applications|uobjnew||
|Applications|ustat||
|Applications|uthreads||
|Applications|ugc||
|Applications|mysqld_qslower||
|Applications|dbstat||
|Applications|dbslower||
|Applications|bashreadline||
|Applications|mysqld_clat||
|Applications|bashfunc||
|Applications|bashfunclat||
|Runtimes|javathreads||
|Runtimes|jnistacks||
|System Libraries|gethostlatency||
|System Libraries|memleak||
|System Libraries|sslsniff||
|System Libraries|threadssnoop||
|System Libraries|pmlock||
|System Libraries|pmheld||
|System Call Interface|scread||
|System Call Interface|opensnoop|跟踪open(2)系统调用.|
|System Call Interface|statsnoop||
|System Call Interface|syncsnoop||
|System Call Interface|ioprofile||
|System Call Interface|syscount||
|System Call Interface|killsnoop||
|System Call Interface|shellsnoop||
|System Call Interface|signals||
|System Call Interface|naptime||
|System Call Interface|eperm||
|System Call Interface|setuids||
|System Call Interface|elfsnoop||
|System Call Interface|modsnoop||
|System Call Interface|execsnoop|跟踪execve(2)系统调用.|
|System Call Interface|exitsnoop||
|System Call Interface|pidpersec||
|VFS|filetop||
|VFS|filelife||
|VFS|fileslower||
|VFS|vfscount||
|VFS|vfsstat| VFS的状态|
|VFS|filetype||
|VFS|fsrwstat||
|VFS|vfssize||
|VFS|mmapfiles||
|VFS|writesync||
|VFS|cacheestat||
|VFS|cachetop||
|VFS|dcstat||
|VFS|dcsnoop||
|VFS|mountsnoop||
|VFS|icstat||
|VFS|bufgrow||
|VFS|readahead||
|VFS|writeback||
|File Systems|btrfsslower, btrfsdist||
|File Systems|ext4slower, ext4dist||
|File Systems|nfsslower, nfsdist||
|File Systems|xfsslower, xfsdist||
|File Systems|zfsslower, zfsdist||
|File Systems|overlayfs||
|Volume Manager|mdflush||
|Block Device|biotop||
|Block Device|biosnoop||
|Block Device|biolatency|以直方图形式展示磁盘I/O.|
|Block Device|bitesize||
|Block Device|seeksize||
|Block Device|biopattern||
|Block Device|biostacks||
|Block Device|bioerr||
|Block Device|issched||
|Block Device|blkthrot||
|Sockets|sofdsnoop||
|Sockets|sockstat||
|Sockets|sofamily||
|Sockets|soprotocol||
|Sockets|sormem||
|Sockets|soconnect||
|Sockets|soaccept||
|Sockets|socketio||
|Sockets|socksize||
|Sockets|soconnlat||
|Sockets|so1stbyte||
|Sockets|skbdrop||
|Sockets|skblife||
|TCP/UDP|tcptop||
|TCP/UDP|tcplife||
|TCP/UDP|tcptracer||
|TCP/UDP|tcpconnect||
|TCP/UDP|tcpaccept||
|TCP/UDP|tcpconnlat||
|TCP/UDP|tcpretrans||
|TCP/UDP|tcpsubnet||
|TCP/UDP|tcpdrop||
|TCP/UDP|tcpstates||
|TCP/UDP|tcpsynbl||
|TCP/UDP|tcpwin||
|TCP/UDP|tcpnagle||
|TCP/UDP|tcpreset||
|TCP/UDP|updconnect||
|IP|ipecn||
|IP|superping||
|IP|qdisc-fq||
|Net Device|ieee80211scan||
|Scheduler|cpudist||
|Scheduler|cpuwalk||
|Scheduler|runqlat||
|Scheduler|runqlen||
|Scheduler|runqslower||
|Scheduler|cppunclaimed||
|Scheduler|deadlock||
|Scheduler|offcputime||
|Scheduler|wakeuptime||
|Scheduler|offwaketime||
|Scheduler|softirqs||
|Scheduler|offcpuhist||
|Scheduler|threaded||
|Scheduler|pidnss||
|Scheduler|mlock||
|Scheduler|mheld||
|Scheduler|smpcalls||
|Scheduler|workq||
|Virtual Memory|slabratetop||
|Virtual Memory|oomkill||
|Virtual Memory|memleak||
|Virtual Memory|shmsnoop||
|Virtual Memory|drssnoop||
|Virtual Memory|kmem||
|Virtual Memory|kpages||
|Virtual Memory|numamove||
|Virtual Memory|mmapsnoop||
|Virtual Memory|brkstack||
|Virtual Memory|faults||
|Virtual Memory|ffaults||
|Virtual Memory|fmapfault||
|Virtual Memory|hfaults||
|Virtual Memory|vmscan||
|Virtual Memory|swapin||
|Device Drivers|hardirqs||
|Device Drivers|criticalstat||
|Device Drivers|ttysnoop||
|Device Drivers|scsilatency||
|Device Drivers|scsiresult||
|Device Drivers|nvmelatency||
|CPU|llcstat||
|CPU|cpufreq||
|Other|capable||
|Other|xenhyper||
|Other|kvmexits||

### ALL

### Applications

传统工具: 系统调试器.

### Runtimes

传统工具: 运行时调试器.

### System Libraries

传统工具: ltrace(1).

### System Call Interface

传统工具: strace(1), perf(1).

#### execsnoop(8)

works by tracing the execve(2) system call

### 内核

传统工具: Ftrace, perf(1).


#### VFS

#### File Systems

#### Volume Manager

#### Block Device

##### biolatency(8)

summarizes block device I/O (disk I/O) as a latency histogram


#### Sockets

#### TCP/UDP

#### IP

#### Scheduler

#### Virtual Memory

### 硬件

传统工具: perf, sar, /proc计数器.

#### Net Device

#### Device Drivers

#### CPU

### Other
