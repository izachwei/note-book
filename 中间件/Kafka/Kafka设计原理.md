# Kafka设计原理

## 核心控制器 Controller

Broker Set -> Controller ，创建 Zookeeper 节点，利用 Zookeeper 实现 Broker 分布式协调。（后续新版本废弃 Zookeeper）

### 具体作用

* Partition Leader 选举
* 分区 ISR 集合发生变化，同步变化信息到其他 broker 节点
* 当使用kafka-topics.sh脚本为某个topic增加分区数量时，同样还是由控制器负责让新分区被其他节点感知到

## GroupCoordinator（组协调器）

消费者组加入

## 如何保证消息不丢失？

### 相关配置

acks

默认 acks = all

```
ack = all/-1：表示分区 isr 的所有副本同步成功以后才返回ack
ack = 0：表示客户端只发送消息，不管服务端是否保存消息成功
ack = 1：表示分区 Leader 节点保存消息成功后返回。
```



### in.insync.replicas

Kafka ISR 列表中最小同步副本数

默认 in.insync.replicas = 1

**根据这两个极端的情况可以看出min.insync.replicas的取值，是kafka系统可用性和数据可靠性的平衡！**

1. 减小 min.insync.replicas 的值，一定程度上增大了系统的可用性，允许kafka出现更多的副本broker crash并且服务正常运行；但是降低了数据可靠性，可能会丢数据（极端情况1）。
2. 增大 min.insync.replicas 的值，一定程度上增大了数据的可靠性，允许一些broker crash掉，且不会丢失数据（只要再次选举的leader是从ISR中选举的就行）；但是降低了系统的可用性，会允许更少的broker crash（极端情况2）。

```
当生产者将确认设置为“all”（或“-1”）时，min.insync.replicas 指定必须确认写入的最小副本数才能被视为成功。如果无法满足此最小值，则生产者将引发异常（NotEnoughReplicas 或 NotEnoughReplicasAfterAppend）。
当一起使用时，min.insync.replicas 和 acks 允许您强制执行更大的持久性保证。一个典型的场景是创建一个复制因子为 3 的主题，将 min.insync.replicas 设置为 2，并使用“all”的 acks 生成。如果大多数副本没有收到写入，这将确保生产者引发异常。
```

