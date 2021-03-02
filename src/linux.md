# linux

## 如何区分一个 linux 命令的 shell 输出是 stdin、还是 stderr 呢？举个具体的例子，curl -v 或者-i 返回的结果是标准 stdin，还是 stderr？

1. linux 中一切设备皆是文件，包括 stdin/stdout/stderr。那他们是指哪个文件？
2. 以 stdin 为例，使用命令`find / -name stdin`查找，其对应 linux 文件为/dev/stdin。那么它是什么样的文件类型？[1]
3. 使用 ls -l 可以查看文件类型，其结果为`lrwxrwxrwx 1 root root 15 Mar 26 20:45 /dev/stdin -> /proc/self/fd/0`，故它是链接文件，我们也可以看到它刚好对应的就是/proc/self/fd/0。这里能看出什么呢？
4. 分别 ls -l 查看 stdin/stdout/stderr，他们刚好对应的就是`fd/0,fd/1,fd/2`，也就是大家常说的 stdin 对应 0，balabala……。在实际中，shell 中怎么使用呢？
5. `curl url > stdin.log 2>stderr.log`，标准输出会到 stdin.log 文件，错误输出会到 stderr.log 文件。正如第四条，1 对应标准输出 stdout，2 对应标准错误 stderr。再回到原问，怎么区分 stdout 和 stderr 的输出呢？
6. 可以将输出替换为有颜色的，比如`\e[91m`是红色，`\e[92m`是绿色，`\e[93m`是黄色。示例命令：`curl -v 111.230.86.254 > >(sed $'s/.*/\e[92m&\e[m/')  2> >(sed $'s/.*/\e[91m&\e[m/'>&2)`。有兴趣的可以自己试试。最后再思考一点，代码里面 stdout 和 stderr 对应什么呢？
7. golang 是 `os.Stdin/os.Stderr`，C 是 `stdout/stderr`，比如`fprintf(stdout,"hello world!\n");`。

[1] linux 文件类型：
- 普通文件
- 字符设备文件
- 块设备文件
- 目录文件
- 链接文件
- 管道文件
- 套接字文件

## linux 查看文件类型的方法

## objdump 命令常用参数有哪些？分别有啥用，解决什么问题？

我常用如下：

- objdump -o，查看符号表
- objdump -g，查看是否开了调试参数（这样才能 gdb/dlv 调试）

## hostname 有什么用？

每次登录到 linux 上，就会看到`yousali@nj-9-113-20-175:~$   `这样的提示符，@后面的就是`nj-9-113-20-175`

```
yousali@nj-9-113-20-175:~$ hostname
nj-9-113-20-175
```

hostname 的用途：
a. 可以知道自己是不是登录错机器了。
b. 在本机的`/etc/hosts`文件中添加经常访问的机器的 ip 地址和主机名的映射关系，下次访问时，可以`ssh yousali@nj-9-113-20-175`来登录，不用记住 IP 地址了（前提是人家的 ip 没有变）。

可以通过如下方式修改 hostname
- ubuntu 在这个文件中  /etc/hostname
- centos 系统是在这个文件中 /etc/sysconfig/network 

## 为什么需要内存对齐？

进行内存对齐的原因：（主要是硬件设备方面的问题）

1. 部分不支持。某些硬件设备只能存取对齐数据，存取非对齐的数据可能会引发异常；
2. 原子操作。某些硬件设备不能保证在存取非对齐数据的时候的操作是原子操作；
3. 性能差。相比于存取对齐的数据，存取非对齐的数据需要花费更多的时间；
4. 特殊bug。某些处理器虽然支持非对齐数据的访问，但会引发对齐陷阱（alignment trap）；
5. 支持不完善。某些硬件设备只支持简单数据指令非对齐存取，不支持复杂数据指令的非对齐存取。

内存对齐的优点：

1. 可移植性好。便于在不同的平台之间进行移植，因为有些硬件平台不能够支持任意地址的数据访问，只能在某些地址处取某些特定的数据，否则会抛出异常；
2. 对齐内存读取性能高。提高内存的访问效率，因为 CPU 在读取内存时，是一块一块的读取。