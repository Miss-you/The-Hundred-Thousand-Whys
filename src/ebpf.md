## bcc和bpftrace有什么区别吖？

bcc是一套工具集，提供基于python写的脚本编译出来的可执行文件，每个可执行文件都有自己的特定的功能，用户可以通过运行这些可执行文件，获取特定的信息，如本机的tcp连接情况等。bcc也提供一堆sdk，方便用户可编程化对接。

bpftrace是一门高阶语言，相对bcc来说，使用起来更灵活。下面是一个典型的bpftrace例子：

```
# Files opened by process
bpftrace -e 'tracepoint:syscalls:sys_enter_open { printf("%s %s\n", comm, str(args->filename)); }'
```

-e后面的语法就是bpftrace特定语法，通过最前面bpftrace命令行工具，转移成bpf指令，然后与bcc对接，实现和系统的集成。

当然可以使用ebpf来干性能指标监控，实现起来比较底层，sysdig和datadog的agent可能实现了。如果是k8s世界，node-exporter和prometheus工具链干这些事情可能更成熟，都是读取本机上关于进程性能的文件。

prometherus比较适合做秒级或者分钟级的监控，但如果出现了性能问题，往往需要火焰图之类的数据，需要实时的采集，这时候prometherus是解决不了的

对于实时采集的场景，bpf可以实现attach到你确定的目标方法上，观测变化的数值。最新看到一家叫pixie lab的公司，给出了一些解决方案：https://blog.pixielabs.ai/ebpf-function-tracing/post/ 你看看是否有所启发

不过简单的火焰图之类的，go-pprof也可以解决
