# Kafka设计原理

## 核心控制器 Controller

Broker Set -> Controller ，创建 Zookeeper 节点，利用 Zookeeper 实现 Broker 分布式协调。（后续新版本废弃 Zookeeper）

### 具体作用

* Partition Leader 选举
* 分区 ISR 集合发生变化，同步变化信息到其他 broker 节点
* 当使用kafka-topics.sh脚本为某个topic增加分区数量时，同样还是由控制器负责让新分区被其他节点感知到

## GroupCoordinator（组协调器）

消费者组加入

## 相关名词

### HW

### LEO

### LSO



## 如何保证消息不丢失？

### Producer 相关配置

**acks**

默认 acks = all

```
ack = all/-1：表示分区 isr 的所有副本同步成功以后才返回ack
ack = 0：表示客户端只发送消息，不管服务端是否保存消息成功
ack = 1：表示分区 Leader 节点保存消息成功后返回。
```



**in.insync.replicas**

Kafka ISR 列表中最小同步副本数

默认 in.insync.replicas = 1

**根据这两个极端的情况可以看出min.insync.replicas的取值，是kafka系统可用性和数据可靠性的平衡！**

1. 减小 min.insync.replicas 的值，一定程度上增大了系统的可用性，允许kafka出现更多的副本broker crash并且服务正常运行；但是降低了数据可靠性，可能会丢数据（极端情况1）。
2. 增大 min.insync.replicas 的值，一定程度上增大了数据的可靠性，允许一些broker crash掉，且不会丢失数据（只要再次选举的leader是从ISR中选举的就行）；但是降低了系统的可用性，会允许更少的broker crash（极端情况2）。

```
当生产者将确认设置为“all”（或“-1”）时，min.insync.replicas 指定必须确认写入的最小副本数才能被视为成功。如果无法满足此最小值，则生产者将引发异常（NotEnoughReplicas 或 NotEnoughReplicasAfterAppend）。
当一起使用时，min.insync.replicas 和 acks 允许您强制执行更大的持久性保证。一个典型的场景是创建一个复制因子为 3 的主题，将 min.insync.replicas 设置为 2，并使用“all”的 acks 生成。如果大多数副本没有收到写入，这将确保生产者引发异常。
```

### 消费者相关配置

**enable-auto-commit = false**

**AckMode** 手动提交 **MANUAL和MANUAL_IMMEDIATE**

MANUAL_IMMEDIATE是消费完一个消息就提交，MANUAL是处理完一批消息，在下一次拉取消息之前批量提交。拉取批量消息可以通过max.poll.record设置最大，默认是500条。前提是消息大小满足最大限制，否则一批也拉取不到最大的500条。

| 模式             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| MANUAL           | poll()拉取一批消息，处理完业务后，手动调用Acknowledgment.acknowledge()先将offset存放到map本地缓存，在下一次poll之前从缓存拿出来批量提交 |
| MANUAL_IMMEDIATE | 每处理完业务手动调用Acknowledgment.acknowledge()后立即提交   |
| RECORD           | 当每一条记录被消费者监听器（ListenerConsumer）处理之后提交   |
| BATCH            | 当每一批poll()的数据被消费者监听器（ListenerConsumer）处理之后提交 |
| TIME             | 当每一批poll()的数据被消费者监听器（ListenerConsumer）处理之后，距离上次提交时间大于TIME时提交 |
| COUNT            | 当每一批poll()的数据被消费者监听器（ListenerConsumer）处理之后，被处理record数量大于等于COUNT时提交 |
| COUNT_TIME       | TIME或COUNT满足其中一个时提交                                |

**auto.offset.reset**

* none
* earliest
* latest

	所有介绍的前提,同一个消费者组下

none

```
如果没有为消费者找到先前的offset的值即没有自动维护偏移量,也没有手动维护偏移量,则抛出异常
```

earliest

```
在各分区下有提交的offset时：从offset处开始消费
在各分区下无提交的offset时：从头开始消费
```

latest

```
在各分区下有提交的offset时：从offset处开始消费
在各分区下无提交的offset时：从最新的数据开始消费
```

**isolation.level=read_committed**

```
READ_UNCOMMITTED((byte) 0), READ_COMMITTED((byte) 1);
```

