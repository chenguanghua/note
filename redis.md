 ## 1.Redis是单线程还是多线程呢？

​    Redis不同版本之间采用的线程模型是不一样的，在Redis4.0版本之前使用的是单线程模型，在4.0版本之后增加了多线程的支持。
在4.0之前虽然我们说Redis是单线程，也只是说它的网络I/O线程以及Set 和 Get操作是由一个线程完成的。但是Redis的持久化、集群同步还是使用其他线程来完成。4.0之后添加了多线程的支持，主要是体现在大数据的异步删除功能上，例如 unlink key、flushdb async、flushall async 

## 2.redis为什么那么快？

 - 纯内存操作
 - 非阻塞io多路复用
 - 单线程避免上下文切换

## 3.redis的过期策略以及内存淘汰机制
  redis采用的是定期删除+惰性删除策略+内存淘汰机制。 为什么不用定时删
除策略? 定时删除,用一个定时器来负责监视key,过期则自动删除。虽然内存及时释放，但是十分消耗CPU资源。在
大并发请求下，CPU要将时间应用在处理请求，而不是删除key,因此没有采用这一策略. 定期删除+惰性删除是如何
工作的呢? 定期删除，redis默认每个100ms检查，是否有过期的key,有过期key则删除。需要说明的是，redis不是
每个100ms将所有的key检查一次，而是随机抽取进行检查(如果每隔100ms,全部key进行检查，redis岂不是卡
死)。因此，如果只采用定期删除策略，会导致很多key到时间没有删除。 于是，惰性删除派上用场。也就是说在你
获取某个key的时候，redis会检查一下，这个key如果设置了过期时间那么是否过期了？如果过期了此时就会删
除。 采用定期删除+惰性删除就没其他问题了么? 不是的果定期删除没删除key。然后你也没即时去请求key，
也就是说惰性删除也没生效。这样，redis的内存会越来越高。那么就应该采用内存淘汰机制。

**内存淘汰策略**：

**noeviction**:返回错误当内存限制达到并且客户端尝试执行会让更多内存被使用的命令（大部分的写入指令，但DEL和几个例外）

**allkeys-lru**: 尝试回收最少使用的键（LRU），使得新添加的数据有空间存放。

**volatile-lru**: 尝试回收最少使用的键（LRU），但仅限于在过期集合的键,使得新添加的数据有空间存放。

**allkeys-random**: 回收随机的键使得新添加的数据有空间存放。

**volatile-random**: 回收随机的键使得新添加的数据有空间存放，但仅限于在过期集合的键。

**volatile-ttl**: 回收在过期集合的键，并且优先回收存活时间（TTL）较短的键,使得新添加的数据有空间存放。

## 4.持久化

持久化策略 AOF 和RDB , AOF和RBD混合

​       RDB 是把内存中的数据集以快照形式写入磁盘,实际操作是通过 fork 子进程执行，采用二进制压缩存储。

​       AOF 以日志形式记录操作新增和删除 



