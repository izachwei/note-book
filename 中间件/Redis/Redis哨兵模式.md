# Redis哨兵模式

## 什么是哨兵模式？

* 在 Redis 主从架构下，Master 节点负责写数据，异步复制给从节点，Slave 只负责读请求。这样就会存在一个问题，主节点挂了，需要运维人员手动修改配置，将从节点晋升为主节点，同时应用连接 Redis 的数据源信息也要修改。这样无法达到高可用，而哨兵模式解决这个问题，达到真正的高可用。
* 哨兵模式是Redis的高可用方式，是特殊的Redis服务，不提供数据读写服务，主要用来监控redis节点。Redis 客户端连接哨兵集群，哨兵集群中维护了Redis的节点信息，将主节点地址返回给客户端，后面的Redis的请求不再经过哨兵，直连Redis服务。当Redis主节点挂掉以后，Sentinel 在从节点选出新的主节点，同时通知客户端连接新的主节点，从而达到高可用。后续Redis服务的节点信息有变化，哨兵集群会将信息同步给客户端，客户端与哨兵建立一个订阅机制，订阅 Sentinel 发布的节点变动消息。

## 哨兵模式的功能

* 监控 Redis 节点信息
* 通知各个 Redis 节点状态信息
* 自动故障转移

## 哨兵模式工作原理

* 哨兵模式是一个分布式系统，使用流言协议（gossip protocols）来传播 Master 下线消息、投票协议（agreement protocols）来实现主节点的选举。

### 心跳机制

* Setinel 根据配置的 Redis 主节点信息，从 主节点 上获取所有从节点的信息，随后定时向所有 Redis 节点发送info命令，获取 Redis 节点信息以及拓扑结构。
* Sentinel 与 Sentinel 间也会建立沟通机制，主 Sentinel 利用 Redis 发布/订阅功能，向相应频道发送 Redis 节点信息， 其他 Sentinel 都会订阅这个频道拿到节点信息

##### 感知 Master 节点下线

* 每个 Setinel 每隔1s 会向所有 Redis 节点以及其他的 Sentinel 发送 Ping 消息，用于心跳检测
* 如果 Master 回复 Ping 命令超过设置的阈值（默认为30s），Sentinel 将会认为将 master 标记为**主观下线**
* 判断 Master 下线的 Sentinel 将会向其他 Sentinel 发送 entinel is-master-down-by-addr 消息，询问是否同意 master 下线
* Sentinel 收到该消息，会根据发送过来 IP Port 自己判断 Master 是否存活，回复是否同意 master 下线
* 如果收到的同意master下线消息，大于 quorum（配置）的值，则将 master 标记为**客观下线**
* 至此 Master 已经被判定为下线，在一般情况下，每个 Sentinel 每隔 10s 向所有的Master，Slave发送 INFO 命令。当Master 被 Sentinel 标记为客观下线时，Sentinel 向下线的 Master 的所有 Slave 发送 INFO 命令的频率会**从 10 秒一次改为每秒一次**。作用：发现最新的集群拓扑结构

### 利用raft算法选举领头sentinel

* 在判定 Master 下线之后，需要进行故障转移选举出新的Master，需要选举出哪个 Sentinel 来执行该操作，选举过程：

> 1. 判定 Master 下线的 Sentinel1 ，在将 Master 标记为客观下线之后，会其他Sentinel 发送消息，推举自己成为领头。
> 2. 投票规则，先到先得（网络）。Sentinel2 收到 Sentinel1 的消息后回复同意消息，后续假设收到 Sentinel3 的消息，将会拒绝。
> 3. 当 Sentinel 发现选自己的节点 超过了majority 的个数，那么自己将会成为领头节点
> 4. 假设没有一个sentinel 超过 majority 个数，然后会休眠一段时间，再次选举

### 故障转移

* 在选出领头 sentinel后，就要进行 故障转移，这里涉及到如何在 多个 slave 节点选出 master 节点。
* 首先过滤一批不可用的 slave 节点，过滤规则：

> 1. 剔除判断为下线的slaver 节点
> 2. 剔除有5s没有回复sentinel的info命令的slaver
> 3. 剔除与已经下线的主服务连接断开时间超过 down-after-milliseconds * 10 + master宕机时长 的slaver

* 选主过程

>1. 选择优先级最高的节点， redis.conf 中 replical-prority 值越小代表优先级越高
>2. 优先级相同选择，选择 offset 大的，offset 代表了 主节点向从节点同步数据的偏移量，值越大同步的数据约新
>3. 若 sffset 也相同，则选择 runid较 小的

### 修改配置（激活主节点）

* 主节点选择出来后，将会修改配置，让主节点生效

> 1. sentinel 会向 选出来的从节点的执行 slave no one 命令，让其成为主节点
> 2. 向 其余从节点发送 slave 命令，让他们知道谁是 master
> 3. 如果之前的master重新恢复上线，领头sentinel 也会向其发送 slave 命令

### 客户端感知主节点

* 在新的 master 重新上线之后，客户端如何感知主节点？

> 1. 领头Sentinel 在完成 故障转移之后，会向 pubsub 发送一条消息，客户端会订阅这条消息拿到最新的master节点信息
> 2. 客户端主动也会主动去拉取redis节点信息
> 3. 哨兵也提供了钩子逻辑，可以在配置文件写一段脚本，在故障转移完成之后会调用脚本，达到通知客户端的效果

## 总结

* 哨兵的选举过程还是很复杂的，其中包含了分布式共识、分布式协商的机制都是用来保证故障恢复的准确性。
