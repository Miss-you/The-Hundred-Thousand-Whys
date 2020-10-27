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


## lua on envoy 缺少的功能

1. 当前不支持正则表达式
2. 当前不支持core.log日志打印
3. 不支持schema校验，jsonschema
4. 不支持cosocket
5. 需要更快更好的json库
6. 

## envoy apisix.core 的一些设计思路

1. 如何管理配置？xDS metadata下发配置
2. apisix.core就是做了一层封装，比如屏蔽or平台和envoy平台接口差异，比如封装日志打印接口、封装内存池、之类的
3. 为什么不是ngx-lua-module？过去or出现主要原因是nginx没有调用luajit逻辑，但envoy有luafilter，or这个nginx模块或许不是那么必要。实际上来看也是如此

设计理念的经验：那么不同平台接口差异怎么屏蔽？很简单，加一个中间层，那就是apisix.core