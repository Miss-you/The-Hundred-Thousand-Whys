# Chapter 1

## 业务高可用通常考虑什么？

### 高可用

高可用HA（High Availability）是分布式系统架构设计中必须考虑的因素之一，它通常是指，一个系统经过专门的设计，以减少停工时间，而保持其服务的高度可用性。

### 服务熔断

服务熔断的作用类似于我们家用的保险丝，当某服务出现不可用或响应超时的情况时，为了防止整个系统出现雪崩，暂时停止对该服务的调用。

### 服务降级

服务降级是当服务器压力剧增的情况下，根据当前业务情况及流量对一些服务和页面有策略的降级，以此释放服务器资源以保证核心任务的正常运行。降级往往会指定不同的级别，面临不同的异常等级执行不同的处理。
根据服务方式：可以拒接服务，可以延迟服务，也有时候可以随机服务。
根据服务范围：可以砍掉某个功能，也可以砍掉某些模块。
总之服务降级需要根据不同的业务需求采用不同的降级策略。主要的目的就是服务虽然有损但是总比没有好。

> 熔断和降级

~ 相同点
目标一致：都是从可用性和可靠性出发，为了防止系统崩溃；
用户体验类似：最终都让用户体验到的是某些功能暂时不可用；
~ 不同点：
触发原因不同：服务熔断一般是某个服务（下游服务）故障引起，而服务降级一般是从整体负荷考虑；

### 服务限流

限流可以认为服务降级的一种，限流就是限制系统的输入和输出流量已达到保护系统的目的。一般来说系统的吞吐量是可以被测算的，为了保证系统的稳定运行，一旦达到的需要限制的阈值，就需要限制流量并采取一些措施以完成限制流量的目的。比如：延迟处理，拒绝处理，或者部分拒绝处理等等。

### 压测

### 容灾

### 高可用方法论

高可用在不同维度考虑的点也各不相同：
产品策略：轻重逻辑分离、用户分流、功能简化
客户端：重试、失败提示优化、客户端随机丢弃请求
接入层：全局限流、服务降级、鉴权和ACL
逻辑层：识别热点对象和热点对象预处理、事务一致性以及事务回滚
存储层：可靠性（主备/异地容灾/数据持久化）、一致性
运维：灰度发布、故障演练、混沌工程

### 线上压测的一些经验

压测有一些常见的注意点：

做业务压力测试时，最好使用线上的数据和线上的环境。因为测试环境和线上环境往往存在各种各样的差异，影响压力测试结果。另一方面，为了不影响正常业务，压测时往往需要在业务低峰期压测，比如22点以后或者早上9点以前。
压测测试时尽量使用线上流量或者模拟线上流量。因为线上业务的访问模型并不是平均的，无差别压测和模拟线上流量的模型的压测他们两者的结果往往会差异很大。
不要从单一的服务器发起流量。一是压测时容易达到压测机器的瓶颈，影响压测结果；另一方面，由于网络链路中负载均衡、会话保持等功能的存在，单台机器压测往往会负载不均，也会影响压测结果。

### 限流业务场景

限流通常用于保护后端服务或者当前服务不被流量冲垮，保障服务的高可用性，常见的比如接入层网关的限流功能或者各种RPC/微服务框架的限流插件（比如 Sentinel 框架），这种情况下，服务的可用性更为重要，偶尔的限流不准是可以接受的；限流也可以用于调用计费，比如腾讯云云市场会将服务商的 API 以流量包(调用次数)的形式售卖给客户，针对 API 调用频次进行计费，因为涉及到钱，这种情况下 API 的调用次数就要求绝对精确，不容闪失。

### 分布式限流

实际接入层网关业务往往是分布式的，单机限流并不能满足需要，往往需要分布式限流。虽然可以通过预设，将限流值平均到每台机器上，但若负载流量不均匀、机器宕机或者临时扩容，均会严重影响分布式限流功能。

本人当前了解到的生产级分布式限流均是集中式限流方案：
全局限流状态在一个集中式节点/集群维护（比如redis等），定期向这个中心计数或者取令牌
集中式节点/集群也存在可能性不可用，若不可用，则通常办法是将分布式限流退化成单机限流。

## Apache APISIX 毕业贺词

“Congratulations to Apache APISIX for graduating as an Apache Top-Level Project,” said Hui Li, Engineer at Tencent Cloud. “Recent growth in demand for interconnection between mobile applications, enterprise interoperability, and the Internet of Things have expanded backend service support objects from single Web applications to a variety of usage scenarios. This increases both the access pressure and the complexity of backend services. A suitable solution for this issue is an API Gateway: in addition to basic request forwarding, protocol conversion, routing and other functions such as high performance and high stability, it also has good scalability and can continuously enhance the capabilities of the gateway. We evaluated many API gateways, and finally chose Apache APISIX as the core component of our new generation API gateway because of its high performance, high scalability, and active community. I hope to see APISIX’s future development have a far-reaching impact on the microservices field.”



