## envoy 基本术语

- Downstream（下游）：下游主机连接到 Envoy，发送请求并接收响应，即发送请求的主机。
- Upstream（上游）：上游主机接收来自 Envoy 的连接和请求，并返回响应，即接受请求的主机。
- Listener（监听器）：监听器是命名网地址（例如，端口、unix domain socket 等)，下游客户端可以连接这些监听器。Envoy 暴露一个或者多个监听器给下游主机连接。
- Cluster（集群）：集群是指 Envoy 连接的一组逻辑相同的上游主机。Envoy 通过服务发现来发现集群的成员。可以选择通过主动健康检查来确定集群成员的健康状态。Envoy 通过负载均衡策略决定将请求路由到集群的哪个成员。

## envoy 国内厂商使用现状

- 百度接入网关是bfe，golang的
- 阿里是tengine的slb
- 腾讯是基于nginx的clb，正在自己重写C++版本的网关，但并不考虑太多旧向兼容
- 腾讯ieg是基于envoy istio 做的东西向南北向统一网关
- 网易是网易轻舟的envoy+luajit+kong插件 + istio的网关（同时融合了mesh