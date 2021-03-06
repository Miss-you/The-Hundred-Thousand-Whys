## 使用容器常见问题

在使用容器的时候，大家很可能遇到过这几个问题：

1、在 Docker 容器中执行 top、free 等命令，会发现看到的资源使用情况都是宿主机的资源情况，而我们需要的是这个容器被限制了多少 CPU，内存，当前容器内的进程使用了多少；

2、在容器里修改/etc/sysctl.conf, 会收到提示”sysctl: error setting key ‘net.ipv4….’: Read-only file system”；

3、程序运行在容器里面，调用 API 获取系统内存、CPU，取到的是宿主机的资源大小；

4、对于多进程程序，一般都可以将 worker 数量设置成 auto, 自适应系统 CPU 核数，但在容器里面这么设置，取到的 CPU 核数是不正确的，例如 Nginx, 其他应用取到的可能也不正确，需要进行测试。

关键指标 `/sys/fs/cgroup/cpu/cpu.cfs_quota_us、/sys/fs/cgroup/cpu/cpu.cfs_period_us 与 /sys/fs/cgroup/cpuset/cpuset.cpus`