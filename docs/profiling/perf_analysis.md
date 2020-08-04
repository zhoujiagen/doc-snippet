# Performacnce Analysis

## 资源

- Brendan Gregg. Systems performance : enterprise and the cloud. 2013.


## Linux 60-Second Analysis

|#|步骤|说明|描述|
|-|---|:---|:---|
|1|`uptime`| 查看平均负载. |Tell how long the system has been running |
|2|`dmesg | tail`| 查看最后几条系统消息. |print or control the kernel ring buffer|
|3|`vmstat 1`| 每秒输出虚拟内存统计信息. |Report virtual memory statistics|
|4|`mpstat -P ALL 1`| 查看每个CPU的统计时间. |Report processors related statistics |
|5|`pidstat 1`| 查看每个进程的CPU使用情况. |Report statistics for Linux tasks|
|6|`iostat -xz 1`| 查看存储设备I/O情况. |Report Central Processing Unit (CPU) statistics and input/output statistics for devices and partitions|
|7|`free -m`| 查看可用内存. |Display amount of free and used memory in the system|
|8|`sar -n DEV 1`| 查看网络设备情况. |Collect, report, or save system activity information|
|9|`sar -n TCP,ETCP 1`| 查看TPC情况. |同上 |
|10|`top`| 查看系统和进程汇总信息. |display Linux processes|


`sysstat`:

```
$ sudo apt install sysstat
$ apt info sysstat
Description: system performance tools for Linux
 The sysstat package contains the following system performance tools:
  - sar: collects and reports system activity information;
  - iostat: reports CPU utilization and disk I/O statistics;
  - tapestat: reports statistics for tapes connected to the system;
  - mpstat: reports global and per-processor statistics;
  - pidstat: reports statistics for Linux tasks (processes);
  - sadf: displays data collected by sar in various formats;
  - cifsiostat: reports I/O statistics for CIFS filesystems.
```

## BCC Tool Checklist

|#|步骤|说明|描述|
|-|---|:---|:---|
|1|`execsnoop`|输出系统调用`execve(2)`创建的新进程.|Trace new processes via exec() syscalls. Uses Linux eBPF/bcc.|
|2|`opensnoop`|输出系统调用`open(2)`及其变种的调用情况.|Trace open() syscalls. Uses Linux eBPF/bcc.|
|3|`ext4slower`<br>`btrfsslower`<br>`xfsslower`<br>`zfsslower`| 查看文件系统中慢的磁盘I/O.|Trace slow ext4/btrfs/xfs/zfs file operations, with per-event details.|
|4|`biolatency`|以直方图形式输出磁盘I/O延迟.|Summarize block device I/O latency as a histogram.|
|5|`biosnoop`|输出每个磁盘I/O详情及其延迟.|Trace block device I/O and print details incl. issuing PID.|
|6|`cachestat`|每秒输出文件系统缓存的统计信息.|Statistics for linux page cache hit/miss ratios. Uses Linux eBPF/bcc.|
|7|`tcpconnect`|输出主动TCP连接的信息.|Trace TCP active connections (connect()). Uses Linux eBPF/bcc.|
|8|`tcpaccept`|输出被动TCP连接信息.|Trace TCP passive connections (accept()). Uses Linux eBPF/bcc.|
|9|`tcpretrans`|输出每个TCP重传报文.|Trace or count TCP retransmits and TLPs. Uses Linux eBPF/bcc.|
|10|`runqlat`|以直方图形式输出线程等待CPU的时间.|Run queue (scheduler) latency as a histogram.|
|11|`profile`|CPU探查器, 用于理解消耗CPU资源的代码路径.|Profile CPU usage by sampling stack traces. Uses Linux eBPF/bcc.|
