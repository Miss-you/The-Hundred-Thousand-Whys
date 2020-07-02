# linux

## 如何区分一个linux命令的shell输出是stdin、还是stderr呢？举个具体的例子，curl -v或者-i返回的结果是标准stdin，还是stderr？

1. linux中一切设备皆是文件，包括stdin/stdout/stderr。那他们是指哪个文件？
2. 以stdin为例，使用命令`find / -name stdin`查找，其对应linux文件为/dev/stdin。那么它是什么样的文件类型？[1]
3. 使用ls -l可以查看文件类型，其结果为`lrwxrwxrwx 1 root root 15 Mar 26 20:45 /dev/stdin -> /proc/self/fd/0`，故它是链接文件，我们也可以看到它刚好对应的就是/proc/self/fd/0。这里能看出什么呢？
4. 分别ls -l查看stdin/stdout/stderr，他们刚好对应的就是`fd/0,fd/1,fd/2`，也就是大家常说的stdin对应0，balabala……。在实际中，shell中怎么使用呢？
5. `curl url > stdin.log 2>stderr.log`，标准输出会到stdin.log文件，错误输出会到stderr.log文件。正如第四条，1对应标准输出stdout，2对应标准错误stderr。再回到原问，怎么区分stdout和stderr的输出呢？
6. 可以将输出替换为有颜色的，比如`\e[91m`是红色，`\e[92m`是绿色，`\e[93m`是黄色。示例命令：`curl -v 111.230.86.254 > >(sed $'s/.*/\e[92m&\e[m/')  2> >(sed $'s/.*/\e[91m&\e[m/'>&2)`。有兴趣的可以自己试试。最后再思考一点，代码里面stdout和stderr对应什么呢？
7. golang 是 `os.Stdin/os.Stderr`，C 是 `stdout/stderr`，比如`fprintf(stdout,"hello world!\n");`。

[1] linux文件类型：
- 普通文件
- 字符设备文件
- 块设备文件
- 目录文件
- 链接文件
- 管道文件
- 套接字文件

## linux查看文件类型的方法

