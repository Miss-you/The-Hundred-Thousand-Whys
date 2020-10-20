## 如何清空 etcd 里面的脏数据的？

我是手动 etcd delete 然后 make init，不知道是否有更好的做法

delete --prefix "",一键清理。不过v2v3不太一样，v3底层是boltdb，v2底层就是内存序列化落盘。对于v2来讲，直接delete是ok的，对于v3的话kv删掉了，但是boltdb的数据依然存在，所以对于v3需要定期或者每次重建和销毁etcd实例

v2/v3都有WAL日志，所有写请求都会先写到WAL日志，可防止crash后，数据丢失，一个请求经过RAFT一致性模块提交后，etcd就会从一致性模块取出已经提交的请求，执行它，更新状态机，V2状态机就是一颗内存数，v3就是一个key value的boltdb嵌入式库，boltdb本身具备持久化能力的，每次写操作都会基于copy on write机制更新，也就是你删除它，它不是原地删除、更新，整个db大小空间不会回收，除非你主动compact压缩。