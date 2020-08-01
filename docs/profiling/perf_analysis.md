# Performacnce Analysis

## 资源

- Brendan Gregg. Systems performance : enterprise and the cloud. 2013.


## Linux 60-Second Analysis

|步骤|说明|
|:---|:---|
|`uptime`| 查看平均负载. |
|`dmesg | tail`| 查看最后几条系统消息. |
|`vmstat 1`| 每秒输出虚拟内存统计信息. |
|`mpstat -P ALL 1`| 查看每个CPU的统计时间. |
|`pidstat 1`| 查看每个进程的CPU使用情况. |
|`iostat -xz 1`| 查看存储设备I/O情况. |
|`free -m`| 查看可用内存. |
|`sar -n DEV 1`| 查看网络设备情况. |
|`sar -n TCP,ETCP 1`| 查看TPC情况. |
|`top`| 查看系统和进程汇总信息. |

- uptime - Tell how long the system has been running.
- dmesg - print or control the kernel ring buffer
- vmstat - Report virtual memory statistics
- free - Display amount of free and used memory in the system
- top - display Linux processes

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

- mpstat - Report processors related statistics.
- pidstat - Report statistics for Linux tasks.
- iostat - Report Central Processing Unit (CPU) statistics and input/output statistics for devices and partitions.
- sar - Collect, report, or save system activity information.




## 工具

|名称|概述|
|:---|:---|
|||
