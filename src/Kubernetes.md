## controller 和 Operator 的异同

一个是CP面，配置下发，也可以认为是一个业务或者是一个svc

另一个是运维部署相关的组件，主要用于自动化扩容缩容，调度部署，自愈恢复，版本升级等等

## CRD 里的 condtions、phase、state这三个该如何理解？

phase/state 是和业务无关的状态，如成功、失败、重试等。conditions 是和业务相关的状态，如校验、初始化、调度等状态，如果业务层的 conditions 状态都没问题，phase/state 状态就是成功的，否则就是其它状态。

phase/state 的一部分原因是因为 没有一个机制把condition转换为string打印出来，所以有的时候为了方便打印，就会引入phase，这样kubectl get的时候就可以看到大概的condition

kubernetes api conventions里面关于condition和phase的介绍：https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api-conventions.md#typical-status-properties