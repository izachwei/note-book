## Log-structured

Log-structured 在分布式系统、数据系统都有 Log-Stuctured 结构的应用，大家所熟知的 MongoDB 和 HBase 这类的 NoSQL 数据库，它们的底层存储数据结构其实也是 Log-Structured 结构

### 解决什么问题

为了解决并发写操作，在高并发下同时更新某个值，需要加锁保证数据一致性，操作安全，那有没有方法可以不用顾及写操作的高并发问题，同时也可以最终获得一个准确的结果呢？答案就是使用 Log-Structured 结构。

Log-structured

Log-Structured结构，有时候也会被称作是 Append-only Sequence of Data ，因为所有的写操作都会不停地添加进这个数据结构中，而不会更新原来已有的值，这也是 Log-Structured 结构的一大特性。

存在的问题

* 无限制的写入，数组在内存中无限的增长，如何处理？
* 每次获取结果，都需要遍历一遍数据，时间复杂度非常高，那该怎么优化？
* 平衡树如何被应用在这里面的
* **先写内存，再写磁盘，不会导致丢数据么？**

> 确保数据的持久性，LSM机制通常会使用一种称为写前日志（Write-Ahead Log，WAL）的技术。在WAL中，每次写入操作都会被先追加到一个特殊的日志文件中。这样，即使系统崩溃或断电，写入的数据也可以从日志文件中进行恢复。

#### Log-Structurd 结构的优化

* 拆分成多个 Segment ，Segment 大小固定，Segment 1 写满之后，新的数据将写入新的 Segment 2 中
* 利用 Compaction 后台线程，将不同的 Segments 合并在一起
* Compaction 合并完成之后，合并的结果放在了新的 Segment 中，合并前的Segment 便删除

### SSTable 和 LSM 树

* SSTable（Sorted String Table）数据结构是在 Log-Structured 结构的基础上，多加了一条规则，就是所有保存在 Log-Structured 结构里的数据都是键值对，并且键必须是字符串，在经过了 Compaction 操作之后，所有的 Compacted Segment 里保存的键值对都必须按照字符排序。

### LSM树

LSM树（Log Structured Merge Tree，结构化合并树）的思想非常朴素，就是将对数据的修改增量保持在内存中，达到指定的大小限制后将这些修改操作批量写入磁盘（由此提升了写性能），是一种基于硬盘的数据结构，与B-tree相比，能显著地减少硬盘磁盘臂的开销。当然凡事有利有弊，LSM树和B+树相比，LSM树牺牲了部分读性能，用来大幅提高写性能。

总结一下 LSM-Tree 删除数据的过程：

1. 删除操作视为正常的写入操作，给数据打上删除标记
2. 在合并过程中，删除数据
3. 查询时，此时数据还存在内存/磁盘上，因此在查询会忽略掉带有删除标记的数据

#### 应用

* 采用 Lucence 作为后台索引引擎的开源搜索框架，像 ElasticSearch 和 Solr，底层用的就是 LSM 树
* 有一种特殊情况：所搜索的数据不在保存的数据中，而想要判断数据是否存在该 Segment 中就需要遍历整个 Segment，针对这种情况，搜索引擎底层还加一个 Bloom Filter 这个数据结构。



### WAL(Write Ahead Log) 预写日志

