# Notes of BPF

## 术语

- tracing: event-based recording.
- snooping: snooping, event dumping, and tracing usually refer to the same thing.
- sampling: take a subset of measurements to paint a coarse picture of the target.
- observability: refers to understanding a system through observation, and classifies the tools that accomplish this. These tools includes tracing tools, sampling tools, and tools based on fixed counters. It does not include benchmark tools.
- dynamic instrumentation: dynamic tracing, insert instrumentation points into live software, in production. kprobes for kernel functions, uprobes for user-level functions.
- static instrumentation: stable event names are coded into the software and manintained by the developers. tracepoints for kernel, user-level statically defined tracing(USDT) for user-level.
- instrument: 探查
- tracepoint: 跟踪点
- probe: 探针

## 前端

### BCC: BPF Compiler Collection

[BCC (BPF Compiler Collection)](https://github.com/iovisor/bcc) was the first higher-level tracing framework developed for BPF.
It provides a C programming environment for writing kernel BPF code and other languages for the user-level interface: Python, Lua, and C++.

- [BCC - Tools for BPF-based Linux IO analysis, networking, monitoring, and more](https://github.com/iovisor/bcc): Github
- [bcc Reference Guide](https://github.com/iovisor/bcc/blob/master/docs/reference_guide.md)
- [bcc Python Developer Tutorial](https://github.com/iovisor/bcc/blob/master/docs/tutorial_bcc_python_developer.md)

#### Features

##### Kernel-Level Features

- dynamic instrumentation, kernel-level: kprobes
- dynamic instrumentation, user-level: uprobes
- static tracing, kernel-level: tracepoints
- timed sampling events: BPF with `perf_event_open()`
- PMC events: BPF with `perf_event_open()`
- filtering: via BPF programs
- debug output: `bpf_trace_printk()`
- per-event output: `bpf_perf_event_output()`
- basic variables: global and per-thread variables, via BPF maps
- associative arrays: via BPF maps
- frequency counting: via BPF maps
- histograms: power-of-two, linear, custom, via BPF maps
- timestamps and time deltas: `bpf_ktime_get_ns()` and BPF programs
- stack trace, kernel: BPF stackmap
- stack trace, user: BPF stackmap
- overwrite ring buffers: `perf_event_attr.write_backward`
- low-overhead instrumentation: BP JIT, BPF map summarizes
- production safe: BPF verifier

##### User-Level Features

- static tracing, user-level: SystemTap-stype USDT probes, via uprobes
- debug output: Python with `BPF.trace_pipe()` and `BPF_.trace_fields()`
- per-event output: `BPF_PERF_OUTPUT` macro and `BPF.open_perf_buffer()`
- interval output: `BPF.get_table()` and `table.clear()`
- histogram printing: `table.print_log2_hist()`
- C struct navigation, kernel-level: BCC rewriter maps to `bpf_probe_read()`
- symbol resolution, kernel-level: `ksym()`, `ksymaddr()`
- symbol resolution, user-level: `usymaddr()`
- debuginfo symbol resolution support
- BPF tracepoint support: via `TRACEPOINT_PROBE`
- BPF stack trace support: `BPF_STACK_TRACE`
- various other helper macros and functions
- examples: under `/examples`
- tools: under `/tools`
- tutorials: under `/docs/tutorial*.md`
- reference guide: under `/docs/reference_guide.md`

### bpftrace

[bpftrace](https://github.com/iovisor/bpftrace) is a newer front end that provides a special-purpose, high-level language for developing BPF tools.

#### Features



### ply

It is designed to be lightweight and require minimal dependencies, which makes it a good fit for embedded Linux environments.


## instrumentation

### dynamic instrumentation

#### kprobes
#### uprobes

### static instrumentation

#### tracepoints

- [Notes on Analysing Behaviour Using Events and Tracepoints](https://www.kernel.org/doc/html/latest/trace/tracepoint-analysis.html): Linux Kernel doc
- [Using the Linux Kernel Tracepoints, by Mathieu Desnoyers](https://www.kernel.org/doc/Documentation/trace/tracepoints.txt)

```
# 查看所有可能的事件
$ find /sys/kernel/debug/tracing/events -type d

# 使用PCL(Performance Counters for Linux)
$ perf list 2>&1 | grep Tracepoint
```

所有可能的事件见[tracepoints.md](tracepoints.md).


#### USDT: user-level statically defined tracing
