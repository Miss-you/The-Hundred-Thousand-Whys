# nginx

## nginx进程在何种情况下会发送RST包给对端？

## nginx graceful shutdown timeout

shutdown timeout
旧 worker 进程不能及时退出，就会一直占用着系统资源（CPU、内存和文件描述符等），这对系统资源是一种浪费，因此 nginx/1.11.11 加入了一个新的指令（即 worker_shutdown_timeout，见 Core functionality），允许用户自定义 shutdown 超时时间，如果一个 worker 在接收到退出的指令后经过 worker_shutdown_timeout 时长后还不能退出，就会被强制退出。

它的实现原理（nginx: 97c99bb43737）也是通过创建定时器来实现的，一旦定时器过期， 所有连接都会被设置为 close 和 error 状态（c->error = 1，c->close = 1），这个标志位事实上意味着 TCP 连接异常，nginx 设计上对于这种状态的连接，都会立刻结束对应的所有请求、事件。通过这样一个标志位的设置，就达到了强制关闭所有连接、删除所有定时器的目的，最终及时退出旧的 worker 进程，释放系统资源。

虽然这个功能早在 nginx/1.11.11 就加入了，但是没有完全覆盖到所有的情况，例如上文所述的 websocket 连接的处理，那部分代码并没有判断 c->close 和 c->error 的状态位。所以仍然无法尽快终止这些 websocket 连接。直到 nginx/1.13.7，这个问题才被修复。所以如果读者们遇到类似的问题，可以考虑升级 nginx 至少到 1.13.7 版本。

## nginx TCP_NODELAY 和 TCP_NOPUSH配置


## nginx 转发请求body被截断

## 业界网关情况

- 百度接入网关是bfe，golang的
- 阿里是tengine的slb
- 腾讯是基于nginx的clb，正在自己重写C++版本的网关，但并不考虑太多旧向兼容
- 腾讯ieg是基于envoy istio 做的东西向南北向统一网关
- 网易是网易轻舟的envoy+luajit+kong插件 + istio的网关（同时融合了mesh

## nginx https常见优化手段

- Session ID 复用
- OCSP Stapling
- HSTS
- HTTP/2
- False Start
- 支持 ChaCha20-Poly1305 加密套件