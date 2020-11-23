# nginx

## nginx 进程在何种情况下会发送 RST 包给对端？

## nginx graceful shutdown timeout

shutdown timeout
旧 worker 进程不能及时退出，就会一直占用着系统资源（CPU、内存和文件描述符等），这对系统资源是一种浪费，因此 nginx/1.11.11 加入了一个新的指令（即 worker_shutdown_timeout，见 Core functionality），允许用户自定义 shutdown 超时时间，如果一个 worker 在接收到退出的指令后经过 worker_shutdown_timeout 时长后还不能退出，就会被强制退出。

它的实现原理（nginx: 97c99bb43737）也是通过创建定时器来实现的，一旦定时器过期， 所有连接都会被设置为 close 和 error 状态（c->error = 1，c->close = 1），这个标志位事实上意味着 TCP 连接异常，nginx 设计上对于这种状态的连接，都会立刻结束对应的所有请求、事件。通过这样一个标志位的设置，就达到了强制关闭所有连接、删除所有定时器的目的，最终及时退出旧的 worker 进程，释放系统资源。

虽然这个功能早在 nginx/1.11.11 就加入了，但是没有完全覆盖到所有的情况，例如上文所述的 websocket 连接的处理，那部分代码并没有判断 c->close 和 c->error 的状态位。所以仍然无法尽快终止这些 websocket 连接。直到 nginx/1.13.7，这个问题才被修复。所以如果读者们遇到类似的问题，可以考虑升级 nginx 至少到 1.13.7 版本。

## nginx TCP_NODELAY 和 TCP_NOPUSH 配置

## nginx 转发请求 body 被截断

## 业界网关情况

- 百度接入网关是 bfe，golang 的
- 阿里是 tengine 的 slb
- 腾讯是基于 nginx 的 clb，正在自己重写 C++版本的网关，但并不考虑太多旧向兼容
- 腾讯 ieg 是基于 envoy istio 做的东西向南北向统一网关
- 网易是网易轻舟的 envoy+luajit+kong 插件 + istio 的网关（同时融合了 mesh

## nginx https 常见优化手段

- Session ID 复用
- OCSP Stapling
- HSTS
- HTTP/2
- False Start
- 支持 ChaCha20-Poly1305 加密套件

## PKCS 是什么？

PKCS 全称是 Public-Key Cryptography Standards ，是由 RSA 实验室与其它安全系统开发商为促进公钥密码的发展而制订的一系列标准，PKCS 目前共发布过 15 个标准。 常用的有：

- PKCS#7 Cryptographic Message Syntax Standard
- PKCS#10 Certification Request Standard
- PKCS#12 Personal Information Exchange Syntax Standard

X.509 是常见通用的证书格式。所有的证书都符合为 Public Key Infrastructure (PKI) 制定的 ITU-T X509 国际标准。

- PKCS#7 常用的后缀是： .P7B .P7C .SPC
- PKCS#12 常用的后缀有： .P12 .PFX
- X.509 DER 编码 (ASCII) 的后缀是： .DER .CER .CRT
- X.509 PAM 编码 (Base64) 的后缀是： .PEM .CER .CRT
- .cer/.crt 是用于存放证书，它是 2 进制形式存放的，不含私钥。
- .pem 跟 crt/cer 的区别是它以 Ascii 来表示。
- pfx/p12 用于存放个人证书/私钥，他通常包含保护密码，2 进制方式
- p10 是证书请求
- p7r 是 CA 对证书请求的回复，只用于导入
- p7b 以树状展示证书链 (certificate chain)，同时也支持单个证书，不含私钥。

## gzip 是什么？

Gzip 是一种用于文件压缩与解压缩的文件格式。它基于 Deflate 算法，可将文件（译者注：快速地、流式地）压缩地更小，从而实现更快的网络传输。 Web 服务器与现代浏览器普遍地支持 Gzip，这意味着服务器可以在发送文件之前自动使用 Gzip 压缩文件，而浏览器可以在接收文件时自行解压缩文件。

## gzip 压缩的是 body 还是整个 response？

gzip 仅仅对 body 进行压缩

nginx 处理 gzip 的逻辑


## ngx.var.http_HEADER 是不是也有可能出现table的值？

不会，只会取第一个值

## ngx.var.http_$header 和 ngx.req.get_headers 的区别

后续整理