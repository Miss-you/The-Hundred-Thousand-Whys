## bcc 和 bpftrace 有什么区别吖？

bcc 是一套工具集，提供基于 python 写的脚本编译出来的可执行文件，每个可执行文件都有自己的特定的功能，用户可以通过运行这些可执行文件，获取特定的信息，如本机的 tcp 连接情况等。bcc 也提供一堆 sdk，方便用户可编程化对接。

bpftrace 是一门高阶语言，相对 bcc 来说，使用起来更灵活。下面是一个典型的 bpftrace 例子：

```
# Files opened by process
bpftrace -e 'tracepoint:syscalls:sys_enter_open { printf("%s %s\n", comm, str(args->filename)); }'
```

-e 后面的语法就是 bpftrace 特定语法，通过最前面 bpftrace 命令行工具，转移成 bpf 指令，然后与 bcc 对接，实现和系统的集成。

当然可以使用 ebpf 来干性能指标监控，实现起来比较底层，sysdig 和 datadog 的 agent 可能实现了。如果是 k8s 世界，node-exporter 和 prometheus 工具链干这些事情可能更成熟，都是读取本机上关于进程性能的文件。

prometherus 比较适合做秒级或者分钟级的监控，但如果出现了性能问题，往往需要火焰图之类的数据，需要实时的采集，这时候 prometherus 是解决不了的

对于实时采集的场景，bpf 可以实现 attach 到你确定的目标方法上，观测变化的数值。最新看到一家叫 pixie lab 的公司，给出了一些解决方案：https://blog.pixielabs.ai/ebpf-function-tracing/post/ 你看看是否有所启发

不过简单的火焰图之类的，go-pprof 也可以解决

## ebpf 是解决 go-pprof 所解决不了的什么问题呢？

当前我的认知是，如果语言有自己的运行时或者虚拟机，比如 java 或者 golang，使用语言原生的监控工具比较合适；如果是裸的，比如 C、C++、rust，那么系统的 perf、ebpf 系列以及 systemtap 比较合适