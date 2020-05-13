# 系统调用

## 资源

> glibc system call wrapper: https://sourceware.org/glibc/wiki/SyscallWrappers
>
> How to find your current set of Linux system calls: https://www.cs.fsu.edu/~langley/current-system-calls.html
>> ls /usr/share/man/man2 | sed -e s/.2.gz//g | xargs man -s 2 -k  | sort | grep -v 'unimplemented system calls'

- [Linux 系统调用权威指南（2016）](https://arthurchiao.github.io/blog/system-call-definitive-guide-zh/)
- [Linux System Call Table for X86 64](https://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)

### Linux Man Page

[The Linux man-pages project](https://www.kernel.org/doc/man-pages/)

1: [User commands](http://man7.org/linux/man-pages/dir_section_1.html); man-pages includes a very few Section 1 pages that document programs supplied by the GNU C library. [菜鸟教程, Linux 命令大全](https://www.runoob.com/linux/linux-command-manual.html)<br>
2: [System calls](http://man7.org/linux/man-pages/dir_section_2.html) documents the system calls provided by the Linux kernel.<br>
3: [Library functions](http://man7.org/linux/man-pages/dir_section_3.html) documents the functions provided by the standard C library.<br>
4: [Devices](http://man7.org/linux/man-pages/dir_section_4.html) documents details of various devices, most of which reside in /dev.<br>
5: [Files](http://man7.org/linux/man-pages/dir_section_5.html) describes various file formats, and includes proc(5), which documents the /proc file system.<br>
7: [Overviews, conventions, and miscellaneous](http://man7.org/linux/man-pages/dir_section_7.html).<br>
8: [Superuser and system administration commands](http://man7.org/linux/man-pages/dir_section_8.html); man-pages includes a very few Section 8 pages that document programs supplied by the GNU C library.

## Ubuntu 14.04

``` Shell
$ uname -a
Linux gda 4.4.0-143-generic #169~14.04.2-Ubuntu SMP Wed Feb 13 15:00:41 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux

$ ls /usr/share/man/man2 | sed -e s/.2.gz//g | xargs man -s 2 -k  | sort
```



|系统调用|说明|
|:---|:---|
|_Exit|terminate the calling process|
|__clone2|create a child process|
|_exit|terminate the calling process|
|_llseek|reposition read/write file offset|
|_newselect|synchronous I/O multiplexing|
|_syscall|invoking a system call without library support (OBSOLETE)|
|_sysctl|read/write system parameters|
|accept|accept a connection on a socket|
|accept4|accept a connection on a socket|
|access|check real user's permissions for a file|
|acct|switch process accounting on or off|
|add_key|add a key to the kernel's key management facility|
|adjtimex|tune kernel clock|
|afs_syscall|unimplemented system calls|
|alarm|set an alarm clock for delivery of a signal|
|alloc_hugepages|allocate or free huge pages|
|arch_prctl|set architecture-specific thread state|
|arm_fadvise|predeclare an access pattern for file data|
|arm_fadvise64_64|predeclare an access pattern for file data|
|arm_sync_file_range|sync a file segment with disk|
|bdflush|start, flush, or tune buffer-dirty-flush daemon|
|bind|bind a name to a socket|
|break|unimplemented system calls|
|brk|change data segment size|
|cacheflush|flush contents of instruction and/or data cache|
|capget|set/get capabilities of thread(s)|
|capset|set/get capabilities of thread(s)|
|chdir|change working directory|
|chmod|change permissions of a file|
|chown|change ownership of a file|
|chown32|change ownership of a file|
|chroot|change root directory|
|clock_getres|clock and time functions|
|clock_gettime|clock and time functions|
|clock_nanosleep|high-resolution sleep with specifiable clock|
|clock_settime|clock and time functions|
|clone|create a child process|
|clone2|create a child process|
|close|close a file descriptor|
|connect|initiate a connection on a socket|
|creat|open and possibly create a file or device|
|create_module|create a loadable module entry|
|delete_module|unload a kernel module|
|dup|duplicate a file descriptor|
|dup2|duplicate a file descriptor|
|dup3|duplicate a file descriptor|
|epoll_create|open an epoll file descriptor|
|epoll_create1|open an epoll file descriptor|
|epoll_ctl|control interface for an epoll descriptor|
|epoll_pwait|wait for an I/O event on an epoll file descriptor|
|epoll_wait|wait for an I/O event on an epoll file descriptor|
|eventfd|create a file descriptor for event notification|
|eventfd2|create a file descriptor for event notification|
|execve|execute program|
|exit|terminate the calling process|
|exit_group|exit all threads in a process|
|faccessat|check user's permissions of a file relative to a direc...|
|fadvise64|predeclare an access pattern for file data|
|fadvise64_64|predeclare an access pattern for file data|
|fallocate|manipulate file space|
|fattach|unimplemented system calls|
|fchdir|change working directory|
|fchmod|change permissions of a file|
|fchmodat|change permissions of a file relative to a directory f...|
|fchown|change ownership of a file|
|fchown32|change ownership of a file|
|fchownat|change ownership of a file relative to a directory fil...|
|fcntl|manipulate file descriptor|
|fcntl64|manipulate file descriptor|
|fdatasync|synchronize a file's in-core state with storage device|
|fdetach|unimplemented system calls|
|finit_module|load a kernel module|
|flock|apply or remove an advisory lock on an open file|
|fork|create a child process|
|free_hugepages|allocate or free huge pages|
|fstat|get file status|
|fstat64|get file status|
|fstatat|get file status relative to a directory file descriptor|
|fstatat64|get file status relative to a directory file descriptor|
|fstatfs|get filesystem statistics|
|fstatfs64|get filesystem statistics|
|fstatvfs|get filesystem statistics|
|fsync|synchronize a file's in-core state with storage device|
|ftruncate|truncate a file to a specified length|
|ftruncate64|truncate a file to a specified length|
|futex|fast user-space locking|
|futimesat|change timestamps of a file relative to a directory fi...|
|get_kernel_syms|retrieve exported kernel and module symbols|
|get_mempolicy|retrieve NUMA memory policy for a process|
|get_robust_list|get/set list of robust futexes|
|get_thread_area|get a thread-local storage (TLS) area|
|getcontext|get or set the user context|
|getcpu|determine CPU and NUMA node on which the calling threa...|
|getcwd|get current working directory|
|getdents|get directory entries|
|getdents64|get directory entries|
|getdomainname|get/set NIS domain name|
|getdtablesize|get descriptor table size|
|getegid|get group identity|
|getegid32|get group identity|
|geteuid|get user identity|
|geteuid32|get user identity|
|getgid|get group identity|
|getgid32|get group identity|
|getgroups|get/set list of supplementary group IDs|
|getgroups32|get/set list of supplementary group IDs|
|gethostid|get or set the unique identifier of the current host|
|gethostname|get/set hostname|
|getitimer|get or set value of an interval timer|
|getmsg|unimplemented system calls|
|getpagesize|get memory page size|
|getpeername|get name of connected peer socket|
|getpgid|set/get process group|
|getpgrp|set/get process group|
|getpid|get process identification|
|getpmsg|unimplemented system calls|
|getppid|get process identification|
|getpriority|get/set program scheduling priority|
|getresgid|get real, effective and saved user/group IDs|
|getresgid32|get real, effective and saved user/group IDs|
|getresuid|get real, effective and saved user/group IDs|
|getresuid32|get real, effective and saved user/group IDs|
|getrlimit|get/set resource limits|
|getrusage|get resource usage|
|getsid|get session ID|
|getsockname|get socket name|
|getsockopt|get and set options on sockets|
|gettid|get thread identification|
|gettimeofday|get / set time|
|getuid|get user identity|
|getuid32|get user identity|
|getunwind|copy the unwind data to caller's buffer|
|gtty|unimplemented system calls|
|idle|make process 0 idle|
|inb|port I/O|
|inb_p|port I/O|
|init_module|load a kernel module|
|inl|port I/O|
|inl_p|port I/O|
|inotify_add_watch|add a watch to an initialized inotify instance|
|inotify_init|initialize an inotify instance|
|inotify_init1|initialize an inotify instance|
|inotify_rm_watch|remove an existing watch from an inotify instance|
|insb|port I/O|
|insl|port I/O|
|insw|port I/O|
|intro|introduction to system calls|
|inw|port I/O|
|inw_p|port I/O|
|io_cancel|cancel an outstanding asynchronous I/O operation|
|io_destroy|destroy an asynchronous I/O context|
|io_getevents|read asynchronous I/O events from the completion queue|
|io_setup|create an asynchronous I/O context|
|io_submit|submit asynchronous I/O blocks for processing|
|ioctl|control device|
|ioctl_list|list of ioctl calls in Linux/i386 kernel|
|ioperm|set port input/output permissions|
|iopl|change I/O privilege level|
|ioprio_get|get/set I/O scheduling class and priority|
|ioprio_set|get/set I/O scheduling class and priority|
|ipc|System V IPC system calls|
|isastream|unimplemented system calls|
|kcmp|compare two processes to determine if they share a ker...|
|kexec_load|load a new kernel for later execution|
|keyctl|manipulate the kernel's key management facility|
|kill|send signal to a process|
|killpg|send signal to a process group|
|lchown|change ownership of a file|
|lchown32|change ownership of a file|
|link|make a new name for a file|
|linkat|create a file link relative to directory file descriptors|
|listen|listen for connections on a socket|
|llseek|reposition read/write file offset|
|lock|unimplemented system calls|
|lookup_dcookie|return a directory entry's path|
|lseek|reposition read/write file offset|
|lstat|get file status|
|lstat64|get file status|
|madvise|give advice about use of memory|
|madvise1|unimplemented system calls|
|mbind|set memory policy for a memory range|
|migrate_pages|move all pages in a process to another set of nodes|
|mincore|determine whether pages are resident in memory|
|mkdir|create a directory|
|mkdirat|create a directory relative to a directory file descri...|
|mknod|create a special or ordinary file|
|mknodat|create a special or ordinary file relative to a direct...|
|mlock|lock and unlock memory|
|mlockall|lock and unlock memory|
|mmap|map or unmap files or devices into memory|
|mmap2|map files or devices into memory|
|modify_ldt|get or set ldt|
|mount|mount filesystem|
|move_pages|move individual pages of a process to another node|
|mprotect|set protection on a region of memory|
|mpx|unimplemented system calls|
|mq_getsetattr|get/set message queue attributes|
|mq_notify|register for notification when a message is available|
|mq_open|open a message queue|
|mq_timedreceive|receive a message from a message queue|
|mq_timedsend|send a message to a message queue|
|mq_unlink|remove a message queue|
|mremap|remap a virtual memory address|
|msgctl|System V message control operations|
|msgget|get a System V message queue identifier|
|msgop|System V message queue operations|
|msgrcv|System V message queue operations|
|msgsnd|System V message queue operations|
|msync|synchronize a file with a memory map|
|munlock|lock and unlock memory|
|munlockall|lock and unlock memory|
|munmap|map or unmap files or devices into memory|
|nanosleep|high-resolution sleep|
|nfsservctl|syscall interface to kernel nfs daemon|
|nice|change process priority|
|oldfstat|get file status|
|oldlstat|get file status|
|oldolduname|get name and information about current kernel|
|oldstat|get file status|
|olduname|get name and information about current kernel|
|open|open and possibly create a file or device|
|openat|open a file relative to a directory file descriptor|
|outb|port I/O|
|outb_p|port I/O|
|outl|port I/O|
|outl_p|port I/O|
|outsb|port I/O|
|outsl|port I/O|
|outsw|port I/O|
|outw|port I/O|
|outw_p|port I/O|
|pause|wait for signal|
|pciconfig_iobase|pci device information handling|
|pciconfig_read|pci device information handling|
|pciconfig_write|pci device information handling|
|perf_event_open|set up performance monitoring|
|perfmonctl|interface to IA-64 performance monitoring unit|
|personality|set the process execution domain|
|phys|unimplemented system calls|
|pipe|create pipe|
|pipe2|create pipe|
|pivot_root|change the root filesystem|
|poll|wait for some event on a file descriptor|
|posix_fadvise|predeclare an access pattern for file data|
|ppoll|wait for some event on a file descriptor|
|prctl|operations on a process|
|pread|read from or write to a file descriptor at a given offset|
|pread64|read from or write to a file descriptor at a given offset|
|preadv|read or write data into multiple buffers|
|prlimit|get/set resource limits|
|process_vm_readv|transfer data between process address spaces|
|process_vm_writev|transfer data between process address spaces|
|prof|unimplemented system calls|
|pselect|synchronous I/O multiplexing|
|pselect6|synchronous I/O multiplexing|
|ptrace|process trace|
|putmsg|unimplemented system calls|
|putpmsg|unimplemented system calls|
|pwrite|read from or write to a file descriptor at a given offset|
|pwrite64|read from or write to a file descriptor at a given offset|
|pwritev|read or write data into multiple buffers|
|quotactl|manipulate disk quotas|
|read|read from a file descriptor|
|readahead|perform file readahead into page cache|
|readdir|read directory entry|
|readlink|read value of a symbolic link|
|readlinkat|read value of a symbolic link relative to a directory ...|
|readv|read or write data into multiple buffers|
|reboot|reboot or enable/disable Ctrl-Alt-Del|
|recv|receive a message from a socket|
|recvfrom|receive a message from a socket|
|recvmmsg|receive multiple messages on a socket|
|recvmsg|receive a message from a socket|
|remap_file_pages|create a nonlinear file mapping|
|rename|change the name or location of a file|
|renameat|rename a file relative to directory file descriptors|
|request_key|request a key from the kernel's key management facility|
|restart_syscall|restart a system call after interruption by a stop signal|
|rmdir|delete a directory|
|rt_sigaction|examine and change a signal action|
|rt_sigpending|examine pending signals|
|rt_sigprocmask|examine and change blocked signals|
|rt_sigqueueinfo|queue a signal and data|
|rt_sigreturn|return from signal handler and cleanup stack frame|
|rt_sigsuspend|wait for a signal|
|rt_sigtimedwait|synchronously wait for queued signals|
|rt_tgsigqueueinfo|queue a signal and data|
|s390_runtime_instr|enable/disable s390 CPU run-time instrumentation|
|sbrk|change data segment size|
|sched_get_priority_max|get static priority range|
|sched_get_priority_min|get static priority range|
|sched_getaffinity|set and get a thread's CPU affinity mask|
|sched_getparam|set and get scheduling parameters|
|sched_getscheduler|set and get scheduling policy/parameters|
|sched_rr_get_interval|get the SCHED_RR interval for the named process|
|sched_setaffinity|set and get a thread's CPU affinity mask|
|sched_setparam|set and get scheduling parameters|
|sched_setscheduler|set and get scheduling policy/parameters|
|sched_yield|yield the processor|
|security|unimplemented system calls|
|select|synchronous I/O multiplexing|
|select_tut|synchronous I/O multiplexing|
|semctl|System V semaphore control operations|
|semget|get a System V semaphore set identifier|
|semop|System V semaphore operations|
|semtimedop|System V semaphore operations|
|send|send a message on a socket|
|sendfile|transfer data between file descriptors|
|sendfile64|transfer data between file descriptors|
|sendmmsg|send multiple messages on a socket|
|sendmsg|send a message on a socket|
|sendto|send a message on a socket|
|set_robust_list|get/set list of robust futexes|
|set_thread_area|set a thread local storage (TLS) area|
|set_tid_address|set pointer to thread ID|
|setcontext|get or set the user context|
|setdomainname|get/set NIS domain name|
|setegid|set effective user or group ID|
|seteuid|set effective user or group ID|
|setfsgid|set group identity used for filesystem checks|
|setfsgid32|set group identity used for filesystem checks|
|setfsuid|set user identity used for filesystem checks|
|setfsuid32|set user identity used for filesystem checks|
|setgid|set group identity|
|setgid32|set group identity|
|setgroups|get/set list of supplementary group IDs|
|setgroups32|get/set list of supplementary group IDs|
|sethostid|get or set the unique identifier of the current host|
|sethostname|get/set hostname|
|setitimer|get or set value of an interval timer|
|setns|reassociate thread with a namespace|
|setpgid|set/get process group|
|setpgrp|set/get process group|
|setpriority|get/set program scheduling priority|
|setregid|set real and/or effective user or group ID|
|setregid32|set real and/or effective user or group ID|
|setresgid|set real, effective and saved user or group ID|
|setresgid32|set real, effective and saved user or group ID|
|setresuid|set real, effective and saved user or group ID|
|setresuid32|set real, effective and saved user or group ID|
|setreuid|set real and/or effective user or group ID|
|setreuid32|set real and/or effective user or group ID|
|setrlimit|get/set resource limits|
|setsid|creates a session and sets the process group ID|
|setsockopt|get and set options on sockets|
|settimeofday|get / set time|
|setuid|set user identity|
|setuid32|set user identity|
|setup|setup devices and filesystems, mount root filesystem|
|sgetmask|manipulation of signal mask (obsolete)|
|shmat|System V shared memory operations|
|shmctl|System V shared memory control|
|shmdt|System V shared memory operations|
|shmget|allocates a System V shared memory segment|
|shmop|System V shared memory operations|
|shutdown|shut down part of a full-duplex connection|
|sigaction|examine and change a signal action|
|sigaltstack|set and/or get signal stack context|
|signal|ANSI C signal handling|
|signalfd|create a file descriptor for accepting signals|
|signalfd4|create a file descriptor for accepting signals|
|sigpending|examine pending signals|
|sigprocmask|examine and change blocked signals|
|sigqueue|queue a signal and data to a process|
|sigreturn|return from signal handler and cleanup stack frame|
|sigsuspend|wait for a signal|
|sigtimedwait|synchronously wait for queued signals|
|sigwaitinfo|synchronously wait for queued signals|
|socket|create an endpoint for communication|
|socketcall|socket system calls|
|socketpair|create a pair of connected sockets|
|splice|splice data to/from a pipe|
|spu_create|create a new spu context|
|spu_run|execute an SPU context|
|ssetmask|manipulation of signal mask (obsolete)|
|stat|get file status|
|stat64|get file status|
|statfs|get filesystem statistics|
|statfs64|get filesystem statistics|
|statvfs|get filesystem statistics|
|stime|set time|
|stty|unimplemented system calls|
|subpage_prot|define a subpage protection for an address range|
|swapoff|start/stop swapping to file/device|
|swapon|start/stop swapping to file/device|
|symlink|make a new name for a file|
|symlinkat|create a symbolic link relative to a directory file de...|
|sync|commit buffer cache to disk|
|sync_file_range|sync a file segment with disk|
|sync_file_range2|sync a file segment with disk|
|syncfs|commit buffer cache to disk|
|syscall|indirect system call|
|syscalls|Linux system calls|
|sysctl|read/write system parameters|
|sysfs|get filesystem type information|
|sysinfo|returns information on overall system statistics|
|syslog|read and/or clear kernel message ring buffer; set cons...|
|tee|duplicating pipe content|
|tgkill|send a signal to a thread|
|time|get time in seconds|
|timer_create|create a POSIX per-process timer|
|timer_delete|delete a POSIX per-process timer|
|timer_getoverrun|get overrun count for a POSIX per-process timer|
|timer_gettime|arm/disarm and fetch state of POSIX per-process timer|
|timer_settime|arm/disarm and fetch state of POSIX per-process timer|
|timerfd_create|timers that notify via file descriptors|
|timerfd_gettime|timers that notify via file descriptors|
|timerfd_settime|timers that notify via file descriptors|
|times|get process times|
|tkill|send a signal to a thread|
|truncate|truncate a file to a specified length|
|truncate64|truncate a file to a specified length|
|tuxcall|unimplemented system calls|
|ugetrlimit|get/set resource limits|
|umask|set file mode creation mask|
|umount|unmount filesystem|
|umount2|unmount filesystem|
|uname|get name and information about current kernel|
|unimplemented|unimplemented system calls|
|unlink|delete a name and possibly the file it refers to|
|unlinkat|remove a directory entry relative to a directory file ...|
|unshare|disassociate parts of the process execution context|
|uselib|load shared library|
|ustat|get filesystem statistics|
|utime|change file last access and modification times|
|utimensat|change file timestamps with nanosecond precision|
|utimes|change file last access and modification times|
|vfork|create a child process and block parent|
|vhangup|virtually hangup the current terminal|
|vm86|enter virtual 8086 mode|
|vm86old|enter virtual 8086 mode|
|vmsplice|splice user pages into a pipe|
|vserver|unimplemented system calls|
|wait|wait for process to change state|
|wait3|wait for process to change state, BSD style|
|wait4|wait for process to change state, BSD style|
|waitid|wait for process to change state|
|waitpid|wait for process to change state|
|write|write to a file descriptor|
|writev|read or write data into multiple buffers|
