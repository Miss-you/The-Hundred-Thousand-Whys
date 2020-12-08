## controller 和 Operator 的异同

一个是 CP 面，配置下发，也可以认为是一个业务或者是一个 svc

另一个是运维部署相关的组件，主要用于自动化扩容缩容，调度部署，自愈恢复，版本升级等等

## CRD 里的 condtions、phase、state 这三个该如何理解？

phase/state 是和业务无关的状态，如成功、失败、重试等。conditions 是和业务相关的状态，如校验、初始化、调度等状态，如果业务层的 conditions 状态都没问题，phase/state 状态就是成功的，否则就是其它状态。

phase/state 的一部分原因是因为 没有一个机制把 condition 转换为 string 打印出来，所以有的时候为了方便打印，就会引入 phase，这样 kubectl get 的时候就可以看到大概的 condition

kubernetes api conventions 里面关于 condition 和 phase 的介绍：https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api-conventions.md#typical-status-properties

## K8S service 自动发现和外挂的自动发现在应用上有什么区别么

服务注册过程指的是在服务注册表中登记一个服务，以便让其它服务发现，这两者都是这个含义。首先需要服务注册中心，然后需要服务注册，其实是发现和访问。

不同点总结来看：
1. 注册粒度不同，一个是进程，一个是 pod.
2. 访问方式不同，zk 的方式更多是直接 ipport 访问，而 k8s 的访问经过层数更多，
3. k8s 的模式更为标准化与云原生。zk/etcd/consul/eureka/自研的方式各种各样，k8s 比较标准化

类似于 zk 的服务注册需要服务自己去往 zk 的某个目录节点写入进程自己的信息 (ip/port), 该目录节点作为服务名或者服务名的一部分，且一般服务 A 通过 zk 拿到服务 B 的某个 ip,port 后直接连接，进行调用

k8s 的 service 代表的是符合某个 selector 的一 系列 pod.k8s 的 pod 是注册在 etcd 中。这两个与 zk 类似。k8s 的注册以 pod 为粒度注册，且 k8s 的 service 访问模式有很多种，

- 集群内访问模式：由 kube- proxy 组件和 Iptables 发现及转发流量。
- 向集群外暴露 Service: LoadBalancer. 外部的负载监听器监听 service 的 pod 变化，然后配置负载均衡器。https://developer.aliyun.com/article/728115

实际访问链路是什么样的呢？比如说从集群内部的一 个 ClientPod3 去访问 Service，就类似于刚才所演示的一个效果。ClientPod3 首先通过 Coredns 这里去解析出 ServicelP,Coredns 会返回给它 ServiceName 所对应的 serviceIP 是什么，这个 ClientPod3 就会拿这个 ServiceIP 去做请求，它的请求到宿主机的网络之后，就会被 kube- proxy 所配置的 iptables 或者 IPVS 去做一层拦截处理，之后去负载均衡到每一个实际的后端 pod 上面去，这样就实现了一个负载均衡以及服务发现。

对于外部的流量，比如说刚才通过公网访问的一一个请求。它是通过外部的一个负载均衡器 CloudController Manager 去监听 service 的变化之后， 去配置的一个负载均衡器，然后转发到节点上的一个 NodePort 上面去，NodePort 也会经过 kube-proxy 的一个配置的一个 iptables，把 NodePort 的流量转换成 ClusterlP，紧接着转换成后端的一 个 pod 的 IP 地址，去做负载均衡以及服务发现。这就是整个 K8s 服务发现以及 K8s Service 整体的结构。

## 南北向和东西向网关融合

service mesh这里有宣传一个理念，南北向和东西向网关融合。

通常来讲，在微服务体系下，网关分为接入网关和业务网关。
istio+envoy（service mesh既定标准和实现）本身没有明显的接入网关业务优势（接入网关的难点是在于网络环境、网络穿越、网络隔离），那看起来 istio ingressgateway 只能替代业务网关。
另一方面，比如比较简单的业务分发，比如路由分发，配置下ingress规则，就可以完成很多常见业务网关要做的事情，感觉没必要再整个业务网关。
ingress做不了的一些事情，我想了下通常是跟实际服务或业务强绑定的，则我理解就是第三层网关（比如shardingphere proxy），这种也不会被归类为业务网关

所以我觉得service mesh南北向和东西向网关统一，应该是指业务网关和东西向流量的统一